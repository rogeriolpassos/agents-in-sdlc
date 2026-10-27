---
title: "レッスン 5 - quality-checks スキルをカスタマイズして使う"
description: "既存の quality-checks スキルを確認し、報告形式をカスタマイズしてフィルター機能を検証します。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

コードを書くことは、開発作業の一部にすぎません。これまでに、手動でコードを検証し、指示ファイルで標準に従わせる方法を確認しました。テスト、lint、その他の継続的インテグレーション (CI) の作業はどうでしょうか。

このような作業には、**エージェント スキル**が適しています。スキルを使うと、こうした操作を正しい方法で実行するよう Copilot に伝えられます。

このレッスンでは、次のことを行います。

- 既存の `quality-checks` スキルを確認します。
- 実行結果の報告形式をカスタマイズします。
- スキルを再読み込みして実行します。

## シナリオ

Tailspin Toys では、単体テスト、lint、型チェックに `quality-checks` スキルを使っています。チームは、結果を読みやすくするため、報告形式を改善したいと考えています。

## 指示、スクリプト、リソース

エージェント スキルには、再利用可能なタスクの指示、実行可能なスクリプト、エージェントが必要に応じて読み込む補助リソースが含まれます。基本的には、スキル名のフォルダーと、その中にある `SKILL.md` ファイルで構成されます。Markdown ファイルには、スキルの名称と説明を定義する frontmatter、スキルの概要、呼び出すタイミングのガイダンスを記載します。スキル用のスクリプトやその他のリソースをサブフォルダーに含めることもできます。

> [!NOTE]
> スキルに追加のフォルダーやファイルが必要とは限りません。Tailspin Toys の `quality-checks` スキルは、プロジェクトにある既存のコマンドを使うため、`SKILL.md` だけで構成されています。

スキルをプロジェクトの `.github/skills` に配置すると、チームで共有して再利用できるリポジトリ資産になります。ユーザーのスキル フォルダー `~/.copilot/skills` に配置することもできます。

## スキルを確認する

Tailspin Toys チームが単体テスト、lint、型チェックの実行に使う `quality-checks` スキルを確認しましょう。

1. codespace に戻ります。Codespaces のエディターで `.github/skills/quality-checks/SKILL.md` を開きます。
2. 先頭にある `name` と `description` を読みます。説明により、Copilot はスキルを呼び出すタイミングを判断します。
3. 指示を読み、テストと lint の手順が Copilot にどのように伝わるかを確認します。
4. まだ **Results output formatting** セクションがないことを確認します。

## 変更前にスキルを実行する

Copilot に単体テスト、lint、型チェックの 3 つを実行するよう依頼します。

1. Interactive モードのフィルター機能の会話に戻ります。
2. 次のプロンプトを入力します。

   ```plaintext
   quality-checks スキルを実行して、単体テスト、lint、型チェックを行ってください。
   ```

3. 最後に表示されるレポートを確認します。

## レポートをカスタマイズする

何を実行したか、成功したか、ツールが実際に報告した内容を分かりやすく示すレポートに変更します。

1. `.github/skills/quality-checks/SKILL.md` に戻ります。
2. ファイルの末尾に次のセクションを追加します。

   ```markdown
   ## Results output formatting

   完了後、実行した各コマンドと、その結果が成功、失敗、またはブロックのいずれかを報告します。ツールが報告した場合に限り、テスト件数、所要時間、エラー、警告、その他の指標を記載します。失敗や阻害要因があれば、次に行うべき操作を示します。スキップされたチェックや未完了のチェックを成功と説明してはいけません。
   ```

3. ファイルが自動的に保存されたことを確認します。

## 更新したスキルを実行する

コマンドライン インターフェイス (CLI) である Copilot CLI は、会話を再起動せずに編集されたスキルを再読み込みできます。更新後のレポートを確認しましょう。

1. 次を入力します。

   ```plaintext
   /skills reload
   ```

2. 前とまったく同じプロンプトを入力します。

   ```plaintext
   quality-checks スキルを実行して、単体テスト、lint、型チェックを行ってください。
   ```

3. 最後に表示されるレポートを確認し、最初の結果と比較します。

## まとめと次のステップ

既存のエージェント スキルをカスタマイズして使用しました。このレッスンでは、次のことを行いました。

- 単体テスト、lint、型チェック用の `quality-checks` スキルを確認しました。
- 結果の報告形式をカスタマイズしました。
- スキルを再読み込みして実行しました。

この変更はフィルター機能のプルリクエスト (PR) に含めます。次は、[Playwright Model Context Protocol (MCP) サーバーを使って][next-lesson] Copilot にサイトを直接操作させます。

## スキルの例

次のコミュニティの例は参考資料であり、追加の作業ではありません。

- [Agent Skills 仕様][skill-spec]
- [コントリビューションのワークフロー: `make-repo-contribution`][contribution-example]
- [要件ドキュメント: `prd`][prd-example]
- [図とバンドルされたエクスポート スクリプト: `drawio`][drawio-example]
- [ブラウザー テスト: `webapp-testing`][browser-example]

[previous-lesson]: ../4-custom-instructions/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing
