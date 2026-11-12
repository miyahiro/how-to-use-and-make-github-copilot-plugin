---
id: CHG-0001
title: GitHub Copilot Plugin の使用方法と作成方法の記事
kind: new
size: standard
phase: implementation
state: active
approvers: 宮下 洋
branch: change/CHG-0001
---

# CHG-0001 GitHub Copilot Plugin の使用方法と作成方法の記事

## 目的と範囲

- 目的: VS Code 1.145.3 を前提に、GitHub Copilot Plugin の利用編・作成編の2本の記事を作成する。各記事は約20分を目安とし、理解しやすさを優先する
- 対象: Copilot Chat を使い始めた開発者向けの利用編、開発プロセスを支援する Plugin を作りたい開発リーダー向けの作成編、必要な調査、Qiita での記事公開と Plugin の社内配布
- 作成編では簡単な新規 Plugin を自分で作成・導入して動作確認できる手順を扱い、sdd は参考例とする。サンプルの題材・構成と具体的な社内配布方式は設計で扱い、Q-3 の回答を受けて GitHub private repository 上の marketplace 管理として反映済み
- 壁打ちの合意内容: [idea.md](idea.md)

## 質問

| ID | 質問 | 選択肢・推奨 | 期限 | 状態 | 回答 |
| --- | --- | --- | --- | --- | --- |
| Q-1 | 記事の公開先はどこか | Qiita／社内サイト | requirements | resolved | Qiita とする。記事公開要件に反映済み |
| Q-2 | Plugin の公開・配布方針は何か | 社内配布／公開配布 | requirements | resolved | 社内配布を前提とする。作成編の要件に反映済み |
| Q-3 | 社内向け Plugin の具体的な配布方式は何か | 社内 Git リポジトリの marketplace／別の社内配布方法 | design | resolved | 利用対象者だけにアクセスを許可した GitHub private repository で marketplace を管理する。設計へ反映済み。これは設計承認や実機検証完了を意味しない |
| Q-4 | プロジェクトごとの Plugin 利用方法の説明で、実機結果と設定の適用範囲をどう扱うか | `.github/copilot/settings.json` と Workspace 単位の有効化・無効化の両方を区別して説明する | requirements | resolved | 両方を扱う方針を要件に反映。実機では `.github/copilot/settings.json` が Copilot CLI と VS Code の Copilot Chat で有効にならなかった一方、CLI の `copilot plugin marketplace add` と `copilot plugin add` で導入した Plugin は両方で利用できたとの報告。詳細は `note.md`。記事執筆時には対象バージョンと適用範囲を再確認する |
| Q-5 | 既存 Plugin のカスタマイズ Tips では、どの方法を扱うか | ソースを fork／コピーして変更する方法、元 Plugin を変更せず Workspace カスタマイズを追加する方法、または両方 | requirements | resolved | 両方を扱う。利用編の要件 REQ-USE-008 と AC-USE-008 に反映済み |
| Q-6 | 既存 Plugin のカスタマイズ Tips の説明の深さはどの程度か | 概念比較のみ／両方の手順／簡潔な比較と具体例1つ | requirements | resolved | 簡潔に比較し、具体例を1つ添える。REQ-USE-008 と AC-USE-008 に反映済み |
| Q-7 | 既存 Plugin のカスタマイズ Tips の具体例は何を示すか | fork／コピーによる拡張／Workspace 固有のカスタマイズによる補完／sdd を使う例 | requirements | resolved | 元 Plugin は変更せず、Workspace 固有の指示・スキル・エージェントなどを追加して補完する例とする。REQ-USE-008 と AC-USE-008 に反映済み |
| Q-8 | 利用手順の確認はどこまで行い、誰が担当するか | 公式資料との照合のみ／実機で導入・利用・Workspace カスタマイズの効果まで確認 | requirements | resolved | 新しい環境で実機確認する。基本的に人が担当し、AI も実施可能なら行ってよい。NFR-USE-002 と AC-USE-009 に反映済み。検証自体は未実施 |
| Q-9 | 記事の手順が環境によって動かない場合、どこまで扱うか | 制約・未確認事項の注意書きのみ／注意書きと動作確認済みの代替手順 | requirements | resolved | 注意書きに加えて、動作確認できた代替手順も示す。REQ-USE-009 と AC-USE-010 に反映済み |
| Q-10 | 作成編はどこを到達点とするか | sdd の構成例を読み作成・検証・社内配布の流れを理解／小さな Plugin を作って動かすハンズオン | requirements | resolved | 当初は構成例の理解としたが、利用者の訂正により、簡単な新規 Plugin を自分で作成・導入して動作確認できることへ変更。sdd は参考例とする。作成編の目的・対象範囲、REQ-AUTHOR-003・007、AC-AUTHOR-003・007 と idea.md に反映済み |
| Q-11 | 実機確認は VS Code 1.145.3 で行われたが、要件・設計の対象版は 1.145.2。記事の対象版をどうするか | 推奨: 対象版を 1.145.3 に改める（要件・設計の修正と再承認が必要）／1.145.2 のまま、確認版との差を記事に明記する（1.145.2 での再確認が必要） | completion | resolved | 対象版を VS Code 1.145.3（確認環境の CLI は 1.0.109）に改める。要件・設計・idea.md を修正済みで、requirements・design の再承認が必要 |

