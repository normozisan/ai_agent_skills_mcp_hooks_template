# app/ — アプリ本体

`/implement` フェーズで実装されるアプリのソースコードがここに作成されます。

実装完了後の基本操作(スタックにより異なる場合はここが更新されます):

```bash
cd app
npm install
npm run dev       # 開発サーバー起動
npm run build     # 本番ビルド
npm test          # ユニットテスト(テストフェーズ後に利用可能)
npm run test:e2e  # E2Eテスト(テストフェーズ後に利用可能)
```

※ `test` / `test:e2e` スクリプトは `/test-app` フェーズで追加されます。実装完了直後はまだ存在しません。
