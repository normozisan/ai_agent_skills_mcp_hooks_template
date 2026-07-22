# UIデザイン仕様書: {アプリ名}

- 作成日: {YYYY-MM-DD}
- 入力: docs/02_requirements.md, docs/01_market_research.md
- プレビュー: docs/design_preview.html

## 1. アートディレクション

- コンセプト名: {例: 「静かな家計簿」}
- ムードワード: {3語}
- 選定理由: {ペルソナ・差別化ポイントとの対応}
- シグネチャー要素: {このアプリらしさが出る要素を最低1つ}
- 参考にした方向性: {実在プロダクト2〜3と、借りるもの/借りないもの}

## 2. デザイントークン(実装にそのまま使用)

```css
:root {
  /* Color — neutral */
  --color-bg: {#};
  --color-surface: {#};
  --color-border: {#};
  --color-text: {#};
  --color-text-muted: {#};
  /* Color — brand */
  --color-primary: {#};
  --color-primary-hover: {#};
  --color-accent: {#};
  /* Color — semantic */
  --color-success: {#};
  --color-warning: {#};
  --color-danger: {#};
  /* Typography */
  --font-heading: {'書体名'}, sans-serif;
  --font-body: {'書体名'}, sans-serif;
  --font-numeric: {数値表示用があれば};
  /* Type scale */
  --text-xs: {12px}; --text-sm: {14px}; --text-base: {16px};
  --text-lg: {20px}; --text-xl: {28px}; --text-2xl: {40px};
  /* Spacing (4/8px grid) */
  --space-1: 4px; --space-2: 8px; --space-3: 12px; --space-4: 16px;
  --space-6: 24px; --space-8: 32px; --space-12: 48px; --space-16: 64px;
  /* Shape & elevation */
  --radius-sm: {8px}; --radius-md: {12px};
  --shadow-sm: {多層の弱い影};
  --shadow-md: {多層の弱い影};
  /* Motion */
  --duration-fast: 150ms; --duration-base: 250ms;
  --ease: cubic-bezier(0.2, 0, 0, 1);
}
```

- タイポグラフィ運用: {見出し/本文/数値の書体・ウェイト・行間の使い分け}
- 色の運用ルール: {ニュートラル/ブランド/アクセントの構成比と使いどころ}

## 3. コンポーネント仕様

| コンポーネント | 仕様(サイズ・色・状態) |
|---|---|
| ボタン Primary | {高さ、padding、色、hover/active/disabled/focus} |
| ボタン Secondary / Ghost | |
| 入力フィールド | {ラベル位置、エラー表示} |
| カード | |
| {アプリ固有のコンポーネント} | |

- アイコンセット: {Lucide / Heroicons / Phosphor のどれか + サイズ}

## 4. 画面デザイン仕様

### SC-001: {画面名}
- レイアウト構成: {ゾーニングを言語化。ヘッダー/メイン/アクションの配置と幅}
- 情報の優先順位: {最初に目に入るべきもの → 次 → 補助}
- 主要要素の仕様: {具体的に}
- 空状態(0件)のデザイン: {必須}
- モバイル幅での変化: {必須}

{全画面について繰り返す}

## 5. モーション仕様

| 対象 | トリガー | 動き | 時間/イージング |
|---|---|---|---|
| {例: 保存ボタン} | クリック | {フィードバック} | 150ms / var(--ease) |

## 6. アクセシビリティ

- コントラスト検証結果: {本文/見出し/ボタンの比}
- フォーカスリングのデザイン: {色・形}
- タッチターゲット: 最小44×44px

## 7. 実装フェーズへの申し送り

- 実装優先度が高いデザイン要素: {シグネチャー要素など}
- 妥協してよい箇所 / 絶対に妥協しない箇所: {明記}
