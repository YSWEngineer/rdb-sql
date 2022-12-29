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

140 文字までということは、最大 140 文字の可変長と言うことですので、VARCHAR で違和感はないかと思いますがいかがでしょうか。</details>


<details><summary>#08 数値、文字列を扱ってみよう</summary>
    
```sql
# 数値は、整数を扱っているのでINT
likes INT

```

```sql
# いいね数はマイナスの値を想定していないので、UNSIGNEDを使うと扱える数値の範囲が広くなる
likes INT UNSIGNED
```

```sql
# 投稿した時の気分を小数点を含む10点満点で管理できるようにします。
likes INT UNSIGNED
mood DECIMAL
```

```sql
# DECIMAL の場合、丸括弧に続けて全体で 4 桁だよ、そのうち小数点以下は 2 桁だよ、と指定してあげてください。
mood DECIMAL(4, 2)
```

```sql
# こちらもマイナス値を想定していないので、 UNSIGNED にしてあげれば扱える数の幅が広くなります。
mood DECIMAL(4, 2) UNSIGNED
```

```sql
# 投稿のような文字数があまり定まっていないようなものは VARCHAR を使ってあげれば OK です。
# この丸括弧の中では、最大文字数を指定することができるのですが、こう書くと日本語英語関係なく 140 文字までという意味になります。
message VARCHAR(140),
```

```sql
# 固定長の文字列には CHAR を使えば OK で、例えば言語コードを 2 文字で保持したかったら、このように丸括弧の中に文字数を書いてあげれば OK です。
lang CHAR(2)
```

では、データを追加してみましょう。moodとlangを追加したので、カラムを増やして、データを追加してあげます。そして、適当に小数点を含む数と、言語コードを書いてあげましょう。

```sql
INSERT INTO posts (message, likes, mood, lang) VALUES
  ('Thanks', 12, 7.825, 'EN'),
  ('Arigato', 4, 4.2138, 'JA');
```

コードを実行します。

```sql
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+------+------+
| message | likes | mood | lang |
+---------+-------+------+------+
| Thanks  |    12 | 7.83 | EN   |
| Arigato |     4 | 4.21 | JA   |
+---------+-------+------+------+
~ $
```

なお、moodですが、小数点以下2桁までと指定した場合、そこで四捨五入されている点にも注意しておいてください。

### 質問：毎回DROP TABLE IF EXISTS posts; を実行しなくてはいけないのですか？

回答：あくまで学習のために行っているため、実際には毎回テーブルを削除する必要はありません。

これは毎回、コマンドがどう作用するか確認するために毎回テーブルを削除して、「まっさらなテーブルに対してこういうコマンドを実行したらこうなりますよ」ということを示すためですね。

あくまで学習のためにそうしているのであって、「このコマンドを実行するときは毎回テーブルを削除しなくてはいけない」というわけではありません（特に現場では既存のテーブルに対して処理することが多いのでいったん運用をしはじめたらテーブルを削除することは普通行いません）。
### 要点まとめ
- 数値型や文字列型のカラムを作ったあとにデータを挿入していきます。
    - INT(整数)
    - UNSIGNED(マイナスを除き、数値の範囲を広くしてくれる)
    - DECIMAL(桁数と小数点を指定)
    - CHAR(固定長の文字列の指定)</details>


<details><summary>#09 ENUM型を扱ってみよう</summary>

特定の文字列の中からひとつだけを格納できる ENUM (エナム)というデータ型について見ていきましょう。

例えば、category というカラムを作ってあげて ENUM に対して 'Gadget' , 'Game' , 'Business' のうちどれかのみが格納できるよと書いてあげます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
  category ENUM('Gadget', 'Game', 'Business')
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4);

SELECT * FROM posts;
```

そのうえで値を挿入するには、カラム名を追加してあげて、今羅列した文字列のどれかをそのまま書いてあげれば OK です。ひとつ目のレコードは 'Gadget' 、ふたつ目のレコードは 'Game' としてあげましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
  category ENUM('Gadget', 'Game', 'Business')
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12, 'Gadget'),
  ('Arigato', 4, 'Game');

SELECT * FROM posts;
```

このように設定することで、これら以外の値を弾くことができます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
  category ENUM('Gadget', 'Game', 'Business')
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12, 'Gadget'),
  ('Arigato', 4, 'Game'),
  ('Merci', 4, 'Fashion') # 以外の値を追加

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1265 (01000) at line 8: Data truncated for column 'category' at row 3
~ $
# categoryのデータがtruncatedされた、つまり切り捨てられたので、エラーが出ている
```

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  category ENUM('Gadget', 'Game', 'Business')
);

INSERT INTO posts (message, likes, category) VALUES
  ('Thanks', 12, 'Gadget'),
  ('Arigato', 4, 'Game'),
  -- ('Merci', 4, 'Fashion');
  ('Merci', 4, 'Business');
SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+----------+
| message | likes | category |
+---------+-------+----------+
| Thanks  |    12 | Gadget   |
| Arigato |     4 | Game     |
| Merci   |     4 | Business |
+---------+-------+----------+
~ $
```

ENUM 型ですが、こちらの値は 1 から始まるインデックス番号でも表現することができて、そのあたりも見ておきましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  category ENUM('Gadget', 'Game', 'Business')
);

-- INSERT INTO posts (message, likes, category) VALUES
--   ('Thanks', 12, 'Gadget'),
--   ('Arigato', 4, 'Game'),
--   -- ('Merci', 4, 'Fashion');
--   ('Merci', 4, 'Business');
  
INSERT INTO posts (message, likes, category) VALUES
  ('Thanks', 12, 1),
  ('Arigato', 4, 2),
  ('Merci', 4, 3);
  
SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+----------+
| message | likes | category |
+---------+-------+----------+
| Thanks  |    12 | Gadget   |
| Arigato |     4 | Game     |
| Merci   |     4 | Business |
+---------+-------+----------+

# インデックス番号を指定しても同じ結果になっている。
```

こうしたENUM型の扱いにも慣れておきましょう。</details>


<details><summary>#10 SET型を扱ってみよう</summary>

ENUM は'Gadget', 'Game', 'Business’からひとつしか選べませんでしたが、もし複数選べるようにしたいなら、こちらを SET 型にしてあげます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  categories SET('Gadget', 'Game', 'Business') # SET型にして、わかりやすいようにカラム名を複数形にする
);

INSERT INTO posts (message, likes, category) VALUES
  ('Thanks', 12, 1),
  ('Arigato', 4, 2),
  ('Merci', 4, 3);

SELECT * FROM posts;
```

そのうえで、データを挿入する際にはカンマ区切りで値を渡してあげます。渡す値は順不同で OK ですが、カンマ以外に空白などを入れてはいけないので注意しましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  categories SET('Gadget', 'Game', 'Business')
);

INSERT INTO posts (message, likes, categories) VALUES
  ('Thanks', 12, 'Gadget,Game'), # 渡す値は順不同でOKだが、カンマ以外に空白などを入れてはいけない
  ('Arigato', 4, 'Business'),
  ('Merci', 4, 'Business,Gadget');

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+-----------------+
| message | likes | categories      |
+---------+-------+-----------------+
| Thanks  |    12 | Gadget,Game     |
| Arigato |     4 | Business        |
| Merci   |     4 | Gadget,Business |
+---------+-------+-----------------+
```

変な値を入れるとはじかれるのは ENUM 型と一緒で、たとえばということで、こちらで 'Personal' と指定してみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  categories SET('Gadget', 'Game', 'Business')
);

INSERT INTO posts (message, likes, categories) VALUES
  ('Thanks', 12, 'Gadget,Game'),
  -- ('Arigato', 4, 'Business'),
  ('Arigato', 4, 'Personal'),
  ('Merci', 4, 'Business,Gadget');

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1265 (01000) at line 8: Data truncated for column 'categories' at row 2
```

SET 型ですが、内部的に値を数値で管理していて、左から 2 の 0 乗である 1 、 2 の 1 乗である 2 、 2 の 2 乗である 4 といった具合に管理されています。したがって、値を指定したい場合はそういった値も使えるのでちょっと試してみましょう。

