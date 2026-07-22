# AI App Create Template — 開発パイプライン規約

このプロジェクトは「市場調査 → 要求仕様 → UIデザイン → 実装 → テスト」を一気通貫で実行するアプリ開発テンプレートです。

## パイプライン全体像

| フェーズ | スキル | 主担当サブエージェント | 成果物 |
|---|---|---|---|
| 1. 市場調査 | `/market-research` | market-analyst | `docs/01_market_research.md` |
| 2. 要求仕様 | `/requirements` | requirements-engineer | `docs/02_requirements.md` |
| 3. UIデザイン | `/design` | ui-designer | `docs/03_ui_design.md`, `docs/design_preview.html` |
| 4. 実装 | `/implement` | architect, implementer, code-reviewer, design-reviewer | `docs/04_architecture.md` + `app/` |
| 5. テスト | `/test-app` | test-engineer | `docs/05_test_plan.md`, `docs/06_test_report.md` |
| 全部一括 | `/pipeline` | 上記すべて | 上記すべて |
| 改修(v1後) | `/iterate` | implementer, test-engineer ほか | 上流ドキュメント更新 + 実装 + 回帰テスト |
| リリース | `/release` | implementer ほか | `docs/07_release.md` + 公開可能なビルド |
| POC試作 | `/poc` | market-analyst(軽量), implementer | `docs/poc/` + `app/`(試作品) |
| POC→本番昇格 | `/promote` | code-reviewer ほか全員 | 正式パイプラインへ移行 |

## 開発モード

- **POCモード(`/poc`)**: 速度優先。品質ラインは「ビルドが通る・主要フローが動く・デザインのアンチパターン回避・セキュリティ最低限」のみ。網羅テスト・レビュー周回はしない(割り切りを必ずユーザーに伝える)。
- **本番モード(`/pipeline` および各フェーズskill)**: 品質優先。全品質基準・完了ゲート・レビューループを省略しない。**フェーズ完了ごとに git commit する**(成果物とコードの履歴を残す。コミットメッセージは「フェーズ名: 要約」形式)。

各フェーズには品質基準ドキュメントがあり、担当エージェントは必ずそれに従う:
市場調査 `.claude/skills/market-research/references/research-guidelines.md` / 要求仕様 `.claude/skills/requirements/references/requirements-guidelines.md` / デザイン `.claude/skills/design/references/design-guidelines.md` / 実装 `.claude/skills/implement/references/implementation-guidelines.md` / テスト `.claude/skills/test-app/references/testing-guidelines.md`(いずれもプロジェクトルートからのパス)

## 絶対に守るルール

1. **成果物は必ず `docs/` に番号付きファイル名で保存する。** 後続フェーズは前フェーズの成果物を読んでから開始する。
2. **前フェーズの成果物が存在しない場合**、そのフェーズを先に実行するか、ユーザーに確認する。勝手に前提を捏造しない。逆に成果物が既に存在する場合は上書きせず、再利用・再開を優先する。
2-2. **v1完成後の変更(機能追加・修正)は `/iterate` の手順で行い、コードだけ直してドキュメントを腐らせない。**
3. **アプリのソースコードは `app/` ディレクトリ配下に作成する。** テンプレートのルートを汚さない。
4. **成果物・コメント・ドキュメントはすべて日本語で書く。** コード内の識別子(変数名・関数名)は英語。
5. **各フェーズ完了時に成果物のサマリーをユーザーに提示し、次フェーズへ進む前に重要な意思決定(技術スタック、MVPスコープ等)は AskUserQuestion で確認する。** `/pipeline` 実行時も同様(自動承認モードを明示された場合を除く)。

## 品質基準

- 実装フェーズでは lint / typecheck / build がすべて通るまで完了としない。
- テストフェーズでは、失敗したテストは原因を修正して再実行する。修正不能な場合はテストレポートに理由を明記する。
- コードレビューは code-reviewer サブエージェントで必ず実施し、Critical/High の指摘はすべて修正してから完了とする。
- **デザイン品質**: UIを持つアプリは、`.claude/skills/design/references/design-guidelines.md` のプロ品質基準と「AIっぽさ」アンチパターンチェックリストに全面的に従う。実装後は design-reviewer によるスクリーンショットベースのデザインレビューに合格するまで完了としない。

## 技術スタック既定値(要求仕様で特に指定がない場合)

- Webアプリ / Webサイト: Vite + React + TypeScript
- スマホアプリ: まずレスポンシブWeb(PWA)で作る。ネイティブが必須要件の場合のみ Expo (React Native)
- テスト: Vitest(ユニット) + Playwright(E2E)
- スタイル: Tailwind CSS もしくはプレーンCSS(要件に応じて)。いずれの場合もデザイントークンをCSS変数で定義し、HEX直書きをしない
- バックエンドが必要な場合: Node.js (Express or Hono) + SQLite から開始
- **SaaS型(認証・決済・サーバーサイドDBのいずれかを含む)の場合**: `.claude/skills/implement/references/saas-guidelines.md` の技術スタック既定値(Next.js / Supabase / Stripe)と大原則(認証を自作しない・カード情報に触れない・認可はサーバー側)に全フェーズで従う。適用判定は要求仕様フェーズで行い、仕様書に明記する

ライブラリの最新の使い方は Context7 MCP (`mcp__context7__*`) で確認してから書くこと。

## MCP サーバー

- `playwright`: E2Eテスト実行、実装したアプリの動作確認、競合サイトの調査に使用。
- `context7`: ライブラリの最新ドキュメント取得。実装前に主要ライブラリのAPIを確認する。

MCPツールが未ロード(deferred)の環境では、使用前に ToolSearch で `mcp__playwright` / `mcp__context7` を検索してロードすること。

## サブエージェントの使い分け

- 調査・分析系は並列で複数起動してよい(例: 競合3社を3エージェントで同時調査)。
- 実装は implementer に委譲し、完了後に必ず code-reviewer(コード品質)と design-reviewer(見た目の品質)でレビューする。
- デザインの作成・変更は ui-designer に委譲する。
- サブエージェントには「必要なファイルパス・前フェーズ成果物の要約・期待する出力形式」を必ずプロンプトに含めて自己完結させる。
