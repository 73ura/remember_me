# おぼえてて - フロントエンド

子供との思い出記録アプリ「おぼえてて」のフロントエンド

## 技術スタック

- **Vue.js**: 3.5.21
- **Tailwind CSS**: 4.1.13
- **Vite**: 開発サーバー・ビルドツール
- **PostCSS**: CSS処理

## 環境セットアップ

### 前提条件

- Node.js (推奨: 18.x以上)
- npm

### セットアップ手順

1. **リポジトリのクローン**
   ```bash
   git clone <repository-url>
   cd remember_me/frontend
   ```

2. **依存関係のインストール（初回のみ）**
   ```bash
   npm install
   ```

3. **開発サーバーの起動**
   ```bash
   npm run dev
   ```

4. **ブラウザで確認**
   ```
   http://localhost:3001
   ```

## 開発

### 便利なコマンド

| 操作                | コマンド                    |
|--------------------|----------------------------|
| 開発サーバー起動     | `npm run dev`               |
| 本番ビルド          | `npm run build`             |
| ビルド結果プレビュー | `npm run preview`           |

### プロジェクト構造

```
frontend/
├── index.html              # メインHTML
├── package.json            # 依存関係管理
├── vite.config.js          # Vite設定
├── tailwind.config.js      # Tailwind CSS設定
├── postcss.config.js       # PostCSS設定
└── src/
    ├── main.js            # エントリーポイント
    ├── App.vue            # メインコンポーネント
    ├── assets/css/main.css # スタイル
    ├── components/        # コンポーネント用
    └── views/            # ページ用
```

## 環境変数
- 各自、.env.local.example を参考に .env.local ファイルを作成してください。
- .env は Git にコミットしないでください←


## API仕様

- **Base URL**: `http://localhost:3000` 
- **Content-Type**: `application/json`


## デプロイ

### 本番ビルド

```bash
npm run build
```

ビルド結果は `dist/` ディレクトリに出力されます。