- Gadget は 1 、 Game は 2 なので、 1 + 2 で 3 で表現できます。
- Business に関しては 4 で OK ですね。
- Business が 4 、 Gadget が 1 なので、 4 + 1 で 5 としてあげれば OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  categories SET('Gadget', 'Game', 'Business') -- 2^0, 2^1, 2^2, ...
);

-- INSERT INTO posts (message, likes, categories) VALUES
--   ('Thanks', 12, 'Gadget,Game'),
--   ('Arigato', 4, 'Business'),
--   -- ('Arigato', 4, 'Personal'),
--   ('Merci', 4, 'Business,Gadget');
  
INSERT INTO posts (message, likes, categories) VALUES
  ('Thanks', 12, 3),
  ('Arigato', 4, 4),
  ('Merci', 4, 5);

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+-----------------+
| message | likes | categories      |
+---------+-------+-----------------+
| Thanks  |    12 | Gadget,Game     |
| Arigato |     4 | Business        |
| Merci   |     4 | Gadget,Business |
+---------+-------+-----------------+
```

こうした SET 型も扱えるようになっておきましょう。
### 質問：SET型では複数の値を指定できるということですが、重複して選択することは可能なのでしょうか？
SET 型では複数の値を指定できるということですが、重複して選択することは可能なのでしょうか？

今回は( 'Gadget', 'Game', 'Business' ) -- 2^0 , 2^1 , 2^2 , ...ということですが、例えば( 'Gadget', 'Game', 'Business' ,' Fashion' ) -- 2^0, 2^1 , 2^2 , 2^3 ...の場合、categories を値 8 で指定したとき、'Fashion'(2^3)または’Business,Business’（2^2 + 2^2）が考えられるかと思います。

2 つ目のように重複してカテゴリを選択することは可能なのでしょうか？

回答：二つ選択することはできません。

2 つ選択することはできません。

’Business,Business’ のような形の場合、値が足されているわけではなく、'2^2, 2^2' と二つの 4 が入っていると考えてください。
### 質問：VALUESは何を意味しているのですか？
回答：INSERTでデータを挿入するときに値を指定するためのものです。

`VALUES` は `INSERT` でデータを挿入するときに値を指定するためのものです。

`INSERT` 文全体はこうなっていますね。

```sql
INSERT INTO posts (message, likes, categories) VALUES
  ('Tnanks', 12, 3),
  ('Arigato', 4, 4),
  ('Merci', 4, 5);

```

`VALUES` で指定されているのは後に続く

```
  ('Tnanks', 12, 3),
  ('Arigato', 4, 4),
  ('Merci', 4, 5)

```

の部分です。

つまり `message`, `likes`, `categories` にそれぞれ`'Tnanks'`, `12`, `3` や`'Arigato'`, `4`, `4` といった値が挿入されることになります。

レッスンで言うとこちらです。[#05 レコードを挿入しよう | MySQL入門 基礎編](https://dotinstall.com/lessons/basic_mysql_beginner/55405)
### 質問：2の0乗は0ではないのですか？
回答：2の0乗は1になります。

これは説明がなかなか難しい問題ですが、2の0乗は1になります。以下も参考にしていただければと思います。

[https://www.google.com/search?q=2%E3%81%AE0%E4%B9%97&oq=2%E3%81%AE0%E4%B9%97&aqs=chrome..69i57j0l3.8775j0j7&sourceid=chrome&ie=UTF-8](https://www.google.com/search?q=2%E3%81%AE0%E4%B9%97&oq=2%E3%81%AE0%E4%B9%97&aqs=chrome..69i57j0l3.8775j0j7&sourceid=chrome&ie=UTF-8)
</details>


<details><summary>#11 真偽値、日時を扱ってみよう</summary>

まず真偽値ですが、下書きかどうかを is_draft で管理してみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  is_draft BOOL, # BOOLは真偽値を表す
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);

SELECT * FROM posts;
```

次にこのレコードが作成された日時をcreatedで管理してみます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  is_draft BOOL,
  created DATETIME # DATETIMEは日時を表す
);

INSERT INTO posts (message, likes, if_draft, created) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);

SELECT * FROM posts;
```

is_draft と created のデータを挿入していきましょう。

- 真偽値ですが、 TRUE か FALSE もしくは TRUE は 1 FALSE は 0 なので、このように書いてあげても OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  is_draft BOOL,
  created DATETIME
);

INSERT INTO posts (message, likes, is_draft, created) VALUES
  ('Thanks', 12, TRUE),
  ('Arigato', 4, FALSE),
  ('Merci', 4, 0);

SELECT * FROM posts;
```

- 日時ですが、ハイフンやコロンで区切った一般的な書式が使えて、このように書いてあげれば OK です。
- 時間を省略すると 0 時 0 分 0 秒になります。
- 現在の日時を表す NOW() というキーワードも使えます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT,
  is_draft BOOL,
  created DATETIME
);

INSERT INTO posts (message, likes, is_draft, created) VALUES
  ('Thanks', 12, TRUE, '2020-10-11 15:32:05'),
  ('Arigato', 4, FALSE, '2020-10-12'),
  ('Merci', 4, 0, NOW());

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+----------+---------------------+
| message | likes | is_draft | created             |
+---------+-------+----------+---------------------+
| Thanks  |    12 |        1 | 2020-10-11 15:32:05 |
| Arigato |     4 |        0 | 2020-10-12 00:00:00 |
| Merci   |     4 |        0 | 2022-05-25 11:27:21 |
+---------+-------+----------+---------------------+
```

こうした真偽値や日時も扱えるようになっておきましょう。
### 質問：is_draftの意味は？
回答：下書きかどうかを真偽値で管理しています。

今回のテーブルは、投稿するものを管理するテーブルというイメージで作成されております。（twitterみたいものをイメージしてください）

その投稿する内容を下書きを判定するためのフィールドとして、is_draft を定義しております。

よって、未完成のsqlという意味ではございません。

- is_draftでTRUEとした方は、「Twitterの"下書き保存"に保存されている投稿」、逆にis_draftをFALSEとした場合は、「下書きではなく実際の投稿」というイメージでOK。
### 要点まとめ
- 真偽値型や日付型の扱い方について見ていきます。
    - BOOL(真偽値)
    - DATETIME(日時)
    - NOW( )(現在の日時を表すキーワード)</details>


<details><summary>#12 NULLの扱いを見ていこう</summary>

レコードの挿入ですが、実は全てのカラムに値を設定していなくても OK です。たとえば、 message だけ指定して、レコードを挿入してみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);
  
INSERT INTO posts (message) VALUES ('Gracias');

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+
| message | likes |
+---------+-------+
| Thanks  |    12 |
| Arigato |     4 |
| Merci   |     4 |
| Gracias |  NULL |
+---------+-------+

# このようになり、値を設定しなかった場合、何もないという意味の NULL という特殊な値になります
```

こうですね、うまくいっていて、値を設定しなかった場合、何もないという意味の NULL (ナル)という特殊な値になります。

ただし、値が設定されていなかったら、エラーではじきたいという場合もあります。その場合はカラムに NOT NULL とつけてあげれば OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  -- likes INT
  likes INT NOT NULL
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);
  
INSERT INTO posts (message) VALUES ('Gracias');

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1364 (HY000) at line 13: Field 'likes' doesn't have a default value
```

ちゃんとエラーになっています。

それから、値が設定されていなかった場合にエラーではじくのではなくて、デフォルト値を設定してあげることもできて、その場合は DEFAULT としたあとに、デフォルトの値を書いてあげれば OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  -- likes INT
  -- likes INT NOT NULL
  likes INT DEFAULT 0
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);
  
INSERT INTO posts (message) VALUES ('Gracias');

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-------+
| message | likes |
+---------+-------+
| Thanks  |    12 |
| Arigato |     4 |
| Merci   |     4 |
| Gracias |     0 |
+---------+-------+
```

