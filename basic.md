# 💻ドットインストール MySQL入門 基礎編

<details><summary>#01 MySQLと使ってみよう</summary>

MySQLは広く使われているデータベース管理システムで、公式サイトは[mysql.com](http://mysql.com)です。また、 MySQL にはそこから派生した MariaDB (マリアディービー)というプロジェクトもあって、このレッスンでも MariaDB を使っていくのですが、大体同じものだと思っておけば OK です。

MySQLでは、表を作ってデータを格納する方法や、文字列で検索する方法、もしくはデータを並び替えるといった方法について詳しく見ていきます。

MySQLができることは多岐に渡るので、基礎編、応用編の二つのパートに分けて進めていきます。

基礎編では、データベースの基本操作について学んでいきます。
応用編では、データの集計や分析など、より高度なトピックを扱っていきましょう。
### 質問：MySQL のマニュアルにある [ ], ( ), { }, | のような記号の意味を教えてください。
    
回答：それぞれ詳しく解説いたします**。**
    
`[]`で囲まれている部分はオプショナルな記述です。書かなくてもよくて必要な場合に記述してくださいという部分です。`|`で連結されたもの例えば`LOW_PRIORITY | DELAYED | HIGH_PRIORITY` は `LOW_PRIORITY` か`DELAYED` か `HIGH_PRIORITY` のどれかひとつが選択できますよという意味です。`{}` は、例えば `{VALUES | VALUE}` の場合は `VALUES` か `VALUE` が必須ですという意味です。`()` は特別な意味はなく文字列として `()` を入力してください。
    
ですので `INSERT` 構文は最小構成としてこのようになります。
    
```r
INSERT tbl_name VALUES ({expr | DEFAULT},...),(...),...
```
    
`expr` のような初めてみる文字列は構文の下に説明が続いていますので意味がわかるかなと思います。初めのうちは最小構成だけ覚えて必要になったら `[]` の部分を調べてみるというのが良いかなと思います。</details>


<details><summary>#02 ブラウザ実行環境を使ってみよう</summary>

命令は基本的に上から実行されていきます。</details>


<details><summary>#03 データベース用語を確認しよう</summary>

MySQLでは表形式でデータベースを管理していきます。

データベースでは一つ一つの表のことをテーブルと呼びます。

テーブルに格納されている一行一行のデータのことをレコード、列をカラムと呼ぶので用語として覚えておいてください。

テーブルを作っていくのに、いくつかステップが必要です。

- CREATE TABLEでテーブルを作成、テーブルの名前をpostsとする。
- 命令の区切りはセミコロンになるので、忘れないように。
- SQLはStructured Query Languageと呼ばれ、命令はデータベースに問い合わせをするという意味で、クエリと呼ばれます。
- それからクエリですが、慣習的に SQL があらかじめ用意している命令は大文字、そして自分で付けるテーブル名やカラム名などは小文字にすることが多いので、その点も意識しておくといいでしょう。
### 質問：MySQL と SQL の違いはなんですか？
回答：MySQL はデータベースシステム、SQL はデータベース言語です。
### 要点まとめ
- TABLE(データベースにおいて一つ一つの表のことをTABLE(テーブル)と呼ぶ)
- RECORD(テーブルに格納されている一行一行のデータのこと)
- COLUMN((カラム)テーブルに格納されている列のデータのこと)
- SQL</details>


<details><summary>#04 テーブルを作ってみよう</summary>

※オンラインターミナルが使用できないため、実際にターミナルからMySQLにログインしてプログラムを入力。

1.データベースを作成

```sql
CREATE DARABASE dotinstall;　
```

これでdotinstallという名前のデータベースができるので、次に

2.データベースの選択

```sql
use dotinstall;
```

として利用するデータベースを選択したのち、レッスンにあったCREATE TABLEを実行する。

オンラインターミナルが使用できるようになったので、ここからはレッスン通りに進める。

テーブルを作るには、`CREATE TABLE` としてあげてテーブル名を書けばいい。

```sql
CREATE TABLE テーブル名
```

テーブル名は複数のレコードを管理するので、複数形にすることが多い点に注意しておきましょう。

```sql
CREATE TABLE posts ();
```

丸括弧の中に、定義したいカラム(テーブルに格納されている列のデータのこと)を書くのですが、今回はmessageとlikesとします。

```sql
CREATE TABLE posts (message, likes);
```

カラムにどのような値を入れるのかを指定する必要があるため、messageは140文字までの文字列、likesは整数という意味でintegerのINT(イント)にする。

```sql
CREATE TABLE posts (message VARCHAR(140), likes INT);
```

SQLでは、見やすいように適宜改行や字下げを入れても構いません。

```sql
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);
```

テーブルができたか確認したいのですが、 `DESC テーブル名;` とすると、テーブルの構造を確認することができて、 `SHOW TABLES;` とするとテーブルの一覧を確認することができます。

```sql
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

DESC posts; # DESC テーブル名; テーブルの構造を確認することができる

SHOW TABLES; # SHOW TABLES; テーブルの一覧を確認することができる
```

```sql
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+--------------+------+-----+---------+-------+
| Field   | Type         | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| message | varchar(140) | YES  |     | NULL    |       |
| likes   | int(11)      | YES  |     | NULL    |       |
+---------+--------------+------+-----+---------+-------+
# テーブルの構造
+-----------------+
| Tables_in_myapp |
+-----------------+
| posts           |
+-----------------+
# テーブルの一覧

# messageとlikesが定義され、テーブルの一覧ではpostsテーブルがあるのが分かる
```

### 質問：SQLなどで大文字を連続して打つ必要があるときについて
    
回答：ShiftでもCAPS LOCKでもどちらでもかまいません。
    
### 質問：オンラインターミナルで実行しているコードの意味を教えて下さい
回答：

`~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql` は、下記のような意味となります。

- mysql: mysqlを操作するコマンド
- h db: mysqlサーバのサーバ名
- t : 表形式での表示
- u dbuser: ユーザ名
- pdbpass: パスワード
- myapp: データベース名
- < main.sql: main.sqlの中身をmysqlコマンドに入力

さらなる詳しい説明は、下記の公式サイトを参照してください。[https://dev.mysql.com/doc/refman/5.6/ja/mysql-command-options.html#option_mysql_host](https://dev.mysql.com/doc/refman/5.6/ja/mysql-command-options.html#option_mysql_host)
### 要点まとめ
- CREATE TABLEでテーブルを作ったあとに、テーブルの構造を確認していきます。
    - CREATE TABLE(テーブルを作成)
    - DESC(`DESC テーブル名;` で、テーブルの構造を確認することができる)
    - SHOW TABLES(`SHOW TABLES;` テーブルの一覧を確認することができる)
