---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェント モード →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が知っておくべき背景情報がある場合は、そのコンテキストを利用できるようにします。Copilot と特定のコンテキストを共有する方法はいくつかあります。中でも重要なのは[指示ファイル][instruction-files]です。指示ファイルを使うと、コード生成に関するガイダンスを提供できます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスに応じた指示ファイルを使って、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準を Copilot に伝える方法を確認します。
- 現在の指示が適用された状態で、フィルタリング用の最初のデータ スライス（出版社ヘルパー）を生成します。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加します。
- 同じプロンプトを再実行し、生成されたコードが新しい標準に従う様子を確認します。
- 指示の更新とフィルタリング スライスを `main` にコミットし、次の演習で Copilot が更新されたガイダンスを利用できるようにします。

> [!CAUTION]
> 生成されたコードが、設定した一部の標準に従わない場合があります。Copilot の動作は確定的ではありません。この演習の目的は、指示を更新した後の動作の**傾向**を確認することであり、出力を一字一句一致させることではありません。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。具体的には次のとおりです。

- データ層には常にユニット テストが必要です。
- UI はダーク モードで、モダンな見た目にする必要があります。
- コードには TSDoc のドキュメント コメントを追加する必要があります。
- 各ファイルの先頭に、そのファイルの機能を説明するコメント ブロックを追加する必要があります。

指示ファイルを使って Copilot に適切な情報を提供し、これらの開発手法に沿ったタスクを実行できるようにします。

### カスタム指示

カスタム指示を使うと、コーディング スタイルや要件に関するコンテキストと設定を Copilot に提供できます。これにより、より関連性の高い提案やコード スニペットを生成できるようになります。コーディング規約やライブラリ、コードに含めたいコメントの種類などを指定できます。リポジトリ全体に適用する指示も、特定のファイル タイプに適用するタスク レベルのコンテキストも作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに関する**すべての**リクエストで Copilot に送信される単一の指示ファイルです。Copilot に送信するチャットや CLI のリクエストの大半に関係する、プロジェクト レベルの情報を記載します。たとえば、技術スタック、開発対象の概要、ベスト プラクティス、全般的なガイダンスなどです。
- 特定のタスクやファイル タイプ向けには、`.github/instructions/*.instructions.md` ファイルを作成できます。TypeScript や Astro などの言語や、UI コンポーネントやユニット テストの作成などのタスクに関するガイドラインを記載できます。

> [!NOTE]
> IDE で作業する場合、指示ファイルは Copilot Chat でのコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot クラウド エージェントは、コード生成の際にリポジトリ レベルの指示ファイルと、`applyTo` フロントマターで対象が指定された `*.instructions.md` ファイルの両方を使います。
>
> また、Copilot は AGENTS.md や CLAUDE.md など、ほかの標準形式の指示ファイルにも対応しています。[指示ファイルの対応状況][custom-instructions-support]を参照してください。

### 指示ファイルを管理するためのベスト プラクティス

指示ファイルの作成について詳しく説明することは、このワークショップの範囲外です。ただし、サンプル プロジェクトには参考となるアプローチが示されています。概要は次のとおりです。

- `copilot-instructions.md` には、開発対象やプロジェクト構成の説明、全般的なコーディング標準など、プロジェクト レベルのガイダンスに絞って記載します。
- `*.instructions.md` ファイルは、ファイル タイプ（ユニット テスト、Astro コンポーネント、データ層など）や特定のタスクに関する指示に使います。
- 自然な言葉で、明確なガイダンスを記載します。コードの良い例と悪い例を示してください。

指示ファイルの作成方法は 1 つではありません。AI の使い方と同様に、試行を重ねることでプロジェクトに合った方法が見つかります。