データを扱っていると、設定する値がない場合もあるので、こうした操作もできるようになっておきましょう。
### 要点まとめ
- MySQLにおけるNULLの扱いについて見ていきます。
    - NULL(「何もない」という意味の特殊な値)
    - NOT NULL(値が設定されていないものは、エラーで弾きたい場合に使用する)
    - DEFAULT(値が設定されていない場合に弾かずにデフォルト値で設定することができる)</details>


<details><summary>#13 値に制限をつけてみよう</summary>

値の範囲に制限をつける方法。たとえば、 likes は 0 以上 100 以下の値だけにしたいという場合は CHECK を使ってこのように書いてあげれば OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT CHECK (likes >= 0 AND likes <= 100)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);

SELECT * FROM posts;
```

100を超えた数値を入力するとエラーになります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140),
  likes INT CHECK (likes >= 0 AND likes <= 100)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 154); # 100を超える数値を入力

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 4025 (23000) at line 7: CONSTRAINT `posts.likes` failed for `myapp`.`posts`
```

重複した値を弾きたい場合。例えば、messageに重複した値を入れたくない場合、UNIQUEと付けてください。UNIQUEが付いたmessageには重複した値が入れられないので、'Arigato’ともう一回入れてもエラーになります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  message VARCHAR(140) UNIQUE,
  likes INT CHECK (likes >= 0 AND likes <= 200)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 154),
  ('Arigato', 4);

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1062 (23000) at line 7: Duplicate entry 'Arigato' for key 'message'
# 'Arigato'が重複している、というエラーが表示される
```

こうした制約をつけることで適切ではないデータをはじくことができるので、使いこなせるようになっておきましょう。
### 要点まとめ
- 値の範囲に制限をつけたり重複する値を弾く方法について見ていきます。
    - CHECK(値の範囲に制限を設ける)
    - UNIQUE(重複する値を弾く)</details>


<details><summary>#14 主キーを設定してみよう</summary>

テーブルですが、特定のレコードを処理するために、そのレコードを一意に識別するためのカラムを設定するのが一般的です。たいていの場合、 id という名前で NULL ではない整数の連番にするので、 INT NOT NULL としてあげましょう。また、こうしたレコードを一意に特定するためのカラムですが、 PRIMARY KEY という指定をすることで、 id をこのテーブルのプライマリーキーつまり、主キーにすることができます。主キーにしておくと、 id の値をうっかり入れ忘れたり、値が重複していたときにエラーにしてくれるというメリットがあります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);

SELECT * FROM posts;
```

わざと重複した値を入れてみます。レコードを識別するのに、重複した値だと困ってしまうので、エラーになるはずです。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (id, message, likes) VALUES
  (1, 'Thanks', 12),
  (2, 'Arigato', 4),
  (2, 'Merci', 4);

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1062 (23000) at line 9: Duplicate entry '2' for key 'PRIMARY'
# 2がDuplicate(重複している)というエラーが表示
```

2を3に直して、コードを実行。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (id, message, likes) VALUES
  (1, 'Thanks', 12),
  (2, 'Arigato', 4),
  (3, 'Merci', 4);

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
+----+---------+-------+
```

idの連番は、自動で振ることもできます。PRIMARY KEY 指定した場合にしか使えないのですが、こちらで AUTO_INCREMENT としてあげてください。その場合は、値を挿入しなければ自動的に連番になるので、こちらでは id を挿入せずに確かめてみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

-- INSERT INTO posts (id, message, likes) VALUES
--   (1, 'Thanks', 12),
--   (2, 'Arigato', 4),
--   (3, 'Merci', 4);
  
INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4);

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
+----+---------+-------+
```

レコードを一意に識別できる主キーを設定しておくと、データの操作がしやすくなるので、こうした設定もできるようになっておきましょう。
### 質問：NOT NULL と AUTO_INCREMENT を同時に指定するのはなぜですか？
回答：NULL を許可しない、とコードで明示できるからです。

`NOT NULL` をつけることで「 NULL を許可しない」ということをコードで明示することができるのでつけることが多いようです。

なお MySQL 公式のドキュメントにあるサンプルコードでも[https://dev.mysql.com/doc/refman/8.0/ja/example-auto-increment.html](https://dev.mysql.com/doc/refman/8.0/ja/example-auto-increment.html)`NOT NULL AUTO_INCREMENT` となっているので「`NOT NULL` をつけるのが好ましい」というのが MySQL の公式見解だと思われます。
### 質問：一意に識別する、とは？
回答：例を挙げて説明します。

「重複するものがない」と言うと分かりやすいかも知れません。

例えば、下記みたいものをイメージして下さい。

社員番号は、一つの番号に一人の社員

商品番号は、一つの番号に一つの商品
### 要点まとめ
- レコードを一意に識別するための主キーについて見ていきます。
    - PRIMARY KEY(プライマリーキー(主キー)の設定)
    - AUTO_INCREMENT(値を入力しなければ自動的に連番になる設定。PRIMARY KEYを設定した場合に使用ができる)</details>


<details><summary>#15 SELECTでデータを抽出しよう</summary>

SELECTについて見ていきましょう。SELECT * FROM posts とすると、全てのレコードを抽出しなさいという意味になります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4),
  ('Gracias', 15),
  ('Danke', 23);

SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
|  4 | Gracias |    15 |
|  5 | Danke   |    23 |
+----+---------+-------+
# ↑全てのレコードが抽出されている
```

また、SELECT * FROM posts の* は全てのカラムという意味で、もし特定のカラムだけ抽出したいならカンマ区切りで、指定してあげれば OK です。では、 id と message だけを posts から抽出してね、と書いてあげましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4),
  ('Gracias', 15),
  ('Danke', 23);

-- SELECT * FROM posts;
SELECT id, message FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+
| id | message |
+----+---------+
|  1 | Thanks  |
|  2 | Arigato |
|  3 | Merci   |
|  4 | Gracias |
|  5 | Danke   |
+----+---------+
# ↑idとmessageだけが抽出されている
```

それから条件に合うレコードだけを抽出したい場合は WHERE を使います。では、ここで likes が 10 以上の投稿だけ抽出してみましょう。その場合、このように書いてあげれば OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4),
  ('Gracias', 15),
  ('Danke', 23);

-- SELECT * FROM posts;
-- SELECT id, message FROM posts;

SELECT * FROM posts WHERE likes >= 10; # WHEREを使い、likesが10以上の投稿だけを抽出する

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  4 | Gracias |    15 |
|  5 | Danke   |    23 |
+----+---------+-------+
```

また、ここで使った記号ですが、他にもあって、何々より大きい、何々以上、何々より小さい、何々以下はこのように表現してあげれば OK です。

```sql
> >= < <=
```

それから何々と等しいは、 `=` で表現できて、何々と等しくないは `!=` か、 `<>` で表現することができます。

では、今度は message のほうでこの条件を使ってみましょう。message が 'Danke' のレコードを抽出しなさい、もしくは 'Danke' じゃないレコードを抽出しなさいと書いてあげましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4),
  ('Gracias', 15),
  ('Danke', 23);

-- SELECT * FROM posts;
-- SELECT id, message FROM posts;

-- > >= < <=
-- SELECT * FROM posts WHERE likes >= 10;

