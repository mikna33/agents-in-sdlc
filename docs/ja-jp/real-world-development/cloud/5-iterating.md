---
title: "演習 5: GitHub Copilot の作業を反復的に改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このラボでは、ユーザー エクスペリエンスの改善や機能追加を目的として、GitHub Copilot といくつかのタスクに取り組みました。コードにドキュメントを追加し、デザイン チームが反復的に改善するための関連ゲーム機能を作成し、ハイ コントラストとライト モードの切り替えを含むアクセシビリティ機能を実装するよう依頼しました。コードの変更内容を確認し、必要に応じて Copilot にフィードバックを伝えて改善してもらいましょう。

### シナリオ

生成 AI を導入しても、ソフトウェア設計と DevOps の基本は変わりません。生成されたコードを常にレビューし、通常の DevOps プロセスに沿って作業を進めます。まず、チームでのレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能の作成に関する GitHub Copilot の提案を確認しましょう。
## セキュリティと GitHub Copilot クラウド エージェント

Copilot クラウド エージェントは監視されずに非同期でタスクを実行するため、安全性を保つためのセキュリティ上の制約が設けられています。次のような制約があります。

- Copilot はリポジトリへの読み取りアクセスのみを持ち、書き込みアクセスはコード用ブランチ**だけ**に限られます。
- クラウド エージェントは GitHub Actions 内で実行され、作業用の独立した一時環境を作成します。
- GitHub Actions のワークフローを実行するには、人間による承認が必要です。
- MCP サーバーを含む[外部リソースへのアクセスは、デフォルトで制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。
## 生成されたドキュメントをレビューする

まず、GitHub Copilot クラウド エージェントが作成した最初のプルリクエスト（PR）である、コードへのドキュメント追加を確認します。GitHub.com の標準的な PR インターフェイスを使って確認します。

> [!NOTE]
> PR を確認するときに、GitHub Copilot がファイアウォールでブロックされているという警告が表示される場合があります。これは**想定どおり**です。Copilot は、外部 MCP サーバーへの呼び出しを含む外部リソースへのアクセスがデフォルトで制限されています。必要に応じて、[Copilot クラウド エージェントのファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して、プルリクエストの一覧を開きます。
3. **Add missing documentation** など、より具体的なタイトルのプルリクエストを開きます。

> [!NOTE]
> Copilot がまだタスクを実行中の場合、プルリクエストには **[WIP]** フラグが付いています。その場合は、Copilot の作業が完了するまで待ちます。数分かかる場合があるため、休憩したり、これまでに学んだことを振り返ったりしてください。

4. プルリクエストの準備ができたら **Files changed** タブを選択して、変更内容を確認します。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. 新たに更新されたコードを確認します。TSDoc のドキュメント コメントや、そのほかのドキュメントが含まれています。具体的な変更内容は異なります。

    変更を確認するときは、TSDoc のドキュメント コメント、TypeScript の規約、コメント ヘッダーを探してください。これらのルールは演習 2 の開始時に確認したカスタム指示ファイルで設定しました。

6. 更新内容を確認し、問題がなければ **Conversation** タブに戻って下にスクロールします。
7. ワークフローの承認待ちを示すインジケーターが表示されます。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)
9. プルリクエストのチェック セクションでワークフローがキューに追加されます。問題がなければ、単一 Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。

## GitHub Copilot に変更を依頼する

プルリクエストで Copilot とやり取りする方法は、一方向だけではありません。チーム メンバーに依頼する場合と同様に、プルリクエストやコードのインライン コメントで Copilot にメンションできます。Copilot はコメントを確認し、対応するための別のセッションを開始します。結果は予測できないため、必ずこう依頼すべきという文面はありません。次のような変更を Copilot に依頼できます。

- 各コード ファイルの先頭に、内容を簡潔に説明するコメント ヘッダーを追加する。
- TypeScript と Astro のファイルに TSDoc のドキュメント コメントを追加する。
- Astro アプリの構成を説明する README を作成する。

1. 前述の案を使うか、コードベースに追加したいドキュメントについて別の案を考え、**@copilot** をメンションして生成されたドキュメントの変更を依頼します。
2. **View Session** を選択して Copilot の作業を確認します。更新のために新しいセッションが始まります。
3. **Back to pull request** を選択して、プルリクエストに戻れます。

    ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot の変更が完了すると、プルリクエストに新しいコミットが表示されます。
5. **Files changed** タブを選択して変更を確認します。

満足するまで反復を続けてください。完了したら、PR をドラフトからレビュー可能な状態に変更して、`main` ブランチにマージできます。

![PR をレビュー可能な状態に変更する](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲームの詳細ページに関連ゲームを表示する issue に対して、Copilot が作成した PR を確認しましょう。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** など、より具体的なタイトルの PR を選択します。
4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 更新内容を確認し、問題がなければ **Conversation** タブに戻って下にスクロールします。
6. ワークフローの承認待ちを示すインジケーターが表示されます。
7. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)
8. プルリクエストのチェック セクションでワークフローがキューに追加されます。問題がなければ、単一 Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
9. **オプション:** Codespace でこのブランチに切り替えて、関連ゲーム機能を手動でテストすることもできます。Codespace に移動し、ターミナルを開いて次のコマンドを実行します（`<branch-name>` は Copilot が作成したブランチ名、たとえば **copilot/fix-8** に置き換えてください）。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot が関連ゲーム機能を実装しました。前と同じように、Copilot クラウド エージェントと反復的に作業して、更新を依頼できます。たとえば、表示する関連ゲームの数を調整したり、コメント ヘッダーや TSDoc のドキュメント コメントを追加したりできます（カスタム指示を更新する**前**に割り当てたタスクであることを思い出してください）。前と同様、**Conversation** タブに新しいコメントを追加すると、Copilot が確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ カスタム エージェントを使って実装したアクセシビリティ機能を確認します。この PR には、演習 3 で依頼したハイ コントラスト モードと、演習 4 のミッション コントロールで依頼したライト モードが含まれています。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** など、より具体的なタイトルの PR を選択します。

> [!NOTE]
> Copilot がまだタスクを実行中の場合、プルリクエストには **[WIP]** フラグが付いています。その場合は、Copilot の作業が完了するまで待ちます。数分かかる場合があります。

4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 実装をレビューします。特に次の点に注意してください。
   - モード切り替え用の UI コンポーネント。
   - ユーザー設定を保持するためのローカル ストレージの利用。
   - ハイ コントラスト モードとライト モードの CSS またはスタイルの変更。
   - アクセシビリティ属性（ARIA ラベル、キーボード ナビゲーションなど）。
   - モード切り替えを管理する JavaScript/TypeScript コード。

6. 更新内容を確認し、問題がなければ **Conversation** タブに戻って下にスクロールします。
7. ワークフローの承認待ちを示すインジケーターが表示されます。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)
9. プルリクエストのチェック セクションでワークフローがキューに追加されます。問題がなければ、単一 Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
10. **オプション:** Codespace でこのブランチに切り替えて、アクセシビリティ機能を手動でテストできます。Codespace に移動し、ターミナルを開いて次のコマンドを実行します（`<branch-name>` は Copilot が作成したブランチ名に置き換えてください）。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    次にアプリケーションを起動し、ブラウザーでハイ コントラスト モードとライト モードを切り替えて、ページを再読み込みしても設定が保持されることを確認します。

