# ai_agent_skills_mcp_hooks_template

**市場調査 → POC試作 / 本番開発 → テスト → リリース** まで一気通貫で実行できるAIアプリ開発基盤のAPMパッケージです。Claude Code・GitHub Copilot など複数のAIコーディングエージェントで共用できます。

- **skills 10個**: market-research / requirements / design / implement / test-app / pipeline / poc / promote / iterate / release
- **agents 8体**: 市場アナリスト、要求エンジニア、UIデザイナー、アーキテクト、実装者、コード/デザインレビュアー、テストエンジニア
- **品質基準7本**: 調査・仕様・デザイン(AIっぽさ排除)・実装・テスト・SaaS(認証/決済)・モバイル(PWA/ネイティブExpo)
- **hooks**: セッション開始時に成果物一覧を自動通知(進捗把握)
- **MCP**: Playwright(E2E・動作確認)+ Context7(最新ライブラリドキュメント)

## セットアップ(部内メンバー向け)

### 前提

- Node.js LTS(MCPサーバーとアプリ実装に必須): https://nodejs.org
- APM CLI: https://github.com/danielmeppiel/apm の手順でインストール
- Python 3.10+(APM CLIの動作要件)

### インストール手順(早見表)

使うツールによって違うのは **`--target` の値と、Claude Codeだけ3行目(権限設定コピー)がある** の2点だけです。開発プロジェクトのフォルダで実行してください。

| 手順 | GitHub Copilot (VS Code) | Claude Code |
|---|---|---|
| 1. パッケージ導入 | `apm install normozisan/ai_agent_skills_mcp_hooks_template --target copilot` | `apm install normozisan/ai_agent_skills_mcp_hooks_template --target claude` |
| 2. 規約ファイル生成 | `apm compile -t copilot` | `apm compile` |
| 3. 権限設定コピー | **不要** | `robocopy apm_modules\normozisan\ai_agent_skills_mcp_hooks_template\template . /E` |
| 4. 開始 | VS CodeでCopilotを開く | `claude` を起動 |

#### GitHub Copilot はなぜ2コマンドで済むのか

skills / agents / hooks / MCP は手順1で `.github/` 等に自動配備され、開発規約は手順2で `.github/copilot-instructions.md` に生成されます。Copilotに必要なものはこれで全部なので、手動コピーはありません。

#### Claude Code の3行目は何をしているのか

`apm install` した時点でパッケージ一式は `apm_modules/` にダウンロード済みです。3行目はその中の `template/`(プロジェクト雛形)をプロジェクト直下にコピーしています。目的はほぼ1つ、**権限の事前許可設定(`.claude/settings.json`)を置くこと**です。

- これが無くても動きますが、npm や git を実行するたびに「許可しますか?」の確認が出て、一気通貫の自動実行が止まりがちになります
- 一緒にコピーされる `docs/`・`app/` フォルダは出力先の説明用で、無くても実行時に自動生成されます
- Mac/Linux の場合の3行目: `cp -r apm_modules/normozisan/ai_agent_skills_mcp_hooks_template/template/. .`

#### 補足(両ツール共通)

- 新規の空フォルダでは `--target` 指定が必須です(既存プロジェクトでは自動検出されます)
- 手順1で `apm.yml` / `apm.lock.yaml` が生成されるため、これをコミットしておけば、以後のメンバーは `apm install`(引数なし)だけで同一環境を再現できます

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

**スマホアプリも5プロセス対応**です。要求仕様フェーズでプラットフォーム判定(既定はPWA、ストア配布・プッシュ通知等がMust要件ならネイティブExpo)が行われ、ネイティブの場合はテスト(jest-expo + Maestro)・デザインレビュー(Expo Web経由)・リリース(EAS Build → TestFlight / Play内部テスト)が自動で切り替わります。E2Eに実機/エミュレータが無い場合は実機必須項目が「手動確認チェックリスト」として成果物に分離記載されます。

## 出力される成果物

本番開発では `docs/` に番号付きドキュメントが自動生成されます:

`01_market_research.md`(市場調査)→ `02_requirements.md`(要求仕様)→ `03_ui_design.md` + `design_preview.html`(デザイン仕様+モック)→ `04_architecture.md`(設計)→ `05_test_plan.md`(テスト仕様)→ `06_test_report.md`(テスト結果)→ `07_release.md`(リリースノート)。アプリ本体は `app/` に出力されます。

## ツール別の対応状況

| ツール | skills | agents | instructions | hooks | MCP | 備考 |
|---|---|---|---|---|---|---|
| Claude Code | ✓ | ✓ | ✓(AGENTS.md) | ✓ | ✓ | **フル機能**。5プロセスすべて動作 |
| GitHub Copilot | ✓ | ✓ | ✓(copilot-instructions) | ✓ | ✓ | VS Codeのチャットで `/` を打つとスキル一覧に表示され `/poc` 等で起動可(自動マッチも有効)。マルチエージェント委譲を伴う `/pipeline` の完全自動はClaude Code推奨 |
| Cursor ほか | ✓ | 一部 | ✓ | 一部 | ✓ | APMのTargets Matrix準拠 |

skills内部のパス参照(`.claude/skills/...`)はClaude Code向けに書かれているため、**パイプラインの完全自動実行はClaude Codeが推奨環境**です。他ツールでは規約(instructions)と各skills/品質基準がコンテキストとして共有されます。

## Tips: Claude Code と GitHub Copilot の使い分け(2026年7月時点)

### `/pipeline` の動き方はツールで違う

| | 並列エージェントの起動主体 | `/pipeline` 一発での5フェーズ完全自動 |
|---|---|---|
| Claude Code | **エージェント自身**が判断してサブエージェント8体に委譲(スキルの指示どおり並列実行) | ○ |
| GitHub Copilot | **ユーザー**が `/fleet` で明示的に起動(エージェントは自発的に委譲できない) | ×(メインエージェントが1人で順にこなす形になる) |

`/pipeline` スキル内の「サブエージェントに並列委譲」という指示はClaude CodeのAgentツール前提の記述のため、Copilotでは実行されません。**CopilotではメインのAIが1人で順番にこなす動き**になります。

### Copilotでの推奨の使い方: フェーズを1つずつ回す

```
/market-research <テーマ>
/requirements
/design
/implement
/test-app
```

一発全自動でなくても品質が落ちない理由: この基盤は**フェーズ間の受け渡しをAIの記憶ではなく `docs/` のファイルで行う**設計のため、1フェーズずつ実行しても要件ID・テストIDの相互参照や品質基準は完全に保たれます。

### Copilot CLI の `/fleet` と組み合わせる

2026年4月リリースの `/fleet`(Copilot CLI)で、並列にしたい箇所だけ手動でファンアウトできます:

```
/fleet <テーマ>の市場調査。競合A・B・Cの機能/料金/レビュー不満を独立に調査し、
       docs/research_a.md, _b.md, _c.md に出力
→ その後 /market-research で統合
```

注意: `/fleet` にはファイルロックが無いため(公式明記)、**同じファイルを複数エージェントに書かせない**こと。出力先を分けて統合はスキルに任せるのが安全です。

### 途中でツールを乗り換えられる

成果物がファイルベースなので、例えば「市場調査と要求仕様はCopilotで → 実装以降(独立レビューのループが効く部分)はClaude Codeで `/implement` から」という運用が成立します。`/pipeline` には docs/ を見て完了フェーズをスキップする再開ロジックが入っているため、どちらのツールから再開しても噛み合います。

### 何がClaude Code固有の強みか

品質の核である「実装 → **独立したコンテキストを持つ** code-reviewer / design-reviewer によるレビュー → 修正ループ」は、役割定義されたサブエージェントへの委譲で成り立っています。実装した本人がセルフチェックするのではなく、思い込みを引き継がない別の目で検証する構造です。ここが現時点でClaude Codeを推奨環境とする理由です。

※ この領域は機能追加が非常に速いため、上記は2026年7月時点の整理です。Copilot側にエージェントの自律的なサブエージェント起動が入れば差は縮まります。導入時に各ツールで一度試走することを推奨します。

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
