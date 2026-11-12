# GitHub Copilot Plugin 利用編：社内 marketplace から導入し、CLI と VS Code で使う

> **調査・検証待ちの草稿（2026-11-12）**  
> この記事は公開完成稿ではありません。筆者の環境（VS Code 1.145.3、GitHub Copilot CLI 1.0.109）での確認結果と、GitHub Docs の公式資料・CLI のヘルプ出力を基に構成しています。更新・削除の確認、既存の導入状態に依存しない新しい環境での再現確認は未実施です。

## 1. Plugin とは：何をまとめ、何を楽にするのか

プロジェクトで AI に仕事を頼むとき、作業の指示、専門のエージェント、再利用するスキルを毎回そろえるのは手間です。チームで同じ手順を使いたくても、個別にコピーしていると変更への追従が難しくなります。

Agent Plugin は、関連するエージェント向けのカスタマイズをまとめて配布・再利用する仕組みです。まとめて見つけ、導入・更新しやすくすることを目指します。

VS Code 拡張機能と同じものではありません。この記事では、エディターの拡張機能そのものではなく、Copilot で使うエージェントやスキルなどのまとまりを扱います。Plugin を入れれば、どのクライアントでも同じように動く、必ず成果が改善する、という意味ではありません。

この記事の読者は、Copilot Chat を使い始め、社内で配られた Plugin を安全に試したい開発者です。流れは次のとおりです。

1. 導入前に、取得元と Plugin の中身を確認する（§3）。
2. 「marketplace の追加 → Plugin のインストール → Plugin の有効化 → ディレクトリごとの設定」の4ステップで使えるようにする（§4）。
3. GitHub Copilot CLI と VS Code の GitHub Copilot Chat で、それぞれ利用できるか確認する（§5）。
4. 必要に応じて更新・削除する（§6）。使い分けのコツは Tips にまとめます（§7）。

## 2. 動作確認環境

以下は筆者が確認した環境です。ほかの版・環境で同じように動作するとは限りません。

| 項目 | 確認状況 |
| --- | --- |
| 草稿の記録日 | 2026-11-12 |
| OS | Ubuntu 24.04 |
| 実行形態 | GitHub Codespaces |
| VS Code | 1.145.3 (user setup) |
| GitHub Copilot 関連拡張機能の名前・版 | 未確認 |
| GitHub Copilot CLI | 1.0.109 |
| Plugin／marketplace の検証版・コミット | 未確認 |
| Workspace の事前状態 | 未確認（既存の導入状態に依存しない新しい環境かどうかを含む） |
| 確認できたこと | 次の箇条書きのとおり |
| 未確認 | GitHub／Git URL からの marketplace 追加、有効化・無効化の切り替え、更新・削除、Workspace 補完の効果、新しい環境での再現 |

この環境で確認できたことは次のとおりです。

- **CLI**：`.github/copilot/settings.json` に marketplace と Plugin の定義があると、その `.github` の親ディレクトリ配下で GitHub Copilot CLI を起動したとき、定義した Plugin のエージェントを利用できた。
- **VS Code**：ローカルディレクトリの marketplace を `copilot plugin marketplace add` で追加し、`copilot plugin install` で Plugin を導入（グローバルに有効化）すると、harness に Copilot を選んだ GitHub Copilot Chat のエージェント一覧に Plugin のエージェントが表示された。グローバルに有効にしないと、VS Code では利用できなかった。
- **harness**：VS Code で harness が Copilot 以外のときは、うまく動作しなかった。

本文では、公式資料に書かれていることと、筆者の環境で確認したことを分けて書きます。

## 3. 導入前の確認

社内 marketplace は、利用対象者だけにアクセスを許可した GitHub private repository で管理する想定です。公開記事には実際の社内 URL や認証情報を載せません。取得元は社内の管理者から案内を受けてください。

導入前に、次を確認します。

