---
title: "演習 3 - カスタム エージェント"
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
カスタム エージェントを使って、次のことを確認します。

- カスタム エージェントの定義方法。
- カスタム エージェントへのタスクの割り当て方法。

## シナリオ

Tailspin Toys は、視覚能力や好みにかかわらず、すべてのユーザーがクラウドファンディング プラットフォームを利用できるようにすることに取り組んでいます。最近、テキストと背景色のコントラストが不十分なため、現在のダーク テーマでは読みにくいと感じるユーザーがいることが分かりました。このアクセシビリティの問題に対処するため、デザイン チームはユーザーが切り替えられるハイ コントラスト モードの実装を依頼しました。

アクセシビリティは重要なので、この機能をできるだけ早く実装したいと考えています。カスタム エージェントを利用して機能を生成します。
## アクセシビリティ カスタム エージェントを確認する

アクセシビリティ用のカスタム エージェントは、すでに作成されています。内容を確認して、Copilot をどのように導くか理解しましょう。

Codespace に戻り、アクセシビリティ カスタム エージェントのファイルを確認します。

1. `.github/agents/accessibility.md` を開きます。
2. `name` と `description` フィールドが含まれる YAML フロントマターを確認します。

> [!CAUTION]
> カスタム エージェントには、`name` と `description` を含むフロントマターが必要です。

3. 続くセクションを確認します。次の内容が説明されています。
   - アクセシブルな Web サイトのコード生成を担当する際の主な責務。
   - アクセシビリティのベスト プラクティス。
   - HTML、CSS、JavaScript のコード例。
   - よくある落とし穴や間違い。
## issue を作成して割り当てる

ミッション コントロールは、環境内のすべてのエージェントを操作する中心的な場所です。Copilot クラウド エージェントへのタスクの割り当て、タスクの監視、作業の方向付けや追加のガイダンスの提供ができます。まず、Copilot にハイ コントラスト モードを作成するタスクを割り当てましょう。

1. リポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい issue のダイアログを開きます。
4. **Blank issue** を選択して issue を作成します。
5. **Title** に `Add high contrast mode to website` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    We need a high contrast mode for the site. There should be a toggle for high contrast which the user can set. It should store the setting in local storage on the browser.
    ```

7. **Create** を選択して issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てのダイアログを開きます。
9. 一覧から **Accessibility agent** を選択します。

    ![カスタム エージェントとして Accessibility agent が選択された、クラウド エージェントの割り当て画面のスクリーンショット](../../../_images/ex5-select-custom-agent.png)

10. **Assign** を選択します。
11. Copilot がバックグラウンドでタスクを開始します。

## まとめと次のステップ

このレッスンでは、特定のタスクや分野に特化した AI アシスタントである、GitHub Copilot の[カスタム エージェント][custom-agents]について学びました。カスタム エージェントを使うと、チームの専門知識や標準を再利用可能な形式にまとめ、特定の作業をより効果的に実行するよう Copilot を導けます。

次の概念を確認しました。

- カスタム エージェントの定義方法。
- カスタム エージェントへのタスクの割り当て方法。

Copilot がハイ コントラスト モードを実装している間に、次は[ミッション コントロールからエージェントのセッションを監視して方向付けます][next-lesson]。

## リソース

- [カスタム エージェントについて][custom-agents]
- [組織でカスタム エージェントを使用するための準備][org-custom-agents]
- [Enterprise でカスタム エージェントを使用するための準備][enterprise-custom-agents]

---

| [← 前のレッスン: GitHub Copilot クラウド エージェント][previous-lesson] | [次のレッスン: エージェントの監視と管理 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../2-cloud-agent/
[next-lesson]: ../4-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[org-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents
[enterprise-custom-agents]: https://docs.github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents
