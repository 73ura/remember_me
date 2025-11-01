# データベース設計書

## 📋 目次

1. [概要](#概要)
2. [ER図](#er図)
3. [テーブル一覧](#テーブル一覧)
4. [テーブル詳細](#テーブル詳細)
5. [インデックス設計](#インデックス設計)
6. [外部キー制約](#外部キー制約)
7. [ER.dbmlファイル](#erdbmlファイル)

---

## 概要

「おぼえてて」アプリケーションのデータベース設計です。

### データベース仕様

- **DBMS**: PostgreSQL 16
- **文字エンコーディング**: UTF-8
- **命名規則**: スネークケース（例: `family_members`）

### 設計方針

- **正規化**: 第3正規形まで正規化
- **パフォーマンス**: 適切なインデックスを設定
- **拡張性**: 将来の機能追加に備えた柔軟な設計
- **データ整合性**: 外部キー制約で参照整合性を保証

---

## ER図

### ER.dbmlで定義

ER図は`ER.dbml`形式で定義しています。以下のツールで表示・編集できます：

- **dbdiagram.io** (推奨): https://dbdiagram.io/
- **VSCode拡張機能**: "DBML - Database Markup Language"

詳細は[ER.dbmlの使い方](./ER.dbmlの使い方.md)を参照してください。

### ER図の表示方法

1. **オンラインエディター（dbdiagram.io）**:
   - https://dbdiagram.io/ にアクセス
   - `Docs/ER.dbml`の内容をコピー&ペースト
   - ER図が自動生成されます

2. **VSCode拡張機能**:
   - VSCodeで"DBML"拡張機能をインストール
   - `Docs/ER.dbml`を開く
   - プレビュー表示が可能です

3. **コマンドライン**:
   ```bash
   # dbml-cliをインストール
   npm install -g @dbml/cli
   
   # PNG画像を生成
   dbml2img Docs/ER.dbml -o Docs/images/er_diagram.png
   ```

### ER図のプレビュー

```dbml
// Docs/ER.dbml の内容を参照
// または、dbdiagram.ioで直接確認
```

<!-- 生成されたER図画像をここに貼り付ける -->
<!-- ![ER図](./images/er_diagram.png) -->

---

## テーブル一覧

| テーブル名 | 説明 | 主要カラム |
|-----------|------|-----------|
| `accounts` | ユーザー認証情報 | accounts_id, email, password_hash, provider |
| `users` | ユーザー基本情報 | users_id, name, nickname, birth_date |
| `families` | 家族グループ情報 | families_id, name |
| `family_members` | ユーザーと家族の関連 | family_members_id, users_id, families_id |
| `children` | 子供の基本情報 | children_id, families_id, family_members_id, name, birth_date |
| `memories` | 思い出の記録 | memories_id, families_id, children_id, family_members_id, title, memory_date |
| `quotes` | 子供の名言・エピソード | quotes_id, memories_id, quote_text, quote_date |
| `media` | 写真・動画のメタデータ | media_id, memories_id, file_url, file_type |
| `voice` | 音声のメタデータ | voice_id, memories_id, file_url, file_type |
| `role` | 役割 | role_id, role_name |
| `family_members_role` | 家族メンバーの役割 | family_members_role_id, role_id, family_members_id |

---

## テーブル詳細

### accounts（アカウント）

ユーザー認証情報を管理します。Deviseを使用します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| accounts_id | uuid | PK, 自動採番 | アカウントID |
| email | varchar(255) | UNIQUE, NOT NULL | メールアドレス |
| password_hash | varchar(255) | NOT NULL | 暗号化されたパスワード |
| provider | varchar(50) | DEFAULT 'email' | 認証プロバイダー（email/line/google等） |
| provider_uid | varchar(255) | unique | 外部認証プロバイダーのユーザーID |
| remember_created_at | timestamp | | ログイン維持開始日時（Devise） |
| current_sign_in_at | timestamp | | 現在のログイン日時（Devise） |
| last_sign_in_at | timestamp | | 最終ログイン日時（Devise） |
| current_sign_in_ip | varchar(255) | | 現在のログインIP（Devise） |
| last_sign_in_ip | varchar(255) | | 最終ログインIP（Devise） |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**インデックス:**
- `(email, provider_uid)` - ログイン時の検索

---

### users（ユーザー）

ユーザー認証・基本情報を管理します。Deviseを使用します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| users_id | uuid | PK, 自動採番 | ユーザーID |
| accounts_id | uuid | FK, UNIQUE, NOT NULL | アカウントID（accounts.accounts_id参照） |
| name | varchar(100) | NOT NULL | ユーザー名 |
| nickname | varchar(50) | | ニックネーム |
| birth_date | date | | 誕生日 |
| gender | varchar(10) | | 性別（boy/girl/other） |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**リレーション:**
- `accounts` テーブルと一対一の関係
- `family_members` テーブルと一対多の関係

---

### families（家族グループ）

家族グループの基本情報を管理します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| families_id | uuid | PK, 自動採番 | 家族ID |
| family_name | varchar(100) | NOT NULL | 家族名 |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**リレーション:**
- `family_members` テーブルと一対多の関係
- `children` テーブルと一対多の関係

---

### family_members（家族メンバー）

ユーザーと家族の関連を管理します。多対多の関係を実現する中間テーブルです。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| family_members_id | uuid | PK, 自動採番 | 家族メンバーID |
| users_id | uuid | FK, NOT NULL | ユーザーID（users.users_id参照） |
| families_id | uuid | FK, NOT NULL | 家族ID（families.families_id参照） |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**インデックス:**
- `(users_id, families_id)` - UNIQUE制約付きインデックス

**リレーション:**
- `users` テーブルと多対一の関係
- `families` テーブルと多対一の関係
- `family_members_role` テーブルと一対一の関係（役割情報）

---

### children（子供情報）

こどもの基本情報を管理します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| children_id | uuid | PK, 自動採番 | こどもID |
| families_id | uuid | FK, NOT NULL | 家族ID（families.families_id参照） |
| family_members_id | uuid | FK, NOT NULL | 家族メンバーID（family_members.family_members_id参照） |
| name | varchar(100) | NOT NULL | 子供の名前 |
| birth_date | date | | 誕生日 |
| gender | varchar(10) | | 性別（boy/girl/other） |
| color | varchar(10) | | イメージカラー |
| avatar_url | varchar(500) | | 子供のプロフィール画像URL |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**インデックス:**
- `families_id` - 家族IDによる検索の高速化

**リレーション:**
- `families` テーブルと多対一の関係
- `family_members` テーブルと多対一の関係
- `memories` テーブルと一対多の関係

---

### memories（思い出）

思い出の記録を管理します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| memories_id | uuid | PK, 自動採番 | 思い出ID |
| families_id | uuid | FK, NOT NULL | 家族ID（families.families_id参照） |
| children_id | uuid | FK | 子供ID（children.children_id参照） |
| family_members_id | uuid | FK | 作成者ID（family_members.family_members_id参照） |
| title | varchar(200) | NOT NULL | タイトル |
| memory_date | date | NOT NULL | 思い出の日付 |
| location | varchar(100) | | 場所 |
| quotes_id | uuid | FK | 名言・エピソードID（quotes.quotes_id参照）, 必須 |
| media_id | uuid | FK | 写真・動画のメタデータID（media.media_id参照）, 任意 |
| voice_id | uuid | FK | 音声のメタデータID（voice.voice_id参照）, 任意 |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**インデックス:**
- `(families_id, memory_date)` - 家族別・日付順の取得を高速化
- `children_id` - 特定の子供の思い出取得を高速化
- `family_members_id` - 作成者別の取得を高速化

**リレーション:**
- `families` テーブルと多対1の関係
- `children` テーブルと多対1の関係
- `family_members` テーブルと多対1の関係
- `quotes` テーブルと一対一の関係(必須)
- `media` テーブルと一対多の関係（任意）
- `voice` テーブルと一対多の関係（任意）

---

### quotes（名言・エピソード）

子供の名言やエピソードを管理します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| quotes_id | uuid | PK, 自動採番 | 名言ID |
| memories_id | uuid | FK, NOT NULL | 思い出ID（memories.memories_id参照） |
| quote_text | text | NOT NULL | 子供の発言・名言 |
| quote_date | date | NOT NULL | 発言した日付 |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**インデックス:**
- `(memories_id, quote_date)` - 思い出別・日付順の取得を高速化

**リレーション:**
- `memories` テーブルと一対一の関係（必須：1つの思い出に1つの名言）

---

### role（役割）

役割を管理します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| role_id | uuid | PK, 自動採番 | 役割ID |
| role_name | varchar(100) | NOT NULL | 役割名 |
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**リレーション:**
- `family_members_role` テーブルと一対多の関係

---

### family_members_role（家族メンバーの役割）

家族メンバーの役割を管理します。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| family_members_role_id | uuid | PK, 自動採番 | 家族メンバーの役割ID |
| role_id | uuid | FK, NOT NULL | 役割ID（role.role_id参照）|
| family_members_id | uuid | FK, NOT NULL | 家族メンバーID（family_members.family_members_id参照）|
| created_by | uuid | NOT NULL | 作成者 |
| updated_by | uuid | NOT NULL | 更新者 |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**リレーション:**
- `role` テーブルと多対一の関係
- `family_members` テーブルと一対一の関係

---

### media（メディア）

写真・動画のメタデータを管理します。実際のファイルはFirebase Storageに保存されます。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| media_id | uuid | PK, 自動採番 | メディアID |
| memories_id | uuid | FK | 思い出ID（memories.memories_id参照） |
| file_url | varchar(500) | NOT NULL | Firebase Storage URL |
| file_type | varchar(20) | NOT NULL | ファイルタイプ（image/video） |
| file_size | bigint | | ファイルサイズ（bytes） |
| width | integer | | 画像幅（pixel）、動画の場合はnull |
| height | integer | | 画像高さ（pixel）、動画の場合はnull |
| thumbnail_url | varchar(500) | | サムネイル画像URL（動画の場合） |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**インデックス:**
- `memories_id` - 思い出に紐づくメディア取得を高速化

**リレーション:**
- `memories` テーブルと多対1の関係（任意）

**注意:**
- `memories_id` が任意（NULL可）
- 実際のファイルはFirebase Storageに保存され、URLのみを管理

---

### voice（音声）

音声のメタデータを管理します。実際のファイルはFirebase Storageに保存されます。

| カラム名 | 型 | 制約 | 説明 |
|---------|---|------|------|
| voice_id | uuid | PK, 自動採番 | 音声ID |
| memories_id | uuid | FK | 思い出ID（memories.memories_id参照） |
| file_url | varchar(500) | NOT NULL | Firebase Storage URL |
| file_type | varchar(20) | NOT NULL | ファイルタイプ（mp3）|
| file_size | bigint | | ファイルサイズ（bytes） |
| created_at | timestamp | NOT NULL | 作成日時 |
| updated_at | timestamp | NOT NULL | 更新日時 |

**インデックス:**
- `memories_id` - 思い出に紐づくメディア取得を高速化

**リレーション:**
- `memories` テーブルと多対1の関係（任意）

**注意:**
- `memories_id` が必須（NOT NULL）
- 実際のファイルはFirebase Storageに保存され、URLのみを管理

---

## インデックス設計

### 主要インデックス

| テーブル | インデックス | 用途 |
|---------|------------|------|
| `accounts` | `(email, provider_uid)` (UNIQUE) | ログイン時の検索 |
| `family_members` | `(users_id, families_id)` (UNIQUE) | 重複防止・検索 |
| `children` | `families_id` | 家族別の子供一覧取得 |
| `memories` | `(families_id, memory_date)` | 家族別・日付順の取得 |
| `memories` | `children_id` | 特定の子供の思い出取得 |
| `memories` | `family_members_id` | 作成者別の取得 |
| `quotes` | `memories_id` | 思い出に紐づく名言取得 |
| `quotes` | `(memories_id, quote_date)` | 思い出別・日付順の取得 |
| `media` | `memories_id` | 思い出に紐づくメディア取得 |
| `voice` | `memories_id` | 思い出に紐づく音声取得 |

### インデックス設計方針

1. **外部キーカラム**: 基本的にインデックスを作成（結合処理の高速化）
2. **検索条件**: WHERE句やORDER BYで頻繁に使用されるカラムにインデックス
3. **複合インデックス**: 複数カラムを組み合わせて検索する場合に作成

---

## 外部キー制約

### 外部キー一覧

| テーブル | カラム | 参照先テーブル | 参照先カラム | ON DELETE | ON UPDATE |
|---------|--------|---------------|-------------|-----------|-----------|
| `users` | `accounts_id` | `accounts` | `accounts_id` | RESTRICT | CASCADE |
| `family_members` | `users_id` | `users` | `users_id` | CASCADE | CASCADE |
| `family_members` | `families_id` | `families` | `families_id` | CASCADE | CASCADE |
| `children` | `families_id` | `families` | `families_id` | CASCADE | CASCADE |
| `children` | `family_members_id` | `family_members` | `family_members_id` | CASCADE | CASCADE |
| `memories` | `families_id` | `families` | `families_id` | CASCADE | CASCADE |
| `memories` | `children_id` | `children` | `children_id` | SET NULL | CASCADE |
| `memories` | `family_members_id` | `family_members` | `family_members_id` | SET NULL | CASCADE |
| `memories` | `quotes_id` | `quotes` | `quotes_id` | RESTRICT | CASCADE |
| `quotes` | `memories_id` | `memories` | `memories_id` | CASCADE | CASCADE |
| `media` | `memories_id` | `memories` | `memories_id` | CASCADE | CASCADE |
| `voice` | `memories_id` | `memories` | `memories_id` | CASCADE | CASCADE |
| `family_members_role` | `role_id` | `role` | `role_id` | CASCADE | CASCADE |
| `family_members_role` | `family_members_id` | `family_members` | `family_members_id` | CASCADE | CASCADE |

### 外部キー制約の方針

- **CASCADE**: 親レコード削除時に子レコードも削除（関連データを保持する必要がない場合）
- **SET NULL**: 親レコード削除時に子レコードの外部キーをNULLに（関連データを保持したい場合）
- **RESTRICT**: 親レコード削除を禁止（削除前に子レコードを削除する必要がある場合）

---

## ER.dbmlファイル

ER図の定義は`Docs/ER.dbml`ファイルに記述されています。

### ファイルの場所

```
remember_me/
└── Docs/
    └── ER.dbml          # ER.dbmlファイル
```

### 使い方

詳細は[ER.dbmlの使い方](./ER.dbmlの使い方.md)を参照してください。

### 表示・編集方法

1. **dbdiagram.io** (推奨):
   - https://dbdiagram.io/ にアクセス
   - `Docs/ER.dbml`の内容をコピー&ペースト
   - ER図が自動生成されます

2. **VSCode拡張機能**:
   - VSCodeで"DBML"拡張機能をインストール
   - `Docs/ER.dbml`を開く
   - プレビュー表示が可能です

3. **コマンドライン**:
   ```bash
   # dbml-cliをインストール
   npm install -g @dbml/cli
   
   # PNG画像を生成
   dbml2img Docs/ER.dbml -o Docs/images/er_diagram.png
   ```

### 更新時の注意

- マイグレーション作成時は`ER.dbml`も更新
- PRレビュー時にER図を確認
- DB設計書と`ER.dbml`の整合性を保つ

---

## 関連ドキュメント

- [ER.dbmlの使い方](./ER.dbmlの使い方.md) - ER.dbmlの詳細な使い方
- [技術仕様書](./技術仕様書.md) - 技術スタック・アーキテクチャ
- [API設計書](./API設計書.md) - APIエンドポイント仕様

