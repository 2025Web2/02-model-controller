---
sort: 2
---
# マイグレーション

マイグレーションとは、Laravelにおいてデータベースのテーブル構造を定義するための仕組みです。
マイグレーションを使うことで、データベースのテーブル構造をPHPのコードで定義し、データベースに反映させることができます。

では、マイグレーションを使って、データベースに`items`テーブルを作成しましょう。
マイグレーションを使うには、まずマイグレーションファイルを作成し、そのファイルを編集してテーブルの構造を定義します。

以下の手順でまずはマイグレーションファイルを作成しましょう。

1. VSCode上で、`Ctrl+Shift+P`(Macの場合は`Cmd+Shift+P`)を押し、コンテナを起動する(既に起動しているなら不要)
2. VSCode上で、`Ctrl+J`(Macの場合は`Cmd+J`)を押し、ターミナルを表示する
3. 以下のコマンドを実行して、`items`テーブル用のマイグレーションファイルを作成する

```bash
php artisan make:migration create_items_table
```

4. `database/migrations/20xx_xx_xx_xxxxxx_create_items_table.php` が作成されていることを確認する
5. `up`メソッドを以下のように修正する

    ```php
    <?php

    use Illuminate\Database\Migrations\Migration;
    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    return new class extends Migration
    {
        /**
         * Run the migrations.
         */
        public function up(): void
        {
            Schema::create('items', function (Blueprint $table) {
                // デフォルトの記述はコメントアウト
                // $table->id();
                // $table->timestamps();

                // --- 以下を追加 ---
                $table->integer('ident')->primary();
                $table->string('name', 50);
                $table->string('maker', 50);
                $table->integer('price');
                $table->string('image', 20);
                $table->string('genre', 10);
                // --- ここまで ---
            });
        }
        /**
         * Reverse the migrations.
         */
        public function down(): void
        {
            Schema::dropIfExists('items');
        }
    };
    ```

    **【解説】**

    `use...`: <br>
    `use`とは、他のクラスを読み込むためのキーワードです。
    PHPでいうところの`require_once`のようなものです。

    `public function up(): void {Schema::create('items', function (Blueprint $table) {`: <br>
    マイグレーションファイル作成時にデフォルトで記述されているコードです。
    ここでは、「`up`メソッド内にテーブル定義を記述するんだ」の理解で問題ございません。

    `$table->integer`: <br>
    整数型のカラムを定義します。
    第1引数にカラム名を指定します。

    `$table->string`: <br>
    文字列型のカラムを定義します。
    第1引数にカラム名、第2引数に文字列の長さを指定します。

    `$table->primary`: <br>
    主キーを定義します。`$table->integer('ident')->primary();`と記述することで、`ident`カラムを主キーに設定しています。

    `public function down(): void {Schema::dropIfExists('items');}`: <br>
    マイグレーションをロールバックする際に実行される処理を記述します。
    ロールバックとは、実行したマイグレーションを取り消すことを指します。
    `php artisan migrate:rollback`コマンドを実行することで、`down`メソッド内の処理が実行されます。
    ここでは、`items`テーブルを削除する処理を記述しています。

6. 以下のコマンドを実行して、マイグレーションを実行する

    ```bash
    php artisan migrate
    ```

これで、`items`テーブルが作成されました。
引き続き、`items`テーブルにデータを挿入するためのシーダーを作成します。

**【補足】**<br>
Laravelでテーブル定義をする際、以下のカラムがデフォルトで定義されています。
- `id`(主キー、自動採番付き(auto increment))
- `timestamps`(作成日時と更新日時を管理するためのカラム)

Laravelの前提としては、カラムとしてこれらを定義することを前提として構造化されているので、`id`、`timestamps`を削除する場合は、他のコード(モデルなど)も修正する必要がでてきます。

本授業では、
- 前期の「ミニショップ」の仕様を再現
- Laravelの前提ルールに則らない場合でもWebアプリを作成できる
ことを目的に、`id`、`timestamps`は使用しない前提で進めていきます。