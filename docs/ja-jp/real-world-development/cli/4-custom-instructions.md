---
title: "レッスン 4 - カスタム インストラクションで Copilot を導く"
description: "リポジトリの instruction を確認し、ドキュメント標準を追加して、フィルタリングのコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使うときはコンテキストが重要です。タスクを特定の方法で進める必要がある場合は、そのガイダンスを Copilot が利用できるようにします。[Instruction file][instruction-files]には、必要なコードだけでなく、その構成方法も記述できます。フィルタリング機能を実装したので、Copilot が参照した instruction を確認し、ドキュメント標準を追加してコードに適用します。

このレッスンでは、次のことを行います。

- リポジトリの instruction とパス単位の instruction file が、エージェントにどのように渡されるかを確認します。
- コーディング標準が守られるよう instruction file を更新します。
- instruction file がコードに与える影響を確認します。

## シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインと要件があります。たとえば、次のとおりです。

- コメントには、コードの言い換えではなく、意図や自明でない判断を記述します。
- `db/` と `src/lib/` のエクスポート関数には、目的、パラメーター、戻り値を説明する TSDoc/JSDoc を記述します。該当する場合は、注入可能な `db` 引数についても記載します。
- 再利用可能な Astro コンポーネントには `Props` の契約を記述します。関連するコードを変更したらコメントも更新します。
- 既存のフォーマットと lint のガイダンスを維持します。

Instruction file を使うと、こうしたプラクティスに沿ってタスクを進めるために必要な情報を Copilot に伝えられます。

## Instruction file

Custom instruction は、コンテキストや設定を伝え、Copilot がコーディング スタイルや要件を理解するために使います。コーディング規約、ライブラリ、コメントの書き方などを指定できます。リポジトリ全体に適用するものと、特定のファイル種別に適用するものを作成できます。

Instruction file には次の 2 種類があります。

- `.github/copilot-instructions.md`: リポジトリへの**すべて**のリクエストで Copilot に渡される単一の instruction file です。ほとんどのリクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md`: 特定の言語、ファイル種別、タスクに適用するガイドラインです。

> [!NOTE]
> ほかの instruction 形式の対応状況は環境によって異なります。形式に依存する前に、[custom instructions のサポート リファレンス][custom-instructions-support]を確認してください。

## このプロジェクトの custom instruction file を確認する

スターター プロジェクトには、すでに instruction file が含まれています。変更を加える前に確認し、影響を把握しましょう。

1. Codespace に戻ります。
2. Codespaces のエディターで `.github/copilot-instructions.md` を開きます。ターミナルでは開かないでください。
3. プロジェクトの説明とコーディング ガイダンスを確認します。この instruction は、このリポジトリでの Copilot とのすべてのやり取りに適用されます。
4. `.github/instructions` フォルダーを開き、Astro、Drizzle データ レイヤー、テストなどの instruction を確認します。
5. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。ここで instruction が適用されるファイルを指定します。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの instruction file や既存ファイルへのリンクを確認します。大きな instruction を小さく再利用しやすいファイルに分割し、Copilot が参考にする例を示せます。

## チームのガイダンスに合わせて instruction file を更新する

既存のファイルを補うため、コアの `copilot-instructions.md` に新しい TypeScript のドキュメント規則を追加します。

1. `.github/copilot-instructions.md` の **Code formatting guidance** セクション (35 行目付近) を探します。
2. 最後の箇条書きに次を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

## 更新したガイダンスを適用する

Copilot CLI は会話の開始時にリポジトリの instruction を読み込みます。instruction の更新を読み込ませつつ、機能のコンテキストを保つため、フィルタリングの会話を終了して再開します。

1. フィルタリングの会話が開いている場合は、`/exit` を入力します。会話は自動的に保存されます。
2. `game-filters-cli` ブランチでリポジトリのルートから、保存済みの会話を再開します。

   ```bash
   copilot --resume
   ```

   プロンプトが表示されたら、フィルタリングの会話を選び、`game-filters-cli` ブランチがチェックアウトされていることを確認します。

3. Copilot に更新後のガイダンスを適用するよう依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。追加された TSDoc が正確に説明されていることを確かめます。

## まとめと次のステップ

Copilot CLI が instruction を読み込む方法を確認し、新しい標準を適用しました。このレッスンでは、次のことを行いました。

- リポジトリの instruction とパス単位の instruction file がエージェントに渡る仕組みを確認しました。
- コーディング標準に合わせて instruction file を更新しました。
- instruction file がコードに与える影響を確認しました。

次は、再利用可能な `quality-checks` スキルを[カスタマイズして実行][next-lesson]し、lint とテストが一貫して実行されるようにします。

## リソース

- [Copilot CLI にカスタム インストラクションを追加する][instruction-files]
- [Custom instructions のサポート][custom-instructions-support]
- [Custom instructions 作成のベスト プラクティス][instructions-best-practices]
- [Awesome Copilot — instruction file などのコレクション][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
