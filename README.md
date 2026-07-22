# ai_agent_skills_mcp_hooks_template

**市場調査 → POC試作 / 本番開発 → テスト → リリース** まで一気通貫で実行できるAIアプリ開発基盤のAPMパッケージです。Claude Code・GitHub Copilot など複数のAIコーディングエージェントで共用できます。

- **skills 10個**: market-research / requirements / design / implement / test-app / pipeline / poc / promote / iterate / release
- **agents 8体**: 市場アナリスト、要求エンジニア、UIデザイナー、アーキテクト、実装者、コード/デザインレビュアー、テストエンジニア
- **品質基準6本**: 調査・仕様・デザイン(AIっぽさ排除)・実装・テスト・SaaS(認証/決済)
- **hooks**: セッション開始時に成果物一覧を自動通知(進捗把握)
- **MCP**: Playwright(E2E・動作確認)+ Context7(最新ライブラリドキュメント)

## セットアップ(部内メンバー向け)

### 前提

- Node.js LTS(MCPサーバーとアプリ実装に必須): https://nodejs.org
- APM CLI: https://github.com/danielmeppiel/apm の手順でインストール
- Python 3.10+(APM CLIの動作要件)

### インストール手順(GitHub Copilot / VS Code)— コマンド2つで完了

開発プロジェクトのフォルダで:

```bash
apm install normozisan/ai_agent_skills_mcp_hooks_template --target copilot
apm compile -t copilot
```

これだけです(手動コピー不要)。skills/agents/hooks/MCPが `.github/` 等に配備され、規約が `.github/copilot-instructions.md` に生成されるので、あとはVS CodeでCopilotを使い始めればOKです。

### インストール手順(Claude Code)— コマンド3つで完了

開発プロジェクトのフォルダで:

```powershell
apm install normozisan/ai_agent_skills_mcp_hooks_template --target claude
apm compile
robocopy apm_modules\normozisan\ai_agent_skills_mcp_hooks_template\template . /E
```

(Mac/Linuxの3行目: `cp -r apm_modules/normozisan/ai_agent_skills_mcp_hooks_template/template/. .`)

あとは `claude` を起動するだけです。

- 1行目: skills 10・agents 8・規約・SessionStartフック・MCPを `.claude/` に自動配備(動作確認済み)。依存は `apm.yml` / `apm.lock.yaml` に記録され、以後メンバーは `apm install` だけで再現可能
- 2行目: 規約を `AGENTS.md` に生成
- 3行目: **権限設定(`.claude/settings.json`)のコピーが目的**。これが無くても動きますが、コマンド実行のたびに許可確認が出て自動実行が止まりがちになります。`docs/`・`app/` フォルダ等も一緒にコピーされますが、これらは無くても実行時に自動生成されます

※ 新規の空フォルダでは `--target` 指定が必須です(既存プロジェクトでは自動検出されます)。

### Windowsでの注意

- **`Filename too long` エラーが出た場合**: `git config --global core.longpaths true` を実行してから再試行してください(深い階層のフォルダで発生します)
- **MCPサーバーの登録が進まない場合**: Node.js がインストールされているか確認してください(MCPサーバーは npx 経由で起動されるため必須)。MCP登録に失敗しても skills/agents/hooks は配備済みなので、`template/.mcp.json` をプロジェクト直下にコピーすれば Claude Code はMCPを利用できます

`template/` には APM プリミティブでは表現できないプロジェクト雛形が入っています:

| ファイル | 用途 |
|---|---|
| `CLAUDE.md` | Claude Code 用のパイプライン規約(クローン利用時。APM利用時はAGENTS.mdで代替可) |
| `.claude/settings.json` | 権限設定(npm/git等の許可、.env読み取り拒否)+ SessionStartフック |
| `.mcp.json` | MCP設定(APMを使わずクローンだけで使う場合のフォールバック) |
| `docs/` `app/` | 成果物・アプリの出力先フォルダ |
| `.gitignore` | node_modules 等の除外設定 |

### APMを使わない場合(クローン利用)

Claude Code だけで使うなら、リポジトリをクローンして `.apm/skills` → `.claude/skills`、`.apm/agents` → `.claude/agents` にコピーし、`template/` の中身をプロジェクト直下に置くだけでも動きます。

## 使い方(5つの開発プロセス)

| # | プロセス | コマンド(Claude Codeのスラッシュコマンド) |
|---|---|---|
| 1 | テーマ→Web調査→**POC試作** | `/poc <テーマ>` |
| 2 | 仕様書→**POC試作**(調査なし) | `/poc --spec 仕様書.md` |
| 3 | テーマ→**いきなり本番開発**(調査から) | `/pipeline <テーマ>` |
| 4 | 仕様書→**いきなり本番開発**(調査なし) | `/pipeline --spec 仕様書.md` |
| 5 | POCを**本番品質に昇格** | `/promote` |

完成後: 機能追加・修正は `/iterate <変更内容>`、公開準備は `/release`。
`--auto` を付けると質問なしの完全自動(例: `/pipeline --auto <テーマ>`)。

本番モード(3・4・5)では、実装品質チェックリスト・コードレビュー(Critical/Highゼロまで)・スクリーンショットによるデザイン審査・境界値/異常系込みの自動テストが自動で効きます。認証・決済・サーバーDBを含むアプリにはSaaSガイドライン(Supabase/Stripe前提、特商法表記等の法務要件含む)が自動適用されます。

## 出力される成果物

本番開発では `docs/` に番号付きドキュメントが自動生成されます:

`01_market_research.md`(市場調査)→ `02_requirements.md`(要求仕様)→ `03_ui_design.md` + `design_preview.html`(デザイン仕様+モック)→ `04_architecture.md`(設計)→ `05_test_plan.md`(テスト仕様)→ `06_test_report.md`(テスト結果)→ `07_release.md`(リリースノート)。アプリ本体は `app/` に出力されます。

## ツール別の対応状況

| ツール | skills | agents | instructions | hooks | MCP | 備考 |
|---|---|---|---|---|---|---|
| Claude Code | ✓ | ✓ | ✓(AGENTS.md) | ✓ | ✓ | **フル機能**。5プロセスすべて動作 |
| GitHub Copilot | ✓ | ✓ | ✓(copilot-instructions) | ✓ | ✓ | 規約・skills知識を共有。マルチエージェント委譲はClaude Code前提 |
| Cursor ほか | ✓ | 一部 | ✓ | 一部 | ✓ | APMのTargets Matrix準拠 |

skills内部のパス参照(`.claude/skills/...`)はClaude Code向けに書かれているため、**パイプラインの完全自動実行はClaude Codeが推奨環境**です。他ツールでは規約(instructions)と各skills/品質基準がコンテキストとして共有されます。

## 更新の反映

このリポジトリが更新されたら、各自のプロジェクトで:

```bash
apm update && apm compile
```

## リポジトリ構成

```
├── apm.yml                  ← APMマニフェスト(MCP依存: playwright, context7)
├── .apm/
│   ├── skills/              ← 10 skills(品質基準・テンプレート同梱)
│   ├── agents/              ← 8 agents(*.agent.md)
│   ├── instructions/        ← パイプライン規約(AGENTS.md/copilot-instructionsに展開)
│   └── hooks/               ← SessionStartフック(進捗自動通知)
└── template/                ← プロジェクト雛形(手動コピー)
```
