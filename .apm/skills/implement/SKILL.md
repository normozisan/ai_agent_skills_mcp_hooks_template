---
name: implement
description: 要求仕様書とUIデザイン仕様からアプリを設計・実装する。アーキテクチャ設計(docs/04_architecture.md)を作成し、app/ 配下にアプリを実装、コードレビューとデザインレビューによる修正まで行う。ユーザーがアプリの実装・開発を求めたときに使う。
argument-hint: "[実装に関する追加指示(任意)]"
---

# 実装フェーズ

要求仕様書とUIデザイン仕様を入力として、設計 → 実装 → 品質ゲート → コードレビュー+デザインレビュー → 修正 まで完了させ、動くアプリを `app/` に作る。

ユーザーからの追加指示: $ARGUMENTS

## 手順

### 1. 入力の確認

- `docs/02_requirements.md` を読む。**存在しない場合**: ユーザーに「先に `/requirements` を実行するか」を確認する。
- `docs/03_ui_design.md` を読む。**存在しない場合**: 「先に `/design` を実行するか、デザイン仕様なしで進めるか」を確認する(デザイン仕様なしの場合でも `.claude/skills/design/references/design-guidelines.md` のルールには従う)。
- `docs/04_architecture.md` が既に存在する場合は読み、設計をやり直すか既存設計で続行するかを判断する(仕様が変わっていなければ続行)。

### 2. アーキテクチャ設計(architect サブエージェントに委譲)

architect サブエージェントに `docs/02_requirements.md`・`docs/03_ui_design.md` のパス、CLAUDE.md の技術スタック既定値、テンプレート `${CLAUDE_PROJECT_DIR}/.claude/skills/implement/templates/architecture-template.md` のパスを渡し、設計書を作らせて `docs/04_architecture.md` に保存する(デザイントークンのCSS変数化・フォント読み込みを設計に含めさせる)。

設計書のうち **技術スタックと実装タスク分割** をユーザーに提示する。技術スタックが既定値から変わる場合や複数の妥当な選択肢がある場合は AskUserQuestion で確認する(`--auto` モードでは既定値で進める)。

### 3. プロジェクトの雛形作成

`app/` ディレクトリに、設計書のスタックでプロジェクトを初期化する。**必ず非対話モードで実行する**(対話プロンプトが出るとパイプラインが停止する):

```bash
npm create vite@latest app -- --template react-ts
```

(他のスタックの場合も同様に、テンプレート指定フラグ・`--yes` 等で対話を回避する。)

この時点で `npm install` と `npm run build` が通ることを確認する。

### 4. タスクごとに実装(implementer サブエージェント)

設計書の実装タスクを順に implementer サブエージェントへ委譲する。各委譲時に必ず渡すこと:
- タスクの内容と完了条件
- `docs/04_architecture.md`・`docs/02_requirements.md`・`docs/03_ui_design.md` のパス、関連する要件ID
- 「UIはデザイン仕様のトークン(CSS変数)を使い、`${CLAUDE_PROJECT_DIR}/.claude/skills/design/references/design-guidelines.md` の実装ルール(セクション10)と `${CLAUDE_PROJECT_DIR}/.claude/skills/implement/references/implementation-guidelines.md` に従うこと」
- 「ビルドが通る状態で終えること」

最初のタスクとして「デザイントークンのCSS変数定義とフォント読み込み」を実装させ、以降のUIタスクはすべてそのトークンを参照させる。

依存関係のない独立したタスクでも、同じファイルを触る可能性がある場合は直列で実行する(コンフリクト防止)。進捗はToDoリスト(利用可能なタスク管理ツール)で管理し、ユーザーに見えるようにする。

### 5. 品質ゲート(自分で実施)

全タスク完了後、以下をすべて実行して通す:
1. `npm run build` — 成功するまで修正
2. lint / typecheck(設定されている場合)
3. アプリを起動し、Playwright MCP で主要画面を実際に開いて、Must要件の基本フローが動くことを目視相当で確認する(コンソールエラーも確認)

### 6. コードレビュー(code-reviewer サブエージェントに委譲)

code-reviewer に `app/` と `docs/02_requirements.md`・`docs/04_architecture.md` のパスを渡してレビューさせる。

- Critical / High の指摘 → implementer に修正を委譲し、修正後に再レビュー(最大2周)。2周で解消しない場合は作業を止め、残っている指摘と選択肢を提示してユーザーの判断を仰ぐ(自動モードでも停止する。Critical/High を残したまま完了扱いにしない)
- Medium / Low → 修正するか docs/04_architecture.md 末尾に「既知の課題」として記録

### 7. デザインレビュー(design-reviewer サブエージェントに委譲)

design-reviewer に以下を渡し、実際にアプリを起動・スクリーンショット確認させる:
- アプリの起動方法(`app` フォルダで `npm run dev` 等)と主要画面のURL
- `docs/03_ui_design.md` のパス

「要修正」の場合は指摘を implementer に修正させ、再レビュー(合格まで、最大3周)。3周で合格しない場合は作業を止め、残指摘とスクリーンショットを提示してユーザーの判断(妥協して進める/さらに修正する)を仰ぐ(自動モードでも停止する)。

### 8. 完了報告

ユーザーに報告する:
- 実装した機能(要件IDとの対応)
- 起動方法(`app` フォルダで `npm run dev` 等)
- ビルド・コードレビュー・デザインレビューの最終結果
- 既知の課題(あれば)
- 次のステップ: `/test-app` でテストに進めること
