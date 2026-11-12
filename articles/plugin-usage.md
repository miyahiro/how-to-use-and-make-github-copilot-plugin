# GitHub Copilot Plugin 利用編：社内 marketplace から導入し、CLI と VS Code で使う

> **調査・検証待ちの草稿（2026-11-12）**  
> この記事は公開完成稿ではありません。筆者の環境（VS Code 1.145.3、GitHub Copilot CLI 1.0.109）での確認結果と、CLI のヘルプ出力を基に構成しています。最新の公式ページとの照合、更新・無効化・削除の確認、既存の導入状態に依存しない新しい環境での再現確認は未実施です。

## 1. Plugin とは：何をまとめ、何を楽にするのか

プロジェクトで AI に仕事を頼むとき、作業の指示、専門のエージェント、再利用するスキルを毎回そろえるのは手間です。チームで同じ手順を使いたくても、個別にコピーしていると変更への追従が難しくなります。

Agent Plugin は、関連するエージェント向けのカスタマイズをまとめて配布・再利用する仕組みです。まとめて見つけ、導入・更新しやすくすることを目指します。

VS Code 拡張機能と同じものではありません。この記事では、エディターの拡張機能そのものではなく、Copilot で使うエージェントやスキルなどのまとまりを扱います。Plugin を入れれば、どのクライアントでも同じように動く、必ず成果が改善する、という意味ではありません。

この記事の読者は、Copilot Chat を使い始め、社内で配られた Plugin を安全に試したい開発者です。流れは次のとおりです。

1. 利用を許可された GitHub private repository から marketplace を取得する。
2. 中身を確認し、`copilot` コマンドからローカル marketplace を登録して Plugin を導入する。
3. GitHub Copilot CLI と VS Code の GitHub Copilot Chat で、それぞれ利用できるか確認する。
4. プロジェクトに合わせて使い分け、必要に応じて更新・無効化・削除する。

以下では、確定できた説明と、公開前に埋める必要がある操作を分けます。

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
| 未確認 | 更新・無効化・削除、Workspace 補完の効果、新しい環境での再現 |

この環境で確認できたことは次のとおりです。

- **CLI**：`.github/copilot/settings.json` に marketplace と Plugin の定義があると、その `.github` の親ディレクトリ配下で GitHub Copilot CLI を起動したとき、定義した Plugin のエージェントを利用できた。
- **VS Code**：`copilot plugin marketplace add` と `copilot plugin install` で marketplace を登録し Plugin を導入（グローバルに有効化）すると、harness に Copilot を選んだ GitHub Copilot Chat のエージェント一覧に Plugin のエージェントが表示された。グローバルに有効にしないと、VS Code では利用できなかった。
- **harness**：VS Code で harness が Copilot 以外のときは、うまく動作しなかった。

最新の公式資料とはまだ照合していません。参照欄の公式 URL は確認先であり、上記の版への適合を公式資料で確認済みという意味ではありません。

## 3. 導入前の前提と安全確認

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

## 4. private marketplace を取得し、CLI から導入する

### 4.1 許可された取得元を手元で確認する

認証済みの環境で、管理者から案内された repository をローカルに clone し、取得元と対象の版を照合する流れを想定しています。この作業では実際の取得は行っていません。

取得後は marketplace 定義、Plugin の参照先、README、コードや設定を確認します。取得元が private であることと、中身を安全と判断できることは別です。

marketplace 定義では、次の値を控えておきます。次節の導入コマンドで使います。

- marketplace の識別名（定義の `name`）
- 導入したい Plugin の識別名（Plugin エントリーの `name`）と参照先（`source`）、記載された版

### 4.2 marketplace 登録と Plugin 導入を分ける

marketplace の登録は「どのカタログから探すか」、Plugin の導入は「その中の何を使うか」という別の操作です。

GitHub Copilot CLI のヘルプでは、marketplace 登録の `<source>` は GitHub の `owner/repo`、URL、ローカルパスに対応しています。この記事では、内容を確認したローカル clone を登録する流れを使います。

```sh
# 実際に取得した marketplace リポジトリのパスへ置き換える
copilot plugin marketplace add /path/to/marketplace-repository

# 登録済み marketplace から Plugin を導入する
copilot plugin install <plugin-name>@<marketplace-name>
```

`<plugin-name>` と `<marketplace-name>` は、前節で控えた Plugin の識別名と marketplace の識別名に置き換えます。筆者の環境（§2）では、この2つの操作の後に VS Code の GitHub Copilot Chat で Plugin のエージェントが表示されました。新しい環境での再現は未確認です。

利用者が実行した `copilot plugin add --help` は、`Usage: copilot plugin install [OPTIONS] [source]` と導入例を表示しました。この記事ではその表示に合わせ、`install` 表記を使います。

