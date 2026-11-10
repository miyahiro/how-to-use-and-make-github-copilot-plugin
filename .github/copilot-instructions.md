# Copilot 向けリポジトリ指示

日本語で応答・文書化する。

## 構成

- ルートは記事「GitHub Copilot Plugin の使用方法と作成方法」の原稿リポジトリ。入口は `README.md`。記事の案件は `docs/spec/changes/CHG-0001/`（`status.md` に工程・状態・質問・承認が入る）。
- `ai-powered-spec-drivien-development/` は、記事の題材であるスペック駆動開発プラグイン `sdd` のソース（別の Git リポジトリ。ルート側では未追跡）。
  - `plugins/sdd/`: 配布するプラグイン本体（`plugin.json`、`agents/`、`skills/`、`common-rules.md`、`user_guide.md`）。
  - `bin/sdd.mjs` + `lib/`: 導入先の `.github/` にプラグインの中身を書き出す CLI（リポジトリ版）。
  - `docs/spec/current/`: 原本（要件・設計）。`docs/spec/changes/<ID>/`: 案件ごとの記録。
- `.github/copilot/settings.json` が上記ディレクトリをローカルのマーケットプレイスとして登録し、`sdd@spec-driven-development` を有効にしている。ルートの旧 `.github/agents`・`.github/skills`・`.github/sdd` はこの登録に置き換わり、作業ツリーでは削除状態。

## コマンド（`ai-powered-spec-drivien-development/` で実行。Node 20 以上、依存なし）

```sh
npm test                                              # node --test test/*.test.mjs
node --test test/check.test.mjs                       # 単一のテストファイル
node --test --test-name-pattern="<名前>" test/*.test.mjs   # 単一のテスト
node plugins/sdd/skills/sdd-spec-check/scripts/check.mjs            # 仕様・案件の形式検査
node plugins/sdd/skills/sdd-spec-check/scripts/check.mjs CHG-0001   # 案件の状況の要約
```

リンタ・ビルドはない。

## 規約

- コードは標準モジュールだけを使い、既存の命名・コメント量に合わせる。起こりえない場合の防御的な分岐、使われない引数・設定、将来のための抽象化を足さない（`ai-powered-spec-drivien-development/.github/copilot-instructions.md`）。
- スペック駆動開発のワークフロー（`plugins/sdd/common-rules.md`）:
  - 案件は規模で工程が決まる。標準は「壁打ち → 要件 → 設計 → 実装 → 完了」、軽量は「要点 → 実装 → 完了」。状態は `status.md` 1枚。
  - 承認は承認者の明確な肯定だけ（「続けて」は承認ではない）。AI は自己承認しない。承認時は対象と `status.md` をパス指定でローカルコミットする（`approve(<工程>): <ID> <題名>`）。
  - Git は status・diff・log、確認後のブランチ作成、パス指定のローカルコミットだけ。`git add -A`/`.`、`commit -a`、push・PR・マージ・リベースはしない。
  - 原本は変更ブランチで直接直し、部分変更は差分の編集で行う（全文を書き直さない）。作業メモは案件の `note.md` に書き、決定事項・秘密の値は書かない。
