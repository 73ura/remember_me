# PRタイトル
<!-- 例: feat: 子どもの名(迷)言一覧ページを実装 (#123) -->
<!-- prefixの例: feat | fix | docs | refactor | test | chore | perf | ci | build -->

## 目的 (Why)
- このPRで解決する課題 / ユースケース
- 関連Issue: # (自動クローズ: close/fix/resolve #123 など)

## 変更内容 (What)
- フロント(Vue): 
  - 例) 名言リストの一覧・詳細・作成フォームを追加
- バック(Rails API):
  - 例) quotes エンドポイントを追加 (index/show/create/update/destroy)
- DB(PostgreSQL):
  - 例) quotes テーブルを追加（migrations/xxxx_add_quotes.rb）
- テスト:
  - フロント: Vitest テスト追加/更新
  - バック: RSpec(Unit/Model/Service) + Request spec 追加/更新
- その他: リンター/フォーマッター修正、型/コメント整備 など

## 動作確認 (How)
1. ローカル/コンテナ起動:
   - Frontend: `npm run dev`
   - Backend: `bin/rails s` または `docker compose up -d api`
   - DB: `docker compose up -d db`
2. 画面動作: 
   - URL: http://localhost:3000 など
   - 期待結果: ○○が表示/保存/削除できる
3. API確認:
   - `GET /api/v1/quotes` で 200/JSON 返却
4. 認証:
   - Devise ログイン動作/セッション維持確認
   - OmniAuth モックで外部ログイン導線を確認(テスト)

## スクリーンショット / 画面差分
<!-- 必要なら画像を貼る。UI変更はBefore/Afterで。 -->
<details>
<summary>Before / After</summary>

- Before: 
- After:
</details>

---

## テスト & 品質チェック
- [ ] フロント: `npm run lint` (ESLint) が通る
- [ ] フロント: `npm run format:check` (Prettier) が通る
- [ ] フロント: `npm run test` (Vitest) が通る
- [ ] バック: `bundle exec rubocop`（使っていれば）/ スタイル規約OK
- [ ] バック: `bundle exec rspec` が通る
- [ ] Request spec 追加/更新済み（APIの成功/失敗系を網羅）
- [ ] Devise test helpers 使用（`sign_in` 等）
- [ ] OmniAuth mock 設定（`OmniAuth.config.test_mode = true`）で外部認証をテスト
- [ ] 型/バリデーション/エラーハンドリングを確認（422/401/403/404/500）

## DB関連 (Migration/Seed)
- [ ] Migration あり / なし（どちらか消す）
  - ありの場合:
    - [ ] `rails db:migrate`（ローカル/CI）実行済み
    - [ ] 既存データ互換性OK（NULL/既定値/Index/外部キー）
    - [ ] Rollback 手順を README/PRに記載
- [ ] Docker上のPostgreSQLで動作確認済み
  - 例: `docker compose exec db psql -U postgres -d app_db -c '\dt'`

**Rollback 方法**
```bash
# 影響範囲が大きい変更の場合、明示してください
bundle exec rails db:rollback STEP=1
