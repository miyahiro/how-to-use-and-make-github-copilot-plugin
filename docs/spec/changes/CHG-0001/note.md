# 作業メモ

## 2026-11-12: GitHub Docs「GitHub Copilot CLI のプラグインの検索とインストール」の照合

- 出典: https://docs.github.com/ja/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing （利用者が提示。2026-11-12 に WebFetch で取得。ページに最終更新日・対象版の記載なし）。
- marketplace の追加元は3つ: `copilot plugin marketplace add OWNER/REPOSITORY`、Git URL（例 `https://gitlab.com/OWNER/REPO.git`）、ローカルディレクトリ `/PATH/TO/MARKETPLACE-DIRECTORY`。対話型セッションでは `/plugin marketplace add ...`。既定の marketplace として `awesome-copilot` が含まれる。
- 一覧・参照: `copilot plugin marketplace list`、`copilot plugin marketplace browse MARKETPLACE-NAME`。インストール: `copilot plugin install PLUGIN-NAME@MARKETPLACE-NAME`。GitHub repo・Git URL・サブディレクトリ・ローカルパスからの直接インストールも可（形式はリファレンスの「install コマンド用プラグインの仕様」、未取得）。
- 管理: `copilot plugin list`／`update`／`disable`（アンインストールせずオフ）／`enable`／`uninstall`。`copilot plugin marketplace remove MARKETPLACE-NAME` は名前で指定し、Plugin が残っていると失敗、`--force` で関連 Plugin も削除。
- 既定値の個人上書き: `.github/copilot/settings.local.json` の `enabledPlugins` に `"PLUGIN@MARKETPLACE": false`。新しいセッションで反映、コミットしない、エンタープライズ管理設定が優先。
- ページに記載がないこと: private repository を追加するときの認証、インストール先のパス、`settings.json` の `extraKnownMarketplaces` のキー仕様（リファレンス「GitHub Copilot CLI の構成ディレクトリ」は未取得）、VS Code への反映。
- これらは公式資料の記載で、筆者環境での実行確認は marketplace のローカルディレクトリ追加・install・settings.json（CLI）だけ。

## 2026-11-12: 利用者による実機確認（環境と結果）

- 出典は利用者の会話での共有。確認者は利用者（人）。
- 環境: OS Ubuntu 24.04、GitHub Codespaces、VS Code 1.145.3 (user setup)、GitHub Copilot CLI 1.0.109。Copilot 関連拡張の名前・版、Plugin／marketplace の版・コミット、事前の導入状態は未確認。
- CLI: `.github/copilot/settings.json` に marketplace（`extraKnownMarketplaces`）と Plugin（`enabledPlugins`）の定義があり、CLI の起動ディレクトリがその `.github` の親ディレクトリ配下なら、定義した Plugin のエージェントを利用できた。設定例の形はこのリポジトリの `.github/copilot/settings.json`（`source.source: "directory"`、`path` は repository ルートからの相対パス）。
- VS Code: Copilot Chat で harness に Copilot を選び、`copilot plugin marketplace add <マーケットプレイスのディレクトリ>`、`copilot plugin install <プラグイン名>@<マーケットプレイス名>` で導入すると、エージェント一覧に Plugin のエージェントが表示された。利用者の結論は「グローバルに有効にしないと VS Code では利用できない」「harness が Copilot でないとうまく動作しない」。代表的な作業の実行結果の詳細は共有されていない。
- 2026-11-10 の既報告「settings.json は CLI と VS Code の両方で有効にならなかった」は、CLI については今回の確認（起動ディレクトリの条件付きで利用できた）で更新された。VS Code で settings.json が効かない点は一致。
- 要件・設計の対象版は VS Code 1.145.2 だが、確認は 1.145.3。記事の環境章は確認した版で記載した。
- Q-11 の回答（2026-11-12）で対象版を 1.145.3 に変更。記事の CLI 表記は 1.0.109 に統一した。ただし source 形式（owner/repo・URL・ローカルパス）・事前登録必須・`install` 表記を読み取ったヘルプ出力は 1.0.108 で取得したもの。1.0.109 で実行して確認できたのは `marketplace add <ディレクトリ>` と `install <plugin>@<marketplace>` の利用結果。

## 2026-11-10: 利用者提供の CLI 1.0.108 ヘルプとの照合