## 承認

| 工程 | 日時 | 承認者 | 対象 |
| --- | --- | --- | --- |
| requirements | 2026-11-10 | 宮下 洋 | docs/spec/current/requirements/plugin-usage.md, docs/spec/current/requirements/plugin-authoring.md, docs/spec/current/requirements/article-publication.md, docs/spec/changes/CHG-0001/idea.md |
| design | 2026-11-10 | 宮下 洋 | docs/spec/current/design/plugin-usage.md, docs/spec/current/design/plugin-authoring.md, docs/spec/current/design/article-publication.md |
| requirements | 2026-11-12 | 宮下 洋 | docs/spec/current/requirements/plugin-usage.md, docs/spec/current/requirements/plugin-authoring.md, docs/spec/current/requirements/article-publication.md, docs/spec/changes/CHG-0001/idea.md |
| design | 2026-11-12 | 宮下 洋 | docs/spec/current/design/plugin-usage.md, docs/spec/current/design/plugin-authoring.md, docs/spec/current/design/article-publication.md |

## 次の作業

- 2026-11-12: requirements（対象版 VS Code 1.145.3）と design（利用編の4ステップ・Tips の章構成、marketplace 追加元3種）を再承認。次は利用者による実機確認（Git URL／GitHub からの marketplace 追加、disable／enable、update／uninstall、marketplace remove、settings.local.json、VS Code への反映）の結果を受けて利用編を更新する。
- 2026-11-12: 利用者の依頼で利用編を4ステップ（marketplace の追加・Plugin のインストール・有効化・ディレクトリごとの設定）と利用上の Tips の章に再構成し、設計 DES-USE-001・DES-USE-004 を変更した。GitHub Docs「GitHub Copilot CLI のプラグインの検索とインストール」を取得・照合し、marketplace の追加元3種と管理コマンドを反映。requirements・design の再承認待ち。
- 2026-11-12: 利用者の依頼で、読者が参照できない sdd への言及を利用編から削除し、導入例をプレースホルダーにした。利用者の実機確認（Ubuntu 24.04／Codespaces、VS Code 1.145.3、CLI 1.0.109）を環境章と §4〜6 に反映。CLI は `.github/copilot/settings.json` で利用可、VS Code は CLI でのグローバル導入と harness Copilot が必要。詳細は `note.md`。新環境での再現・管理操作・Workspace 補完は未確認。Q-11 の回答により対象版を VS Code 1.145.3 に改め、要件・設計を修正したため requirements・design の再承認待ち。記事の CLI 表記は 1.0.109 に統一し、参照欄の作業メモへのリンクを削除した。
- 利用者提供の CLI 1.0.108 ヘルプを照合済み。登録元は owner/repo・URL・ローカルパス、導入は登録済み marketplace の `plugin@marketplace`、ヘルプの表記は `install`。以下の従来の未確認事項のうち版・登録／導入構文は解消したが、導入先・管理操作・両クライアントの利用確認は未解消。実機導入成功とは扱わない。
- 実装: 利用者から利用編の調査・執筆開始と原稿配置・READMEリンクの明確な許可を受領し、[利用編草稿](../../../../articles/plugin-usage.md) と入口リンクを作成。ローカル出典と既報告を整理したが、公開完成稿ではない。[tasks.md](tasks.md) の TASK-1・2・3・5・6・7 は `blocked`、残る7件は `todo`。共有タスクの作成編部分は未着手。
- 次は公式資料・対象版CLIヘルプの照合と、人による新環境検証が必要。Webページ取得ツールはなく、引継ぎのサンドボックス起動エラーによりshell経由の取得・CLI形式検査・Git差分検査は未実施（再試行・修正・迂回なし）。実インストール・外部書き込み・コミットなし。目視の構造・相対リンク確認だけを自動検査や実機成功の代用にしていない。状態は `implementation / active` を維持する。
- 窓口への質問（公開前に必要）: 対象CLIの版と marketplace登録／Plugin導入ヘルプの該当箇所（`@` 右側の識別子）、CLI／VS Code各版の導入先・有効化・更新・無効化・削除の確認済み操作、Workspace指示ファイルの対応配置・適用先を提供可能か。推奨は秘密を除いた公式資料・ヘルプと新環境の実測記録の提供。確認できれば具体手順を完成でき、未回答なら草稿のまま保留する。新環境の担当者と、OS・実行形態・各クライアント／拡張版・Plugin／marketplace版・事前状態も確認する。
- 必須要件の説明は削除していない。確認不能を理由に設定・スコープ・管理・補完例などの必須説明を削る方針に変える場合は、要件修正・再承認の論点として窓口へ戻す。承認を検証完了とは扱わない。