- 許可された GitHub アカウントで認証され、対象 repository を読み取れること。
- 利用する Copilot の契約・組織ポリシーで、対象機能が許可されていること。
- 提供者、README、利用条件、変更履歴、取得した版が確認できること。
- marketplace が参照する Plugin の取得先を確認できること。marketplace の管理者と Plugin の提供者が同一とは限りません。
- 同梱する指示・スキル・エージェントに加え、hooks や MCP servers など実行や外部接続を伴うものがないか確認すること。
- 必要な権限、接続先、コマンド、送信される可能性がある情報を理解すること。

アクセスを拒否されたら取得を中断し、管理者へ権限を確認します。トークンを URL に埋め込んだり、認証情報をチャットや共有ログに貼ったりしないでください。

private repository のアクセス制御は、取得前のアクセス範囲を制限します。取得済みの内容の複製・再配布まで防ぐ保証ではありません。社内の取り扱いルールも別に必要です。

## 4. Plugin を使えるようにする：4つのステップ

Plugin を使えるようにするまでには、次の4つのステップがあります。ステップ1〜3は個人の環境での操作、ステップ4は repository に置くチーム向けの設定です。

| ステップ | 何をするか | GitHub Copilot CLI | VS Code の GitHub Copilot Chat（筆者の環境） |
| --- | --- | --- | --- |
| 1. marketplace の追加 | Plugin を探すカタログを登録する | 必要 | 必要（CLI で追加） |
| 2. Plugin のインストール | カタログから使う Plugin を導入する | 必要 | 必要（CLI で導入） |
| 3. Plugin の有効化 | 導入した Plugin を使える状態にする | インストールで有効になる。オン／オフを切り替えられる | グローバルに有効化されている必要がある |
| 4. ディレクトリごとの設定 | repository の既定値として marketplace と Plugin を指定する | 効いた | 効かなかった |

管理用のコマンドは `copilot plugin [SUBCOMMAND] --help` でヘルプを確認できます。

### 4.1 ステップ1：marketplace を追加する

marketplace は、インストールできる Plugin のレジストリ（カタログ）です。公式資料によると、既定の marketplace として `awesome-copilot` が最初から含まれています。社内の Plugin を使うには、社内 marketplace を追加します。

公式資料では、追加元として次の3つの方法が案内されています。

| 追加元 | コマンド | 向いている場面 |
| --- | --- | --- |
| GitHub repository | `copilot plugin marketplace add OWNER/REPOSITORY` | GitHub 上の marketplace を直接追加する |
| Git URL（GitHub 以外も可） | `copilot plugin marketplace add https://gitlab.com/OWNER/REPO.git` | GitLab など、ほかの場所でホストされた Git repository を追加する |
| ローカルディレクトリ | `copilot plugin marketplace add /PATH/TO/MARKETPLACE-DIRECTORY` | clone して中身を確認した marketplace を追加する |

```sh
# GitHub repository を指定する
copilot plugin marketplace add OWNER/REPOSITORY

# Git URL を指定する
copilot plugin marketplace add https://gitlab.com/OWNER/REPO.git

# ローカルディレクトリを指定する（実際に clone したパスへ置き換える）
copilot plugin marketplace add /PATH/TO/MARKETPLACE-DIRECTORY
```

対話型セッションの中では、`/plugin marketplace add OWNER/REPOSITORY` のようにスラッシュコマンドでも実行できます。

**社内の private repository の場合**

- `OWNER/REPOSITORY` や Git URL で直接追加する方法は公式資料にありますが、private repository を追加するときの認証の扱いは、この草稿では確認できていません。
- 筆者の環境で確認できたのは、認証済みの環境で clone し、中身を確認したローカルディレクトリを追加する方法です。§3 の確認をしやすい点でも、まずはこの方法をおすすめします。

**識別名を控える**

追加した marketplace の中身（marketplace 定義）で、次の値を控えておきます。次のステップで使います。

- marketplace の識別名（定義の `name`）
- 導入したい Plugin の識別名（Plugin エントリーの `name`）と参照先（`source`）、記載された版

