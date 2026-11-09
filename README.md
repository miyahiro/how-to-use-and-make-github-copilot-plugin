# GitHub Copilot Plugin の使用方法と作成方法

## はじめに

GitHub Copilot を使っていると、コードレビューの手順やテスト方法など、毎回同じ説明をしたり、必要なツールを個別に設定したりすることがあります。こうした設定をまとめ、チームや自分の開発環境で再利用できるのが **Agent Plugin（以下、Plugin）** です。

この記事では、Visual Studio Code で Plugin を探して使う方法と、簡単な Plugin を作成して動作を確認する方法を紹介します。

### 環境

- Visual Studio Code 1.145.2
- GitHub Copilot を利用できるアカウントと、VS Code でのサインイン

Plugin の利用可否や画面の表示は、選択中のエージェント環境（harness）や組織のポリシーによって異なる場合があります。

## GitHub Copilot Plugin とは

Plugin は、Copilot のエージェントをカスタマイズするための機能をまとめたパッケージです。Plugin によって、たとえば次のような要素をひとまとまりで配布できます。

- **Skills**: 特定の作業で使う手順や補助ファイル
- **Custom agents**: 専門的な役割や利用可能なツールを定義したエージェント
- **MCP servers**: 外部サービスやツールと連携するサーバー
- **Hooks**: エージェントの処理中に実行するコマンド
- **Slash commands**: チャットから呼び出すコマンド

Plugin は Visual Studio Code の拡張機能（Extension）とは別のものです。VS Code の拡張機能がエディター自体に機能を追加するのに対し、Plugin は Copilot エージェントの作業手順やツールを追加します。

### どんなことを解決するのか

たとえば、チームでコードレビューを行うときに、レビュー手順をまとめた Skill、読み取り専用のレビュー用 Agent、Issue 情報を取得する MCP server をそれぞれ設定するのは手間がかかります。Plugin にまとめることで、関連する設定を一緒に見つけ、導入、更新、無効化できます。

一方、ひとつの短い指示だけを使いたい場合は、Plugin 全体ではなく個別のカスタマイズを用意するほうが適しています。複数の要素を組み合わせたワークフローを共有したいときに Plugin を使いましょう。

## GitHub Copilot Plugin 利用方法

### 利用方法

1. VS Code で Copilot にサインインし、チャットを開きます。
2. コマンドパレットから **Chat: Open Customizations** を実行します。チャットのメニューに **Open Customizations** が表示される場合は、そこから開くこともできます。
3. **Plugins** を選択し、マーケットプレイスを参照します。画面によっては **Browse** または **Browse Marketplace** から一覧を開きます。
4. Plugin の説明、発行元、README、含まれる Skills や Agent、Hooks、MCP servers を確認します。
5. 信頼できることを確認してから **Install** を選択します。初めて利用するマーケットプレイスでは、信頼を確認するダイアログが表示されることがあります。
6. インストール後、Plugin が有効になっていることを確認し、新しい Copilot チャットで含まれる Skill やコマンドを試します。

マーケットプレイスにない Plugin は、**Plugins** の追加（`+`）メニューから Git リポジトリの URL またはローカルフォルダーを指定してインストールできます。コマンドパレットの **Chat: Install Plugin from Source** からも操作できます。

Plugin のサポートが無効になっている場合は、VS Code の設定 `chat.plugins.enabled` を確認してください。組織の管理ポリシーによって利用できない場合もあります。

### Tips

- **導入前に中身を確認する**: Plugin には Hooks や MCP servers など、ローカル環境でコマンドを実行したり外部サービスへ接続したりする機能が含まれることがあります。発行元、ソースコード、必要な権限、実行される処理を確認してください。
- **小さな作業で試す**: インストールしただけで、すべての依頼に Plugin の機能が自動で使われるとは限りません。Skill が利用可能か、期待したタスクで動作するかを確認しましょう。
- **必要な範囲にインストールする**: 個人用かワークスペース用かを選べる場合は用途に合わせます。共有ワークスペースでは、チームの設定や管理ポリシーも確認してください。
- **依存関係を確認する**: Plugin によっては Node.js などの実行環境や、外部サービスの設定が必要です。README の前提条件に従ってください。
- **更新内容も確認する**: Plugin の更新で含まれる設定や実行コードが変わる場合があります。更新前後に差分を確認し、不要になった場合は無効化またはアンインストールします。
- **再現性を重視する**: マーケットプレイスを追加設定する場合、利用可能な環境ではブランチ名だけでなくコミット SHA を指定すると、参照するリビジョンを固定できます。

## GitHub Copilot Plugin の作成方法

ここでは、Skill をひとつ含む最小限の Plugin を作成します。Plugin を公開する場合は、利用者が目的や必要なセットアップを判断できるよう README も用意しましょう。

1. 作業用フォルダーを作成します。

   ```text
   my-review-plugin/
   ├── plugin.json
   ├── README.md
   └── skills/
       └── review-checklist/
           └── SKILL.md
   ```

2. ルートに `plugin.json` を作成し、Plugin の名前や説明を記述します。

   ```json
   {
     "$schema": "https://agent-plugins.org/schemas/1.0.0/plugin.schema.json",
     "name": "my-review-plugin",
     "version": "1.0.0",
     "description": "プロジェクトのコードレビューを支援します"
   }
   ```

3. `skills/review-checklist/SKILL.md` に、Skill の名前、説明、Copilot に従ってほしい手順を書きます。たとえば、重要な問題から指摘すること、関連するテストを確認すること、問題が見つからない場合はその旨を報告することなどです。Skill の説明には、どのような依頼で使うものかを明記します。

4. `README.md` に、Plugin の用途、含まれる機能、必要な環境、セットアップと利用方法を記載します。

5. VS Code の **Chat: Open Customizations** から Plugin のソースを指定してインストールし、意図したカスタマイズが表示されることを確認します。新しいチャットで代表的なレビュー依頼を試し、結果が期待どおりか確認してください。

Skill 以外に Copilot 固有の Agent や Hooks、slash command を含める場合は、Plugin のルートに `com.github.copilot/` を作り、その中の対応するフォルダーに配置します。MCP server の定義はルートの `mcp.json` に置けます。構成の詳細や対応形式は、後述の公式リファレンスを参照してください。

作成した Plugin をチームで利用するには、Git リポジトリやマーケットプレイスで共有します。公開前に、含まれるスクリプトや接続先を確認し、インストール・動作確認・更新の手順を試しましょう。

## まとめ

Plugin を使うと、Copilot エージェント向けの手順、役割、ツールなどをひとつのパッケージにまとめて再利用できます。まずは信頼できる Plugin をひとつ選び、内容を確認して小さなタスクで試してみましょう。繰り返し使うワークフローが見つかったら、Skill から作成を始め、必要に応じて Agent や MCP server などを追加していくのがおすすめです。

### 参考資料

- [Agent plugins in Visual Studio Code](https://code.visualstudio.com/docs/copilot/customization/agent-plugins)
- [Agent plugin authoring reference](https://code.visualstudio.com/docs/agents/reference/agent-plugin-reference)
- [Agent Plugins specification](https://agent-plugins.org/)