-- = != <>
SELECT * FROM posts WHERE message = 'Danke';
SELECT * FROM posts WHERE message != 'Danke';
SELECT * FROM posts WHERE message <> 'Danke';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  5 | Danke   |    23 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
|  4 | Gracias |    15 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
|  4 | Gracias |    15 |
+----+---------+-------+
# ↑最初は Danke だけ、次に Danke 以外のレコードが 2 回抽出されているので
```

こうした操作もできるようになっておきましょう。

### 質問：SELECT、SHOW、DESCの違いがわかりません
回答：データを抽出できるのはSELECTだけです。それぞれ説明します。

`SELECT`　→　テーブルの中身を抽出するための命令

`DESC`　→　テーブルの構造を確認するための命令

`SHOW` →　サーバーの設定やテーブル名、データベース名などを確認するための命令

上記のようになっております。よって、データを抽出することができるのは、`SELECT`のみとなります。</details>


<details><summary>#16 条件を組み合わせてみよう</summary>

条件を組み合わせるためのANDとORについて見ていきましょう。それぞれ、なおかつ、もしくは、という意味になります。

```sql
-- AND なおかつ
-- OR  もしくは
```

例を出していきましょう。likesが10以上、なおかつ20以下のレコードを抽出して見ます。

```sql
-- AND なおかつ
-- OR  もしくは
SELECT * FROM posts WHERE likes >= 10 AND likes <= 20; # なおかつなので、ANDを使う
```

それから何々以上、何々以下という条件の場合に限っては特殊な書き方もできます。BETWEEN というキーワードを使うのですが、 10 以上 20 以下という表現をするには `BETWEEN 10 AND 20` と書いてあげれば上と全く同じ意味になります。

```sql
SELECT * FROM posts WHERE likes BETWEEN 10 AND 20;
```

BETWEENの条件を反転したい場合にはBETWEENの前にNOTを付けてあげればOKです。

```sql
SELECT * FROM posts WHERE likes NOT BETWEEN 10 AND 20;
```

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4),
  ('Gracias', 15),
  ('Danke', 23);

-- AND なおかつ
-- OR  もしくは
SELECT * FROM posts WHERE likes >= 10 AND likes <= 20;
SELECT * FROM posts WHERE likes BETWEEN 10 AND 20;
SELECT * FROM posts WHERE likes NOT BETWEEN 10 AND 20;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  4 | Gracias |    15 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  4 | Gracias |    15 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
|  5 | Danke   |    23 |
+----+---------+-------+

# 3つ目のNOT BETWEENは反転(逆の意味)なので、10以下と20以上のレコードが抽出される
```

ORの例を見ていきましょう。例えば、likesが4もしくは12のレコードを抽出したかったとします。その場合、もしくはなので、ORを使ってこのように書きます。

```sql
SELECT * FROM posts WHERE likes = 4 OR likes = 12;
```

それから ＝ で判定する条件を OR で繋いだ場合、特殊な書き方ができて、 IN を使って書き換えることができます。

```sql
SELECT * FROM posts WHERE likes IN (4, 12);
# 上のORの書き方と同じ
```

それから IN を反転させたい場合にはこちらに NOT を付けてあげてください。

```sql
SELECT * FROM posts WHERE likes NOT IN (4, 12);
```

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Arigato', 4),
  ('Merci', 4),
  ('Gracias', 15),
  ('Danke', 23);

-- AND なおかつ
-- OR  もしくは
-- SELECT * FROM posts WHERE likes >= 10 AND likes <= 20;
-- SELECT * FROM posts WHERE likes BETWEEN 10 AND 20;
-- SELECT * FROM posts WHERE likes NOT BETWEEN 10 AND 20;
SELECT * FROM posts WHERE likes = 4 OR likes = 12;
SELECT * FROM posts WHERE likes IN (4, 12);
SELECT * FROM posts WHERE likes NOT IN (4, 12);

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |     4 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  4 | Gracias |    15 |
|  5 | Danke   |    23 |
+----+---------+-------+
# 3つ目のNOT INは、4か12の結果を反転させた結果になる
```

こうした条件の組み立て方にも慣れておきましょう。
### 要点まとめ
- 複数の条件を組み合わせるためのAND、ORの使い方について見ていきます。
    - AND(なおかつ)
    - BETWEEN(何々以上何々以下という条件に限って使用可能)
    - OR(もしくは)
    - IN(＝ で判定する条件を OR で繋いだ場合にINを使用可能)</details>


<details><summary>#17 LIKEと%で文字列を抽出しよう</summary>

文字列の抽出について見ていきますが、完全一致なら今まで見てきたように、=を使ってあげればOKです。

```sql
SELECT * FROM posts WHERE message = 'Gracias';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  3 | Gracias |     4 |
+----+---------+-------+
```

ただ、 LIKE キーワードを使えば特殊な記号も使うことができて、 % で 0 文字以上の任意の文字、 _ で任意の 1 文字を表現することができます。

```sql
-- %: 0文字以上の任意の文字
-- _: 任意の1文字
```

% を使えば前方一位の検索をすることができて、たとえば 't' から始まるメッセージだけを抽出したい場合は、このように書いてあげれば OK です。

```sql
SELECT * FROM posts WHERE message LIKE 't%';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------+-------+
| id | message     | likes |
+----+-------------+-------+
|  1 | Thank you!  |    12 |
|  2 | thanks 100% |     4 |
+----+-------------+-------+
```

ここで大文字小文字を区別したい場合は BINARY (バイナリー)というキーワードを使ってあげてください。

```sql
SELECT * FROM posts WHERE message LIKE BINARY't%';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------+-------+
| id | message     | likes |
+----+-------------+-------+
|  2 | thanks 100% |     4 |
+----+-------------+-------+

# 小文字のtから始まるレコードだけが抽出できている
```

また、同様に後方一致や部分一致も % を使えば実現することができます。たとえば 'su' で終わるという条件はこのように書けばいいですし、 'i' を含むという条件はこのように書いてあげれば OK です。

```sql
SELECT * FROM posts WHERE message LIKE '%su';
SELECT * FROM posts WHERE message LIKE '%i%';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------------+-------+
| id | message           | likes |
+----+-------------------+-------+
|  4 | Arigato_gozaimasu |    15 |
|  5 | Arigato! desu     |    23 |
+----+-------------------+-------+
+----+-------------------+-------+
| id | message           | likes |
+----+-------------------+-------+
|  3 | Gracias           |     4 |
|  4 | Arigato_gozaimasu |    15 |
|  5 | Arigato! desu     |    23 |
+----+-------------------+-------+

