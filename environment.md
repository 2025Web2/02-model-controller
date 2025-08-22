---
sort: 1
---
# Laravelでのデータベース環境構築

本章の前半部ではデータベースとのやり取りを担当する**モデル**について学びますが、その前にデータベース環境を構築する必要があります。
Laravelでは、

- データベースの接続情報を記述するための **.envファイル**
- データベースのテーブル構造を定義するための**マイグレーション**
- データベースに初期データを挿入するための**シーダー**
  
を使ってデータベースの準備を行います。

## .env ファイルの編集

Laravelでは、データベースの接続情報を`.env`ファイルに記述します。
前期のPHPでは、`PDO`クラスを使ってデータベースに接続する際、接続情報を直接記述していましたね。

Laravelでは`.env`ファイルに記述することで、より安全にデータベースに接続することができます。
ここを正しく設定しない限り、この後出てくるテーブルを作成するためのマイグレーション、シーダーの実行ができません。

では、今回のDocker環境に合わせて`.env`ファイルを編集しましょう。

```bash
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:UwGfTSBkd2fawCCiK1eBmOLhQKNF5Ll7Bk1QcKtwhSI=
APP_DEBUG=true
APP_TIMEZONE=UTC
APP_URL=http://localhost

# --- 途中省略 ---

LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

# --- 以下のように編集 ---
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=SAMPLE
DB_USERNAME=sampleuser
DB_PASSWORD=samplepass
# --- ここまで ---

# --- 以下省略 ---
```

**【解説】**

`DB_CONNECTION=mysql`:<br>
データベースの接続方法を指定します。
ここでは、MySQLを使用するため、`mysql`と記述します。

`DB_HOST=db`:<br>
データベースのホスト名を指定します。
ここでは、Dockerコンテナのサービス名を指定します。

`DB_PORT=3306`:<br>
データベースのポート番号を指定します。
MySQLのデフォルトポート番号は`3306`です。

`DB_DATABASE=SAMPLE`、`DB_USERNAME=sampleuser`、`DB_PASSWORD=samplepass`:<br>
データベース名、データベースに接続するためのユーザー名、パスワードを指定します。
これらの情報は、Docker環境の`docker-compose.yml`ファイルで設定した`env.txt`ファイルの内容に合わせて設定します。

データベースの接続情報を`.env`ファイルに記述したら、次にマイグレーションを実行し、テーブル定義を行います。
ただし、マイグレーションを実行する前に、定義する商品テーブル`items`の構造を確認しておきましょう。

## 商品テーブル「items」

前期同様、ミニショップの商品を管理する商品テーブル`items`は以下のような構造になっています。

| カラム名 | データ型 | 制約 | 備考 |
| - | - | - | - |
|ident|int型|主キー、not null制約|商品番号|
|name|varchar型|最大文字数50、not null制約|商品名|
|maker|varchar型|最大文字数50、not null制約|メーカー・著者・アーティスト|
|price|int型||価格|
|image|varchar型|最大文字数20|画像名|
|genre|varchar型|最大文字数10|ジャンル|