---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前の演習では、Copilot cloud agent にユーザー体験の改善や機能追加に関する 3 つのタスクを依頼しました。cloud agent は非同期かつ自律的に作業しますが、タスクを監視することも重要です。

cloud agent に割り当てたタスクは、GitHub.com の[エージェント ページ][agents-page]など、さまざまなツールで管理できます。このミッション コントロールでは、オープンなプルリクエスト (PR) があるエージェントのタスクを確認できます。実行内容を調べたり、進行中のセッションに指示を追加したりできます。

このレッスンでは、次のことを行います。

- エージェント ページを使って cloud agent のタスクを監視します。
- 実行中のセッションに追加の機能を依頼します。

## シナリオ

エージェントにハイコントラスト モードの作成を依頼した後、チームはライトモードも追加するとよいと考えました。サイトのスタイルと切り替え機能を更新しているため、ハイコントラストに加えてライトモードも実装するよう、エージェントの作業に追加の指示を出します。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたタスクの現在の状態を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)を開きます。
2. メイン ペインと左ペインの両方に表示されるタスクの一覧を確認します。割り当てたタスクには、次の作業が含まれます。
   - コードベースのドキュメントの更新
   - ゲームを変更するアプリケーション プログラミング インターフェイス (API) の生成
   - Web サイトへのハイコントラスト モードの追加
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。次のような操作が表示されます。
   - リポジトリのチェックアウト
   - Copilot の作業環境の作成
   - Model Context Protocol (MCP) サーバーの設定
   - 割り当てられたタスクを完了するための各種作業

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に表示される PR ペインも確認します。ここから PR と変更されたファイルを確認し、作業を監視できます。

## cloud agent に指示を追加する

進行中のタスクを確認したので、エージェントがハイコントラスト モードに取り組んでいる間に、ライトモードの切り替えも追加するよう依頼します。

1. ハイコントラスト モードの追加に関するセッションを選択します。タイトルは Copilot が付けた名前や作業状況によって異なります。

    ![ミッション コントロールに表示されたアクセシビリティ セッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了して作業が始まるまで、数分間セッションを確認します。作業開始後は、次のプロンプトに似たメッセージが表示されます。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

    ```plaintext
    ハイコントラスト モードに加えて、ライトモードも追加してください。ユーザーが表示モードを選べるように、ライトモード用のスイッチも必要です。
    ```

    ![エージェント ページの cloud agent タスクで、Steer active session while Copilot is working ダイアログが強調表示されています](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に反映することを確認します。

## Copilot の作業を続ける

Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で作業中だった手順を終えた後、新しい要望を作業の流れに取り込みます。

この処理には数分かかります。その間に、ここまでに学んだことを振り返ってみましょう。

## まとめと次のステップ

Copilot cloud agent のタスクを監視して案内する中心的な場所である Copilot agents ページについて学びました。進行中と完了済みのタスクを追跡し、作業内容を確認して、進行中のタスクの範囲や指示を調整できます。

このレッスンでは、次のことを行いました。

- ミッション コントロールとエージェント ページを使って cloud agent のタスクを監視しました。
- 実行中のセッションに指示を追加し、機能を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、Copilot が作成した PR を[反復して改善する][next-lesson]方法を確認します。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復して改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