# suで終わるレコード、iが含まれるレコードが抽出されている
```

### 要点まとめ
- %を使った条件で文字列を抽出する方法について見ていきます。
    - %(任意の複数の文字を意味する)
    - LIKE(任意の文字を選択)
    - BINARY(大文字小文字を区別)</details>


<details><summary>#18 LIKEと_で文字列を抽出しよう</summary>

任意の1文字を表す_(アンダーバー、アンダースコア)について。例えば、今回の条件ですが、 message が任意の 1 文字がふたつ続いて、その次が a でその後が 0 文字以上の任意の文字としてみましょう。すると、3 文字目が a のレコードだけがうまく抽出できているのが分かります。

```sql
SELECT * FROM posts WHERE message LIKE '__a%'

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------+-------+
| id | message     | likes |
+----+-------------+-------+
|  1 | Thank you!  |    12 |
|  2 | thanks 100% |     4 |
|  3 | Gracias     |     4 |
+----+-------------+-------+
# 3文字目がaのレコードが抽出されている
```

LIKE を反転させるには NOT を付けてあげれば OK です。今回の場合は3文字目がa以外のレゴードが抽出されるということ。

```sql
SELECT * FROM posts WHERE message NOT LIKE '__a%'

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------------+-------+
| id | message           | likes |
+----+-------------------+-------+
|  4 | Arigato_gozaimasu |    15 |
|  5 | Arigato! desu     |    23 |
+----+-------------------+-------+
# 3文字目がa以外のレゴードが抽出される
```

% と _ の文字自体を検索したい場合もあるので、その方法も見ていきましょう。例えば、% を含むレコードだけを抽出したくて、 % を含むという意味で部分一致の書き方を使ってこう書いても、実はうまくいきません。

```sql
SELECT * FROM posts WHERE message LIKE '%%%';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------------+-------+
| id | message           | likes |
+----+-------------------+-------+
|  1 | Thank you!        |    12 |
|  2 | thanks 100%       |     4 |
|  3 | Gracias           |     4 |
|  4 | Arigato_gozaimasu |    15 |
|  5 | Arigato! desu     |    23 |
+----+-------------------+-------+
# 全てが抽出されてしまう
```

%を任意の文字として抽出したい場合は、 抽出したい%の前に\ をその前に付けてあげること。

```sql
SELECT * FROM posts WHERE message LIKE '%\%%';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------+-------+
| id | message     | likes |
+----+-------------+-------+
|  2 | thanks 100% |     4 |
+----+-------------+-------+
```

同様に _ が入ったレコードだけを抽出したかったら、 \_ としてあげてください。

```sql
SELECT * FROM posts WHERE message LIKE '%\_%';

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+-------------------+-------+
| id | message           | likes |
+----+-------------------+-------+
|  4 | Arigato_gozaimasu |    15 |
+----+-------------------+-------+
```

### 要点まとめ
- _を使った条件で文字列を抽出する方法について見ていきます。
    - _(任意の1文字を意味する)
    - \%(%を抽出したい時は%の前に\を付けてあげること)
    - \_(_も同様、抽出したい_の前に\を付けてあげること)</details>


<details><summary>#19 NULL(ナル)のレコードを抽出しよう</summary>

NULLについて。とりあえず、全てのレコードを抽出して見ます。

```sql
SELECT * FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  3 | Merci   |  NULL |
|  4 | Gracias |    15 |
|  5 | Danke   |  NULL |
+----+---------+-------+
# NULLもちゃんと抽出されています
```

次に SELECT * FROM posts WHERE likes != 12 としてみましょう。その場合 12 以外が抽出されるのですが、 NULL のレコードがどうなるか見ておきましょう。見てみると NULL のレコードは結果に含まれていないのが分かります。

```sql
SELECT * FROM posts WHERE likes != 12;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  2 | Arigato |     4 |
|  4 | Gracias |    15 |
+----+---------+-------+
# この場合、NULLのレコードは結果に含まれていない
```

NULLも含まれるようにしたかった場合は、ORを使い、`likes IS NULL`と書きます。

```sql
SELECT * FROM posts WHERE likes != 12 OR likes IS NULL;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  2 | Arigato |     4 |
|  3 | Merci   |  NULL |
|  4 | Gracias |    15 |
|  5 | Danke   |  NULL |
+----+---------+-------+
# NULLのレコードも抽出されている
```

IS NULL ですが、この条件を反転させたかった場合は、 `IS NOT NULL` としてあげる必要があります。

```sql
SELECT * FROM posts WHERE likes IS NOT NULL;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Arigato |     4 |
|  4 | Gracias |    15 |
+----+---------+-------+
# NULL以外のレコードが抽出されている
```

データにNULLが入っている時は抽出条件に注意が必要なので、意識しておいてください。
### 要点まとめ
- NULLを抽出条件に含める方法を見ていきます。
    - IS NULL(ORを使用して、NULLを含むデータを抽出する)
	  - IS NOT NULL(NULLを含まないものを抽出する(IS NULLを反転させる))</details>


<details><summary>#20 抽出結果を並び替えよう</summary>

抽出結果を並び替えてみましょう。たとえば、 likes を小さい順で並び替えたかったら、 `SELECT * FROM posts ORDER BY likes` としてあげます。一方、逆に並べたい場合は、`SELECT * FROM posts ORDER BY likes DESC`とDESCというキーワードを付けます。

```sql
SELECT * FROM posts ORDER BY likes;
SELECT * FROM posts ORDER BY likes DESC;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  2 | Merci   |     4 |
|  3 | Arigato |     4 |
|  5 | Danke   |     8 |
|  1 | Thanks  |    12 |
|  4 | Gracias |    15 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  4 | Gracias |    15 |
|  1 | Thanks  |    12 |
|  5 | Danke   |     8 |
|  2 | Merci   |     4 |
|  3 | Arigato |     4 |
+----+---------+-------+
# ORDER BYは小さい順、ORDER BY...DESCは大きい順
```

likes の数が同じだったときに、アルファベット順に並び替えたかったら、カンマ区切りでさらに message を加えてあげれば OK です。

```sql
SELECT * FROM posts ORDER BY likes DESC, message;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  4 | Gracias |    15 |
|  1 | Thanks  |    12 |
|  5 | Danke   |     8 |
|  3 | Arigato |     4 |
|  2 | Merci   |     4 |
+----+---------+-------+
```

それからこの状態で、上位 3 件だけ抽出したかった場合、 LIMIT というキーワードが使えます。

```sql
SELECT * FROM posts ORDER BY likes DESC, message;
SELECT * FROM posts ORDER BY likes DESC, message LIMIT 3;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  4 | Gracias |    15 |
|  1 | Thanks  |    12 |
|  5 | Danke   |     8 |
|  3 | Arigato |     4 |
|  2 | Merci   |     4 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  4 | Gracias |    15 |
|  1 | Thanks  |    12 |
|  5 | Danke   |     8 |
+----+---------+-------+
# 上位3件が抽出されている
```

それから何らかの理由で、最初の 2 件を除外して、その後 3 件という抽出をしたい場合、先頭 0 から数えて 0、1、2 件目から 3 件分抽出したいという書き方をします。OFFSET というキーワードを使うのですが、抽出するのは 3 件で、 0 1 2 件目からというのは OFFSET 2 と書いてあげてください。

```sql
SELECT * FROM posts ORDER BY likes DESC, message LIMIT 3 OFFSET 2;
# 2件目から上位3件を抽出する、という意味
```

それから別の書き方もできて、先頭から数えて 0 1 2 件目から 3 件分といった書き方をすれば OK です。

```sql
SELECT * FROM posts ORDER BY likes DESC, message LIMIT 2, 3;
# 先頭から0、1、2件目から3件分を抽出する、という意味。
```

```sql
SELECT * FROM posts ORDER BY likes DESC, message;
SELECT * FROM posts ORDER BY likes DESC, message LIMIT 3 OFFSET 2;
SELECT * FROM posts ORDER BY likes DESC, message LIMIT 2, 3;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  4 | Gracias |    15 |
|  1 | Thanks  |    12 |
|  5 | Danke   |     8 |
|  3 | Arigato |     4 |
|  2 | Merci   |     4 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  5 | Danke   |     8 |
|  3 | Arigato |     4 |
|  2 | Merci   |     4 |
+----+---------+-------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  5 | Danke   |     8 |
|  3 | Arigato |     4 |
|  2 | Merci   |     4 |
+----+---------+-------+
```

### 質問：先頭の行2件が除外されて上位3件が表示される仕組みがわかりません。
    
回答：LIMIT で件数を指定し、 OFFSET で何件目のレコードから結果を返すかを指定しています。

`OFFSET` は何件目のレコードから結果を返すかを指定するものになります。但し、 0 から数えますので 3 件目からのレコードが欲しい場合は `OFFSET 2` を指定することになります。

また、 `LIMIT` は結果に欲しい件数を指定するものでしたので `LIMIT 3` を指定すると 3 件の結果が得られます。 `OFFSET` と `LIMIT` を組み合わせて `LIMIT 3 OFFSET 2` のように指定すると 3 件目から数えて 3 件のレコードを結果として返します。

最後に説明した `LIMIT 2, 3` は `LIMIT 3 OFFSET 2` と同じ意味で、このように `LIMIT` だけで記述することもできます。
### 要点まとめ
- ORDER BYを使って抽出結果を並び替える方法を見ていきます。
    - ORDER BY(小さい順に並び替える)
    - DESC(大きい順に並び替える)
    - LIMIT(結果に欲しい件数を指定する)
    - OFFSET(何件目のレコードから結果を返すかを指定する)</details>


<details><summary>#21 数値の関数を見ていこう</summary>

SQLでは計算をさせることもできます。いくつかの演算子が用意されていて、足し算は + 、引き算は - 、掛け算は * 、割り算は / 、そして余りを求めるには % を使います。

```sql
-- + - * / %
```

今回 likes の数だけ何らかの報酬が支払われることになったとしましょう。1 いいねに対して 500 円の売り上げが立つけれども、そのうち貰える報酬は 1/3 だけといったシーンを想定してみましょう。

```sql
SELECT likes * 500 / 3 FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+-----------------+
| likes * 500 / 3 |
+-----------------+
|       2000.0000 |
|        666.6667 |
|        666.6667 |
|       2500.0000 |
|       1333.3333 |
+-----------------+
```

likes * 500 / 3の見出しをASで別名を付けてわかりやすくすることもできます。

```sql
SELECT likes * 500 / 3 AS bonus FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+-----------+
| bonus     |
+-----------+
| 2000.0000 |
|  666.6667 |
|  666.6667 |
| 2500.0000 |
| 1333.3333 |
+-----------+
```

端数を処理するための命令が用意されています。

- 端数を切り捨てたい場合はFLOOR()
- 端数を切り上げたい場合はCEIL()(シール)
- 四捨五入をしたい場合はROUND()

```sql
SELECT
  likes * 500 / 3 AS bonus,
  FLOOR(likes * 500 / 3) AS floor,
  CEIL(likes * 500 / 3) AS ceil,
  ROUND(likes * 500 / 3) AS round