登録済みの marketplace と、その中の Plugin は次のコマンドで確認できます。

```sh
# 登録済み marketplace の一覧
copilot plugin marketplace list

# marketplace 内の Plugin を見る
copilot plugin marketplace browse MARKETPLACE-NAME
```

### 4.2 ステップ2：Plugin をインストールする

marketplace の追加は「どのカタログから探すか」、Plugin のインストールは「その中の何を使うか」という別の操作です。公式資料・CLI のヘルプとも、marketplace から導入する前に、ステップ1で追加しておく必要があります。

```sh
copilot plugin install PLUGIN-NAME@MARKETPLACE-NAME
```

`PLUGIN-NAME` と `MARKETPLACE-NAME` は、ステップ1で控えた Plugin の識別名と marketplace の識別名に置き換えます。

| 値 | 意味 | 指定する場所 |
| --- | --- | --- |
| ローカルディレクトリのパス・URL | marketplace の追加元 | ステップ1の `marketplace add` |
| marketplace 定義の `name` | marketplace の識別名 | `@` の右側 |
| Plugin 定義の `name` | 導入したい Plugin の識別名 | `@` の左側 |

**`@` の右側は、ローカルディレクトリ名や URL ではなく、登録済み marketplace の名前です。** 登録済みの名前は `copilot plugin marketplace list` で確認できます。

公式資料によると、marketplace を経由せず、GitHub repository、Git URL、repository のサブディレクトリ、ローカルパスから直接インストールすることもできます。形式は公式リファレンスの「`install` コマンド用プラグインの仕様」にあります。この草稿では、出所を確認しやすい marketplace 経由の方法を扱います。

### 4.3 ステップ3：Plugin を有効化する

公式資料では、インストール済みの Plugin を次のコマンドで一覧・切り替えできます。

```sh
# インストール済み Plugin の一覧
copilot plugin list

# アンインストールせずにオフにする
copilot plugin disable PLUGIN-NAME

# オフにした Plugin を再びオンにする
copilot plugin enable PLUGIN-NAME
```

無効化（`disable`）とアンインストール（`uninstall`、§6）は別の操作です。無効化は Plugin を残したまま使わない状態にします。

**VS Code の GitHub Copilot Chat では、グローバルに有効化する必要があった**

筆者の環境（§2）では、ステップ1・2を CLI で行い Plugin をグローバルに有効化すると、VS Code の GitHub Copilot Chat（harness は Copilot）のエージェント一覧に Plugin のエージェントが表示されました。ステップ4のディレクトリごとの設定だけでは、VS Code では利用できませんでした。

`disable`／`enable` で切り替えた結果が VS Code 側にどう反映されるかは、未確認です。

### 4.4 ステップ4：ディレクトリごとに設定する（`.github/copilot/settings.json`）

repository に `.github/copilot/settings.json` を置くと、その repository で使う marketplace と、既定で有効にする Plugin を指定できます。チームで同じ Plugin を使いたいときに便利です。

```json
{
  "extraKnownMarketplaces": {
    "MARKETPLACE-NAME": {
      "source": {
        "source": "directory",
        "path": "./MARKETPLACE-DIRECTORY"
      }
    }
  },
  "enabledPlugins": {
    "PLUGIN-NAME@MARKETPLACE-NAME": true
  }
}
```

- `extraKnownMarketplaces`：使う marketplace と、その追加元。上の例はローカルディレクトリを指定しています。確認した環境では、`path` に repository ルートからの相対パスを指定していました。
- `enabledPlugins`：既定で有効にする Plugin を `PLUGIN-NAME@MARKETPLACE-NAME` の形で指定します。

筆者の環境（§2）では、この `.github` の親ディレクトリ配下で GitHub Copilot CLI を起動したとき、定義した Plugin のエージェントを利用できました。

