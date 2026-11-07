---
title: "演習 2 - GitHub Copilot クラウド エージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩んでいない組織は、ほとんどないでしょう。未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残ったままの機能リクエストなど、さまざまな例があります。GitHub Copilot のクラウド エージェントは、コードの更新や機能の追加といったタスクを自律的に実行できます。エージェントが作業を終えると、人間の開発者がレビューできるようにドラフト PR を作成します。これにより、面倒な作業を任せて開発プロセスを加速し、開発者はより大局的な作業に集中できます。

Copilot クラウド エージェントを使って、次のことを確認します。

- コード生成のために環境をカスタマイズする方法。
- 操作を安全に実行する方法。
- issue のスコープを明確に定義する重要性。
- issue を Copilot に割り当てる方法。

## シナリオ

Tailspin Toys は、いくつかの技術的負債を解消したいと考えています。サイトの初期バージョンを開発した請負業者が、ドキュメントを十分に整備していませんでした。実際、ドキュメントがまったくありません。まず、アプリケーションのエクスポートされたすべての関数に TSDoc のドキュメント コメントを追加したいと考えています。

さらに、デザイン チームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリのほかのゲームを表示して、ユーザーが閲覧を続けられるようにします。まだ完成度の高い実装は必要なく、UX の受け入れテストに使えるものを求めています。この作業は現在ブロッカーですが、ほかに優先度の高い issue があります。

どちらも、優先度を下げられがちな作業の例です。Copilot クラウド エージェントに割り当てると、開発者がほかの作業に集中している間に非同期で処理できます。後から作業内容を確認し、期待どおりかレビューできます。
## GitHub Copilot クラウド エージェントの概要

[GitHub Copilot クラウド エージェント](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)は、人間の開発者と同じようにバックグラウンドでタスクを実行できます。人間の開発者と作業するときと同様に、[GitHub issue を Copilot に割り当てる](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)など、複数の方法で作業を任せられます。割り当てると、Copilot は進捗を追跡するドラフト プルリクエストを作成し、環境をセットアップしてタスクを開始します。作業中でも完了後でも、Copilot のセッションを詳しく確認できます。提案された解決策のレビュー準備ができると、プルリクエストで通知されます。
## 適切なスコープの指示が重要

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単そうな操作にも、詳しく調べると複雑な面があります。そのため、[Copilot クラウド エージェントに依頼するタスクのスコープに注意してください][cloud-agent-best-practices]。AI ペア プログラマーとして扱い、段階を踏んで学習、実験、適応してください。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習ではカスタム指示と、その指示が Copilot を導く仕組みについて学びました。カスタム指示の演習をすでに完了した場合は、`.github/copilot-instructions.md` が機能することを確認しています。まだの場合は、この機会に簡単に確認してください。Copilot クラウド エージェントに作業を割り当てる前に、このリポジトリに含まれる指示ファイルを読み取り専用で確認しておくと、後で指示の効果を見つけやすくなります。

リポジトリの GitHub Web UI で次のファイルを開くか、Codespace がすでに実行中の場合はそこで開いてください。

- `.github/copilot-instructions.md` - **Code standards** セクション、特に TypeScript の規約と TSDoc のドキュメント コメントに関する要件を確認します。
- `.github/instructions/unit-tests.instructions.md` - `applyTo` フロントマターで、指示の適用先が `**/*.test.ts` ファイルに限定されていることを確認します。

次のセクションでは、issue「Code lacks documentation」をクラウド エージェントに割り当てます。生成されたプルリクエストで TSDoc のドキュメント コメント、TypeScript の規約、コメント ヘッダーを確認してください。これらのルールは指示ファイルに記載されています。後の演習で PR をレビューする際にも取り上げます。

## Copilot クラウド エージェント用の開発環境をセットアップする

誰が担当する場合でも、コードを作成する際には通常、特定の環境と、良好な状態を保つために実行するセットアップ スクリプトが必要です。SWE と同様の方法でタスクを実行する Copilot に作業を割り当てる場合も同じです。

クラウド エージェントは、作業環境に [GitHub Actions][github-actions]を使用します。作業開始前に実行する[特別なセットアップ ワークフロー][setup-workflow]を `.github/workflows/copilot-setup-steps.yml` ファイルで構成すると、環境をカスタマイズできます。これにより、必要な開発ツールや依存関係を利用できるようになります。このラボの流れをスムーズにし、学習に集中できるように、あらかじめ設定してあります。Copilot が Node.js、プロジェクトの依存関係、エンドツーエンド テスト用のブラウザー バイナリ、単一 Astro アプリ用にマイグレーションとシードを適用した SQLite データベースを利用できるようにします。

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

