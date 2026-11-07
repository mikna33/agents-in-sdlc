---
title: "演習 4 - カスタム エージェント"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## カスタム エージェントとは

GitHub Copilot の[カスタム エージェント][custom-agents-concept]を使うと、開発ワークフロー内の特定のタスクや分野に特化した AI アシスタントを作成できます。リポジトリの `.github/agents` フォルダーに Markdown ファイルでエージェントを定義すると、特定の作業をより効果的に実行できるよう、対象を絞った指示、ベスト プラクティス、コーディング パターン、分野固有の知識を Copilot に与えられます。チームの専門知識を再利用可能なエージェントとして形式化できます。たとえば、[WCAG][wcag] 準拠を徹底するアクセシビリティ エージェント、安全なコーディングを実践するセキュリティ エージェント、一貫したテスト パターンを保つテスト エージェントを作成できます。

カスタム エージェントは、プロジェクトの `.github/agents` フォルダーにある Markdown ファイル、または `~/.copilot/agents` にあるグローバル ファイルで定義されます。各ファイルには、少なくとも `name` と `description` を含む YAML フロントマターと、エージェントの動作、専門知識、指示を定義する Markdown プロンプトが必要です。

### カスタム エージェントとエージェント スキルの違い

カスタム エージェントと[エージェント スキル][agent-skills-concept]には、役割が一部重なるところがあります。どちらも主に Markdown ファイルで定義し、AI に作業の実行方法を指示します。最も分かりやすい区別は、**カスタム エージェントが作業者で、スキルがツール**だということです。

カスタム エージェントには独自のコンテキスト ウィンドウがあり、スキルやほかのエージェントを組み合わせて作業を進めるように構成できます。このラボでは、アクセシビリティのカスタム エージェントがガイドラインに沿ってサイトをレビューし、更新します。その作業の一部として、プルリクエストのワークフロー スキルやテストの実行・管理スキルなどを呼び出すこともできます。

> [!NOTE]
> カスタム エージェントを作成するための唯一の「正しい」方法はありません。AI を使うときと同様に、さまざまな環境やシナリオでテストと反復を行ってください。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/
## シナリオ

Tailspin Toys は、視覚能力や好みにかかわらず、すべてのユーザーがクラウドファンディング プラットフォームを利用できるようにすることに取り組んでいます。最近、テキストと背景色のコントラストが不十分なため、現在のダーク テーマでは読みにくいと感じるユーザーがいることが分かりました。このアクセシビリティの問題に対処するため、デザイン チームはユーザーが切り替えられるハイ コントラスト モードの実装を依頼しました。

アクセシビリティは重要なので、この機能をできるだけ早く実装したいと考えています。カスタム エージェントを利用して機能を生成します。
この演習では、次のことを行います。

- 既存のアクセシビリティ カスタム エージェントを確認します。
- Copilot Chat でアクセシビリティ エージェントを使い、ハイ コントラスト モードを実装します。

## アクセシビリティ カスタム エージェントを確認する

アクセシビリティ用のカスタム エージェントは、すでに作成されています。内容を確認して、Copilot をどのように導くか理解しましょう。

まず Codespace に戻り、アクセシビリティ作業用の `main` から新しいブランチを作成して切り替えます（演習 3 のフィルタリング PR とは分けて作業します）。

```bash
git checkout main
git pull
git checkout -b accessibility-vscode
```

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` フィールドが含まれる YAML フロントマターを確認します。

> [!CAUTION]
> カスタム エージェントには、`name` と `description` を含むフロントマターが必要です。

3. 続くセクションを確認します。次の内容が説明されています。
   - アクセシブルな Web サイトのコード生成を担当する際の主な責務。
   - アクセシビリティのベスト プラクティス。
   - HTML、CSS、JavaScript のコード例。
   - よくある落とし穴や間違い。
## Copilot Chat でカスタム エージェントを使う

VS Code では、`.github/agents` で定義されているすべてのカスタム エージェントが、Copilot Chat ビュー下部のエージェント ドロップダウンに表示されます。カスタム エージェントを選択すると、そのエージェントの指示とツールに合わせてチャット セッションを設定できます。

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なります。必要な場合は各手順で説明します。

1. Chat ビューのエージェント ドロップダウンから、まだ選択されていない場合は **Agent** を選択します。

   ![Chat ビューのエージェント ピッカーのスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

2. チャット ビューの下部にあるエージェント ドロップダウンを選択します。アクティブなエージェント（デフォルトは **default**）が表示されます。
3. 利用可能なエージェントの一覧から **Accessibility agent** を選択します。
4. 次のプロンプトをアクセシビリティ エージェントに送信します。

    ```plaintext
    Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
    ```

5. Copilot Chat が作業を始めます。コードベースを調べて変更を提案し、プロジェクト ファイルを編集します。編集内容は、ファイル パスと差分とともにチャットに表示されます。
6. 編集が反映されると **Files changed** インジケーターが更新され、エージェントが変更したファイルを確認できます。

> [!NOTE]
> この処理には数分かかる場合があります。Copilot はリポジトリに実際の変更を加えています。Astro コンポーネント、CSS、関連テストなど既存のファイルを編集することがあります。

進行中の作業は、次の演習で確認して方向付けます。

## まとめと次のステップ

このレッスンでは、特定のタスクや分野に特化した AI アシスタントである、GitHub Copilot の[カスタム エージェント][custom-agents]について学びました。カスタム エージェントを使うと、チームの専門知識や標準を再利用可能な形式にまとめ、特定の作業をより効果的に実行するよう Copilot を導けます。

次の概念を確認しました。

- カスタム エージェントの定義方法。
- Copilot Chat のエージェント モードでカスタム エージェントを使う方法。

次は[エージェントの作業を監視して方向付けます][next-lesson]。変更を確認し、同じセッションにライト モードの切り替えを追加します。

## リソース

- [カスタム エージェントについて][custom-agents]
- [IDE でカスタム エージェントを作成する][creating-custom-agents-ide]
- [VS Code のカスタム エージェント][custom-agents-vscode]
- [カスタム エージェントの構成][custom-agents-config]
- [awesome-copilot のカスタム エージェント][awesome-copilot-agents]

---

| [← 前のレッスン: Playwright MCP サーバーで機能をテストする][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
