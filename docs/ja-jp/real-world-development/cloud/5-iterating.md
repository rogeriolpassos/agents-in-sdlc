---
title: "演習 5: GitHub Copilot の作業を反復して改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このワークショップでは、ユーザー体験の改善や機能追加を目的に、GitHub Copilot といくつかのタスクに取り組みました。コードへのドキュメント追加、デザイン チームが改善できる関連ゲーム機能の作成、ハイコントラストとライトモードの切り替えを含むアクセシビリティ機能の実装を依頼しました。コードの変更を確認し、必要に応じて Copilot にフィードバックを伝え、作業を改善しましょう。

### シナリオ

これまでに何度も確認したように、生成 AI を導入しても、ソフトウェア設計と DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスを進める必要があります。チームでのレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能に関する GitHub Copilot の提案を確認しましょう。

## GitHub Copilot cloud agent のセキュリティ

Copilot cloud agent は非同期に、監督者なしでタスクを実行するため、安全性を保つためのセキュリティ制約があります。

- Copilot はリポジトリへの読み取りアクセス権を持ち、書き込みアクセスはコード用ブランチに限られます。
- cloud agent は GitHub Actions 内で動作し、作業用に分離された一時的な環境を作成します。
- GitHub Actions のワークフローを実行するには、人間による承認が必要です。
- Model Context Protocol (MCP) サーバーを含む[外部リソースへのアクセスは、既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

まず、GitHub Copilot cloud agent が作成した最初のプルリクエスト (PR)、コードへのドキュメント追加を確認します。GitHub.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR を確認するときに、ファイアウォールによって GitHub Copilot がブロックされたという警告が表示される場合があります。これは想定された動作です。Copilot は、外部 MCP サーバーへの呼び出しを含む外部リソースへのアクセスが既定で制限されています。必要に応じて、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して、PR の一覧を開きます。
3. **Add missing documentation** などのタイトルが付いたプルリクエストを開きます。タイトルはこれと少し異なる場合があります。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が含まれます。その場合は作業が完了するまで待ちます。数分かかることがあるため、休憩したり、ここまでに学んだことを振り返ったりしてください。

4. PR の準備ができたら、**Files changed** タブを選択し、変更をレビューします。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. TSDoc のドキュメント コメントなど、新しく追加されたドキュメントを含むコードを確認します。変更内容は状況によって異なります。TSDoc のドキュメント コメント、TypeScript の規約、コメント ヘッダーを確認してください。これらは、演習 2 の最初に確認したカスタム指示ファイルによって追加されます。
6. 更新内容を確認して問題がなければ、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示があるか確認します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

    ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR のチェック セクションにワークフローが追加されることを確認します。正常に動作すれば、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。

## GitHub Copilot に変更を依頼する

Copilot と PR で作業するとき、一方的に作業を依頼するだけではありません。チーム メンバーに依頼するのと同じように、PR のコメントやコードへのインライン コメントで Copilot にメンションできます。Copilot はコメントを確認し、対応するための新しいセッションを開始します。結果は確定的ではないため、必ずしも同じ文面を指定する必要はありません。たとえば、次のような更新を依頼できます。

- 各コード ファイルの先頭に、ファイルの概要を説明するコメント ヘッダーを追加する。
- TypeScript と Astro のファイルに TSDoc のドキュメント コメントを追加する。
- Astro アプリの構造を説明する README を作成する。

1. 上の例を使うか、コードベースに追加したいドキュメントを考えて、**@copilot** にメンションするコメントを追加し、生成されたドキュメントの変更を依頼します。
2. **View Session** を選択し、Copilot の作業を確認します。更新のために新しいセッションが開始されます。
3. **Back to pull request** を選択すると、PR に戻れます。

    ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot の変更が完了したら、PR に新しいコミットが追加されたことを確認します。
5. **Files changed** タブを選択し、変更をレビューします。

納得できるまで、引き続き改善を依頼できます。問題がなければ、ドラフト PR をレビュー可能な状態に変更して `main` ブランチにマージします。

![PR をレビュー可能な状態に変更する](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

次に、ゲーム詳細ページへの関連ゲーム表示を実装したプルリクエスト (PR) を確認します。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。タイトルはこれと少し異なる場合があります。
4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 更新内容を確認して問題がなければ、**Conversation** タブに戻り、下にスクロールします。
6. ワークフローの承認待ちを示す表示があるか確認します。
7. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

    ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

8. PR のチェック セクションにワークフローが追加されることを確認します。正常に動作すれば、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
9. **オプション:** codespace でこのブランチに切り替え、関連ゲーム機能を手動でテストできます。codespace に移動してターミナルを開き、次のコマンドを実行します (`<branch-name>` は Copilot が作成したブランチ名、たとえば **copilot/fix-8** に置き換えます)。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

関連ゲーム機能が実装されました。Copilot cloud agent には、コメントを追加して変更を依頼することで、引き続き改善を依頼できます。たとえば、表示する関連ゲームの数を調整したり、コメント ヘッダーと TSDoc のドキュメント コメントを追加したりできます。ただし、この Issue はカスタム指示を更新する**前**に割り当てたため、その点に注意してください。前と同じように、**Conversation** タブでコメントを追加すると、Copilot が確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ用のカスタムエージェントを使って実装した機能をレビューします。この PR には、演習 3 で依頼したハイコントラスト モードと、演習 4 のミッション コントロールで依頼したライトモードの両方が含まれているはずです。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。タイトルはこれと少し異なる場合があります。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が含まれます。その場合は完了するまで待ちます。数分かかる場合があります。

4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 実装を確認し、特に次の点に注意します。
   - 表示モードを切り替える UI コンポーネント
   - ユーザー設定を保存する local storage の使用
   - ハイコントラスト モードとライトモードの Cascading Style Sheets (CSS) またはスタイルの変更
   - アクセシビリティ属性 (Accessible Rich Internet Applications (ARIA) ラベル、キーボード ナビゲーションなど)
   - モード切り替えを管理する JavaScript / TypeScript コード

6. 更新内容を確認して問題がなければ、**Conversation** タブに戻り、下にスクロールします。
7. ワークフローの承認待ちを示す表示があるか確認します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

    ![ワークフローを承認して実行する](../../../_images/shared-approve-workflows.png)

9. PR のチェック セクションにワークフローが追加されることを確認します。正常に動作すれば、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
10. **オプション:** codespace でこのブランチに切り替え、アクセシビリティ機能を手動でテストできます。codespace に移動してターミナルを開き、次のコマンドを実行します (`<branch-name>` は Copilot が作成したブランチ名に置き換えます)。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    次にアプリケーションを起動し、ブラウザーでハイコントラストとライトモードの切り替えをテストします。ページを再読み込みしても設定が保持されることを確認してください。

アクセシビリティ用カスタムエージェントが、アクセシビリティのベストプラクティスに沿って Copilot の実装を導いたことを確認します。アクセシビリティ上の懸念や改善点があれば、前の PR と同じようにコメントで **@copilot** に変更を依頼できます。

## オプションの演習 — 委任を続ける

cloud agent には実際のバックログ項目を割り当て、その間に別の作業を進める方法が適しています。この方法に慣れるため、リポジトリにいくつか Issue を登録し、Copilot に割り当ててみてください。たとえば次のような作業があります。

- ゲーム詳細ページに支援者向けの問い合わせフォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データ アクセス ヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent ハーネスを完了しました。各レッスンを通じて、次のことを行いました。

- **このリポジトリに含まれるカスタム指示ファイルを確認し、後で cloud agent の出力に反映される内容を把握しました。**
- **Copilot cloud agent に Issue を割り当て、環境の準備、計画、非同期の実行を確認しました。**
- **アクセシビリティ用カスタムエージェントを作成して使用し、ハイコントラストとライトモードの切り替えを追加しました。**
- **Copilot agents ページをミッション コントロールとして使い、アクセシビリティ セッションを監視して作業中に指示を追加しました。**
- **cloud agent のプルリクエストをレビューして改善し、`@copilot` に変更を依頼し、ワークフローを承認しました。**

## 振り返りと次のステップ

Cloud agent ハーネスはこれで完了です。さらに学びたい場合は、ほかのハーネスで今回の内容を発展させられます。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE から Copilot Chat のエージェント モードと MCP の連携を確認します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot モード、エージェント スキル、QA カスタムエージェント、Playwright MCP、Agent Merge、cloud agent への任意の `/delegate` を使い、ターミナルからレビュー済みの変更を 2 つ届けます。

自分のリポジトリでは、次のアイデアを試してください。

- リファクタリングやテスト カバレッジの Issue を cloud agent に割り当てます。
- 別の分野向けのカスタムエージェントを作成します。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定します。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot agents について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローに関するベストプラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを設定する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
