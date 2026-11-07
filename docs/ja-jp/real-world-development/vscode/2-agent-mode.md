---
title: "演習 2 - Copilot エージェント モードで新機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

最も簡単なアプリケーションの更新でも、通常は複数のファイルを変更し、テストを実行するなどの操作が必要です。開発者は通常、必要なファイルを探し、変更を加え、テストを実行して、問題をデバッグし、変更し忘れたファイルを見つけて、さらに更新するという作業を行います。こうした作業は際限なく続きます。

Copilot エージェント モードは、このようなときに役立ちます。

Copilot エージェント モードは、IDE 内でより自律的に作業できるように設計されています。開発者と同様に、まず既存のプロジェクト構成を調べ、必要な更新を行い、テストなどのタスクを実行して、見つかった問題を自動的に修正します。エージェント モードを使ってサイトに新しい機能を追加する方法を確認しましょう。

> [!NOTE]
> 名前は似ていますが、エージェント モードとクラウド エージェントは異なるタイプのエクスペリエンスを提供します。エージェント モードは IDE 内でタスクを実行するため、すばやいフィードバックとやり取りができます。クラウド エージェントはチーム メンバーのように非同期で作業し、issue とプルリクエストを通じてやり取りする、ペア プログラマーとして設計されています。

この演習では、次のことを学びます。

- Copilot エージェント モードでプロジェクトを調べ、関連するファイルを特定し、複数のファイルにまたがる変更を行う方法。
- GitHub Copilot エージェント モードで UI とデータ層にまたがる新機能を実装する方法。
- コードベースにマージする前に、Copilot エージェント モードが生成した変更とテストをレビューする方法。

## シナリオ

ゲームの一覧が増えてきたため、ユーザーがカテゴリや出版社で絞り込めるようにしたいと考えています。前の演習では出版社ヘルパーを追加しました。次は Copilot エージェント モードを使って、残りのデータ層、UI、テストの作業を完了します。

## Tailspin Toys Web サイトを実行する

変更を加える前に、Tailspin Toys Web サイトを確認して、現在の機能を把握しましょう。

この Web サイトは、開発者向けのテーマを備えたボード ゲームのクラウドファンディング プラットフォームです。ゲームの一覧や詳細を表示できます。サイトは Astro アプリ 1 つで構成され、ビルド時にページを静的 HTML としてレンダリングします。ページは frontmatter から Drizzle ORM を使ってローカルの SQLite データベースに直接クエリを実行します。独立したバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータ アクセス ヘルパーは `src/lib/` に置き、インタラクティブな機能は標準 DOM API を使う小さなスコープ付き Astro `<script>` で追加します。

### Web サイトを起動する

Web サイトを簡単に実行できるよう、単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で次の手順を実行します。

1. Codespace に戻ります。前の演習後に `main` ブランチにいるはずです。
2. Codespace 内で <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナル ウィンドウを開きます。
3. フィルタリング作業用のブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドを実行して Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL を示すバナーが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう求めるダイアログ ボックスが表示された場合は、**x** を選択して閉じます。

5. ターミナルで <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら Astro サーバーのアドレス `http://localhost:4321` を選択して、Web サイトを開きます。

> [!NOTE]
> Codespace を使っている場合、Codespace のターミナルで localhost の URL を選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` にリダイレクトされます。Codespace で Web サーバーをホストするための、プライベート トンネルです。

### Web サイトを確認する

Web サイトの実行後、機能を確認できます。主な機能は次のとおりです。

- **ホーム ページ**: ボード ゲームのタイトル、画像、説明を一覧表示します。
- **ゲーム詳細ページ**: ゲームを選択すると詳細ページが表示され、ゲームのタイトル、説明、出版社、カテゴリなどの情報を確認できます。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は各手順で説明します。

Web サイトの初期実装は機能しますが、新しい機能を追加して改善したいと考えています。まずバックログを確認しましょう。テンプレートからプロジェクトをセットアップしたときに、issue のバックログが自動的に作成されています。GitHub Copilot に項目を表示してもらいましょう。

1. Chat ビューのエージェント ドロップダウンから **Agent** を選択します。**Agent** エージェントは、自律的に変更の計画と実装を行い、ファイルをまたぐ変更やターミナル コマンドの実行、ツールの呼び出しを行います。

   ![Chat ビューのエージェント ピッカーのスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルの一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> ワークショップの作成者は、特定のモデルを推奨しているわけではありません。このワークショップの作成時に Claude Sonnet 4.5 を使用したため、手順にも記載しています。快適に学習できるよう、比較的一貫したコードの提案を期待しています。ただし、LLM は確率的に動作するため、ワークショップに記載したものとは異なる提案が表示される場合があります。これは正常な動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使う場合がありますが、タスクは同様に完了できるはずです。

3. 次のプロンプトを Copilot に送信して、issue のバックログについて尋ねます。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```
4. **Continue** を選択して、すべての issue を一覧表示するコマンドを実行します。
5. 生成された issue の一覧を確認します。