特に次の3つを混同しないことが重要です。

| 値 | 意味 | 現在の確認状況 |
| --- | --- | --- |
| ローカルフォルダーのパス・名前 | 取得した marketplace の保存先 | `marketplace add <source>` のローカルパスとして指定 |
| marketplace 定義の `name` | marketplace の識別名 | 導入時は登録済み marketplace 名を `@` の右側に指定 |
| Plugin 定義の名前 | 導入したい Plugin の識別名 | `plugin@marketplace` の `@` 左側に指定 |

**`@` の右側は、ローカルフォルダー名ではなく登録済み marketplace 名です。**

ヘルプには、marketplace から導入する前に `copilot plugin marketplace add` で登録が必要と明記されています。公開前には登録結果・導入先を確認し、新しい環境で登録・導入を再現します。ヘルプに GitHub／URL の指定があっても private repository の認証成功を保証するものではないため、ここでは private URL の直接登録を前提にしません。

### 4.3 導入場所と有効化範囲は別に考える

| 観点 | 区別するもの |
| --- | --- |
| 導入スコープ | User 向けか、Workspace 向けか |
| 有効化範囲 | 広く有効にするか、その Workspace で有効／無効にするか |
| 適用先 | CLI、VS Code の Copilot Chat、その他の harness のどれか |

導入しただけで、すべての Workspace・クライアントで有効になるとは限りません。また、Workspace で無効にすることとアンインストールは別です。

筆者の環境（§2）では、クライアントによって有効にできる範囲が違いました。

| クライアント | 利用できた方法 |
| --- | --- |
| GitHub Copilot CLI | `.github/copilot/settings.json` の定義（Workspace の既定値）、または CLI での導入 |
| VS Code の GitHub Copilot Chat | CLI での導入（グローバルに有効化）。harness は Copilot を選択 |

CLI の既定の導入先や、Workspace 単位の有効化・無効化の操作は未確認です。選択画面や設定キーを推測して操作しないでください。

## 5. CLI と VS Code で、それぞれ利用を確認する

以下は実行済みの結果ではなく、新環境での確認項目です。Plugin の README にある、副作用のない代表的な作業を1つ選びます。CLI と VS Code では同じ Workspace・同じ Plugin 版・同じ入力を使い、別々に記録します。

| 確認先 | 確認すること | 期待する観測 |
| --- | --- | --- |
| GitHub Copilot CLI | 対象 Workspace での認識・有効状態と、README に従った呼び出し | 目的の Plugin のスキルやエージェントが見つかり、意図した作業を行える |
| VS Code の GitHub Copilot Chat | harness に Copilot を選び、エージェント一覧に Plugin のエージェントが表示されるか。README に従った呼び出し | 対象のスキルやエージェントを利用できる |

発見・一覧・呼び出しの正確な CLI 構文は確認待ちです。回答がそれらしく見えるだけで Plugin が使われたと判断せず、対象コンポーネントの認識と実行を確認します。

VS Code で見つからない場合は、まず harness が Copilot になっているか、CLI で導入（グローバルに有効化）したかを確かめます。それでも見つからない場合は、クライアントの版、ユーザー、実行形態、導入先、組織ポリシーを照合します。未確認の設定変更を代替策として案内することは避けます。

検証記録には、実施者・確認日・環境・各クライアントの版・Plugin と marketplace の版・事前状態・操作・期待結果・実際の結果を残します。未実施や失敗は成功と分けます。

## 6. プロジェクトごとの使い分け

チームが配るリポジトリ既定値と、利用者が自分の Workspace で行う有効化・無効化は別です。設定ファイルが repository にあるだけで、全員のすべてのクライアントへ導入・有効化されるとは考えないでください。

### リポジトリ既定値：`.github/copilot/settings.json`（CLI で確認）

repository に `.github/copilot/settings.json` を置き、marketplace と有効にする Plugin を定義できます。筆者の環境（§2）では、この `.github` の親ディレクトリ配下で GitHub Copilot CLI を起動したとき、定義した Plugin のエージェントを利用できました。

```json
{
  "extraKnownMarketplaces": {
    "<marketplace-name>": {
      "source": {
        "source": "directory",
        "path": "./<marketplace-directory>"
      }
    }
  },
  "enabledPlugins": {
    "<plugin-name>@<marketplace-name>": true
  }
}
```

`<marketplace-name>` は marketplace 定義の `name`、`<plugin-name>` は Plugin の識別名です。確認した環境では、`path` に repository ルートからの相対パスで marketplace のディレクトリを指定していました。

**この設定だけでは、VS Code の GitHub Copilot Chat では利用できませんでした。** VS Code でも使う場合は、§4.2 のとおり CLI で導入（グローバルに有効化）し、harness に Copilot を選びます。

