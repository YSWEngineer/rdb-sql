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
    - SHOW TABLES(`SHOW TABLES;` テーブルの一覧を確認することができる)</details>


<details><summary>#05 レコードを挿入しよう</summary>

前回のコマンドは、実は再度実行しようとするとエラーになります。

```sql
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1050 (42S01) at line 1: Table 'posts' already exists
~ $
# postsは既に存在しているので、新しく作れない
```

そこで今回は、実行する度にまっさらな状態から始められるように、いったんここでテーブルを削除してあげましょう。

単にテーブルを削除したいなら `DROP TABLE テーブル名`でいいのですが、 posts が存在していなかったらエラーになってしまうので、こちらで　`IF EXISTS` としてあげます。そうすると、postsが存在する時だけ削除するという意味になります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

DESC posts;
SHOW TABLES;

# コードを実行
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1050 (42S01) at line 1: Table 'posts' already exists
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+--------------+------+-----+---------+-------+
| Field   | Type         | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| message | varchar(140) | YES  |     | NULL    |       |
| likes   | int(11)      | YES  |     | NULL    |       |
+---------+--------------+------+-----+---------+-------+
+-----------------+
| Tables_in_myapp |
+-----------------+
| posts           |
+-----------------+
~ $
```

レコードを挿入する。INSERT INTO テーブル名として、データを挿入したいカラムの名前をカンマ区切りで渡してあげて、VALUESの後に渡したい値そのものをカンマ区切りで渡す。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

DESC posts;
SHOW TABLES;

INSERT INTO posts (message, likes) VALUES ('Thanks', 12);
INSERT INTO posts (message, likes) VALUES ('Arigato', 4);

# なお、文字列はシングルクォーテーションか、ダブルクォーテーションで囲うので覚えておくこと
```

レコードはまとめて挿入することもできて、VALUESのあとにカンマ区切りで書いてあげればOKです。

```sql
# 上も下も同じ意味
INSERT INTO posts (message, likes) VALUES ('Thanks', 12);
INSERT INTO posts (message, likes) VALUES ('Arigato', 4);

INSERT INTO posts (message, likes) VALUES ('Thanks', 12),('Arigato', 4);
```

挿入したレコードを確認するにはSELECT * FROM テーブル名とする。

```sql
SELECT * FROM posts; #=> postsテーブルからすべてのレコードを抽出せよという意味になる
```

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

DESC posts;
SHOW TABLES;

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4);
  
SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+--------------+------+-----+---------+-------+
| Field   | Type         | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| message | varchar(140) | YES  |     | NULL    |       |
| likes   | int(11)      | YES  |     | NULL    |       |
+---------+--------------+------+-----+---------+-------+
+-----------------+
| Tables_in_myapp |
+-----------------+
| posts           |
+-----------------+
+---------+-------+
| message | likes |
+---------+-------+
| Thanks  |    12 |
| Arigato |     4 |
+---------+-------+
~ $
# 二つのレコードが挿入されているのが確認できる
```

### 質問：postsテーブルを削除しても、messageやlikesカラムが残っているのは何故ですか？
    
回答：DROPで完全に削除した後、CREATEでもう一度postsテーブルを作成しています。
    
`DROP` 文ではテーブルは完全に削除されています。ただ、レッスンのクエリは以下の構成になっています。

```sql
DROP TABLE IF EXISTS posts;    # postsテーブルがあれば削除
CREATE TABLE posts (           # もう一度postsテーブルを作成
  message VARCHAR(140),
  likes INT
);

DESC posts;   # postsテーブルの定義を表示
SHOW TABLES;  # 存在するテーブル名を表示

INSERT INTO posts (message, likes) VALUES # postsテーブルにデータを挿入
  ('Thanks', 12),
  ('Arigato', 4);

SELECT * FROM posts;  # postsテーブルの中身を表示

```

一度消してからもう一度作るということをしていますので `posts` テーブルの中身が表示されているというわけです。
### 要点まとめ
- テーブルの削除およびレコードの挿入方法についてみていきます。
    - DROP TABLE：テーブルを削除する
    - INSERT INTO：レコードを挿入する
    - SELECT：レコードを確認する</details>


<details><summary>#06 エラーメッセージを読み解こう</summary>

エラーメッセージの見方について。9行目をセミコロンではなく、カンマにしてしまった場合。MySQLはエラーメッセージが見づらいのですが、見るべきは最後の箇所です。near ‘SELECT * FROM posts’でエラーが出ていると表示されています。このヒントをもとにSELECTの周りを見てあげて、適宜修正するようにしましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1064 (42000) at line 7: You have an error in your SQL syntax
; check the manual that corresponds to your MariaDB server version
 for the right syntax to use near 'SELECT * FROM posts' at line 5
~ $
```

コメントについて。3つの方法があり、

```sql
① -- comment
② # comment #=> 行末までコメントを書くことができる
③ /*
	comment
	comment
	*/ #=> この場合は何行でもコメントを書いていくことができる

-- comment
# comment
/*
comment
comment
*/

DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4);

SELECT * FROM posts;
```

コメントはメモ書きに使えますし、実行時に無視されるので、一時的に命令を無効にしたい場合にもよく使われます。

一部をコメントにして実行します。