> [!TIP]
> GitHub Copilot を使うすべてのプロジェクトに、充実した指示ファイルを用意することをお勧めします。このプロジェクトの指示ファイルを確認すると、[UI 更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けのファイルが見つかります。
>
> Copilot に指示ファイルを生成してもらうこともできます。各環境で方法は異なります（たとえば VS Code の **Configure Chat → Generate Agent Instructions** や Copilot CLI の `/init`）。詳細が関係する場合は、このレッスンで説明します。
>
> テンプレートや出発点を探していますか？指示ファイル、カスタム エージェント、その他のリソースを集めたリポジトリ [awesome-copilot][awesome-copilot]を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれる指示ファイルを確認してください。中心となる `copilot-instructions.md` が 1 つと、さまざまなタスク向けの `*.instructions.md` ファイルがあります。エディターまたは GitHub の Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明や、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下には、入れ子になった **GitHub Actions Workflows** のガイダンスもあります。これらは Copilot とやり取りする際に適用されます。
3. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データ層、テストなどに関する指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドで、指示の適用対象を決めるリポジトリ ルートからの glob が設定されています。この例では、`**/*.test.ts` に一致する TypeScript テスト ファイルが対象です。
5. このプロジェクトでユニット テストを作成する際に適用される指示を確認します。
6. 最後に `.github/instructions/drizzle.instructions.md` を開いて末尾までスクロールします。`unit-tests.instructions.md` などの指示ファイルや、プロジェクト内の既存ファイルへのリンクがあります。これにより、大きな指示セットを小さく再利用可能なファイルに分け、コード生成時の参考例を Copilot に示せます（ここにあるパスはリポジトリ ルートではなく、指示ファイルからの相対パスです）。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションではプロジェクトのコーディング標準を説明していますが、コード内のドキュメントはまだ必須ではありません。次の手順で、TSDoc のドキュメント コメントとファイル コメント ヘッダーのルールを追加します。
## 変更用のブランチを作成する

ここではベスト プラクティスに従い、変更用のブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する**前**に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は各手順で説明します。

カスタム指示の効果を確認するには、まず現在の指示ファイルを適用した状態でプロンプトを送信します。その後、指示を更新して同じプロンプトを再送信し、違いを確認します。

1. Copilot が必要なコンテキストだけを取得できるよう、前の演習で開いたエディター タブをすべて閉じます。
2. ヘルパーの配置場所を Copilot が把握できるように `src/lib/publishers.ts` を開きます。
3. Chat ビューのエージェント ドロップダウンから **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント ピッカーのスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot がプロジェクトを調べて、ヘルパー ファイルとテストを含むコードの更新を適用します。
6. 提案されたヘルパーが、最初の引数に `db` クライアントを受け取り、出版社の型付き配列を返す関数であることを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータ層規約によるものです。
7. 提案されたコードに TSDoc のドキュメント コメントとファイル レベルのコメント ヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくてもドキュメント コメントを追加する場合があります。その場合も問題ありません。ここで確認したいのは、指示を更新した後に動作の一貫性が向上することです。

## リポジトリ全体に適用する標準を追加する

前述のとおり、`.github/copilot-instructions.md` は Copilot にプロジェクト レベルの情報を提供します。コードの提案を改善するために、リポジトリのコーディング標準を記載しましょう。

1. `.github/copilot-instructions.md` を開き直します。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。プロジェクトのコーディング標準が記載されていますが、コード内ドキュメントに関するルールがないため、生成されたヘルパーにドキュメント コメントがありませんでした。
3. 既存の標準の直後に、次の Markdown 行を追加します。Copilot にファイル コメント ヘッダーと TSDoc のドキュメント コメントを追加するよう指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、指示ファイルはリポジトリ レベル（`.github/copilot-instructions.md`）で作成して全般的なガイダンスを提供することも、特定の言語、ファイル タイプ、タスク向けの `*.instructions.md` ファイルとして作成することもできます。今回追加したドキュメント コメントのルールのように、プロジェクト全体に適用する標準はリポジトリ レベルのファイルに記載します。
## プロンプトを再実行して変化を確認する

1. Copilot Chat に戻り **New Chat** を選択して、入力内容をクリアします。
2. `src/lib/publishers.ts` を選択して、Copilot に適切なファイルを認識させます。
3. 前と**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメント ブロックが追加されていることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に、次のような TSDoc コメントが含まれていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

新しいプロジェクト標準に従うよう Copilot を導き、次の演習で使う実際のコードに適用できました。

## 指示をコミットしてブランチをプッシュする

指示ファイルもリポジトリのほかのファイルと同様に、ソース管理で管理します。指示の更新をコミットし、ブランチをリポジトリにプッシュしましょう。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナル ウィンドウを開きます。
2. ターミナルで次のコマンドを実行し、指示の更新とヘルパーの変更が反映されていることを確認します。

   ```bash
   git status
   ```

3. ターミナルで次のコマンドを実行し、指示の更新とヘルパーの基盤に Copilot が加えたすべての変更をステージングしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ターミナルからブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、プルリクエストを作成してバックログにあるドキュメント標準の issue に関連付けます。手動で行うこともできますが、すでにプロジェクトに接続されている GitHub ツールを使って Copilot に依頼してみましょう。issue を検索し、issue をクローズする PR を作成して、マージするようにプロンプトで依頼します。

> [!NOTE]
> この演習では、Copilot が初めて GitHub 上で作業します。プロジェクト テンプレートでは GitHub のツールがあらかじめワークスペースに接続されています。追加の設定は不要です。Copilot が初めてツールを使うとき、VS Code で GitHub へのサインインを求められる場合があります。指示に従って認証してください。この接続方法である Model Context Protocol については、後の演習で学びます。

1. Codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント ピッカーで **Agent** が選択されていることを確認します。
3. 指示ファイルの更新と、新しい出版社ヘルパーの基盤の両方を説明する PR を現在のブランチから作成し、関連する issue を検索するよう Copilot に依頼します。

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が issue を検索して PR を作成します。
5. Copilot が代わりに GitHub の操作を実行するための **Allow** を求めたら、コマンドを確認して適切に **Allow** を選択します。
6. PR が作成されたら、次のプロンプトを使って PR をマージし、ブランチを `main` に戻すよう Copilot に依頼します。

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot が代わりに GitHub の操作やシェル コマンドを実行するための **Allow** を求めたら、コマンドを確認して適切に **Allow** を選択します。

これで、GitHub Copilot を使ってプルリクエストを作成し、マージできました。

## まとめと次のステップ

このプロジェクトの指示ファイルが Copilot にコンテキストを伝える方法を確認し、VS Code の Copilot Chat を使って次のことを行いました。

- コード生成プロンプトを送り、*既存の*指示で Copilot が生成する内容を確認しました。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加しました。
- 同じプロンプトを再実行し、提案されたコードが新しい標準に従うことを確認しました。
- 次の演習で更新した指示を使えるよう、指示の更新と出版社ヘルパーの基盤を `main` にコミットしました。

次は[エージェント モード][next-lesson]を使って、Copilot がコードベース全体に新機能を追加する際に、指示を活用する方法を確認しましょう。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot 向けのカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [Awesome Copilot — 指示ファイルとその他のリソースのコレクション][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
