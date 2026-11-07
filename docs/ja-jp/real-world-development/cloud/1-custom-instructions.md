---
title: "演習 1 - カスタム指示 (クラウド エージェント)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot クラウド エージェント →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が知っておくべき背景情報がある場合は、そのコンテキストにアクセスできるようにします。[指示ファイル][instruction-files]を使うと、Copilot にガイダンスを提供できるため、何をしてほしいかだけでなく、どのように実行してほしいかも理解してもらえます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスに応じた指示ファイルを使って、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準を Copilot に伝える方法を確認します。
- `.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加します。

> [!NOTE]
> VS Code や CLI のハーネスとは異なり、この演習ではプロンプトの実行前と実行後を比較しません。Copilot クラウド エージェントは GitHub issue を使って非同期に動作するため、リアルタイムで並べて効果を示すのは困難です。このハーネスの後半で、クラウド エージェントが作成したプルリクエストをレビューする際に、指示ファイルの影響を確認します。

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
## リポジトリ全体に適用する標準を追加する

この演習で行う編集は、コード内に TSDoc のドキュメント コメントとファイル レベルのコメント ヘッダーを記載するという、プロジェクト全体のルールを追加することです。後続の演習で issue を割り当てると、クラウド エージェントはこのルールに従います。

編集の前に、作業用のブランチを作成します（クラウド エージェントは issue が対象とするブランチの指示を読み取りますが、ここでは編集内容をきちんとコミットします）。

1. Codespace のターミナルで、新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

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
## 指示をコミット、プッシュしてマージする

クラウド エージェントは、issue の対象ブランチにある指示ファイルを読み取ります。次の演習で Copilot に issue を割り当てると、`main` からブランチが作成されます。そのため、クラウド エージェントが指示を利用できるようにするには、変更を `main` にマージする必要があります。

1. ステージングしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを作成し、マージします。次の演習で issue を割り当てると、クラウド エージェントはこの指示を読み取ります。

> [!TIP]
> このワークショップで `main` ブランチを直接使う場合は、ブランチを作成せずに `main` にコミットしてもかまいません。ここでは、実際のプロジェクトでの作業方法に近づけるためにブランチを作成しています。

## まとめと次のステップ

このプロジェクトの指示ファイルが Copilot にコンテキストを伝える方法を確認し、`.github/copilot-instructions.md` に新しいリポジトリ全体の標準を追加しました。残りのハーネスでクラウド エージェントが作成するプルリクエストを確認する際に、この標準が適用されているかを確認します。

次に、[Copilot クラウド エージェントに最初の issue を割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot 向けのカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルとその他のリソースのコレクション][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