公開前には次を埋めます。

- 公式資料での `.github/copilot/settings.json` の仕様と、上記の確認結果との差。
- CLI と VS Code それぞれの Workspace 単位の有効化・無効化方法。
- チーム既定値と個人の選択の関係。

## 7. 更新・無効化・アンインストール

| 操作 | 目的 | 確認する内容 |
| --- | --- | --- |
| 更新 | 新しい版を利用する | clone、marketplace 情報、導入済み Plugin の更新を区別。更新前後の版・差分を確認し、両クライアントで再確認 |
| 無効化 | 必要な範囲で利用を止める | Workspace 限定か、より広い範囲か。対象 Workspace で有効でなくなったか |
| アンインストール | 導入済み Plugin を取り除く | 他の Workspace への影響、認識されなくなったか。marketplace の登録解除とは別に確認 |

取得した clone を更新すれば、導入済み Plugin も更新されるとは限りません。更新時も参照先・コード・設定・権限・変更履歴を見直します。追加された hooks や MCP 接続も再確認が必要です。

実際の更新・無効化・削除コマンドと VS Code の操作名は未照合のため、操作手順は未完成です。管理者が提供する対象版向けの確認済み手順を確認し、不明なら実行を止めて問い合わせてください。

## 8. 元の Plugin を変えず、プロジェクトに合わせる

### fork／コピーと Workspace 補完の比較

| 方法 | 向いている場面 | 変更元と更新の管理 |
| --- | --- | --- |
| ソースを fork／コピーして変更 | Plugin 自体の共通の振る舞いを変えたい | ライセンス・社内規則を確認し、元の取得先・版、自分の差分を管理。元の更新を取り込み、変更した版を検証する責任を持つ |
| 元 Plugin を変えず Workspace で補完 | 特定プロジェクトの情報や作業ルールだけを足したい | 補足指示をプロジェクト側で版管理。Plugin の更新時に補足との整合を確認する |

インストール済み Plugin のキャッシュを直接編集する方法は勧めません。更新で失われたり、元の版との差が追えなくなったりするためです。

### 具体例は1つ：回答にプロジェクトのテスト確認方法を含める

元の Plugin は変更せず、Workspace 側にプロジェクト固有の指示を追加する例です。**指示文の案であり、配置・適用範囲・効果は未検証です。**

> 変更内容を説明するときは、このプロジェクトの README や開発ガイドに根拠があるテスト実行方法を回答に含める。根拠が見つからない場合は「テスト実行方法は未確認」と書く。実行していないテストを実行済みとは書かない。

配置先の候補は、リポジトリ単位の指示ファイル `.github/copilot-instructions.md` です。ただし、CLI と VS Code の双方での読み込み条件は確認できていないため、確定した配置手順とはしません。

検証では、対象クライアントでサポートされる配置を確認した後に、この指示を1つだけ追加します。追加前後で同じ入力「この変更の内容と確認方法を説明してください」を使い、回答に根拠のあるテスト方法、または未確認の明示が含まれるか比べます。

Plugin のファイルが不変であることと、Workspace の指示が作用したことは別々に確認します。単に回答が変わっただけでは、補完の成功や適用範囲までは確定できません。

## 9. 公開前に残る確認

登録・導入の構文は CLI のヘルプで照合し、筆者の環境（§2、VS Code 1.145.3・CLI 1.0.109）で CLI の `settings.json` による利用と、CLI 導入後の VS Code での表示を確認しました。導入先・スコープ、Workspace 単位の有効化・無効化、管理操作、指示ファイルの適用条件、新しい環境での再現は未確認です。

これらは記事の必須部分なので、説明を削って完成扱いにはしません。公式資料・対象版ヘルプの照合と、人による新環境での実機確認を経て、確認できた操作で本文を完成させます。

## 参照

### 公式資料の確認先（今回のページ取得・対象版照合は未実施）

- [VS Code: Agent plugins](https://code.visualstudio.com/docs/agent-customization/agent-plugins) — 既存要件が参照する利用ガイド。資料の対象版は未確認。

### 草稿の根拠（ローカル資料・既報告）

- GitHub Copilot CLI のヘルプ出力：`copilot --version`、`copilot plugin marketplace add --help`、`copilot plugin add --help`。登録元の形式、登録必須条件、`plugin@marketplace` 構文、`install` 表記を照合。
- 筆者の環境での確認（2026-11-12、§2）：CLI での `settings.json` による利用、CLI 導入後の VS Code での表示、harness による違い。

上記のローカル参照は執筆用です。Qiita 公開前に、公開可能な固定版 URL への置き換えと社内情報の非掲載を確認します。