FROM
  posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+-----------+-------+------+-------+
| bonus     | floor | ceil | round |
+-----------+-------+------+-------+
| 2000.0000 |  2000 | 2000 |  2000 |
|  666.6667 |   666 |  667 |   667 |
|  666.6667 |   666 |  667 |   667 |
| 2500.0000 |  2500 | 2500 |  2500 |
| 1333.3333 |  1333 | 1334 |  1333 |
+-----------+-------+------+-------+
```

↑このように () がついた命令を関数と呼ぶので用語として覚えておいてください。

ROUND 関数ですが、桁数を指定することもできて、小数点以下二桁で丸めたいという場合は、こちらにカンマ区切りでこのように桁数を指定してあげてください。

```sql
SELECT
  likes * 500 / 3 AS bonus,
  FLOOR(likes * 500 / 3) AS floor,
  CEIL(likes * 500 / 3) AS ceil,
  ROUND(likes * 500 / 3, 2) AS round
FROM
  posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+-----------+-------+------+---------+
| bonus     | floor | ceil | round   |
+-----------+-------+------+---------+
| 2000.0000 |  2000 | 2000 | 2000.00 |
|  666.6667 |   666 |  667 |  666.67 |
|  666.6667 |   666 |  667 |  666.67 |
| 2500.0000 |  2500 | 2500 | 2500.00 |
| 1333.3333 |  1333 | 1334 | 1333.33 |
+-----------+-------+------+---------+
```

こうした関数も使えるようになっておきましょう。
### 要点まとめ
- 数値関連の関数を使ってデータを抽出していきます。
    - 四則演算(+ - * / %)
    - ASによる別名
    - FLOOR()(端数を切り捨てたい場合)
    - CEIL()(端数を切り上げたい場合)
    - ROUND()(四捨五入したい場合)</details>


<details><summary>#22 文字列の関数を見ていこう</summary>

文字列を加工する関数について、よく使うものを見ていきましょう。文字列の一部を切り出す関数はSUMSTRING()を使います。では、 message と message の 3 文字目以降を切り出した文字列を抽出してみましょう。

```sql
SELECT message, SUBSTRING(message, 3) FROM posts;
```

もしくは、3文字目から2文字分を切り出したい場合は、このように書きます。

```sql
SELECT message, SUBSTRING(message, 3, 2) FROM posts;
```

もしくはマイナスの値を与えると、末尾から何文字分という意味になります。

```sql
SELECT message, SUBSTRING(message, -2) FROM posts;
```

```sql
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+---------+-----------------------+
| message | SUBSTRING(message, 3) |
+---------+-----------------------+
| Thanks  | anks                  |
| Merci   | rci                   |
| Arigato | igato                 |
| Gracias | acias                 |
| Danke   | nke                   |
+---------+-----------------------+
# 3文字目以降を切り出した文字列

+---------+--------------------------+
| message | SUBSTRING(message, 3, 2) |
+---------+--------------------------+
| Thanks  | an                       |
| Merci   | rc                       |
| Arigato | ig                       |
| Gracias | ac                       |
| Danke   | nk                       |
+---------+--------------------------+
# 3文字目から2文字分を切り出した文字列

+---------+------------------------+
| message | SUBSTRING(message, -2) |
+---------+------------------------+
| Thanks  | ks                     |
| Merci   | ci                     |
| Arigato | to                     |
| Gracias | as                     |
| Danke   | ke                     |
+---------+------------------------+
# 末尾から2文字分を切り出した文字列
```

次に文字列の連結ですが、 message といいねの数をハイフンで繋ぎたいなら、 CONCAT() (コンカット)という関数を使ってあげます。連結したい値をカンマ区切りで渡してあげればいいので、このように書いてあげてください。

```sql
SELECT CONCAT(message, ' - ', likes) FROM posts;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
+-------------------------------+
| CONCAT(message, ' - ', likes) |
+-------------------------------+
| Thanks - 12                   |
| Merci - 4                     |
| Arigato - 4                   |
| Gracias - 15                  |
| Danke - 8                     |
+-------------------------------+
```

文字数をLENGTH関数で求めることができます。では、messageとmessageの文字数を抽出してみましょう。

```sql
SELECT message, LENGTH(message) AS len FROM posts;

+---------+------+
| message | len  |
+---------+------+
| Thanks  |    6 |
| Merci   |    5 |
| Arigato |    7 |
| Gracias |    7 |
| Danke   |    5 |
+---------+------+
```

このLENGTH()は、日本語のデータだと少しおかしなことになります。

```sql
INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  -- ('Arigato', 4),
  ('ありがとう', 4),
  ('Gracias', 15),
  ('Danke', 8);

-- SELECT message, SUBSTRING(message, 3) FROM posts;
-- SELECT message, SUBSTRING(message, 3, 2) FROM posts;
-- SELECT message, SUBSTRING(message, -2) FROM posts;

SELECT CONCAT(message, ' - ', likes) FROM posts;
SELECT message, LENGTH(message) AS len FROM posts;

+-------------------------------+
| CONCAT(message, ' - ', likes) |
+-------------------------------+
| Thanks - 12                   |
| Merci - 4                     |
| ありがとう - 4               　 |
| Gracias - 15                  |
| Danke - 8                     |
+-------------------------------+
+-----------------+------+
| message         | len  |
+-----------------+------+
| Thanks          |    6 |
| Merci           |    5 |
| ありがとう     　 |   15 |
| Gracias         |    7 |
| Danke           |    5 |
+-----------------+------+
# ありがとうの文字数が15と表示される
```

そのため、日本語の場合はLENGTH()ではなく、CHARLENGTH()にしてください。

```sql
INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  -- ('Arigato', 4),
  ('ありがとう', 4),
  ('Gracias', 15),
  ('Danke', 8);

-- SELECT message, SUBSTRING(message, 3) FROM posts;
-- SELECT message, SUBSTRING(message, 3, 2) FROM posts;
-- SELECT message, SUBSTRING(message, -2) FROM posts;

SELECT CONCAT(message, ' - ', likes) FROM posts;
SELECT message, LENGTH(message) AS len FROM posts;
SELECT message, CHAR_LENGTH(message) AS len FROM posts;

+-------------------------------+
| CONCAT(message, ' - ', likes) |
+-------------------------------+
| Thanks - 12                   |
| Merci - 4                     |
| ありがとう - 4                  |
| Gracias - 15                  |
| Danke - 8                     |
+-------------------------------+
+-----------------+------+
| message         | len  |
+-----------------+------+
| Thanks          |    6 |
| Merci           |    5 |
| ありがとう        |   15 |
| Gracias         |    7 |
| Danke           |    5 |
+-----------------+------+
+-----------------+------+
| message         | len  |
+-----------------+------+
| Thanks          |    6 |
| Merci           |    5 |
| ありがとう        |    5 |
| Gracias         |    7 |
| Danke           |    5 |
+-----------------+------+
```

日本語がおかしくなるのは、LENGTH()だけで、SUMSTRING()は正しく動作します。

```sql
INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  -- ('Arigato', 4),
  ('ありがとう', 4),
  ('Gracias', 15),
  ('Danke', 8);

