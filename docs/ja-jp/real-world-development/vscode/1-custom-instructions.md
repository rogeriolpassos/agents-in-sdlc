---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェント モード →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。作業の進め方に決まりがある場合や、Copilot に知っておいてほしい背景情報がある場合は、その情報を参照できるようにします。Copilot に特定のコンテキストを伝える方法はいくつかあります。重要な方法の 1 つが[指示ファイル][instruction-files]です。指示ファイルを使うと、コード生成に関するガイダンスを伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパス指定の指示ファイルを使い、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準を Copilot に伝える方法を確認します。
- *現在の*指示を使って、フィルター機能の最初のデータ部分 (publishers ヘルパー) を生成します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する標準を追加します。
- 同じプロンプトを再実行し、生成コードに新しい標準が反映されることを確認します。
- 次の演習で Copilot が最新のガイダンスを利用できるよう、指示の変更とフィルター機能の下地を `main` にコミットします。

> [!CAUTION]
> 生成コードが設定した標準から外れる場合があります。Copilot は確率的に動作します。この演習の目的は、指示の更新後に動作の傾向がどう変わるかを確認することであり、出力を 1 文字ずつ一致させることではありません。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。具体的には、次のとおりです。

- データ層には必ず単体テストを用意します。
- ユーザー インターフェイス (UI) はダークモードにし、現代的な印象にします。
- TypeScript のドキュメント コメント (TSDoc) をコードに追加します。
- 各ファイルの先頭に、そのファイルの役割を説明するコメント ブロックを追加します。

指示ファイルを使って、Copilot がこれらの開発慣行に沿って作業できるようにします。

### カスタム指示

カスタム指示は、コーディング スタイルや要件に関するコンテキストや好みを Copilot に伝えます。規約、ライブラリ、コードに含めたいコメントを指定でき、より適切な提案やコード スニペットを得るのに役立ちます。リポジトリ全体に適用する指示も、特定のファイルの種類やタスクに適用する指示も作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリへの**すべての**リクエストで Copilot に送信される単一の指示ファイルです。技術スタック、構築するものの概要、ベストプラクティスなど、ほとんどのチャットや CLI リクエストに関係するプロジェクト全体の情報を記載します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイルの種類に関するガイドラインを定義します。TypeScript や Astro などの言語、ユーザー インターフェイス (UI) コンポーネントの作成、新しい単体テストの作成といったタスクを対象にできます。

> [!NOTE]
> 統合開発環境 (IDE) では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリ レベルの指示ファイルと、`applyTo` frontmatter を持つ `*.instructions.md` ファイルの両方を使います。
>
> また、Copilot は AGENTS.md や CLAUDE.md など、[ほかの標準形式の指示ファイル][custom-instructions-support]にも対応しています。

### 指示ファイルを管理するためのベストプラクティス

指示ファイルの作成方法を詳しく扱うことは、このワークショップの範囲外です。ただし、サンプル プロジェクトには代表的な方法が含まれています。基本的には、次のようにします。

- `copilot-instructions.md` には、構築するものの説明、プロジェクト構成、全体的なコーディング標準など、プロジェクト レベルの指針を記載します。
- `*.instructions.md` には、単体テスト、Astro コンポーネント、データ層など、特定のファイルの種類やタスクに関する指針を記載します。
- 自然な言葉を使い、明確な指針とコードの例を示します。

指示ファイルの作り方や AI の使い方に唯一の正解はありません。プロジェクトで試しながら、効果的な方法を見つけてください。

