# 作業メモ

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
