---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩まされていない組織は、ほとんどないでしょう。未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残った機能要望など、さまざまです。GitHub Copilot の cloud agent は、コードの更新や機能の追加などを自律的に実行できます。作業が完了すると、人間の開発者がレビューできるドラフトのプルリクエスト (PR) を作成します。これにより、手間のかかる作業を任せ、開発者はより大きな課題に集中できます。

Copilot cloud agent を使って、次のことを確認します。

- コード生成環境のカスタマイズ
- 操作を安全に行う方法
- Issue の範囲を明確に定める重要性
- Copilot への Issue の割り当て

## シナリオ

Tailspin Toys には解決したい技術的負債があります。サイトの初期バージョンを開発した業者はドキュメントを十分に作成していません。まず、アプリケーションのエクスポートされたすべての関数に TypeScript のドキュメント コメント (TSDoc) を追加したいと考えています。

また、デザイン チームはゲームを見つけやすくしたいと考えています。ゲームの詳細ページに同じカテゴリの別のゲームを表示すれば、ユーザーはゲームを見続けられます。完成度の高い実装ではなく、ユーザー体験 (UX) の受け入れテストに使えるものを求めています。これは現在の阻害要因ですが、さらに優先度の高い Issue もあります。

どちらも、優先順位が下がりやすいタスクです。Copilot cloud agent に割り当てれば、非同期で作業を進めてもらいながら、開発者は別の作業に集中できます。その後、戻って成果をレビューし、期待どおりか確認できます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同じようにバックグラウンドで作業できます。たとえば、[GitHub Issue を Copilot に割り当てる](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)方法があります。割り当てると、Copilot は進捗を追跡するドラフトのプルリクエストを作成し、環境を準備してタスクに取り組みます。作業中または完了後にセッションを詳しく確認できます。提案された解決策のレビュー準備ができると、Copilot がプルリクエストで通知します。

## 範囲を明確に定める重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単そうな作業でも、詳しく調べると複雑なことがあります。[Copilot cloud agent に依頼するタスクの範囲を慎重に決めてください][cloud-agent-best-practices]。AI ペア プログラマーとして扱い、段階的に進め、学び、試し、必要に応じて調整します。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

前の演習では、カスタム指示と Copilot への影響を確認しました。カスタム指示の演習をすでに行った場合は、`.github/copilot-instructions.md` が機能することを確認済みです。まだの場合は、この機会に簡単に確認してください。Copilot cloud agent に作業を割り当てる前に、このリポジトリに含まれる指示ファイルを読み取り専用で確認しておくと、後でその影響を見つけやすくなります。

リポジトリの GitHub Web UI、または起動済みの codespace で次のファイルを開きます。

- `.github/copilot-instructions.md` - **Code standards** セクション、特に TypeScript の規約と TSDoc ドキュメント コメントに関する要件を確認します。
- `.github/instructions/unit-tests.instructions.md` - `applyTo` frontmatter を確認します。この指示は `**/*.test.ts` ファイルに適用されます。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てたら、作成されたプルリクエストに TSDoc ドキュメント コメント、TypeScript の規約、コメント ヘッダーが反映されているか確認してください。後の演習で PR をレビューするときに、もう一度取り上げます。

## Copilot cloud agent の開発環境を設定する

コードを作成する際は、通常、環境を整え、状態を確認するためのセットアップ スクリプトを実行します。Copilot にタスクを割り当てる場合も同様です。cloud agent もソフトウェア エンジニアのように作業します。

cloud agent は作業環境に [GitHub Actions][github-actions] を使用します。作業開始前に実行する[専用のセットアップ ワークフロー][setup-workflow]を `.github/workflows/copilot-setup-steps.yml` に作成すれば、必要な開発ツールと依存関係を利用できるよう環境をカスタマイズできます。このワークショップでは、演習を円滑に進められるよう、あらかじめ設定されています。単一の Astro アプリ用に、Node.js、プロジェクトの依存関係、エンドツーエンド テスト用のブラウザー バイナリ、マイグレーションとシードが済んだ SQLite データベースを利用できます。

```yaml
name: "Copilot Setup Steps"

# リポジトリの "Actions" タブからセットアップ手順をテストできます
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Copilot には操作用の独自トークンが付与されます。セットアップ手順に必要な最小限の権限を設定してください。
    permissions:
      # セットアップ手順の一部としてリポジトリをクローンする場合 (依存関係のインストールなど) は、contents: read 権限が必要です。クローンしない場合は、手順の完了後に Copilot が自動的にクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンド / アプリのセットアップ - Node.js (アプリ全体は Astro + Drizzle/libSQL です)
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

      # ローカルの SQLite データベースをマイグレーションしてシードします。ビルドとテストでデータを利用できます。
      - name: Set up the database
        run: npm run db:setup
```

