# 作業メモ

## 2026-11-10: Plugin のプロジェクト別利用方法の実機確認メモ

- 利用者の回答: Tips では `.github/copilot/settings.json` と Workspace 単位の有効化・無効化の両方を扱う（C）。
- 利用者による実機確認: `.github/copilot/settings.json` の設定は GitHub Copilot CLI と GitHub Copilot Chat（VS Code 拡張）では有効にならなかった。
- 利用者による実機確認: 次の CLI コマンドで marketplace と Plugin をインストールすると、GitHub Copilot CLI と GitHub Copilot Chat（VS Code）の両方で利用できた。
  - `copilot plugin marketplace add <マーケットプレイスフォルダ>`
  - `copilot plugin add <プラグイン>@<マーケットプレイスフォルダ>`
- この結果は利用者の報告であり、こちらでの再現確認は未実施。記事に手順や設定の効果を記載する前に、対象バージョン、実行環境、コマンドの仕様、`.github/copilot/settings.json` の適用範囲を確認する。