**この設定は、VS Code の GitHub Copilot Chat ではうまく動作しませんでした。** VS Code でも使う場合は、ステップ1〜3を CLI で行い、グローバルに有効化してください。

`settings.json` のキーの公式な仕様（公式リファレンス「GitHub Copilot CLI の構成ディレクトリ」）とは、まだ照合していません。個人の環境でこの既定値をオフにする方法は、Tips（§7.1）で説明します。

## 5. CLI と VS Code で、それぞれ利用を確認する

以下は新しい環境で確かめる項目です。Plugin の README にある、副作用のない代表的な作業を1つ選びます。CLI と VS Code では同じ Workspace・同じ Plugin 版・同じ入力を使い、別々に記録します。

| 確認先 | 確認すること | 期待する観測 |
| --- | --- | --- |
| GitHub Copilot CLI | `copilot plugin list` で有効状態を確かめ、対象 Workspace で README に従って呼び出す | 目的の Plugin のスキルやエージェントが見つかり、意図した作業を行える |
| VS Code の GitHub Copilot Chat | harness に Copilot を選び、エージェント一覧に Plugin のエージェントが表示されるか。README に従って呼び出す | 対象のスキルやエージェントを利用できる |

回答がそれらしく見えるだけで Plugin が使われたと判断せず、対象コンポーネントの認識と実行を確認します。

検証記録には、実施者・確認日・環境・各クライアントの版・Plugin と marketplace の版・事前状態・操作・期待結果・実際の結果を残します。未実施や失敗は成功と分けます。

## 6. 更新・削除

公式資料では、次のコマンドが案内されています。

```sh
# Plugin を更新する
copilot plugin update PLUGIN-NAME

# Plugin を削除する
copilot plugin uninstall PLUGIN-NAME

# marketplace を削除する（marketplace 名で指定）
copilot plugin marketplace remove MARKETPLACE-NAME
```

- marketplace は、追加するときは追加元で、削除するときは `marketplace list` に表示される marketplace 名で指定します。
- その marketplace の Plugin がインストールされたままだと、`marketplace remove` は失敗します。先に Plugin を削除するか、関連する Plugin もまとめて削除する `--force` を付けます。
- ローカルディレクトリから追加した場合、clone を更新すれば導入済み Plugin も更新されるとは限りません。clone の更新と `plugin update` を分けて確認します。
- 更新時も参照先・コード・設定・権限・変更履歴を見直します。追加された hooks や MCP 接続も再確認が必要です。

これらの操作は、筆者の環境ではまだ実行していません。更新・削除の後、CLI と VS Code の両方で状態が変わったかは未確認です。

## 7. 利用上の Tips

### 7.1 プロジェクトごとに使い分ける

チームが repository に置く既定値（ステップ4）と、各利用者の個人の選択は別です。設定ファイルが repository にあるだけで、全員のすべてのクライアントで有効になるとは考えないでください。

公式資料によると、repository の既定値で有効になっている Plugin を自分の環境だけでオフにするには、`.github/copilot/settings.local.json` に `false` を書きます。

```json
{
  "enabledPlugins": {
    "PLUGIN-NAME@MARKETPLACE-NAME": false
  }
}
```

- 保存後、新しいセッションを開始すると反映されます。再びオンにするには `true` に戻します。
- `settings.local.json` は個人用の設定なので、コミットしないでください。
- エンタープライズで管理された設定は、repository の既定値やローカルの上書きより優先されます。

この上書きは公式資料の記載で、筆者の環境ではまだ試していません。また、ステップ4と同じく、VS Code の GitHub Copilot Chat に効くかは未確認です。

### 7.2 VS Code の GitHub Copilot Chat で使う

筆者の環境（§2）では、次の2点がそろって初めて、VS Code の GitHub Copilot Chat で Plugin のエージェントが表示されました。

- CLI でステップ1〜3を行い、Plugin をグローバルに有効化している。
- GitHub Copilot Chat の harness に Copilot を選んでいる。harness が Copilot 以外だと、うまく動作しませんでした。

