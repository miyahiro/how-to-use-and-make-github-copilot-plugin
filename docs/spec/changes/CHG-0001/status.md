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

- 目的: VS Code 1.145.2 を前提に、GitHub Copilot Plugin の利用編・作成編の2本の記事を作成する。各記事は約20分を目安とし、理解しやすさを優先する
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

## 承認

| 工程 | 日時 | 承認者 | 対象 |
| --- | --- | --- | --- |
| requirements | 2026-11-10 | 宮下 洋 | docs/spec/current/requirements/plugin-usage.md, docs/spec/current/requirements/plugin-authoring.md, docs/spec/current/requirements/article-publication.md, docs/spec/changes/CHG-0001/idea.md |
| design | 2026-11-10 | 宮下 洋 | docs/spec/current/design/plugin-usage.md, docs/spec/current/design/plugin-authoring.md, docs/spec/current/design/article-publication.md |

## 次の作業

- 実装: 利用者から開始の依頼を受けてから、承認済み設計に基づくタスク分解、公式資料との照合、利用編・作成編の原稿作成とサンプルの具体化を進める。新しい環境での実機検証は原則として人が担当し、検証結果に応じて章立て・掲載内容を見直す。公式資料との照合と実機検証は未実施であり、設計承認を検証完了とは扱わない。
