---
title: "レッスン 3 - エージェント モード: Plan と Autopilot"
description: "Plan で方針を決め、Autopilot で issue からフィルタリングを実装し、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

最初に小さな機能を追加しましたが、大きな変更にはより確かなプロセスが必要です。GitHub Copilot CLI は、組織の既存の開発フローに沿って適切なものを適切な方法で作れるよう支援します。ここから数回のレッスンでは、issue を起点に機能を作り、コードの妥当性と機能の動作を確認して、最終的にプロジェクトにマージする、一般的なエージェント主導の開発プロセスを体験します。

> [!NOTE]
> 機能の作業を続ける間は、同じ会話とブランチを使います。通常はファイル種別ごとに異なるブランチやプル リクエストを使いますが、ここでは主な概念に集中するため手順を簡略化します。

このレッスンでは、次のことを行います。

- GitHub issue から Copilot の会話を始めます。
- Plan モードで要件を決めます。
- Autopilot モードで新機能を実装します。
- コードをレビューします。
- 転送されたブラウザーで機能を手動検証します。

この機能の作業を続ける中で、リポジトリの instruction を更新し、既存の `quality-checks` スキルをカスタマイズし、MCP による検証を追加して QA エージェントを作成し、機能のプル リクエストを開きます。

## シナリオ

Tailspin Toys のカタログが増えてきたため、訪問者がゲームをカテゴリや出版社で絞り込めるようにします。バックログの issue には機能が記載されていますが、カテゴリを組み合わせる方法など、実装前に合意しておきたい詳細があります。Plan モードで判断事項を整理し、範囲を定めた実装を Autopilot に任せます。

## 背景

AI コーディング エージェントを開発フローに導入しても、基本は変わりません。むしろ、基本を守ることがいっそう重要になります。多くの開発者は、次のような流れで作業します。

1. 必要な作業を記述した issue から始めます。
2. 実装する内容を計画します。
3. コードを作成してレビューします。
4. テストを実行してコードを検証します。
5. 新機能を手動で検証します。
6. プル リクエスト (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功したら、コードをマージします。

> [!NOTE]
> 手順の詳細はチームや組織によって異なりますが、ほとんどの場合はここに示した流れを調整したものです。

この標準的な進め方に沿うことで、AI が生成したコードも要件を満たし、手書きのコードと同じ検証プロセスを通るようにできます。

## 会話モード

**会話モード**は、エージェントにどの程度の自律性を与えるかを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押すとモードを切り替えられます。

- **Interactive**: エージェントと一緒に作業します。エージェントは変更を提案し、次に進む前に入力を待ちます。
- **Plan**: エージェントが計画を作成します。プロジェクト ファイルを編集することはできません。
- **Autopilot**: エージェントがコードの作成、テストの実行、反復を自律的に行い、タスクの完了を目指します。

まず Plan モードで計画を確認し、その後 Autopilot で実装します。

## issue から始める

フィルタリングの作業を始める前に、Codespace に戻り、リポジトリとターミナルを準備します。

1. Codespace に戻ります。停止している場合は再起動します。
2. 星評価のプル リクエストがマージ済みであることを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押します。
4. `main` を更新して、フィルタリング用ブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を 2 回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの issue を選択します。
9. <kbd>C</kbd> を押して issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトが `#7` (または同様の番号) で始まっていることを確認します。`#` を使うと、GitHub の issue やプル リクエスト (PR) をコンテキストに追加できます。

## フィルタリング機能を計画する

計画を立てると、機能の実装やタスクを Copilot に任せる前に、その進め方を決められます。複雑な作業では、まず計画を立てることをお勧めします。Plan モードに切り替えて、Copilot に計画を作成してもらいましょう。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替え、プロンプトの下にあるモード表示が **Plan** になっていることを確認します。
2. 直前の手順で追加した issue の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調べ、最適な方法を検討して計画を作成します。

3. フィルターの動作について質問されたら、希望する内容を回答します。唯一の正解はありません。
4. 計画ができたら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画を展開します。
5. 計画を上から下まで確認します。
6. 判断内容と異なる部分があれば、Copilot に修正を依頼します。

## Autopilot を承認する

計画を作成してレビューしたら、Autopilot で実装します。Autopilot を使うと、Copilot は完了したと判断するまで問題に取り組み続けます。

1. **Accept plan and build on autopilot (recommended)**、またはインストール済みのバージョンで同様の名前が付いた項目を選択します。
2. プロンプトの下にあるモード表示が **Autopilot** であることを確認します。
3. Copilot が計画に沿って反復し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 承認すると実装がすぐに始まることがあります。先に計画をレビューしてください。依存関係の不足やポートの競合が報告された場合は、チェック完了とみなす前にセットアップの問題を解決してください。

## 実装をレビューして検証する

生成されたコードは、ほかのコードと同様にマージ前にレビューします。コードを確認し、サイトを起動して動作も確かめましょう。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードにします。モード表示に **Plan** や **Autopilot** がないことを確認します。
2. `/diff` を入力し、フィルタリングの実装とテストを調べます。
3. 結果が issue と計画で決めた内容に合っているか確認します。
4. レビューが終わったら <kbd>Esc</kbd> を押して差分表示を終了します。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に解決を依頼します。

## 新しい機能を試す

コードに問題がなさそうでも、動作を確認しましょう。前と同じようにアプリを起動し、Codespaces の転送ポートからサイトを開きます。

1. Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能になったら、**Open in Browser** を選択します。
3. カテゴリ、出版社、および計画で合意した組み合わせでフィルターを試します。
4. リセットと結果が空の場合の動作が、issue と合意内容に合っていることを確認します。
5. Codespace に戻り、Copilot に起動した開発サーバーの停止を依頼します。

## まとめと次のステップ

会話モードを切り替えて機能を作成し、レビューしました。このレッスンでは、次のことを行いました。

- GitHub issue から Copilot の会話を始めました。
- Plan モードで要件を決めました。
- Autopilot モードで新機能を実装しました。
- コードをレビューしました。
- 転送されたブラウザーで機能を手動検証しました。

次は、コード生成の進め方をさらに詳しく確認します。[カスタム インストラクションを使う][next-lesson]に進みましょう。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンド リファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
