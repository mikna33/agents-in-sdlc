---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: エージェント モード][previous-lesson] · [次のレッスン: カスタム エージェント →][next-lesson]

エージェント モードを使ってフィルタリング機能を作成しました。プルリクエストを作成する前に、ブラウザーで実際に動作することを確認します。アプリを手動で操作する代わりに、**Playwright MCP サーバー**を接続して、Copilot に実際のブラウザーで機能をテストしてもらいます。その後、ブランチを公開して PR を作成します。

この演習では、次のことを行います。

- Model Context Protocol (MCP) とは何か、MCP サーバーが新しいツールで Copilot を拡張する方法を学びます。
- Playwright MCP サーバーをワークスペースに追加します。
- Copilot に Playwright MCP サーバーを使って、ブラウザーでフィルタリング機能を手動テストするよう依頼します。
- ブランチを公開し、フィルタリング作業のプルリクエストを作成します。

## Model Context Protocol (MCP) とは

エージェント モードは、エディターの外部にアクセスできると、さらに強力になります。Copilot は Model Context Protocol (MCP) を使って外部ツールにアクセスします。MCP は、エージェントが外部のツールやサービスと通信するための標準です。

![エージェント モードの内部構造と、コンテキスト、LLM、ツール（MCP サーバーと VS Code 拡張機能が提供するツールを含む）とのやり取りを示す図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/)を使うと、AI エージェントが外部ツールやサービスと通信できます。MCP を利用すると、AI エージェントは外部のツールやサービスとリアルタイムで通信できます。これにより、リソースを使って最新情報にアクセスしたり、ツールを使って代わりに操作を実行したりできます。

これらのツールやリソースは、AI エージェントと外部ツールやサービスの橋渡しとなる MCP サーバーを通じて利用できます。MCP サーバーは、AI エージェントと外部ツール（既存の API や NPM パッケージなどのローカル ツール）間の通信を管理します。各 MCP サーバーは、AI エージェントが利用できるツールとリソースを提供します。

よく使われる MCP サーバーをいくつか紹介します。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理するための API を提供します。AI エージェントは、新しいリポジトリの作成、既存リポジトリの更新、issue やプルリクエストの管理などを実行できます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供します。AI エージェントは、Web ページへの移動、フォームへの入力、ボタンの選択などを実行できます。

さまざまなツールやリソースを提供する MCP サーバーはほかにもあります。GitHub は、エコシステム内の MCP サーバーの発見や貢献を促進するために、[MCP レジストリ](https://github.com/mcp)をホストしています。

> [!CAUTION]
> セキュリティに関して、MCP サーバーはプロジェクト内のほかの依存関係と同様に扱ってください。MCP サーバーを使う前に、ソース コードを慎重に確認し、発行元を検証して、セキュリティへの影響を検討してください。信頼できる MCP サーバーのみを利用し、機密リソースや操作へのアクセスを許可する際には注意してください。
## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースで利用する MCP サーバーを構成します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. `github` サーバーがすでに構成されていることを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

この `github` エントリはプロジェクト テンプレートに含まれています。そのため、前の演習で Copilot がバックログの issue を読み取ることができました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使うため、ローカルにインストールするものはありません。VS Code は HTTP 経由で接続し、Copilot がサーバーのツールを初めて使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次に、2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]は、Copilot が操作できるブラウザーを提供します。これは機能のテストに必要なものです。

1. `.vscode/mcp.json` で `github` と並べて `playwright` エントリを追加し、ファイルを次のようにします。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` は**ローカル** サーバーです。VS Code が `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、Playwright はウィンドウを表示せずにブラウザーを実行します。Codespace には表示用のデスクトップがないため、この設定が必要です。

> [!NOTE]
> Tailspin Toys プロジェクトではエンドツーエンド テストに Playwright をすでに使用しているため、通常、Playwright が必要とするブラウザーはインストール済みです。後で Copilot がブラウザーが見つからないと報告した場合は、`npx playwright install chromium` を実行して、もう一度試してください。

## サーバーを起動して信頼する

VS Code は MCP サーバーを**必要に応じて**起動します。Copilot がサーバーのツールを初めて必要としたときに VS Code が起動し、サーバーを信頼するか確認します。