```sql
-- comment
# comment
/*
comment
comment
*/

DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

INSERT INTO posts (message, likes) VALUES
  -- ('Thanks', 12),
  ('Arigato', 4);

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+
| message | likes |
+---------+-------+
| Thanks  |    12 |
| Arigato |     4 |
+---------+-------+
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+
| message | likes |
+---------+-------+
| Arigato |     4 |
+---------+-------+
# コメントされた箇所が無効化されて、レコードが1行になっている
```

コメントのショートカットキー。

```sql
# macOSの場合、コメントにしたい行にカーソルを合わせて
command + /
# VS Codeと同じ
```

</details>


<details><summary>#07 データ型を見ていこう</summary>

My SQLで扱えるデータ型はたくさんあり、よく使うのはこの辺りです。

```sql
整数　　TINYINT INT BIGINT # (タイニーイント、イント、ビッグイント)
実数　　DECIMAL FLOAT DOUBLE # (デシマル、フロート、ダブル)
文字列　CHAR VARCHAR TEXT ENUM SET # (キャラ、バーキャラ、テキスト、エーナム、セット)
真偽値　BOOL
日時　　DATE TIME DATETIME
```

```sql
整数　　TINYINT　-128 ~ +127
　　　　INT　　　 -21億 ~ +21億
　　　　BIGINT   -922京 ~ +922京

# マイナスの値を扱う必要がなければ、データ型にUNSIGNEDキーワードを付けることでさらに扱える数の幅を広げることができる
　　　　TINYINT UNSIGNED  0 ~ 255
　　　　INT UNSIGNED      0 ~ 42億
　　　　BIGINT UNSIGNED   0 ~ 1844京
```

扱える数の幅が違うので、状況によって使い分けていけばいいのですが、通常はINTを使っておけばOKでしょう。

```sql
# 通常はDECIMALを使えば良い
実数　　DECIMAL　　　固定小数点
　　　　FLOAT　　　　浮動小数店
　　　　DOUBLE　　　 浮動小数店(高精度)
```

小数点を含む実数の表現は、浮動小数店は細かいところで誤差が出てくる数値なので、通常は**DECIMAL**を使ってあげればOKかと思います。

```sql
文字列　　CHAR  0 ~ 255文字
　　　　　VARCHAR  0 ~ 65535文字
　　　　　TEXT  それ以上
　　　　　ENUM  特定の文字列から1つ
　　　　　SET   特定の文字列から複数
```

商品コードなど固定長のデータには**CHAR**、文字数がバラバラになるようなデータの場合は**VARCHAR**、それよりが長くなる文字列には**TEXT**を使ってあげればOKです。

特定の文字列に限定して、そのうちの一つだけの値を格納したいなら**ENUM**(エーナム)、特定の文字列のうちの複数の値なら**SET**を使います。

```sql
真偽値　　BOOL　　TRUE　/　FALSE
　　　　　TINYINT(1)  1 / 0
```

真偽値は、TRUEかFLASEのどちらかを保持することができますが、内部的にTRUEが1、FLASEが0として管理されているので、実は1桁のTINYINTと同じ型です。0か1か、真か偽かといった二択の値を保持するときに使ってあげましょう。

```sql
日時　　DATE　　　　　日付
　　　　TIME　　　　　時間
　　　　DATETIME　　日時
```

日時に関しては、日付だったらDATE、時間だけならTIME、両方扱いたいならDATETIMEを使ってあげればOKです。

MySQLが扱えるデータ型は他にもありますが、この辺りを知っておけばよいでしょう。

### 質問：VARCHARやTINYINTなどで文字や数値を制限するのは何故ですか？
    
回答：昔からの名残が大きいと推測しますが、現在でも情報量制限をするメリットを挙げてみます。

かつては、あえて無駄な情報量は制限した方がデータベースの速度やストレージの使用効率がよいと言われていたこともあったのですが、最近では顕著な違いはないとも言われています。

SQL言語自体は昔からある言語なのでその名残ともいえます。

それでもあえて、現在において情報量制限をするメリットを挙げるとしたら以下の項目が挙げられます。

- **データベースにアクセスする言語が可変長文字列や大きなデータ型に対応していない場合**
- **顧客番号や印刷の都合であえて文字数を制限し、それ以上をエラーとして検出したい場合**

データベースにアクセスする言語が可変長文字列や大きなデータ型に対応していない場合は、たとえば組み込みなどで古いＣ言語などを使う場合が考えられます。

印刷の都合であえて文字数制限とは、帳票などを印刷する場合に文字数を整えるのに、例えば名前は6文字以内、などとデータベースのレベルで取り決めておくということです。そうすることでクライアントでバリデーションを行わなくてもルールを順守させることができます。

と、こんな感じで挙げましたが稀なケースとも言えるので現在においては基本的に大きめの情報量でデータベース設計しておいても大丈夫なことがほとんどですね。
    
### 質問：140文字までとするのでVARCHAR(140)とコードを書きましたがCHARと書かなかったのは何故でしょうか？
    
回答：固定長のデータには CHAR 、 可変長になるようなら VARCHAR、と使い分けていただければよいかと思います。

基本的にはレッスン動画でご説明している通り、商品コードなど固定長のデータには CHAR 、 文字数がバラバラになるようなデータの場合は VARCHAR というように使い分けていただければよいかと思います。

140 文字までということは、最大 140 文字の可変長と言うことですので、VARCHAR で違和感はないかと思いますがいかがでしょうか。