SELECT message, SUBSTRING(message, 3) FROM posts;
SELECT message, SUBSTRING(message, 3, 2) FROM posts;
SELECT message, SUBSTRING(message, -2) FROM posts;

-- SELECT CONCAT(message, ' - ', likes) FROM posts;
-- SELECT message, LENGTH(message) AS len FROM posts;
-- SELECT message, CHAR_LENGTH(message) AS len FROM posts;

+-----------------+-----------------------+
| message         | SUBSTRING(message, 3) |
+-----------------+-----------------------+
| Thanks          | anks                  |
| Merci           | rci                   |
| ありがとう      　| がとう                 |
| Gracias         | acias                 |
| Danke           | nke                   |
+-----------------+-----------------------+
+-----------------+--------------------------+
| message         | SUBSTRING(message, 3, 2) |
+-----------------+--------------------------+
| Thanks          | an                       |
| Merci           | rc                       |
| ありがとう      　| がと                      |
| Gracias         | ac                       |
| Danke           | nk                       |
+-----------------+--------------------------+
+-----------------+------------------------+
| message         | SUBSTRING(message, -2) |
+-----------------+------------------------+
| Thanks          | ks                     |
| Merci           | ci                     |
| ありがとう      　| とう                    |
| Gracias         | as                     |
| Danke           | ke                     |
+-----------------+------------------------+
```

こうした操作もできるようになっておきましょう。

### 質問：CHAR_LENGTH は日本語のデータが含まれる場合に使用するのですか？
    
回答：はい、マルチバイト文字列が含まれる場合に使用します。

マルチバイト文字列の場合利用するのですが、日本語以外あまり馴染みがありませんので日本語を含む場合使用するという理解で大丈夫かと思います。
### 要点まとめ
- 文字列関連の関数を使ってデータを抽出していきます。
    - SUBSTRING( )(文字列の一部を切り出す)
    - CONCAT( )(文字列の連結)
    - LENGTH( )(文字数を求める)
	  - CHAR_LENGTH( )(日本語を含む場合に使用し、文字数を求める)</details>


<details><summary>#23 日時の関数を見ていこう</summary>

日時周りの関数を使ってみましょう。今回createdカラムを追加して、’ありがとう’は英語に直しました。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  created DATETIME,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, created) VALUES
  ('Thanks', 12, '2020-05-01'),
  ('Merci', 4, '2020-05-03'),
  ('Arigato', 4, '2020-06-14'),
  ('Gracias', 15, '2020-07-04'),
  ('Danke', 8, '2020-08-22');
```

created から年月日を抜き出したかったとします。その場合ですが、それぞれ YEAR 関数、 MONTH 関数、 DAY 関数を使ってあげれば OK です。

```sql
SELECT created, YEAR(created) FROM posts;
SELECT created, MONTH(created) FROM posts;
SELECT created, DAY(created) FROM posts;

+---------------------+---------------+
| created             | YEAR(created) |
+---------------------+---------------+
| 2020-05-01 00:00:00 |          2020 |
| 2020-05-03 00:00:00 |          2020 |
| 2020-06-14 00:00:00 |          2020 |
| 2020-07-04 00:00:00 |          2020 |
| 2020-08-22 00:00:00 |          2020 |
+---------------------+---------------+
+---------------------+----------------+
| created             | MONTH(created) |
+---------------------+----------------+
| 2020-05-01 00:00:00 |              5 |
| 2020-05-03 00:00:00 |              5 |
| 2020-06-14 00:00:00 |              6 |
| 2020-07-04 00:00:00 |              7 |
| 2020-08-22 00:00:00 |              8 |
+---------------------+----------------+
+---------------------+--------------+
| created             | DAY(created) |
+---------------------+--------------+
| 2020-05-01 00:00:00 |            1 |
| 2020-05-03 00:00:00 |            3 |
| 2020-06-14 00:00:00 |           14 |
| 2020-07-04 00:00:00 |            4 |
| 2020-08-22 00:00:00 |           22 |
+---------------------+--------------+
# 年、月、日、それぞれが抽出される
```

好きなフォーマットで抽出したい場合、 DATE_FORMAT() という関数が使えます。フォーマットのための記号はいくつかあるのですが、 DATE_FORMAT() を使って created を英語表記の月、日、年、曜日としたい場合は、このように書いてあげてください。そして、別名を付けてわかりやすく表示します。

```sql
SELECT
  created,
  DATE_FORMAT(created, '%M %D %Y, %W') AS date
FROM
  posts;

+---------------------+----------------------------+
| created             | date                       |
+---------------------+----------------------------+
| 2020-05-01 00:00:00 | May 1st 2020, Friday       |
| 2020-05-03 00:00:00 | May 3rd 2020, Sunday       |
| 2020-06-14 00:00:00 | June 14th 2020, Sunday     |
| 2020-07-04 00:00:00 | July 4th 2020, Saturday    |
| 2020-08-22 00:00:00 | August 22nd 2020, Saturday |
+---------------------+----------------------------+
```

日付の計算などもできて、たとえば created から 7 日後を抽出したい場合は DATE_ADD() を使って 7 日後は INTERVAL 7 DAY としてあげてください。別名はわかりやすくnextとします。

```sql
SELECT
  created,
  DATE_FORMAT(created, '%M %D %Y, %W') AS date
FROM
  posts;
  
SELECT
  created,
  DATE_ADD(created, INTERVAL 7 DAY) AS next
FROM
  posts;

+---------------------+----------------------------+
| created             | date                       |
+---------------------+----------------------------+
| 2020-05-01 00:00:00 | May 1st 2020, Friday       |
| 2020-05-03 00:00:00 | May 3rd 2020, Sunday       |
| 2020-06-14 00:00:00 | June 14th 2020, Sunday     |
| 2020-07-04 00:00:00 | July 4th 2020, Saturday    |
| 2020-08-22 00:00:00 | August 22nd 2020, Saturday |
+---------------------+----------------------------+
+---------------------+---------------------+
| created             | next                |
+---------------------+---------------------+
| 2020-05-01 00:00:00 | 2020-05-08 00:00:00 |
| 2020-05-03 00:00:00 | 2020-05-10 00:00:00 |
| 2020-06-14 00:00:00 | 2020-06-21 00:00:00 |
| 2020-07-04 00:00:00 | 2020-07-11 00:00:00 |
| 2020-08-22 00:00:00 | 2020-08-29 00:00:00 |
+---------------------+---------------------+
```

現在の日付よりどれくらい前かを知りたいなら、 DATEDIFF() という関数が使えます。

```sql
SELECT
  created,
  DATE_FORMAT(created, '%M %D %Y, %W') AS date
FROM
  posts;
  
SELECT
  created,
  DATE_ADD(created, INTERVAL 7 DAY) AS next
FROM
  posts;
  
SELECT
  created,
  NOW(),
  DATEDIFF(created, NOW()) AS diff
FROM
  posts;

+---------------------+----------------------------+
| created             | date                       |
+---------------------+----------------------------+
| 2020-05-01 00:00:00 | May 1st 2020, Friday       |
| 2020-05-03 00:00:00 | May 3rd 2020, Sunday       |
| 2020-06-14 00:00:00 | June 14th 2020, Sunday     |
| 2020-07-04 00:00:00 | July 4th 2020, Saturday    |
| 2020-08-22 00:00:00 | August 22nd 2020, Saturday |
+---------------------+----------------------------+
+---------------------+---------------------+
| created             | next                |
+---------------------+---------------------+
| 2020-05-01 00:00:00 | 2020-05-08 00:00:00 |
| 2020-05-03 00:00:00 | 2020-05-10 00:00:00 |
| 2020-06-14 00:00:00 | 2020-06-21 00:00:00 |
| 2020-07-04 00:00:00 | 2020-07-11 00:00:00 |
| 2020-08-22 00:00:00 | 2020-08-29 00:00:00 |
+---------------------+---------------------+
+---------------------+---------------------+------+
| created             | NOW()               | diff |
+---------------------+---------------------+------+
| 2020-05-01 00:00:00 | 2022-05-28 08:17:05 | -757 |
| 2020-05-03 00:00:00 | 2022-05-28 08:17:05 | -755 |
| 2020-06-14 00:00:00 | 2022-05-28 08:17:05 | -713 |
| 2020-07-04 00:00:00 | 2022-05-28 08:17:05 | -693 |
| 2020-08-22 00:00:00 | 2022-05-28 08:17:05 | -644 |
+---------------------+---------------------+------+
# diffはdifferenceの略で、差分を意味する
```