1. `.vscode/mcp.json` で `playwright` エントリの上に **Start** が表示されます。これを選択してサーバーを起動し、構成が正しいことを確認します。
2. VS Code からサーバーを信頼するか確認されたら、構成を確認して、サーバーを起動できるように信頼することを選択します。

手動での起動は省略できます。スキップした場合は、次の手順で Copilot が初めてツールを必要としたときに、VS Code がサーバーを起動して信頼を確認します。

## フィルタリング機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が実際のブラウザーを操作できます。アプリを手動で操作して動作を確認する代わりに、エージェントにページを開き、移動し、フィルターを適用して、その結果を確認してもらえます。その後、確認した内容を要約できます。会話を離れずに機能が期待どおりに動作するか確認できる、最も速い方法です。

内部では、Playwright MCP サーバーはスクリーンショットではなく、ページの[アクセシビリティ ツリー][playwright-mcp-server]を使用します。つまり、エージェントは支援技術と同様に、ラベル付きの構造化要素（ボタン、リンク、リスト項目など）をもとに判断します。そのため、簡単な機能確認をアクセシビリティの簡易チェックとしても活用できます。

サーバーを接続してアプリを実行した状態で、Copilot に次のように依頼し、作成したフィルタリング機能をテストします。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバーを通じてブラウザーを起動し、各手順を実行して結果を報告します。issue の受け入れ基準と照らし合わせて報告内容を確認してください。想定と異なる場合は、追加の質問をするか、プルリクエストを作成する前にコードの修正を依頼します。

> [!NOTE]
> このテストでは、アプリが `http://localhost:4321` で実行されている必要があります。開発サーバーを停止した場合は、プロンプトを送る前に再起動します。Copilot が Playwright MCP サーバーを初めて使うとき、ブラウザーをダウンロードする場合があります。ブラウザーが見つからないと報告された場合は、`npx playwright install chromium` を実行して、もう一度試してください。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
## ブランチを公開してプルリクエストを作成する

機能が動作することを確認したので、チームでレビューするためにプルリクエスト（PR）を作成します。まず `filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルに移動し、Copilot が加えた変更を確認します。
2. **+** アイコンを選択して変更をステージングします。
3. **Sparkle** ボタンを使ってコミット メッセージを生成します。

   ![加えた変更が表示された Source Control パネルのスクリーンショット](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択して、ブランチをリポジトリにプッシュします。

## プルリクエストを作成する

プルリクエストを作成する方法はいくつかあります。github.com や GitHub コマンドライン インターフェイス (CLI) を使う方法などです。GitHub Copilot を使っているので、PR の作成も依頼してみましょう。Copilot に関連する issue を検索し、その issue に関連付けられた PR を作成してもらいます。

1. Copilot Chat パネルに移動して **New Chat** を選択し、新しいセッションを開始します。
2. GitHub ツールを使えるよう、エージェントのドロップダウンから **Agent** が選択されていることを確認します。
3. 次のように Copilot に PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 必要に応じて **Continue** を選択し、Copilot が情報を収集して操作を実行することを許可します。GitHub ツールを初めて使うときは、GitHub での認証を求められることがあります。指示に従って認証してください。
5. Copilot が issue を検索し、該当するものを見つけて PR を作成します。
6. Copilot が生成したリンクを選択してプルリクエストを確認します。ただし、**まだマージしないでください**。

## まとめと次のステップ

おめでとうございます。この演習では、次のことを行いました。

- Model Context Protocol (MCP) とは何か、MCP サーバーが新しいツールで Copilot を拡張する方法を学びました。
- Playwright MCP サーバーをワークスペースに追加しました。
- 出荷前に、ブラウザーでフィルタリング機能を手動テストしました。
- ブランチを公開し、フィルタリング作業のプルリクエストを作成しました。

MCP サーバーを使って機能を*テスト*しましたが、MCP は Copilot を拡張する方法の 1 つにすぎません。次は[カスタム エージェントを作成][next-lesson]して、アクセシビリティ レビューなどの対象を絞ったタスクを効率化しましょう。

## リソース

- [MCP とは何か、なぜ話題になっているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP レジストリ][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
