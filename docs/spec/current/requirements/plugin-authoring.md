# GitHub Copilot Plugin 作成編の要件

入力: [CHG-0001 idea.md](../../changes/CHG-0001/idea.md)

## 目的

開発プロセスを支援する Plugin を作りたい開発リーダーが、VS Code 向けの簡単な新規 Plugin を自分で作成・導入し、動作確認できるようにする。あわせて、構成と検証・社内配布の流れを理解できるようにする。

## 利用者と利用場面

| 利用者・役割 | 契機・事前状態 | 代表的なシナリオ |
| --- | --- | --- |
| 開発プロセスを支援する Plugin を作りたい開発リーダー | 開発チームで繰り返す作業やルールを共有したい | 記事の手順に沿って簡単な Plugin を新規作成・導入し、実行結果を確認する。sdd は実用的な構成の参考例として参照する |

## 対象範囲

- 対象内: Agent Plugin の基本構成、`plugin.json`、Copilot 向け agent／skill 等の配置、marketplace の役割、作成と検証の流れ、社内配布の考え方。
- 対象内: 簡単な新規 Plugin の作成に必要なファイルの内容・配置、導入手順、実行方法と期待結果を示す。
- 対象内: `ai-powered-spec-drivien-development/plugins/sdd/` は参考例として、Plugin 本体と marketplace の関係、テスト・バージョン管理の例を説明する。
- 到達点: 読者が記事の手順に沿って簡単な Plugin を一から作成し、動作確認できることとする。sdd の理解や再実装を前提にしない。具体的なサンプルの題材・構成は設計工程で決める。
- 対象外: sdd Plugin 自体の変更、実際の社内公開・配布の実行、他クライアント・他 Plugin 形式の網羅的な作成手順。
- 前提・外部との境界: 読者は開発プロジェクトの基本と Git の概念を知っている。対象バージョンは VS Code 1.145.2。実際の社内公開方法は社内のアクセス権・運用方針に従う。

## 機能要件