> [!TIP]
> GitHub Copilot を使うプロジェクトでは、指示ファイルを整備しておくと役立ちます。このプロジェクトの指示ファイルを確認すると、[UI の更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けのファイルが見つかります。
>
> Copilot に指示ファイルを生成してもらうこともできます。操作方法は利用する環境によって異なります (例: VS Code の **Configure Chat → Generate Agent Instructions**、Copilot CLI の `/init`)。必要な場合は、各環境のレッスンで説明します。
>
> テンプレートや出発点を探している場合は、指示ファイル、カスタムエージェントなどを集めたリポジトリ [awesome-copilot][awesome-copilot]を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれる指示ファイルを確認します。中心となる `copilot-instructions.md` が 1 つあり、さまざまなタスク向けの `*.instructions.md` ファイルも用意されています。エディターまたは GitHub の Web UI で開いてください。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡潔な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** にある **GitHub Actions Workflows** の入れ子のガイダンスにも注目してください。これらの指示は Copilot とのあらゆるやり取りに適用されます。
3. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データ層、テストなどに関する指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。このフィールドは、指示の適用対象を決めるリポジトリ ルートからの glob を指定します。この例では、`**/*.test.ts` に一致する TypeScript テスト ファイルが対象です。
5. このプロジェクトで単体テストを作成する際の指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの別の指示ファイルや、プロジェクト内の既存ファイルへのリンクを確認します。大きな指示を再利用できる小さなファイルに分け、コード生成の例を示せます。ここに記載されたパスはリポジトリ ルートではなく、指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションではプロジェクトのコーディング標準を説明していますが、コード内のドキュメントはまだ必須ではありません。次に、TSDoc のドキュメント コメントとファイル先頭のコメント ヘッダーを追加するルールを設定します。

## 変更用のブランチを作成する

変更を安全に行えるよう、作業用のブランチを作成します。

1. 前の演習で使った codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 次のコマンドで新しいブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する*前に* Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードとモデルの選択は演習によって異なるため、必要な箇所で説明します。

カスタム指示の効果を確かめるため、まず現在の指示ファイルでプロンプトを送信します。その後、指示を更新して同じプロンプトを再送信し、違いを確認します。

1. 前の演習で開いたエディター タブを閉じ、Copilot が必要なコンテキストだけを取得するようにします。
2. `src/lib/publishers.ts` を開き、ヘルパーを配置する場所を Copilot に示します。
3. Chat ビューのエージェント選択メニューから **Agent** を選び、ファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   src/lib/publishers.ts に、すべてのパブリッシャーについて名前と ID の一覧を返すデータ アクセス ヘルパーを作成または更新してください。ファイルの変更を適用してください。
   ```

5. Copilot がプロジェクトを調べてコードを更新します。ヘルパーとテストなど、複数のファイルが変更されることがあります。
6. 提案されたヘルパーが型付き関数であり、最初の引数として `db` クライアントを受け取り、型付きのパブリッシャー配列を返すことを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータ層規約によるものです。
7. 提案されたコードに TSDoc のドキュメント コメントとファイル レベルのコメント ヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくてもドキュメント コメントを追加することがあります。その場合も問題ありません。指示の更新後に一貫性が高まることが重要です。

## リポジトリ全体に適用する標準を追加する

`.github/copilot-instructions.md` は Copilot にプロジェクト全体の情報を伝えるファイルです。コーディング標準を記載し、コード提案の質を高めましょう。

1. `.github/copilot-instructions.md` をもう一度開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを見つけます。既存のコーディング標準には、コード内ドキュメントのルールがありません。そのため、生成されたヘルパーにもドキュメント コメントがありませんでした。
3. 既存の標準の直後に、次の Markdown 行を追加します。ファイル コメント ヘッダーと TSDoc のドキュメント コメントを Copilot に指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、指示ファイルはリポジトリ全体に適用する `.github/copilot-instructions.md` と、特定の言語、ファイルの種類、タスクに適用する `*.instructions.md` のどちらにも作成できます。今回追加したドキュメント コメントのルールのように、プロジェクト全体の標準にはリポジトリ レベルのファイルが適しています。

## プロンプトを再実行して変化を確認する

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` を選択し、Copilot が適切なファイルに注目するようにします。
3. 前と**同じプロンプト**を送信します。

   ```plaintext
   src/lib/publishers.ts に、すべてのパブリッシャーについて名前と ID の一覧を返すデータ アクセス ヘルパーを作成または更新してください。ファイルの変更を適用してください。
   ```

4. 提案されたファイルの先頭に、次のようなコメント ブロックが追加されていることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に、次のような TSDoc コメントが含まれていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

Copilot に新しいプロジェクト標準を適用させ、次の演習で使うコードに反映できました。

## 指示とブランチをコミットしてプッシュする

指示ファイルも、ほかのリポジトリ資産と同じようにソース管理します。変更をコミットして、ブランチをリポジトリにプッシュしましょう。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
2. ターミナルで次を実行し、指示の変更とヘルパーの変更を確認します。

   ```bash
   git status
   ```

3. ターミナルで指示の変更と、Copilot がヘルパーの下地のために変更したすべてのファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # git status にテストなどの追加変更が表示された場合は、それらのファイルも追加します。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 次のコマンドでブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログにあるドキュメント標準の Issue に関連付けてプルリクエストを作成します。GitHub ツールがプロジェクトに接続されているため、Copilot に Issue を探し、Issue を閉じる PR を作成して、マージするよう依頼できます。

> [!NOTE]
> Copilot が GitHub に対して操作するのは、これが最初の演習です。プロジェクト テンプレートでは、GitHub ツールがワークスペースにあらかじめ設定されています。Copilot が初めてツールを使うとき、VS Code で GitHub へのサインインを求められることがあります。画面の指示に従って許可してください。後の演習では、この接続に使われる Model Context Protocol について確認します。

1. codespace で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント選択メニューで **Agent** が選択されていることを確認します。
3. 指示ファイルの更新に関する Issue を探し、現在のブランチから PR を作成するよう Copilot に依頼します。指示の更新に加え、今後のフィルター機能のための publishers ヘルパーの下地も含めるよう伝えます。

   ```plaintext
   指示ファイルの更新に関する Issue を探してください。現在のブランチから PR を作成し、その Issue を閉じることを明記してください。また、今後のフィルター機能のために publishers ヘルパーの下地も追加したことを記載してください。
   ```

4. Copilot が Issue を探して PR を作成することを確認します。
5. Copilot が GitHub 操作の許可を求めたら、コマンドを確認して、適切な場合に **Allow** を選択します。
6. PR が作成されたら、次のプロンプトでマージしてローカル ブランチを `main` に戻すよう依頼します。

   ```plaintext
   PR を main にマージしてください。その後、ローカルで main に戻り、最新のコードを pull して最新状態にしてください。
   ```

7. Copilot が GitHub 操作とシェル コマンドの許可を求めたら、コマンドを確認して、適切な場合に **Allow** を選択します。

GitHub Copilot の支援でプルリクエストを作成してマージできました。

## まとめと次のステップ

このプロジェクトの指示ファイルから Copilot がコンテキストを得る方法を確認し、VS Code の Copilot Chat で次のことを行いました。

- リポジトリの指示とパス指定の指示ファイルを確認しました。
- *現在の*指示でフィルター機能の最初のデータ部分を生成しました。
- `.github/copilot-instructions.md` に新しいリポジトリ標準を追加しました。
- 同じプロンプトを再実行し、生成コードに新しい標準が反映されることを確認しました。
- 次の演習で Copilot が最新のガイダンスを使えるよう、指示の変更とフィルター機能の下地を `main` にコミットしました。

次は、Copilot がコードベース全体に新機能を追加する際に指示を活用できるよう、[エージェント モード][next-lesson]を使います。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot によりよい指示を書くための 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [Awesome Copilot - 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