アクセシビリティ カスタム エージェントがアクセシビリティのベスト プラクティスに沿って Copilot の実装を導いたことに注目してください。アクセシビリティに懸念がある場合や改善が必要な場合は、前の PR と同様にコメントで **@copilot** に更新を依頼できます。

## オプションの演習 — さらに作業を委任する

クラウド エージェントは、実際のバックログ項目を任せて、ほかの作業に取り組める場合に特に役立ちます。習慣として身に付けるために、リポジトリにいくつか issue を作成して Copilot に割り当ててみてください。たとえば、次のようなものがあります。

- ゲームの詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データ アクセス ヘルパーに入力検証とエラー処理を追加する。

## まとめ

クラウド エージェント ハーネスを完了しました。これらのレッスンでは、次のことを行いました。

- **このリポジトリに用意されたカスタム指示ファイルを確認し**、クラウド エージェントの出力への影響を後で確認できるようにしました。
- **issue を Copilot クラウド エージェントに割り当て**、非同期で環境をセットアップし、計画を立て、実行する様子を確認しました。
- **カスタム エージェントを作成して利用し**、アクセシビリティのためにハイ コントラスト モードとライト モードの切り替えを追加しました。
- **Copilot エージェント ページをミッション コントロールとして使い**、進行中のアクセシビリティ セッションを監視して方向付けました。
- **クラウド エージェントのプルリクエストをレビューして反復的に改善し**、変更を依頼するために `@copilot` にメンションしてワークフローを承認しました。

## レビューと次のステップ

クラウド エージェント ハーネスを完了しました。引き続き学習したい場合は、ほかのハーネスで今回の内容をさらに実践できます。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE から直接 Copilot Chat のエージェント モードと MCP 統合について学びます。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot モード、エージェント スキル、カスタム QA エージェント、Playwright MCP、Agent Merge、およびクラウド エージェントに戻るオプションの `/delegate` ブリッジを使い、ターミナルからレビュー済みの変更を 2 つ実施します。

ご自身のリポジトリで、次のことを試してください。

- リファクタリングやテスト カバレッジの issue をクラウド エージェントに割り当てる。
- 別の分野向けのカスタム エージェントを作成する。
- 異なるスタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot エージェントについて][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot クラウド エージェントのセットアップ ワークフローに関するベスト プラクティス][cloud-agent-best-practices]
- [Copilot クラウド エージェントのファイアウォールを構成する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
