---
name: test-app
description: 実装済みアプリのテストを設計・実装・実行する。要求仕様からテストケースを網羅的に列挙して docs/05_test_plan.md を作成し、ユニットテスト(Vitest)とE2Eテスト(Playwright)を実装・実行、結果を docs/06_test_report.md に出力する。ユーザーがテスト作成・テスト実行・品質確認を求めたときに使う。
argument-hint: "[テストに関する追加指示(任意)]"
---

# テストフェーズ

要求仕様の受け入れ基準を基準に、テストケース列挙 → 自動テスト実装 → 実行 → 不具合修正 → レポート までを完了させる。

ユーザーからの追加指示: $ARGUMENTS

## 手順

### 1. 入力の確認

- `docs/02_requirements.md` と `app/` の存在を確認する。ない場合は先行フェーズの実行をユーザーに確認する。
- `app/` の構成(テストランナーの有無、スクリプト)を把握する。

### 2. テストケース設計(test-engineer サブエージェントに委譲)

test-engineer に以下を渡し、テスト計画を作らせて `docs/05_test_plan.md` に保存する:
- `docs/02_requirements.md` のパス(全Must要件と受け入れ基準を網羅するよう指示)
- 観点: 正常系 / 境界値 / 異常系 / データ永続化 / UI表示
- 出力テンプレート: `${CLAUDE_PROJECT_DIR}/.claude/skills/test-app/templates/test-plan-template.md` のパス
- 品質基準: `${CLAUDE_PROJECT_DIR}/.claude/skills/test-app/references/testing-guidelines.md` のパス(境界値の定番セットを適用)

計画をユーザーに要約提示する(テストケース数、自動化対象の内訳)。

### 3. テスト環境の整備

- ユニットテスト: Vitest を `app/` に導入(未導入の場合)
- E2Eテスト: Playwright を導入し、`npx playwright install chromium` を実行(未導入の場合)
- `package.json` に `test` / `test:e2e` スクリプトを整備
- **ネイティブ(Expo)アプリの場合**: `${CLAUDE_PROJECT_DIR}/.claude/skills/implement/references/mobile-guidelines.md` セクション4に従い、Vitest/Playwright の代わりに jest-expo + Maestro を整備する(実機・エミュレータが無い環境ではExpo Web版へのPlaywrightテストで近似し、実機必須項目は手動確認チェックリストへ分離)

### 4. テスト実装(test-engineer サブエージェントに委譲)

テスト計画の「自動化対象」を test-engineer に実装させる。優先度: 高のケースから着手し、以下を渡す:
- `docs/05_test_plan.md` のパスと対象テストケースID
- 品質基準: `${CLAUDE_PROJECT_DIR}/.claude/skills/test-app/references/testing-guidelines.md` のパス
- テスト配置規約(ユニット: `src/**/*.test.ts`、E2E: `e2e/*.spec.ts`)

### 5. テスト実行と不具合対応ループ

1. 全テストを実行する(`npm test` → `npm run test:e2e`)
2. 失敗があれば原因を切り分ける:
   - **テストコードの問題** → test-engineer に修正させる
   - **アプリのバグ** → implementer に修正を委譲し、修正後に全テストを再実行
3. 全テストがパスするか、修正不能な問題が特定されるまで繰り返す(最大3周。超えたら状況を整理してユーザーに報告)

### 6. テストレポート作成

`${CLAUDE_PROJECT_DIR}/.claude/skills/test-app/templates/test-report-template.md` の構成で `docs/06_test_report.md` を作成する。実行結果の数値は実際の実行ログに基づくこと。

### 7. 完了報告

ユーザーに報告する:
- テスト結果サマリー(総数 / パス / 失敗 / スキップ)
- 発見・修正した不具合の一覧
- 残存する既知の問題とリスク
- テストの再実行方法
