# docs/ — 成果物ディレクトリ

パイプラインの各フェーズの成果物がここに出力されます。

| ファイル | 生成フェーズ | 内容 |
|---|---|---|
| `00_idea.md` | /pipeline 開始時(個別実行時は作られない場合あり) | 元のアイデア |
| `01_market_research.md` | /market-research | 市場調査レポート |
| `02_requirements.md` | /requirements | 要求仕様書 |
| `03_ui_design.md` | /design | UIデザイン仕様書 |
| `design_preview.html` | /design | ブラウザで開けるデザインプレビュー |
| `04_architecture.md` | /implement | アーキテクチャ設計書 |
| `05_test_plan.md` | /test-app | テスト計画書 |
| `06_test_report.md` | /test-app | テスト結果レポート |
| `07_release.md` | /release | リリースノート・デプロイ手順 |
| `poc/00_poc_spec.md` | /poc | POC仕様(1ページ: 仮説・Must機能・簡易デザイン) |
| `poc/01_poc_report.md` | /poc | POCレポート(割り切った点・本番化への推奨) |

ファイルを手で編集してから後続フェーズを再実行することもできます(例: 要求仕様を修正 → `/implement` をやり直す)。