一般的な GitHub ワークフロー ファイルと似ていますが、重要な点がいくつかあります。

- `copilot-setup-steps` という単一のジョブが含まれています。このジョブは、Copilot がプルリクエストの作業を開始する前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーがあるため、リポジトリの **Actions** タブからワークフローを手動で実行できます。Copilot に実行させる前にワークフローが正常に動くか確認できるので便利です。

## ドキュメントを追加する

ドキュメントが重要だと誰もが理解していますが、多くのプロジェクトでは情報が古かったり、そもそもドキュメントがなかったりします。この種の技術的負債は放置されがちで、生産性を下げたり、コードベースの保守や新しい開発者の受け入れを難しくしたりします。Copilot はドキュメントの作成を得意としているため、Copilot クラウド エージェントに割り当てるのに最適な issue です。バックグラウンドで必要なドキュメントを生成します。後の演習で作業内容を確認します。

1. 新しいブラウザー タブで github.com のリポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue のダイアログを開きます。
4. **Blank issue** を選択して issue を作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てのダイアログを開きます。

  ![issue への Copilot の割り当て](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

  ![Copilot の割り当ての詳細](../../../_images/ex4-assign-copilot-details.png)

10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト（PR）を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものです。一覧に新しい PR が表示されない場合は、少し待ってからブラウザー ウィンドウを更新してください。
12. 数分後、Copilot が ToDo リストを作成していることを確認できます。

> [!NOTE]
> Copilot が作成する ToDo リストが PR に表示されるまで、数分かかることがあります。Copilot は、前に説明したワークフローを実行して環境を作成し、プロジェクトを分析して、問題に取り組むための最適な方法を検討しています。

13. リストと、実行するタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールすると、Copilot が issue の作業を開始したことを知らせる更新が表示されます。
15. **View session** ボタンを選択します。

  ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新されたインジケーターを表示するには、ウィンドウを更新する必要がある場合があります。

16. ライブ セッションをスクロールして Copilot の問題解決の様子を確認できることに注目してください。コードを調べて状態を理解し、適切な計画を立てるために思考を整理し、コードを作成していく過程を確認できます。

完了まで数分かかる場合があります。Copilot クラウド エージェントの主な目的の 1 つは、タスクを非同期に実行して、ほかの作業に集中できるようにすることです。この機能を活用するため、もう 1 つのタスクをクラウド エージェントに割り当ててから、アプリケーションに機能を追加するコードの作成に取りかかります。

## ゲームの詳細ページに関連ゲームのセクションを追加する

前述のとおり、GitHub Copilot クラウド エージェントの大きな利点は、作業を分担できることです。デザイン チーム向けに関連ゲームのセクションを追加する作業はそれほど時間がかからないかもしれませんが、その時間をほかの作業に使えます。Copilot クラウド エージェントに割り当てましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue のダイアログを開きます。
4. **Blank issue** を選択して、空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てのダイアログを開きます。

  ![issue への Copilot の割り当て](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Copilot が作業を開始したことを示す 👀 が issue の最初のコメントに表示されます。

![issue の作業中であることを示す Copilot の目の絵文字](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼にも取り組んでいます。Copilot クラウド エージェントは SWE と同じような方法で作業するため、常に監視する必要はありません。完了後にレビューできます。次は、カスタム エージェントの作成と使用について学びましょう。

## まとめと次のステップ

このレッスンでは、[GitHub Copilot クラウド エージェント][copilot-agents]について学びました。クラウド エージェントに issue を割り当てると、タスクを非同期に実行できます。技術的負債への対応、新機能の作成、あるフレームワークから別のフレームワークへのコード移行などに Copilot を利用できます。

次の概念を確認しました。

- コード生成のために環境をカスタマイズする方法。
- 操作を安全に実行する方法。
- issue のスコープを明確に定義する重要性。
- issue を Copilot に割り当てる方法。

クラウド エージェントがバックグラウンドで作業を進めている間に、次はカスタム エージェントの作成と使用に取り組みます。[Copilot クラウド エージェントは MCP サーバーも利用でき][cloud-agent-mcp]、前のモジュールで学んだカスタム指示にも対応しています。

## リソース

- [Copilot クラウド エージェントについて][copilot-agents]
- [GitHub issue を Copilot に割り当てる][assign-issue]
- [Copilot クラウド エージェントのセットアップ ワークフローに関するベスト プラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタム エージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