- 出典は利用者が会話に貼り付けた `copilot --version`、`copilot plugin marketplace add --help`、`copilot plugin add --help` の実行出力。版は GitHub Copilot CLI 1.0.108。
- marketplace 登録の source は GitHub の owner/repo、URL、ローカルパス。導入 source の `plugin@marketplace` は登録済み marketplace を指定し、事前登録が必要。`@` の右側はローカルフォルダー名ではない。
- `copilot plugin add --help` の Usage と Examples は `copilot plugin install` 表記。草稿の導入例を `copilot plugin install sdd@spec-driven-development` として、ローカル定義の名前と照合した。
- ヘルプ照合は導入・利用の再現確認ではない。private repository 認証、登録結果、導入先・スコープ、CLI／VS Code の共有条件、管理操作、新環境での検証は未確認のまま。公式ページ取得・CLI形式検査・Git検査も今回は未実施。

## 2026-11-10: Plugin のプロジェクト別利用方法の実機確認メモ

- 利用者の回答: Tips では `.github/copilot/settings.json` と Workspace 単位の有効化・無効化の両方を扱う（C）。
- 利用者による実機確認: `.github/copilot/settings.json` の設定は GitHub Copilot CLI と GitHub Copilot Chat（VS Code 拡張）では有効にならなかった。
- 利用者による実機確認: 次の CLI コマンドで marketplace と Plugin をインストールすると、GitHub Copilot CLI と GitHub Copilot Chat（VS Code）の両方で利用できた。
  - `copilot plugin marketplace add <マーケットプレイスフォルダ>`
  - `copilot plugin add <プラグイン>@<マーケットプレイスフォルダ>`
- この結果は利用者の報告であり、こちらでの再現確認は未実施。記事に手順や設定の効果を記載する前に、対象バージョン、実行環境、コマンドの仕様、`.github/copilot/settings.json` の適用範囲を確認する。

## 2026-11-10: 設計時に参照した事実・前提

- 要件では対象を VS Code 1.145.2 とし、利用編の新しい環境での実機検証は未実施、基本的に人が担当する方針としている（`docs/spec/current/requirements/plugin-usage.md` の NFR-USE-001〜002、AC-USE-009）。
- 作成編は新規 Plugin の作成・導入・実行を到達点とし、sdd は参考例とする。作成編の検証環境にはサンプル Plugin が未導入であることが受入条件に含まれる（`docs/spec/current/requirements/plugin-authoring.md` の REQ-AUTHOR-003〜007、AC-AUTHOR-007）。
- 社内配布方式は CHG-0001 Q-3 として design 期限で未回答である（`docs/spec/changes/CHG-0001/status.md`、`docs/spec/current/requirements/plugin-authoring.md` の未解決質問）。
- sdd リポジトリの README は `plugin.json`、`skills/`、`com.github.copilot/agents/` の構成を示し、manifest の実例は `plugins/sdd/plugin.json` にある（`ai-powered-spec-drivien-development/plugins/sdd/README.md`、`ai-powered-spec-drivien-development/plugins/sdd/plugin.json`）。これは当該リポジトリの事実であり、一般的な公式仕様の確認とは別である。
- 根拠を持たない前提: 記事の実機検証はサンプルに影響されない新しい Workspace で行い、利用者が操作・結果を記録する。未実施や失敗は成功扱いにしない。
- 未確認事項: manifest／marketplace の具体的な schema・配置、対象バージョンの導入 UI と CLI の動作は、この作業では実機・公式仕様の照合をしていない。記事の確定手順にする前に確認が必要。
- 検査上の制約: この作業環境でコマンド実行ツールを利用できず、ルートでの形式検査スクリプトを実行できていない。検査結果を成功とは扱わない。

## 2026-11-10: タスク分解時の確認・検査制約

