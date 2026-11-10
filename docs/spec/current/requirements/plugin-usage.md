# GitHub Copilot Plugin 利用編の要件

入力: [CHG-0001 idea.md](../../changes/CHG-0001/idea.md)

## 目的

Copilot Chat を使い始めた開発者が、VS Code の GitHub Copilot Plugin を見つけ、目的と利用範囲に合う形で使えるようにする。

## 利用者と利用場面

| 利用者・役割 | 契機・事前状態 | 代表的なシナリオ |
| --- | --- | --- |
| Copilot Chat を使い始めた開発者 | 仕事やプロジェクトに役立つ Plugin を使いたい | Plugin の内容と提供元を確認し、導入範囲を選んで利用を確認する |

## 対象範囲

- 対象内: VS Code 1.145.2 を前提にした Plugin の概要、発見、確認、導入、スコープ選択、有効化、動作確認、更新・無効化・アンインストール。
- 対象内: 個人用の User 導入と Workspace 導入、導入場所と有効化範囲の違い、Copilot harness における `.github/copilot/settings.json` のリポジトリ既定値を紹介し、プロジェクトごとの使い分けを Tips として扱う。
- 対象内: Plugin の役割、解決を目指す課題、利点を短く紹介する。既存 Plugin をプロジェクトの用途に合わせる方法も Tips として扱う。
- 対象外: 他のエディターや harness の網羅的な利用手順、Plugin の作成手順。
- 前提・外部との境界: VS Code の UI と機能はバージョン、harness、組織ポリシーにより異なる。記事執筆時に公式ガイドと対象バージョンを照合する。

## 機能要件

| ID | 要件 | 出所 |
| --- | --- | --- |
| REQ-USE-001 | 利用編は、Plugin が関連するエージェントカスタマイズをまとめて導入する仕組みであることと、VS Code 拡張機能との違いを読者に説明する。 | CHG-0001 idea.md、[VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins) |
| REQ-USE-002 | 利用編は、Plugin の提供元・含まれるカスタマイズ・README を確認してから、マーケットプレイスまたは Git リポジトリ／ローカルフォルダーから導入する手順を説明する。 | [VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins) |
| REQ-USE-003 | 利用編は User と Workspace の導入スコープ、および導入スコープとグローバル／Workspace の有効化が別の選択であることを説明する。 | 利用者の回答、[VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins) |
| REQ-USE-004 | 利用編は、プロジェクトごとの Plugin 利用Tipsで、`.github/copilot/settings.json` によるリポジトリ既定値と、各利用者による Workspace 単位の有効化・無効化を区別して説明する。各機能の適用対象（harness／クライアント）と、公式説明・実機確認結果が異なる場合はその違いを明示し、確認できた導入手順を示す。 | 利用者の回答、[VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins)、[この workspace の設定例](../../../../.github/copilot/settings.json)、CHG-0001 Q-4 |
| REQ-USE-005 | 利用編は、意図した harness と workspace で Plugin が有効であることを確認し、代表的な作業で利用を確かめる方法を説明する。 | [VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins) |
| REQ-USE-006 | 利用編は、更新・無効化・アンインストールの方法と、Plugin の提供元や実行コード（hooks、MCP servers 等）を確認する注意点を説明する。 | [VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins) |
| REQ-USE-007 | 利用編は、Plugin がエージェント向けの関連カスタマイズをまとめて再利用・配布しやすくする仕組みであること、個別の設定や手順を毎回用意する負担を減らすことを目指す点、まとめて発見・導入・更新しやすい利点を簡潔に説明する。 | 利用者の回答 |
| REQ-USE-008 | 利用編は、既存 Plugin のソースを fork／コピーして変更・検証する方法と、元 Plugin を変更せず Workspace 側のカスタマイズを追加する方法の両方を簡潔に比較し、それぞれの適する場面と変更元の管理方法を示す。具体例は、インストール済み Plugin を変更せずに Workspace 固有の指示・スキル・エージェントなどを追加して補完する例を1つとする。 | 利用者の回答、CHG-0001 Q-5、Q-6、Q-7 |
| REQ-USE-009 | 利用編は、記事の手順が特定の環境で動作しない場合の制約・確認状況を明示し、代替手順の動作を確認できた場合は、その手順と確認した環境を示す。代替手順も未確認の場合は、その旨を明示する。 | 利用者の回答、CHG-0001 Q-9 |

## 業務ルール

| ルール | 関連要件 ID |
| --- | --- |
| Plugin の導入場所と有効化する範囲を同一の設定として説明しない。 | REQ-USE-003 |
| `.github/copilot/settings.json` の説明は公式資料上の仕様と実機確認結果を区別し、Copilot CLI／VS Code Copilot Chat の双方で有効だと未検証のまま断定しない。 | REQ-USE-004 |
| 未確認の Plugin を信頼済みとみなさず、提供元と内容を確認してから導入するよう案内する。 | REQ-USE-006 |
| Plugin の利点を全てのクライアントで同一に動くことや、必ず作業が改善することとして断定しない。 | REQ-USE-007 |
| インストール済み Plugin のキャッシュを直接編集する方法を、カスタマイズ手順として勧めない。変更可能なソースを編集して検証するか、元 Plugin と分離した Workspace カスタマイズを使う。 | REQ-USE-008 |