Copilot が、ユーザーに最も役立つと考えた項目に基づいて優先順位も付けていることに注目してください。

## 指示ファイルを確認する

Copilot にコードを生成させる前に、作業のコンテキストを伝えるために使用する指示ファイルを確認しましょう。Web サイトへの機能追加に関するガイダンスを含む [ユーザー インターフェイス (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md) ファイルを使います。

1. Codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加の進め方に関する全般的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネントの設計、テスト可能性、アクセシビリティに関する原則。
   - 次のファイル タイプなど、さまざまな指示ファイルへのリンク。
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 指示ファイルでは、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルの場所からの相対パスです。これにより、複雑な指示を再利用可能な小さなファイルに分け、例やテンプレートを示せます。

## フィルタリング機能を実装する

フィルタリングを完成させるには、アプリケーションに少なくとも 3 つの更新が必要です。

- データ層 (`src/lib/`) に残りのフィルタリング ロジックを追加または調整する。
- フィルタリングの動作を対象とするテストを追加または更新する。
- ゲーム一覧ページにフィルタリング UI を追加する。

さらに、コードベースにマージする前にテストを実行して成功させる必要があります。Copilot エージェント モードを使えば、これらのタスクを実行できます。機能を追加しましょう。

1. Copilot との現在の会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択して、指示ファイルを指定します。

   ![UI 指示ファイルの選択例を示すスクリーンショット](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント ドロップダウンで、引き続き **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント ピッカーのスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. モデルとして引き続き **Claude Sonnet 4.5** が選択されていることを確認します。
5. バックログにある関連 issue の要件に基づいて機能を実装するよう、次のプロンプトを使って Copilot に依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がまずプロジェクトを調べて、必要な機能に関連するファイルを探す様子を確認します。データ層のヘルパー、UI、テストを見つけて、ファイルを変更し、テストを実行します。

   ![Copilot がプロジェクト ファイルを調べている様子を示すスクリーンショット](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行など、複数のタスクを実行します。タスクやコードベースの複雑さによっては、数分かかる場合があります。その間、コード エディターに **Keep** と **Undo** ボタンが表示されることがあります。Copilot の作業中に選択する必要はありません。作業が完了すると、すべての変更に対して **Keep** または **Undo** を選択できます。

7. Copilot に求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat ペインでテストの実行を確認するダイアログのスクリーンショット](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりすることがあります。問題ありません。Copilot はコード生成とテストを繰り返し、タスクを完了してエラーが見つからなくなるまで作業します。

   ![Copilot エージェント モードのチャット セッションのスクリーンショット](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot やほかの AI ツールが生成したコードをレビューすることは、常に重要です。

10. Web サイトを実行しているブラウザーに戻り、新しい機能を確認します。
11. すべてが動作することを確認してコードをレビューしたら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

おめでとうございます。この演習では、GitHub Copilot エージェント モードを使って Tailspin Toys Web サイトに機能を追加する方法を確認しました。次のことを学びました。

- GitHub Copilot エージェント モードで UI とデータ層にまたがる新機能を実装する方法。
- Copilot エージェント モードでプロジェクトを調べ、関連するファイルを特定し、複数のファイルにまたがる変更を行う方法。
- コードベースにマージする前に、Copilot エージェント モードが生成した変更とテストをレビューする方法。

次は[Playwright MCP サーバーを使って機能をテストし][next-lesson]、プルリクエストを作成します。

### ボーナスの演習 — ページネーションを実装する

ゲームの一覧が増えると、ページネーションが必要になります。この演習で学んだ方法を使い、Copilot にページネーションを実装するよう依頼してください。次のような点を検討できます。

- 既存の指示ファイルを使うなど、既存のベスト プラクティスに従う。
- ページサイズをユーザーが選べるようにするか、固定にするかなど、ページネーションの実装方法を検討する。
- 希望どおりの実装ができるよう、Copilot に十分なガイダンスを与えるプロンプトを作成する。
- GitHub Copilot に変更を依頼してコンテキストを追加するなど、何度か反復する必要がある場合があります。これは Copilot を使う際の通常の流れです。

## リソース

- [Copilot の ask、edit、agent モード: 機能と使い分け][choose-mode]
- [VS Code のエージェント モード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