- 原稿保存先の具体的なパスは、今回参照した `docs/spec/current/design/article-publication.md` の DES-PUB-001、`plugin-usage.md`、`plugin-authoring.md` では確定していない。執筆前に配置と編集範囲を確認する前提でタスク化した。サンプルの正確な相対パス・schema は `plugin-authoring.md` 内部設計§1・DES-AUTHOR-005で公式照合後に具体化する扱いになっている。
- 既存メモの「Q-3 未回答」と要件 `docs/spec/current/requirements/plugin-authoring.md` の未解決質問欄には古い記載がある。現在の `docs/spec/changes/CHG-0001/status.md` の Q-3 は resolved で、`docs/spec/current/design/plugin-authoring.md` の DES-AUTHOR-005 に GitHub private repository 上の marketplace 管理として反映されている。これは公式仕様の調査・実機検証の証拠ではない。
- 手動確認方法: 3要件文書のAC一覧と3設計文書のDES見出しを `docs/spec/changes/CHG-0001/tasks.md` の対応表へ照合。AC 20件・DES 10件に対応があり、依存先は先行タスクのみ。タスクの実行・受入達成を示す確認ではない。
- CLI形式検査の方法は、ルートで `node /workspaces/how-to-use-and-make-github-copilot-plugin/ai-powered-spec-drivien-development/plugins/sdd/skills/sdd-spec-check/scripts/check.mjs CHG-0001`（出典: `ai-powered-spec-drivien-development/plugins/sdd/skills/sdd-spec-check/SKILL.md`）。今回は未実施。窓口から親のshell実行に Bubblewrap／iptables legacy・`/run/xtables.lock` 起因のサンドボックス起動エラーがあったとの引継ぎを受け、再試行・設定変更・迂回をしていない。`git --no-pager diff --stat` も未実施であり、読み取りによる照合だけを行った。
- Web調査・原稿執筆・サンプル作成・実機検証は今回行っていない。調査開始は次の依頼待ちであり、起動・動作確認の成功記録はない。

## 2026-11-10: 利用編草稿のローカル調査・未実施記録

### 読取で確認できた事実

- 参照日 2026-11-10。`ai-powered-spec-drivien-development/.github/plugin/marketplace.json` の marketplace `name` は `spec-driven-development`、Plugin の `name` は `sdd`、`source` は `./plugins/sdd`、記載版は `0.6.0`。フォルダー名とは異なる名前が記載されているが、CLI の `@` 右側の識別子を証明するものではない。参照コミット・実インストール版は未確認。
- `ai-powered-spec-drivien-development/plugins/sdd/README.md` §1にエージェント・スキルの構成、§2に `.github/copilot-instructions.md` をプロジェクトの基本ルールの置き場とする説明がある。対象版・CLIでの読み込み確認や公式一般仕様の証拠ではない。草稿の補完例は指示文の案にとどめ、配置・効果は未検証とした。
- `docs/spec/current/requirements/plugin-usage.md` に既存の公式参照URL `https://code.visualstudio.com/docs/agent-customization/agent-plugins` がある。今回はローカル要件内のURLだけを確認。公式ページ本文の確認日・資料対象版・VS Code 1.145.2との一致は未確認（本文取得は未実施）。草稿の§参照にも「確認先」として記載し、最新公式仕様の確認済み根拠にはしていない。
- 既報告の出典は本メモ冒頭の実機確認メモ。CLI導入後に両クライアントで使えた報告と、`.github/copilot/settings.json` が両方で有効にならなかった報告は、今回の再現結果とは別。クライアント／拡張版・実行形態・共有条件・事前導入状態は引き続き不明。動かなかった設定は草稿の実行例に採用していない。

### 確認方法・制約

- `articles/plugin-usage.md` と `README.md` を読取ツールで再読。見出し・表・引用・相対リンクを目視照合し、READMEから原稿、原稿からsdd README・marketplace定義・本メモの参照先が読めることを確認した。レンダリング・自動構文検査・外部リンクの到達性は未確認。自己確認を独立レビューとはしていない。
- 自動検査予定: ルートで `node /workspaces/how-to-use-and-make-github-copilot-plugin/ai-powered-spec-drivien-development/plugins/sdd/skills/sdd-spec-check/scripts/check.mjs CHG-0001`（案件の形式確認）、`git --no-pager diff --check`（差分の空白検査）、`git --no-pager diff --stat`（変更範囲の統計）。すべて未実施。今回の確認コミットも未確認。
- 今回のAPIにはWeb検索・ページ読取の専用ツールがなく、shell経由の外部取得・ヘルプ検査は、親の Bubblewrap／iptables legacy・lock権限に関する起動失敗の引継ぎにより試していない。環境修正・sandbox迂回・再試行は行っていない。新たな実行失敗やテスト成功の記録はない。
- 新環境での実機確認、Plugin実インストール、認証を伴う取得は未実施。起動方法・正式な呼び出し構文は未確認。確認には対象CLIの版・公式ヘルプ（登録、導入、管理、`@`右側）、VS Code／拡張版、導入先・有効化範囲、指示ファイルの配置・適用条件が必要。
- 草稿§5・8に、人が新環境で行う観測の手順案を記載。導入前後の認識、同じWorkspace・同じ入力での各クライアントの利用、Workspace指示の追加前後、元Plugin不変、管理操作後の状態を別々に記録する。環境・実施者・事前状態・操作・期待結果・実結果の記録はまだない。