## 非機能要件

| ID | 要件・適用範囲 | 測定条件・判定基準 | 出所 |
| --- | --- | --- | --- |
| NFR-USE-001 | 利用手順と設定名は対象の VS Code 1.145.2 および Copilot harness に照らして正確であること。 | 手順・設定名を公式資料と対象バージョンで一つずつ照合する。公式仕様と実機確認結果を区別し、差異があれば適用範囲・確認環境とともに説明する。未確認事項はその旨を記す。 | CHG-0001 idea.md、利用者の回答 |
| NFR-USE-002 | 利用編の導入・利用・Workspace カスタマイズの効果を、既存の Plugin 導入状態に依存しない新しい環境で実機確認する。確認は原則として人が担当し、AI エージェントも実施可能な範囲で行ってよい。 | 確認した環境、クライアント・バージョン、実施者、事前状態、操作、期待結果・実際の結果を記録する。未実施・確認不能は成功扱いにしない。 | 利用者の回答、CHG-0001 Q-8 |

## 受入条件

| ID | 対応要件 ID | 事前状態 | 操作・事象 | 観測可能な期待結果 |
| --- | --- | --- | --- | --- |
| AC-USE-001 | REQ-USE-001 | 利用編の草稿がある | Plugin の説明を確認する | Plugin の役割と VS Code 拡張機能との違いが説明されている |
| AC-USE-002 | REQ-USE-002 | 利用編の草稿がある | 導入手順を確認する | 読者が出所と内容を確認し、少なくともマーケットプレイスまたは Git／ローカルソースから導入できる手順が記載されている |
| AC-USE-003 | REQ-USE-003, REQ-USE-004 | 利用編の草稿がある | スコープと Tips を確認する | User／Workspace の導入、導入と有効化の違い、リポジトリ既定値と利用者ごとの Workspace 有効化の違い、各クライアントでの適用範囲が区別されている。未検証の挙動は事実として断定されていない |
| AC-USE-004 | REQ-USE-005 | 利用編の草稿がある | 利用確認手順を確認する | 意図した harness／workspace で有効かを確かめ、代表的な作業で試す手順がある |
| AC-USE-005 | REQ-USE-006 | 利用編の草稿がある | 管理と安全上の説明を確認する | 更新・無効化・アンインストールに加え、提供元と実行コードを確認する注意がある |
| AC-USE-006 | NFR-USE-001 | 利用編の草稿、参照資料、実機確認の記録がある | 手順・設定名を公式資料および実機確認の記録と照合する | 公式仕様と実機確認結果が区別され、差異があれば適用範囲・確認環境とともに説明されている。未確認事項は明記され、公式仕様との一致だけを合格条件にしていない |
| AC-USE-007 | REQ-USE-007 | 利用編の草稿がある | Plugin の概要説明を確認する | Plugin の役割、解決を目指す課題、利点が簡潔に説明され、保証されない効果を断定していない |
| AC-USE-008 | REQ-USE-008 | 利用編の草稿がある | 既存 Plugin のカスタマイズ Tips を確認する | Plugin のソースを fork／コピーして変更する方法と、Workspace 側に補足カスタマイズを追加する方法が簡潔に比較されている。元 Plugin を変更せずに Workspace 固有のカスタマイズを追加する具体例が1つあり、適用場面と変更元の管理が説明されている |
| AC-USE-009 | NFR-USE-002 | 利用編の草稿と新しい検証環境がある | 原則として人が記事の導入・利用・Workspace カスタマイズ例を実機で試す。AI も実施可能な範囲で確認してよい | 環境、クライアント・バージョン、実施者、事前状態、操作、期待結果・実際の結果の記録があり、導入、代表的な利用、Workspace カスタマイズの効果を確認できる。未実施・確認不能な項目が確認済みと混同されていない |
| AC-USE-010 | REQ-USE-009 | 記事の手順が動かない環境の確認結果がある | 利用編の制約と代替手順を確認する | 動かなかった環境・制約・確認状況が示され、動作確認済みの代替手順があれば、その手順と確認環境が記載されている。未確認の代替手順を動作確認済みとして扱っていない |

## 出所・判断根拠

| 対応項目・要件 ID | 資料・人の回答の参照 | 判断根拠 |
| --- | --- | --- |
| 全要件 | [CHG-0001 idea.md](../../changes/CHG-0001/idea.md)、[VS Code Plugin 利用ガイド](https://code.visualstudio.com/docs/agent-customization/agent-plugins) | 読者、到達点、対象バージョンは壁打ちの合意。Plugin の機能・操作範囲・カスタマイズ方法は記事執筆時に公式資料と対象バージョンで検証する。 |