VS Code で見つからない場合は、まず harness と、CLI で導入済みかを確かめます。それでも見つからない場合は、クライアントの版、ユーザー、実行形態、組織ポリシーを照合します。未確認の設定変更を代替策として試すことは避けます。

### 7.3 元の Plugin を変えず、プロジェクトに合わせる

#### fork／コピーと Workspace 補完の比較

| 方法 | 向いている場面 | 変更元と更新の管理 |
| --- | --- | --- |
| ソースを fork／コピーして変更 | Plugin 自体の共通の振る舞いを変えたい | ライセンス・社内規則を確認し、元の取得先・版、自分の差分を管理。元の更新を取り込み、変更した版を検証する責任を持つ |
| 元 Plugin を変えず Workspace で補完 | 特定プロジェクトの情報や作業ルールだけを足したい | 補足指示をプロジェクト側で版管理。Plugin の更新時に補足との整合を確認する |

インストール済み Plugin のキャッシュを直接編集する方法は勧めません。更新で失われたり、元の版との差が追えなくなったりするためです。

#### 具体例は1つ：回答にプロジェクトのテスト確認方法を含める

元の Plugin は変更せず、Workspace 側にプロジェクト固有の指示を追加する例です。**指示文の案であり、配置・適用範囲・効果は未検証です。**

> 変更内容を説明するときは、このプロジェクトの README や開発ガイドに根拠があるテスト実行方法を回答に含める。根拠が見つからない場合は「テスト実行方法は未確認」と書く。実行していないテストを実行済みとは書かない。

配置先の候補は、リポジトリ単位の指示ファイル `.github/copilot-instructions.md` です。ただし、CLI と VS Code の双方での読み込み条件は確認できていないため、確定した配置手順とはしません。

検証では、対象クライアントでサポートされる配置を確認した後に、この指示を1つだけ追加します。追加前後で同じ入力「この変更の内容と確認方法を説明してください」を使い、回答に根拠のあるテスト方法、または未確認の明示が含まれるか比べます。

Plugin のファイルが不変であることと、Workspace の指示が作用したことは別々に確認します。単に回答が変わっただけでは、補完の成功や適用範囲までは確定できません。

## 8. 制約・確認状況

- marketplace の追加・インストール・有効化・更新・削除のコマンドは、GitHub Docs の公式資料（2026-11-12 確認）と CLI のヘルプで照合しました。
- 筆者の環境（§2）で確認できたのは、ローカルディレクトリからの追加とインストール、CLI での `settings.json` による利用、CLI 導入後の VS Code での表示です。
- GitHub／Git URL からの private marketplace の追加、`disable`／`enable`、更新・削除、`settings.local.json` による上書き、Workspace 指示ファイルの適用条件、新しい環境での再現は未確認です。

これらは記事の必須部分なので、説明を削って完成扱いにはしません。人による新しい環境での実機確認を経て、確認できた操作で本文を完成させます。

## 参照

### 公式資料

- [GitHub Copilot CLI のプラグインの検索とインストール（GitHub Docs）](https://docs.github.com/ja/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing) — marketplace の追加元3種、インストール、一覧・有効化・無効化・更新・削除、marketplace の削除、`settings.local.json` による上書き。2026-11-12 確認。ページに対象版の記載はなし。
- [VS Code: Agent plugins](https://code.visualstudio.com/docs/agent-customization/agent-plugins) — VS Code の利用ガイド。内容と対象版は未照合。

### 筆者の確認

- GitHub Copilot CLI のヘルプ出力：`copilot plugin marketplace add --help`、`copilot plugin add --help`。追加元の形式、事前登録の必要性、`plugin@marketplace` 構文、`install` 表記を照合。
- 筆者の環境での確認（2026-11-12、§2）：ローカルディレクトリからの追加とインストール、CLI での `settings.json` による利用、CLI 導入後の VS Code での表示、harness による違い。
