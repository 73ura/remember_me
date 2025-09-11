# おぼえてて - バックエンド

## 技術スタック
- Ruby 3.3.0
- Rails 8.0.2.1 (API mode)
- PostgreSQL 16
- Docker & Docker Compose

## 環境セットアップ

### 前提
- 🐳Docker / Docker Compose

### セットアップ手順
1. リポジトリをクローン

   ```bash
   git clone <repository-url>
   cd remember_me/backend
   ```

2. Dockerコンテナ起動

    ```bash
    docker-compose up -d
    ```
3. データベース作成（1.初回環境構築時 2. データベースを削除した後 3. データベース名を変更した場合DB ）

    ```bash
    docker-compose exec web ./bin/rails db:create
    ```
4. 起動確認

    ```bash
    curl http://localhost:3000
    ```

## 開発時の便利コマンド

| 操作                | コマンド                                      |
|--------------------|---------------------------------------------|
| コンテナ起動         | `docker-compose up -d`                        |
| コンテナ停止         | `docker-compose down`                         |
| Railsコンソール      | `docker-compose exec web ./bin/rails console`|
| マイグレーション     | `docker-compose exec web ./bin/rails db:migrate`|
| DBリセット          | `docker-compose exec web ./bin/rails db:reset`|
| シード投入          | `docker-compose exec web ./bin/rails db:seed`|

## 環境変数
- 各自、.env.example を参考に .env ファイルを作成してください。
- .env は Git にコミットしないでください←

## API仕様

- **Base URL（開発用）**: `http://localhost:3000`  
  → 開発中はこのURLにリクエストしてください
- **Content-Type**: `application/json`  
  → 送信データはJSON形式で