| ID | 要件 | 出所 |
| --- | --- | --- |
| REQ-AUTHOR-001 | 作成編は Plugin が関連するカスタマイズを配布する単位であること、および VS Code が読む Agent Plugins の基本的なディレクトリ構成を説明する。 | CHG-0001 idea.md、[Agent plugin authoring reference](https://code.visualstudio.com/docs/agents/reference/agent-plugin-reference) |
| REQ-AUTHOR-002 | 作成編は `plugin.json` の役割と最低限必要な識別・説明情報、Copilot 固有コンポーネントの名前空間・配置を説明する。 | [Agent plugin authoring reference](https://code.visualstudio.com/docs/agents/reference/agent-plugin-reference) |
| REQ-AUTHOR-003 | 作成編は sdd を参考例として、Plugin 本体、marketplace の定義、agent／skill 等の構成の対応関係を示す。新規作成の手順は sdd の理解に依存させない。 | 利用者の回答、CHG-0001 Q-10、[sdd Plugin README](../../../../ai-powered-spec-drivien-development/plugins/sdd/README.md)、[sdd plugin.json](../../../../ai-powered-spec-drivien-development/plugins/sdd/plugin.json) |
| REQ-AUTHOR-004 | 作成編は作成した Plugin の構成確認、対象 harness／workspace での動作確認、関連テストの実施を含む検証手順を説明する。 | [Agent plugin authoring reference](https://code.visualstudio.com/docs/agents/reference/agent-plugin-reference)、[sdd Plugin README](../../../../ai-powered-spec-drivien-development/plugins/sdd/README.md) |
| REQ-AUTHOR-005 | 作成編は利用者の決定に基づき社内配布を前提とした公開・導入の流れを説明し、具体的な配布方式は設計工程で選定した方式に合わせる。 | 利用者の回答、CHG-0001 Q-3 |
| REQ-AUTHOR-006 | 作成編は社内配布する Plugin の提供元・コード・必要な設定をレビューし、更新の責任を明確にする必要性を説明する。 | [VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins)、[Agent plugin authoring reference](https://code.visualstudio.com/docs/agents/reference/agent-plugin-reference) |
| REQ-AUTHOR-007 | 作成編は、簡単な新規 Plugin を一から作成する手順を示す。必要なファイルの内容と配置、導入・実行の操作、期待する結果を掲載し、読者が参考例の sdd を再実装せずに Plugin を作って動作確認できるようにする。 | 利用者による Q-10 の回答変更 |

## 業務ルール

| ルール | 関連要件 ID |
| --- | --- |
| sdd は説明例であり、sdd の固有の設計や運用ルールをすべての Plugin に必須の仕様として扱わない。 | REQ-AUTHOR-003 |
| 配布対象は社内利用者とする。具体的な配布方式は設計で決定し、未決の方式を確定した手順として記載しない。 | REQ-AUTHOR-005 |
| 社内向けであることを理由に安全性を推定せず、利用者が配布元と Plugin の内容を確認できる説明を含める。 | REQ-AUTHOR-006 |

## 非機能要件

| ID | 要件・適用範囲 | 測定条件・判定基準 | 出所 |
| --- | --- | --- | --- |
| NFR-AUTHOR-001 | 作成・公開手順は対象の VS Code 1.145.2 と社内配布方針に照らして正確であること。 | 構成と手順を公式資料および設計で決めた配布方式と照合し、対象範囲を明記する。 | 利用者の回答、公式資料 |

## 受入条件

| ID | 対応要件 ID | 事前状態 | 操作・事象 | 観測可能な期待結果 |
| --- | --- | --- | --- | --- |
| AC-AUTHOR-001 | REQ-AUTHOR-001 | 作成編の草稿がある | 基本構成を確認する | Plugin の役割と認識可能な基本ディレクトリ構成が示されている |
| AC-AUTHOR-002 | REQ-AUTHOR-002 | 作成編の草稿がある | manifest と Copilot 固有構成を確認する | `plugin.json` と Copilot 固有ファイルの役割・配置が示されている |
| AC-AUTHOR-003 | REQ-AUTHOR-003 | 作成編と sdd の例がある | 参考例と新規作成手順の関係を確認する | sdd の本体・marketplace・agent／skill の関係が説明されている。sdd は参考例として区別され、新規 Plugin の作成に sdd の理解・再実装を必要としない |
| AC-AUTHOR-004 | REQ-AUTHOR-004 | 作成編の草稿がある | 検証手順を確認する | 構成、対象環境での利用、関連テストの確認方法が含まれている |
| AC-AUTHOR-005 | REQ-AUTHOR-005 | 配布方式が設計で決定済み | 社内公開・導入の説明を確認する | 社内配布を前提とし、設計で決めた方式に従う公開・導入の手順と対象読者が示されている |
| AC-AUTHOR-006 | REQ-AUTHOR-006, NFR-AUTHOR-001 | 作成編の草稿と配布方式がある | 配布前の確認事項を確認する | 提供元・内容・設定・更新の確認事項が説明され、構成・手順が公式資料および選定方式と一致する |
| AC-AUTHOR-007 | REQ-AUTHOR-007 | 作成編の草稿と、サンプル Plugin が未導入の検証環境がある | 記事に従い必要なファイルを新規作成し、Plugin を導入・実行する。確認は原則として人が担当し、AI も実施可能な範囲で行ってよい | 記事に掲載した情報で Plugin を作成・導入でき、実行結果が記事の期待結果と一致する。確認環境・クライアントとバージョン・実施者・操作・結果が記録され、未実施を確認済みと扱っていない |

## 未解決質問

| 質問・参照 | 選択肢 | 推奨・理由 | 影響する要件・作業 | 確認期限の工程 |
| --- | --- | --- | --- | --- |
| Q-3: 社内向け Plugin の具体的な配布方式は何か | 社内 Git リポジトリの marketplace／別の社内配布方法 | 社内利用者がアクセス・更新でき、管理しやすい方式を選ぶ | REQ-AUTHOR-005 と配布手順 | design |

## 出所・判断根拠

| 対応項目・要件 ID | 資料・人の回答の参照 | 判断根拠 |
| --- | --- | --- |
| 全要件 | [CHG-0001 idea.md](../../changes/CHG-0001/idea.md)、[Agent plugin authoring reference](https://code.visualstudio.com/docs/agents/reference/agent-plugin-reference)、[sdd Plugin README](../../../../ai-powered-spec-drivien-development/plugins/sdd/README.md) | 読者・sdd の例・社内配布方針は人の合意。Plugin 構成・VS Code の動作は公式資料を根拠とする。 |
