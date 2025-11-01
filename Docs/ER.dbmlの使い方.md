# ER.dbmlの使い方

ER.dbml（Database Markup Language）を使ってデータベース設計図を作成する方法を説明します。

## 📚 目次

1. [ER.dbmlとは](#erdbmlとは)
2. [ツールの選択](#ツールの選択)
3. [基本的な書き方](#基本的な書き方)
4. [実践例](#実践例)
5. [DB設計書との連携](#db設計書との連携)

---

## ER.dbmlとは

**ER.dbml**は、データベースのER図（Entity-Relationship Diagram）をテキスト形式で記述するためのマークアップ言語です。

### メリット

- ✅ **テキストベース**: Gitでバージョン管理できる
- ✅ **可読性**: コードレビューしやすい
- ✅ **自動生成**: ER図を自動生成できる
- ✅ **保守性**: 変更履歴を追跡しやすい
- ✅ **ツール連携**: 複数のツールで表示・編集可能

---

## ツールの選択

ER.dbmlを記述・表示するための主なツールを紹介します。

### 1. dbdiagram.io（推奨）

**オンラインエディター**。最も手軽に始められます。

- **URL**: https://dbdiagram.io/
- **特徴**:
  - ブラウザで動作（インストール不要）
  - リアルタイムプレビュー
  - PDF/PNG/SVGでのエクスポート
  - 無料プランあり
- **使い方**:
  1. サイトにアクセス
  2. `.dbml`ファイルをアップロード、または直接記述
  3. ER図が自動生成される
  4. 必要に応じてエクスポート

### 2. VSCode拡張機能

**エディター内で編集・プレビュー**。

- **拡張機能**: `DBML - Database Markup Language`
- **特徴**:
  - シンタックスハイライト
  - プレビュー表示
  - ローカルで完結
- **インストール方法**:
  1. VSCodeを開く
  2. 拡張機能タブを開く（`Ctrl+Shift+X`）
  3. "DBML"で検索
  4. "DBML - Database Markup Language"をインストール

### 3. dbml-cli（コマンドラインツール）

**コマンドラインから画像生成**。

- **インストール**:
  ```bash
  npm install -g @dbml/cli
  ```
- **使い方**:
  ```bash
  # PNG画像を生成
  dbml2img schema.dbml -o schema.png
  
  # PDFを生成
  dbml2img schema.dbml -o schema.pdf
  ```

### 4. ERBuilder（デスクトップアプリ）

**デスクトップアプリケーション**。

- **特徴**:
  - より高度な機能
  - オフライン動作
- **用途**: より大規模なプロジェクト向け

---

## 基本的な書き方

### テーブル定義

```dbml
Table users {
  id integer [primary key, increment]
  name varchar [not null]
  email varchar [unique, not null]
  created_at timestamp
  updated_at timestamp
}
```

### リレーション定義

```dbml
// 1対多のリレーション
Table users {
  id integer [primary key]
  name varchar
}

Table posts {
  id integer [primary key]
  user_id integer [ref: > users.id]
  title varchar
  content text
}

// 多対多のリレーション
Table posts {
  id integer [primary key]
  title varchar
}

Table tags {
  id integer [primary key]
  name varchar
}

Table post_tags {
  id integer [primary key]
  post_id integer [ref: > posts.id]
  tag_id integer [ref: > tags.id]
}
```

### 主な構文

| 構文 | 説明 | 例 |
|------|------|-----|
| `Table table_name` | テーブル定義 | `Table users` |
| `[primary key]` | 主キー | `id integer [primary key]` |
| `[increment]` | 自動インクリメント | `id integer [primary key, increment]` |
| `[not null]` | NOT NULL制約 | `name varchar [not null]` |
| `[unique]` | UNIQUE制約 | `email varchar [unique]` |
| `[ref: > table.column]` | 外部キー（1対多） | `user_id integer [ref: > users.id]` |
| `[ref: - table.column]` | 外部キー（1対1） | `profile_id integer [ref: - profiles.id]` |
| `[ref: < table.column]` | 外部キー（多対多） | （中間テーブルで使用） |
| `Note` | テーブル/カラムへの注釈 | `Note: 'ユーザー情報を保存'` |

### 詳細な制約

```dbml
Table users {
  id integer [primary key, increment]
  email varchar [unique, not null]
  age integer [note: '年齢', default: 0]
  status varchar [note: 'active/inactive']
}

Table orders {
  id integer [primary key]
  user_id integer [ref: > users.id, note: '注文者']
  total_amount decimal(10,2) [not null]
  created_at timestamp [default: `now()`]
}

// インデックス
Table users {
  id integer [primary key]
  email varchar [unique]
  created_at timestamp
}

Table indexes {
  (email) [name: 'idx_email']
}
```

---

## 実践例

### 「おぼえてて」プロジェクトの例

```dbml
// ユーザーテーブル
Table users {
  id bigint [primary key, increment]
  email varchar(255) [unique, not null]
  encrypted_password varchar(255) [not null]
  name varchar(100) [not null]
  nickname varchar(50)
  avatar_url varchar(500)
  provider varchar(50) [note: 'email/line等']
  provider_uid varchar(255) [note: '外部認証のID']
  created_at timestamp [not null]
  updated_at timestamp [not null]
  
  Note: 'ユーザー情報（Devise）'
}

// 家族テーブル
Table families {
  id bigint [primary key, increment]
  name varchar(100) [not null]
  invite_code varchar(20) [unique, note: '招待コード']
  created_at timestamp [not null]
  updated_at timestamp [not null]
  
  Note: '家族グループ'
}

// 家族メンバー（中間テーブル）
Table family_members {
  id bigint [primary key, increment]
  user_id bigint [ref: > users.id, not null]
  family_id bigint [ref: > families.id, not null]
  role varchar(20) [default: 'member', note: 'owner/member']
  created_at timestamp [not null]
  updated_at timestamp [not null]
  
  indexes {
    (user_id, family_id) [unique, name: 'idx_user_family']
  }
  
  Note: 'ユーザーと家族の関連'
}

// 子供情報
Table children {
  id bigint [primary key, increment]
  family_id bigint [ref: > families.id, not null]
  name varchar(100) [not null]
  birth_date date
  gender varchar(10) [note: 'boy/girl/other']
  avatar_url varchar(500)
  created_at timestamp [not null]
  updated_at timestamp [not null]
  
  Note: '子供の基本情報'
}

// 思い出
Table memories {
  id bigint [primary key, increment]
  family_id bigint [ref: > families.id, not null]
  child_id bigint [ref: > children.id]
  user_id bigint [ref: > users.id, not null, note: '作成者']
  title varchar(200) [not null]
  content text
  memory_date date [not null, note: '思い出の日付']
  location varchar(100)
  created_at timestamp [not null]
  updated_at timestamp [not null]
  
  indexes {
    (family_id, memory_date)
    (child_id)
  }
  
  Note: '思い出の記録'
}

// 名言・エピソード
Table quotes {
  id bigint [primary key, increment]
  family_id bigint [ref: > families.id, not null]
  child_id bigint [ref: > children.id, not null]
  user_id bigint [ref: > users.id, not null, note: '記録者']
  quote_text text [not null, note: '子供の発言']
  context text [note: '状況・エピソード']
  quote_date date [not null]
  created_at timestamp [not null]
  updated_at timestamp [not null]
  
  indexes {
    (family_id, quote_date)
    (child_id)
  }
  
  Note: '子供の名言・エピソード'
}

// 写真・動画（メディア）
Table media {
  id bigint [primary key, increment]
  memory_id bigint [ref: > memories.id, note: '思い出に紐づく場合']
  quote_id bigint [ref: > quotes.id, note: '名言に紐づく場合']
  file_url varchar(500) [not null, note: 'Firebase Storage URL']
  file_type varchar(20) [not null, note: 'image/video']
  file_size bigint [note: 'ファイルサイズ（bytes）']
  width integer [note: '画像幅（pixel）']
  height integer [note: '画像高さ（pixel）']
  created_at timestamp [not null]
  updated_at timestamp [not null]
  
  indexes {
    (memory_id)
    (quote_id)
  }
  
  Note: '写真・動画のメタデータ'
}
```

---

## DB設計書との連携

### 1. `.dbml`ファイルを作成

プロジェクトルートまたは`Docs/`ディレクトリに`.dbml`ファイルを作成します。

```
remember_me/
├── Docs/
│   ├── DB設計書.md
│   └── schema.dbml          # ← ER.dbmlファイル
└── ...
```

### 2. DB設計書にER図を埋め込む

`DB設計書.md`に、ER.dbmlのコードブロックを追加します。

```markdown
## ER図

### ER.dbml

```dbml
// ER.dbmlの内容をここに記載
```

### 生成されたER図

<!-- dbdiagram.ioで生成した画像を貼り付けるか、リンクを記載 -->
![ER図](./images/er_diagram.png)
```

### 3. 定期的に更新

- マイグレーション作成時に`.dbml`ファイルも更新
- PRレビュー時にER図を確認
- DB設計書と`.dbml`ファイルの整合性を保つ

---

## 参考リンク

- **dbdiagram.io**: https://dbdiagram.io/
- **DBML公式ドキュメント**: https://www.dbml.org/
- **VSCode拡張機能**: DBML - Database Markup Language

---

## 次のステップ

1. `Docs/schema.dbml`ファイルを作成
2. 現在のDB設計に基づいてER.dbmlを記述
3. dbdiagram.ioでプレビュー確認
4. DB設計書にER図を追加


# schema.dbml の解説 - family_membersテーブルのカラム（62-63行目）

## 📋 対象箇所

```dbml
Table family_members {
  family_members_id uuid [pk, default: `uuid_generate_v7()`, note: 'アカウントのUUID(v7)はサーバー側で生成']
  user_id uuid [ref: > users.id, not null]           // 62行目
  families_id uuid [ref: > families.id, not null]    // 63行目
  role varchar(20) [default: 'member', note: 'owner/member/guest']
  ...
}
```

---

## 🎯 この2つのカラムの意味

### `user_id` (62行目)
- **意味**: 「どのユーザーが」家族メンバーになるかを示すID
- **型**: `uuid`（一意の識別子）
- **制約**: `not null`（必須） + `ref: > users.id`（外部キー）

### `families_id` (63行目)
- **意味**: 「どの家族グループに」所属するかを示すID
- **型**: `uuid`（一意の識別子）
- **制約**: `not null`（必須） + `ref: > families.id`（外部キー）

---

## 🤔 なぜ2つのカラムが必要なのか？

### 多対多の関係を実現するため

**例:**
- ユーザーAさんは「家族グループ1」と「家族グループ2」の両方に所属できる
- 家族グループ1には「ユーザーA」「ユーザーB」「ユーザーC」の3人が所属できる

```
┌─────────┐         ┌──────────────┐         ┌──────────┐
│  user   │         │family_members│         │ families │
├─────────┤         ├──────────────┤         ├──────────┤
│user_id  │──┐      │ user_id      │      ┌──│families_id
│name: A  │  │      │ families_id  │      │  │name: 家族1
└─────────┘  │      │ role         │      │  └──────────┘
             │      └──────────────┘      │
┌─────────┐  │             ▲              │  ┌──────────┐
│  user   │  │             │              │  │ families │
├─────────┤  │             │              │  ├──────────┤
│user_id  │──┘             │              └──│families_id
│name: B  │                │                 │name: 家族2
└─────────┘                │                 └──────────┘
                            │
                     中間テーブル（多対多を実現）
```

### 中間テーブル（交差テーブル）とは？

**多対多の関係**をデータベースで表現するために使用するテーブルです。

#### もし中間テーブルがなかったら？

❌ **できないこと:**
```sql
-- ❌ ユーザーテーブルに「家族ID」を直接入れると...
user_id | name | family_id
--------|------|----------
1       | A    | 1
1       | A    | 2        ← 同じユーザーが複数の家族に所属できない！

-- または
family_id | name   | user_id
----------|--------|----------
1         | 家族1  | 1
1         | 家族1  | 2
1         | 家族1  | 3        ← 複数のユーザーIDを格納できない！
```

#### 中間テーブルを使うと？

✅ **できること:**
```sql
-- family_membersテーブル
family_members_id | user_id | families_id | role
------------------|---------|------------|------
1                 | 1       | 1          | owner
2                 | 1       | 2          | member  ← 同じユーザーが複数の家族に所属！
3                 | 2       | 1          | member
4                 | 3       | 1          | member  ← 複数のユーザーが同じ家族に所属！
```

---

## 📚 具体例で理解する

### シナリオ: 田中家の家族共有アプリ

#### 1. ユーザー登録
```
userテーブル:
user_id  | name
---------|--------
user-001 | 田中太郎（お父さん）
user-002 | 田中花子（お母さん）
user-003 | 田中一郎（息子）
```

#### 2. 家族グループ作成
```
familiesテーブル:
families_id | name
------------|----------
family-001  | 田中家
family-002  | 花子の実家（おばあちゃんの家）
```

#### 3. 家族メンバーとして登録
```
family_membersテーブル:
family_members_id | user_id   | families_id | role
------------------|-----------|-------------|------
1                 | user-001  | family-001  | owner    ← 太郎が「田中家」のオーナー
2                 | user-002  | family-001  | member   ← 花子が「田中家」のメンバー
3                 | user-003  | family-001  | member   ← 一郎が「田中家」のメンバー
4                 | user-002  | family-002  | member   ← 花子が「実家」のメンバー（複数家族所属！）
```

### データの意味

| レコード | 意味 |
|---------|------|
| 1 | 田中太郎（user-001）は田中家（family-001）のオーナー |
| 2 | 田中花子（user-002）は田中家（family-001）のメンバー |
| 3 | 田中一郎（user-003）は田中家（family-001）のメンバー |
| 4 | 田中花子（user-002）は実家（family-002）のメンバー |

---

## 🔍 なぜ`user_id`と`families_id`の両方が必要？

### 例: ユーザー太郎の情報を取得する

```sql
-- 太郎（user-001）が所属している家族を取得
SELECT f.*
FROM families f
INNER JOIN family_members fm ON f.families_id = fm.families_id
WHERE fm.user_id = 'user-001';

-- 結果: 太郎は「田中家（family-001）」に所属している
```

### 例: 家族「田中家」のメンバーを取得する

```sql
-- 田中家（family-001）に所属しているユーザーを取得
SELECT u.*
FROM user u
INNER JOIN family_members fm ON u.user_id = fm.user_id
WHERE fm.families_id = 'family-001';

-- 結果: 太郎、花子、一郎が「田中家」のメンバー
```

**両方のカラムがあるから、どちらの方向からも検索できる！**

---

## ⚠️ 注意: スキーマの不一致

現在のスキーマには以下の不一致があります：

### 問題点
- 62行目: `ref: > users.id` となっているが、実際のテーブル名は`user`で、主キーは`user_id`
- 63行目: `ref: > families.id` となっているが、実際の主キーは`families_id`

### 正しい記述
```dbml
user_id uuid [ref: > user.user_id, not null]              // 修正後
families_id uuid [ref: > families.families_id, not null]   // 修正後
```

---

## 📊 まとめ

### `user_id`と`families_id`の役割

| カラム | 意味 | 用途 |
|--------|------|------|
| `user_id` | 「どのユーザーが」メンバーか | ユーザーから所属家族を検索 |
| `families_id` | 「どの家族グループに」所属か | 家族からメンバー一覧を検索 |

### 重要なポイント

1. **多対多の関係**: 1人のユーザーが複数の家族に所属でき、1つの家族に複数のユーザーが所属できる
2. **中間テーブル**: `family_members`テーブルが多対多を実現
3. **両方のカラム**: どちらか一方では不十分。両方必要
4. **外部キー**: データの整合性を保証（存在しないユーザーや家族を参照できない）

---

## 🔗 関連ドキュメント

- [ER.dbmlの使い方](./ER.dbmlの使い方.md)
- [DB設計書](./DB設計書.md)