ほかの GitHub ワークフローと同じように見えますが、いくつか重要な点があります。

- `copilot-setup-steps` という単一のジョブがあります。このジョブは Copilot がプルリクエストの作業を開始する前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーを使うと、リポジトリの **Actions** タブからワークフローを手動実行できます。Copilot が実行するのを待たずに、正常に動くかテストできます。

## ドキュメントを追加する

多くのプロジェクトでは、ドキュメントが古くなっていたり、まったくなかったりします。この種の技術的負債は放置されやすく、生産性を下げ、コードベースの保守や新しい開発者の受け入れを難しくします。Copilot はドキュメント作成を得意とするため、cloud agent に依頼するのに適した Issue です。バックグラウンドでドキュメントを作成してもらい、後の演習で成果を確認します。

1. 新しいブラウザー タブで github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. **Blank issue** を選択して、新しい Issue を作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    組織では、関数やメソッドの動作を明確にするうえで有用な場合、TSDoc のドキュメント コメントを付けることを求めています。しかし、最近の更新ではこの標準が守られていません。動作を明確にするドキュメント コメントを既存のコードに追加してください。
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てダイアログを開きます。

    ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものです。一覧に PR が表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が todo リストを作成したことを確認します。

> [!NOTE]
> Copilot の todo リストが PR に表示されるまで数分かかる場合があります。Copilot は、前に説明したワークフローを実行して環境を作成し、プロジェクトを分析して、作業方法を決めています。

13. リストを確認し、実行するタスクを把握します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が Issue の作業を開始したことを示す更新を確認します。
15. **View session** ボタンを選択します。

    ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された表示を確認するために、ウィンドウの再読み込みが必要な場合があります。

16. ライブ セッションをスクロールし、Copilot の作業を確認します。コードを調べて状態を理解し、適切な計画を考え、コードを作成する様子が表示されます。

作業には数分かかることがあります。Copilot cloud agent の主な目的の 1 つは、非同期でタスクを進め、開発者が別の作業に集中できるようにすることです。その機能を活用し、もう 1 つのタスクを cloud agent に割り当ててから、アプリケーションの機能追加に取り組みます。

## ゲーム詳細ページに関連ゲームのセクションを追加する

Copilot cloud agent の利点の 1 つは、作業を分担できることです。デザイン チーム向けの関連ゲームの追加に時間がかからないとしても、その時間をほかの作業に使えます。このタスクも Copilot cloud agent に割り当てましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. **Blank issue** を選択して、空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    各ゲームの詳細ページに関連ゲームを表示して、ゲームを見つけやすくします。デザイン チームはユーザー体験を確認し、受け入れテストを行いたいと考えています。要件は次のとおりです。

    - `src/lib/` にデータ アクセス ヘルパーを追加します。指定したゲームを除き、同じカテゴリのほかのゲームを返します。
    - ヘルパーを使って、ゲーム詳細ページに "Related games" セクションを表示します。
    - 関連ゲームがない場合に対応します。
    - 新しいヘルパーの単体テストを作成します。
    - PR を作成する前に、すべてのテストが成功することを確認します。
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てダイアログを開きます。

    ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Issue の最初のコメントに目の絵文字が表示され、Copilot が作業を開始したことが分かります。

![Copilot が Issue に取り組んでいることを示す目の絵文字](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼にも取り組んでいます。Copilot cloud agent はソフトウェア エンジニアと同じように作業するため、常に監視する必要はありません。完了後にレビューできます。続いて、カスタムエージェントの作成と使用を確認します。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents]について学びました。Issue を Copilot に割り当て、非同期に作業を進めてもらえます。技術的負債への対応、新機能の作成、別のフレームワークへのコード移行などに活用できます。

この演習では、次のことを確認しました。

- コード生成環境のカスタマイズ
- 操作を安全に行う方法
- Issue の範囲を明確に定める重要性
- Copilot への Issue の割り当て

cloud agent がバックグラウンドで作業している間に、カスタムエージェントの作成と使用に取り組みます。[Copilot cloud agent は Model Context Protocol (MCP) サーバー][cloud-agent-mcp]も使用できます。また、前のモジュールで確認したカスタム指示も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローに関するベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