日時に関する関数は他にもありますが、こうした操作もできるようになっておきましょう。

### 質問：DATE_ADD で表示したレコードをフォーマットする方法はありますか？
    
回答：具体的なコードで説明していきます。

なるほど、確かに DATE_ADD した後の日付を DATE_FORMAT で好きな形式で表示出来たら便利ですね。

これは可能です！

以下を見てください。

```lisp
DATE_FORMAT(DATE_ADD(created, INTERVAL 7 DAY), '%M %D %Y, %W') AS next

```

こんな感じで created が入っていた部分をそのまま `DATE_ADD(created, INTERVAL 7 DAY)` に入れ替えるだけで、足された日付を形式を指定して表示、という事が出来るわけですね。
### 要点まとめ
- 日時関連の関数を使ってデータを抽出していきます。
    - YEAR( )
    - MONTH( )
    - DAY( )
    - DATE_FORMAT( )(日付表記を好きなフォーマットで抽出する)
    - DATE_ADD( )(日付の計算(プラス))
		- DATEDIFF( )(現在の日付からどれくらい前なのかを知る)</details>


<details><summary>#24 レコードの更新をしてみよう</summary>

これまでデータを加工して抽出する方法について見ていきましたが、たとえば `likes` に 10 を渡して抽出したとしても、元データの値が変わるわけではありません。したがって、この後に `SELECT * FROM posts` としても、ちゃんと元データが表示されるはずです。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  ('Arigato', 4),
  ('Gracias', 15),
  ('Danke', 8);
  
SELECT likes + 10 FROM posts;
SELECT * FROM posts;

+------------+
| likes + 10 |
+------------+
|         22 |
|         14 |
|         14 |
|         25 |
|         18 |
+------------+
+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  2 | Merci   |     4 |
|  3 | Arigato |     4 |
|  4 | Gracias |    15 |
|  5 | Danke   |     8 |
+----+---------+-------+
# 元のデータは変更されていない
```

ただ、元のデータ自体を更新したい場合もあります。その場合はUPDATEを使います。UPDATE テーブル名 SET で、更新したいカラムに新しい値をセットしてあげます。

```sql
-- SELECT likes + 10 FROM posts;
UPDATE posts SET likes = likes + 5;
SELECT * FROM posts;
```

このようにすればすべてのレコードが更新されますが、条件をつけて特定のレコードだけ更新することもできます。では likes が 10 以上のものについて likes を 5 増やしてみましょう。

```sql
-- SELECT likes + 10 FROM posts;
UPDATE posts SET likes = likes + 5 WHERE likes >= 10;
SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    17 |
|  2 | Merci   |     4 |
|  3 | Arigato |     4 |
|  4 | Gracias |    20 |
|  5 | Danke   |     8 |
+----+---------+-------+
# 10以上のレコードだけ5点増えているのが分かる
```

複数のカラムの値を一気に更新することもできます。`UPDATE テーブル名 SET` で likes は今の likes より 5 点増やしてね、と書いてあげます。複数のカラムを更新するにはカンマ区切りで書いていきます。message を UPPER() の message にしてあげましょう。likes が 10 以上のレコードについてそのような処理をしてみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  ('Arigato', 4),
  ('Gracias', 15),
  ('Danke', 8);
  
-- SELECT likes + 10 FROM posts;
-- UPDATE posts SET likes = likes + 5 WHERE likes >= 10;
UPDATE
  posts
SET
  likes = likes + 5,
  message = UPPER(message) # UPPER()は全てを大文字にする関数
WHERE
  likes >= 10;
SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | THANKS  |    17 |
|  2 | Merci   |     4 |
|  3 | Arigato |     4 |
|  4 | GRACIAS |    20 |
|  5 | Danke   |     8 |
+----+---------+-------+
# likesが10以上のレコードにlikesの数を5点増やし、大文字に変更する 

```

こうしたデータの更新もできるようになっておきましょう。
### 要点まとめ
- UPDATEを使ってレコードを更新する方法を見ていきます。
    - UPDATE(データ自体を更新)
		- UPPER(全てを大文字にする関数)</details>


<details><summary>#25 レコードの削除をしてみよう</summary>

こうすると、全てのレコードを削除するのですが、通常は条件を付けるので　likes が 10 より小さいものを削除してみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  ('Arigato', 4),
  ('Gracias', 15),
  ('Danke', 8);
  
DELETE FROM posts WHERE likes < 10;

SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  4 | Gracias |    15 |
+----+---------+-------+
# likesが10より小さいものが削除されている
```

レコードを削除したあとにデータを挿入した場合に、連番がどうなるか見ておきましょう。この時点で 1 から 5 まであったデータが、 1 と 4 だけになったのですが、次が 2 になるのか、 5 になるのか、 6 になるのか気になるところです。

削除したあとに INSERT 文でデータを挿入してみましょう。適当なデータを入れてみます。見てあげると…、こうですね、一度使われた連番は使われずに次の連番である 6 になっているので、こうした挙動にも注意しておきましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  ('Arigato', 4),
  ('Gracias', 15),
  ('Danke', 8);
  
DELETE FROM posts WHERE likes < 10;

INSERT INTO posts (message, likes) VALUES ('Xie Xie', 10);

SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Thanks  |    12 |
|  4 | Gracias |    15 |
|  6 | Xie Xie |    10 |
+----+---------+-------+
# 一度使われた連番は使われずに次の連番である6になっている
```

全件削除した場合でもこれは同じで、こちらで `DELETE FROM posts;` としたあとに INSERT とすると、これでも 6 になっているので注意しておいてください。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  ('Arigato', 4),
  ('Gracias', 15),
  ('Danke', 8);
  
-- DELETE FROM posts WHERE likes < 10;
DELETE FROM posts;

INSERT INTO posts (message, likes) VALUES ('Xie Xie', 10);

SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  6 | Xie Xie |    10 |
+----+---------+-------+
# 全権削除した場合でもidは6になっているので注意
```

ただ、ここで連番を最初から振り直したい場合もあります。その場合は、テーブルごと削除して再作成する、TRUNCATE(トランケイト)を使います。`TRUNCATE TABLE テーブル名`とします。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('Thanks', 12),
  ('Merci', 4),
  ('Arigato', 4),
  ('Gracias', 15),
  ('Danke', 8);
  
-- DELETE FROM posts WHERE likes < 10;
-- DELETE FROM posts;
TRUNCATE TABLE posts;

INSERT INTO posts (message, likes) VALUES ('Xie Xie', 10);

SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | Xie Xie |    10 |
+----+---------+-------+
# 連番がリセットされるため、idが1からとなる
```

### 質問：DROP から CREATE するのと TRUNCATE に違いはありますか？
    
回答：同じと考えて大丈夫ですが、TRUNCATE は 1 つのクエリで完了するので効率的です。

`DROP`して`CREATE`しているのと同じと考えていただいていいと思いますが、大きな違いとしては`TRUNCATE`のクエリのみで完了するので`DROP`して`CREATE`するより効率的です。
### 要点まとめ
- DELETEやTRUNCATEを使ってレコードを削除する方法について見ていきます。
    - DELETE(レコードを削除する。通常は条件を付ける)
    - AUTO_INCREMENTの挙動(指定したカラム(フィールド)に対してデータが追加されると、MySQLが一意の値を自動的に付与する機能のこと、カラムに登録されたデータに連番を自動で付ける際に便利)
	  - TRUNCATE TABLE(テーブルを削除して、再作成する)</details>


<details><summary>
