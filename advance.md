# 💻ドットインストール MySQL入門 応用編

<details><summary>#01 集計関数を使っていこう</summary>

データ集計や複数のテーブルを扱っていく応用編に入っていきましょう。コードについては基礎編で使った posts テーブルを使っていきますが、 message は分かりやすく連番にしてあるのと、集計や分析を扱っていきたいので、少しレコードを増やしてあります。今、9レコードある状態です。それから今回は、説明の都合上post-5、post-8をNULLにしておきます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('post-1', 12),
  ('post-2', 8),
  ('post-3', 11),
  ('post-4', 3),
  ('post-5', NULL),
  ('post-6', 9),
  ('post-7', 4),
  ('post-8', NULL),
  ('post-9', 31);
```

今回こちらのデータを使って、集計処理について見ていきます。まず、データの個数を調べるには COUNT 関数を使います。今 likes にはふたつ NULL があるのですが、 likes の NULL を除いた個数を調べるには `COUNT(likes)` としてあげれば OK です。もしくは、単に全体の行数を取得したいなら NULL ではないことが保証されている主キーで数えてあげるといいでしょう。したがって、 `COUNT(id)` としてあげればいいですね。もしくは `SELECT COUNT(*)` としても全体の行数を取得することができます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('post-1', 12),
  ('post-2', 8),
  ('post-3', 11),
  ('post-4', 3),
  ('post-5', NULL),
  ('post-6', 9),
  ('post-7', 4),
  ('post-8', NULL),
  ('post-9', 31);
  
SELECT COUNT(likes) FROM posts; # likesの数だけを取得
SELECT COUNT(id) FROM posts; # idの数を取得
SELECT COUNT(*) FROM posts; # 全体の行数を取得

+--------------+
| COUNT(likes) |
+--------------+
|            7 |
+--------------+
# likesにふたつNULLがあるので7
+-----------+
| COUNT(id) |
+-----------+
|         9 |
+-----------+
+----------+
| COUNT(*) |
+----------+
|        9 |
+----------+
# 全体の行数はどちらも9行になっている
```

合計や平均などを求めることもできます。

- likesの合計を求めてみましょう、その場合はSUM関数を使えばOKです。
- 平均だったらAVG関数。
- 最大値はMAX関数。
- 最小値はMIN関数。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes) VALUES
  ('post-1', 12),
  ('post-2', 8),
  ('post-3', 11),
  ('post-4', 3),
  ('post-5', NULL),
  ('post-6', 9),
  ('post-7', 4),
  ('post-8', NULL),
  ('post-9', 31);
  
-- SELECT COUNT(likes) FROM posts;
-- SELECT COUNT(id) FROM posts;
-- SELECT COUNT(*) FROM posts;

SELECT SUM(likes) FROM posts; # SUMは合計値
SELECT AVG(likes) FROM posts; # AVGは平均値
SELECT MAX(likes) FROM posts; # MAXは最大値
SELECT MIN(likes) FROM posts; # MINは最小値

+------------+
| SUM(likes) |
+------------+
|         78 |
+------------+
+------------+
| AVG(likes) |
+------------+
|    11.1429 |
+------------+
+------------+
| MAX(likes) |
+------------+
|         31 |
+------------+
+------------+
| MIN(likes) |
+------------+
|          3 |
+------------+
```

集計のための関数も使えるようになっておきましょう。
### 要点まとめ
合計や平均など、集計のための関数を扱う方法について見ていきます。

- COUNT()：データの個数を調べる
- SUM()：合計値
- AVG()：平均値
- MAX()：最大値
- MIN()：最小値</details>


<details><summary>#02 GROUP BYでグループ化しよう</summary>

どこでこの投稿がされたかを表すareaというカラムを追加してみましょう。データの挿入もしておきます。areaカラムを足して、適当なデータを追加していきましょう。area として、 'Tokyo' と 'Fukuoka' と 'Osaka' を追加したとしましょう。では、 `SELECT * FROM posts` で見てあげます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20), # areaというカラムを追加
  PRIMARY KEY (id)
);

#areaカラムを足して、適当なデータを追加していく
INSERT INTO posts (message, likes, area) VALUES 
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

SELECT * FROM posts;

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  1 | post-1  |    12 | Tokyo   |
|  2 | post-2  |     8 | Fukuoka |
|  3 | post-3  |    11 | Tokyo   |
|  4 | post-4  |     3 | Osaka   |
|  5 | post-5  |     8 | Tokyo   |
|  6 | post-6  |     9 | Osaka   |
|  7 | post-7  |     4 | Tokyo   |
|  8 | post-8  |    10 | Osaka   |
|  9 | post-9  |    31 | Fukuoka |
+----+---------+-------+---------+
```

ここでこのデータにどの area が含まれているか、一覧で見たかったとします。その場合、 DISTINCT というキーワードを使えば、重複を省いたデータを抽出することができます。`DISTINCT area` としてみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

-- SELECT * FROM posts;
SELECT DISTINCT area FROM posts; # DISTINCTで重複したデータを抽出することが可能

+---------+
| area    |
+---------+
| Tokyo   |
| Fukuoka |
| Osaka   |
+---------+
# areaの一覧が表示される
```

こうした操作もできるようになっておきましょう。

次に、このarea毎のいいね数の合計が欲しかった場合、レコードをグループ化してくれるGROUP BYが使えます。合計なので SUM の likes としてあげて、 GROUP BY で area ごとの合計なので、 area を指定してあげましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

-- SELECT * FROM posts;
-- SELECT DISTINCT area FROM posts;
SELECT area, SUM(likes) FROM posts GROUP BY area;

+---------+------------+
| area    | SUM(likes) |
+---------+------------+
| Fukuoka |         39 |
| Osaka   |         22 |
| Tokyo   |         35 |
+---------+------------+
# area毎のlikesの合計が表示されている
```

こうした集計もできるようになっておきましょう。
### 要点まとめ
GROPU BYを使って、エリアごとの合計を集計する方法について見ていきます。

- DISTINCT：重複を省いたデータを抽出することができる。
- GROUP BY：レコードをグループ化してくれる。</details>


<details><summary>#03 HAVINGで抽出条件を指定しよう</summary>

今はarea毎のlikesの合計が出ている状態です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

SELECT area, SUM(likes) FROM posts GROUP BY area;

+---------+------------+
| area    | SUM(likes) |
+---------+------------+
| Fukuoka |         39 |
| Osaka   |         22 |
| Tokyo   |         35 |
+---------+------------+
```

SUMの結果から30より大きなものだけを抽出したかったとします。その場合ですが、長くなるので改行を入れながら書いてあげると、この結果で likes の合計が 30 より大きいものを抽出したいので、 WHERE を使って、このように書きたいところですが、実はこれではうまくいきません。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

-- SELECT area, SUM(likes) FROM posts GROUP BY area;

SELECT
  area,
  SUM(likes)
FROM
  posts
GROUP BY
  area
WHERE
  SUM(likes) > 30;

ERROR 1064 (42000) at line 23: You have an error in your SQL syntax;
 check the manual that corresponds to your MariaDB server version for
 the right syntax to use near 'WHERE
  SUM(likes) > 30' at line 8
# エラーが表示される
```

これは、 WHERE が GROUP BY より前に処理されるので、後ろに書いてはいけないというルールがあるからです。もし GROUP BY した結果に条件を付けたかったら WHERE ではなく、 HAVING という命令を使ってあげる必要があります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

-- SELECT area, SUM(likes) FROM posts GROUP BY area;

SELECT
  area,
  SUM(likes)
FROM
  posts
GROUP BY
  area
-- WHERE
HAVING # GROUP BYした結果に条件をつけたい場合は、WHEREではなく、HAVINGで命令すること
  SUM(likes) > 30;

+---------+------------+
| area    | SUM(likes) |
+---------+------------+
| Fukuoka |         39 |
| Tokyo   |         35 |
+---------+------------+
# GROUP BYを使った時はこの点に注意しておくこと
```

WHEREと組み合わせて使った例を見てみましょう。GROUP BY より前に書く必要があるので、たとえばということで、こちらで WHERE likes が 10 より大きいもの、と書いてあげましょう。その場合ですが、① posts の全てのレコードから likes が 10 より大きいものだけを抽出したあとに ②GROUP BY してくれます。今回だと likes が 10 より大きいものなので、 Tokyo の 12 、 Tokyo の 11 、 Fukuoka の 31 がグループ化して集計されるはずです。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

-- SELECT area, SUM(likes) FROM posts GROUP BY area;

-- SELECT
--   area,
--   SUM(likes)
-- FROM
--   posts
-- GROUP BY
--   area
-- -- WHERE
-- HAVING
--   SUM(likes) > 30;

SELECT
  area,
  SUM(likes)
FROM
  posts
WHERE
  likes > 10
GROUP BY
  area;
# ① posts の全てのレコードから likes が 10 より大きいものだけを抽出したあとに ②GROUP BY してくれる

+---------+------------+
| area    | SUM(likes) |
+---------+------------+
| Fukuoka |         31 |
| Tokyo   |         23 |
+---------+------------+
```

こうした挙動も理解しておきましょう。
### 質問：WHERE句にSUMを入れるとエラーになりました。
回答：WHERE句にSUMなどの集約関数を指定することはできません。

WHERE句にSUMなどの集約関数は指定できません。集約した値を条件として指定したい場合にはHAVINGを使ってください。
### 要点まとめ
GROPU BYした結果に抽出条件を設定できるHAVINGについて見ていきます。

- HAVING：GROUP BYで出した結果に抽出条件を設定する場合に使う
- WHEREを使う場合の注意点：WHEREはGROUP BYより先に処理されるので、GROUP BYより後ろに書いてはいけないというルールがある</details>


<details><summary>#04 IF()、CASEを扱ってみよう</summary>

条件によって違う値を抽出する方法について見ていきましょう。たとえば、 likes の数に応じてチームを分けてみたいといったシーンを想定してみます。では、 10 より大きかったら A チーム、それ以外は B チームとしたい場合、 IF likes が 10 より大きかったら A 、そうじゃなかったら B と書いてあげます。分かりやすく team という別名にしてあげて、抽出してみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');
  
SELECT
  *,
  IF(likes > 10, 'A', 'B') AS team # likesの数が10より多い場合はAチーム、それ以外はBチーム
FROM
  posts;

+----+---------+-------+---------+------+
| id | message | likes | area    | team |
+----+---------+-------+---------+------+
|  1 | post-1  |    12 | Tokyo   | A    |
|  2 | post-2  |     8 | Fukuoka | B    |
|  3 | post-3  |    11 | Tokyo   | A    |
|  4 | post-4  |     3 | Osaka   | B    |
|  5 | post-5  |     8 | Tokyo   | B    |
|  6 | post-6  |     9 | Osaka   | B    |
|  7 | post-7  |     4 | Tokyo   | B    |
|  8 | post-8  |    10 | Osaka   | B    |
|  9 | post-9  |    31 | Fukuoka | A    |
+----+---------+-------+---------+------+
```

もしくは CASE という命令も使えて、その場合はいくらでも条件を増やせるので、たとえば、 likes が 10 より大きかったら A チーム、 10 以下だけど 5 より大きかったら B チーム、そしてそれ以外は C チームと書きたい場合は、このように書いてあげれば OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');
  
-- SELECT
--   *,
--   IF(likes > 10, 'A', 'B') AS team
-- FROM
--   posts;

SELECT
  *,
  CASE
    WHEN likes > 10 THEN 'A' # likesの数が10より多い場合はAチーム
    WHEN likes > 5 THEN 'B' # likesの数が10以下だけど、5より多い場合はBチーム
    ELSE 'C' # それ以外はCチーム
  END AS team
FROM
  posts;

+----+---------+-------+---------+------+
| id | message | likes | area    | team |
+----+---------+-------+---------+------+
|  1 | post-1  |    12 | Tokyo   | A    |
|  2 | post-2  |     8 | Fukuoka | B    |
|  3 | post-3  |    11 | Tokyo   | A    |
|  4 | post-4  |     3 | Osaka   | C    |
|  5 | post-5  |     8 | Tokyo   | B    |
|  6 | post-6  |     9 | Osaka   | B    |
|  7 | post-7  |     4 | Tokyo   | C    |
|  8 | post-8  |    10 | Osaka   | B    |
|  9 | post-9  |    31 | Fukuoka | A    |
+----+---------+-------+---------+------+
```

こうした処理も書けるようになっておきましょう。
### 質問：IFの省略した書き方がよくわかりません。
回答：ドキュメントを参照してみると良いでしょう。

`IF ( likes > 10,  'A',  'B' )  AS team  FROM  posts;`

１つ目の条件式(likes > 10)が真の場合２番目の値(’A’)を、１つ目の条件式(likes > 10)が偽の場合３番目(’B’)の値を返します。

こちらも参照してみてください。[https://dev.mysql.com/doc/refman/5.6/ja/control-flow-functions.html#function_if](https://dev.mysql.com/doc/refman/5.6/ja/control-flow-functions.html#function_if)

### 要点まとめ
条件によって違う値を抽出する方法について見ていきます。

- IF()：条件によって違う値を抽出する方法
- CASE：IFと違い、いくらでも条件を増やすことができる</details>


<details><summary>#05 抽出結果を別のテーブルにしよう</summary>

抽出結果を別テーブルとして切り出す方法を見ていきます。`CREATE TABLE テーブル名`としたあとに AS に続けて抽出条件を指定してあげれば OK です。今回は area が Tokyo のレコードだけを抽出して posts_tokyo というテーブルにしてあげましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
```

この考え方を使えばテーブルのコピーを作ることもできますね。テーブル名は posts_copy としてあげて、 AS に続ける抽出条件で WHERE を外してあげれば、全てのレコードが posts_copy にコピーされるはずです。

```sql
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
CREATE TABLE posts_copy AS SELECT * FROM posts;
```

データの構造だけコピーして中身のレコードはいらないよ、という場合は LIKE テーブル名としてあげれば OK です。

```sql
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
CREATE TABLE posts_copy AS SELECT * FROM posts;
CREATE TABLE posts_skeleton LIKE posts;
```

それから、これらのテーブルが既に存在していたらエラーになってしまうので、実行する度にそれぞれのテーブルを消してあげたいので DROP TABLE IF EXISTS を使っておいてあげましょう。

```sql
DROP TABLE IF EXISTS posts_tokyo;
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
CREATE TABLE posts_copy AS SELECT * FROM posts;
CREATE TABLE posts_skeleton LIKE posts;
```

では、 posts_tokyo 、 posts_copy 、 posts_skeleton が存在していたら、削除すると書いておきましょう。そのうえで SHOW TABLES でテーブルの一覧を表示してみます。それから、それぞれのテーブルの中身も表示してみましょう。posts_tokyo の中身、そして posts_copy の中身、そして skeleton のほうは中身が何もないので、表示はされませんが、一応確かめてみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

DROP TABLE IF EXISTS posts_tokyo;
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';

DROP TABLE IF EXISTS posts_copy;
CREATE TABLE posts_copy AS SELECT * FROM posts;

DROP TABLE IF EXISTS posts_skeleton;
CREATE TABLE posts_skeleton LIKE posts;

SHOW TABLES;
SELECT * FROM posts_tokyo;
SELECT * FROM posts_copy;
SELECT * FROM posts_skeleton;

+-----------------+
| Tables_in_myapp |
+-----------------+
| posts           |
| posts_copy      |
| posts_skeleton  |
| posts_tokyo     |
+-----------------+
# SHOW TABLES;
+----+---------+-------+-------+
| id | message | likes | area  |
+----+---------+-------+-------+
|  1 | post-1  |    12 | Tokyo |
|  3 | post-3  |    11 | Tokyo |
|  5 | post-5  |     8 | Tokyo |
|  7 | post-7  |     4 | Tokyo |
+----+---------+-------+-------+
# SELECT * FROM posts_tokyo;
+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  1 | post-1  |    12 | Tokyo   |
|  2 | post-2  |     8 | Fukuoka |
|  3 | post-3  |    11 | Tokyo   |
|  4 | post-4  |     3 | Osaka   |
|  5 | post-5  |     8 | Tokyo   |
|  6 | post-6  |     9 | Osaka   |
|  7 | post-7  |     4 | Tokyo   |
|  8 | post-8  |    10 | Osaka   |
|  9 | post-9  |    31 | Fukuoka |
+----+---------+-------+---------+
# SELECT * FROM posts_copy;

# SELECT * FROM posts_skeleton;はからのテーブルなので何も表示されない
```

3 つのテーブルが追加されていて、次が Tokyo だけのテーブル、その次がコピーなので全てのレコード、そして最後は空のテーブルで何も表示されていないので、うまくいっているようです。

テーブルから別のテーブルを作る方法も知っておきましょう。
### 要点まとめ
抽出結果を別テーブルとして切り出す方法を見ていきます。

- CREATE TABLE ... AS ...：テーブルから別テーブルを作成する
- CREATE TABLE ... LIKE ...：データの構造だけコピーして中身のレコードはいらないよ、という場合は`LIKE テーブル名`とする</details>


<details><summary>#06 VIEWを扱ってみよう</summary>

コードのほうは posts_tokyo だけにしておきました。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

DROP TABLE IF EXISTS posts_tokyo;
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
```

この posts_tokyo ですが、 posts テーブルから一部のデータを抽出した別のテーブルなので、当然ですがこちらを更新しても posts_tokyo が自動的に更新されるという訳ではありません。

posts テーブルを更新していきます。id が 1 のレコードの likes を 15 にしてあげましょう。そのうえで posts テーブルと、 posts_tokyo テーブルの中身を確認してみます。posts のほうは更新されますが、 posts_tokyo のほうは更新されないはずです。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

DROP TABLE IF EXISTS posts_tokyo;
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';

UPDATE posts SET likes = 15 WHERE id = 1; # idが1のレコードのlikesの数を15にする

SELECT * FROM posts;
SELECT * FROM posts_tokyo;

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  1 | post-1  |    15 | Tokyo   |
|  2 | post-2  |     8 | Fukuoka |
|  3 | post-3  |    11 | Tokyo   |
|  4 | post-4  |     3 | Osaka   |
|  5 | post-5  |     8 | Tokyo   |
|  6 | post-6  |     9 | Osaka   |
|  7 | post-7  |     4 | Tokyo   |
|  8 | post-8  |    10 | Osaka   |
|  9 | post-9  |    31 | Fukuoka |
+----+---------+-------+---------+
# postsテーブルは15に更新されている
+----+---------+-------+-------+
| id | message | likes | area  |
+----+---------+-------+-------+
|  1 | post-1  |    12 | Tokyo |
|  3 | post-3  |    11 | Tokyo |
|  5 | post-5  |     8 | Tokyo |
|  7 | post-7  |     4 | Tokyo |
+----+---------+-------+-------+
# posts_tokyoは12のまま
```

VIEW という仕組みを使えば元テーブルと連動する仮想的なテーブルを作ることができます。どうするかというと、 CREATE VIEW としてあげます。名前も分かりやすく posts_tokyo_view にしてあげましょう。この VIEW ですが、こちらの抽出条件だけを保持した仮想的なテーブルで実行する度に、元データから再度値を抽出してくれるという仕組みになっています。

```sql
DROP TABLE IF EXISTS posts_tokyo;
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';

CREATE VIEW posts_tokyo_view AS SELECT * FROM posts WHERE area = 'Tokyo';

UPDATE posts SET likes = 15 WHERE id = 1;

SELECT * FROM posts;
SELECT * FROM posts_tokyo;
```

それから既にこの VIEW があるとエラーになってしまうので、こちらで削除しておきましょう。posts_tokyo のほうはコメントにしてあげて、 posts_tokyo_view の中身を最後に確認してあげます。

```sql
-- DROP TABLE IF EXISTS posts_tokyo;
-- CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';

DROP VIEW IF EXISTS posts_tokyo_view;
CREATE VIEW posts_tokyo_view AS SELECT * FROM posts WHERE area = 'Tokyo';

UPDATE posts SET likes = 15 WHERE id = 1;

SELECT * FROM posts;
-- SELECT * FROM posts_tokyo;
SELECT * FROM posts_tokyo_view;

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  1 | post-1  |    15 | Tokyo   |
|  2 | post-2  |     8 | Fukuoka |
|  3 | post-3  |    11 | Tokyo   |
|  4 | post-4  |     3 | Osaka   |
|  5 | post-5  |     8 | Tokyo   |
|  6 | post-6  |     9 | Osaka   |
|  7 | post-7  |     4 | Tokyo   |
|  8 | post-8  |    10 | Osaka   |
|  9 | post-9  |    31 | Fukuoka |
+----+---------+-------+---------+
# postsが15
+----+---------+-------+-------+
| id | message | likes | area  |
+----+---------+-------+-------+
|  1 | post-1  |    15 | Tokyo |
|  3 | post-3  |    11 | Tokyo |
|  5 | post-5  |     8 | Tokyo |
|  7 | post-7  |     4 | Tokyo |
+----+---------+-------+-------+
# VIEWは実行する度に、データを抽出するので、こちらも15になっている
```

こうしたVIEWも使いこなせるようになっておきましょう。
### 質問：VIEWテーブルの値はいつ更新されているのですか？
回答：VIEWを取得する度に最新の情報が抽出されます。

`VIEW`は抽出条件を保存した仮想的なテーブルです。

値そのものを保持しているわけではなく、元のテーブルから抽出する条件を保存しているものと考えてください。

ですので、元テーブルが`UPDATE`されたタイミングで`VIEW`で参照するものも更新されています。
### 要点まとめ
抽出結果だけを保持することのできるVIEWについて見ていきます。

- VIEW：元テーブルと連動する仮想的なテーブルを作ることができる
- DROP VIEW：作成済みのビューを削除する</details>


<details><summary>#07 UNIONで抽出結果をまとめよう

UNION について見ていきたいのですが、例を出していきます。likesの大きい順に並び替えてみましょう。`ORDER BY likes DESC` でいいですね。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

SELECT * FROM posts ORDER BY likes DESC; # DESCは大きい(多い)順に並び替える命令

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  9 | post-9  |    31 | Fukuoka |
|  1 | post-1  |    12 | Tokyo   |
|  3 | post-3  |    11 | Tokyo   |
|  8 | post-8  |    10 | Osaka   |
|  6 | post-6  |     9 | Osaka   |
|  2 | post-2  |     8 | Fukuoka |
|  5 | post-5  |     8 | Tokyo   |
|  7 | post-7  |     4 | Tokyo   |
|  4 | post-4  |     3 | Osaka   |
+----+---------+-------+---------+
# DESCでlikesの多い順に並んでいる
```

では、ここでこちらの上位 3 名と最後の 1 つだけを抽出したかったとします。その場合のクエリを書いていきましょう。

- まず上位3名は、そのまま`LIMIT 3`としてあげればいいですね。
- 1番下のレコードは、小さい順に並び替えて1個取り出してあげればOKです。

SELECT * FROM posts ORDER BY likes DESC;をコメントにして、コードを実行します。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

-- SELECT * FROM posts ORDER BY likes DESC;

SELECT * FROM posts ORDER BY likes DESC LIMIT 3;
SELECT * FROM posts ORDER BY likes LIMIT 1;

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  9 | post-9  |    31 | Fukuoka |
|  1 | post-1  |    12 | Tokyo   |
|  3 | post-3  |    11 | Tokyo   |
+----+---------+-------+---------+
+----+---------+-------+-------+
| id | message | likes | area  |
+----+---------+-------+-------+
|  4 | post-4  |     3 | Osaka |
+----+---------+-------+-------+
# 上位3名と最後のレコードの抽出ができている
```

ここで、これらをひとつの結果として出したい場合、UNIONを使うことができます。

どうするかというと、こちらのクエリと、下のクエリを、 UNION ALL でこのようにつなげてあげれば OK です。

こちらのクエリですが、ここからここまでがひとつのクエリなので、セミコロンは最後にひとつしか付かない点に注意しておいてください。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

-- SELECT * FROM posts ORDER BY likes DESC;

(SELECT * FROM posts ORDER BY likes DESC LIMIT 3)
UNION ALL
(SELECT * FROM posts ORDER BY likes LIMIT 1);

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  9 | post-9  |    31 | Fukuoka |
|  1 | post-1  |    12 | Tokyo   |
|  3 | post-3  |    11 | Tokyo   |
|  4 | post-4  |     3 | Osaka   |
+----+---------+-------+---------+
# 結果が縦に繋がっている
```

UNION を使う場合、この 2 つのクエリのカラム数とデータ型が一致している必要がありますが、結果をこのように縦に繋げたいときに便利なので、使いこなせるようにしておきましょう。
### 質問：UNION ALLの前後の文を()で囲むときと囲まないときとの違いはなんですか？
回答：SELECT 句に ORDER BY または LIMIT を使う際はカッコで囲い、そうでない場合はそのまま記述できるようです。

リファレンスを参照したところ以下の記述が見つかりました

> 個々の SELECT に ORDER BY または LIMIT を適用するには、この句を SELECT を囲む括弧内に配置します。https://dev.mysql.com/doc/refman/5.6/ja/union.html
> 

どうやら`SELECT`句に`ORDER BY`または`LIMIT`を使う際はカッコで囲い、そうでない場合はそのまま記述できるということのようです。
### 要点まとめ
複数の抽出結果を一つの結果としてまとめる方法を見ていきます。

- UNION ALL：クエリとクエリを一つに繋げることができる ※繋げるクエリのカラム数とデータ型が一致している必要がある</details>


<details><summary>#08 サブクエリを使ってみよう</summary>

先ずは全てのレコードを表示してみましょう。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

INSERT INTO posts (message, likes, area) VALUES
  ('post-1', 12, 'Tokyo'),
  ('post-2', 8, 'Fukuoka'),
  ('post-3', 11, 'Tokyo'),
  ('post-4', 3, 'Osaka'),
  ('post-5', 8, 'Tokyo'),
  ('post-6', 9, 'Osaka'),
  ('post-7', 4, 'Tokyo'),
  ('post-8', 10, 'Osaka'),
  ('post-9', 31, 'Fukuoka');

SELECT * FROM posts;

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  1 | post-1  |    12 | Tokyo   |
|  2 | post-2  |     8 | Fukuoka |
|  3 | post-3  |    11 | Tokyo   |
|  4 | post-4  |     3 | Osaka   |
|  5 | post-5  |     8 | Tokyo   |
|  6 | post-6  |     9 | Osaka   |
|  7 | post-7  |     4 | Tokyo   |
|  8 | post-8  |    10 | Osaka   |
|  9 | post-9  |    31 | Fukuoka |
+----+---------+-------+---------+
```

ここで、likesの平均を横に表示したかった場合、普通に考えると、*のあとにlikesの平均を`SELECT *, AVG(likes) AS avg FROM posts;`と　表示すればいいと思うかもしれませんが、これではうまくいきません。AVG 関数は全てのレコードを集計してひとつのレコードにしてしまうので、このような結果になってしまいます。

```sql
SELECT *, AVG(likes) AS avg FROM posts;

+------+---------+-------+-------+---------+
| id   | message | likes | area  | avg     |
+------+---------+-------+-------+---------+
|    1 | post-1  |    12 | Tokyo | 10.6667 |
+------+---------+-------+-------+---------+
```

そこで、改行を入れながら見やすくクエリを書いていきましょう。どうするかというと、SELECTの中でさらにSELECT文を使ってあげればOKです。普通の SELECT の結果に加えて、レコードごとに集計関数を使うことで、うまく結果が表示されます。

```sql
-- SELECT *, AVG(likes) AS avg FROM posts;
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg
FROM
  posts;

+----+---------+-------+---------+---------+
| id | message | likes | area    | avg     |
+----+---------+-------+---------+---------+
|  1 | post-1  |    12 | Tokyo   | 10.6667 |
|  2 | post-2  |     8 | Fukuoka | 10.6667 |
|  3 | post-3  |    11 | Tokyo   | 10.6667 |
|  4 | post-4  |     3 | Osaka   | 10.6667 |
|  5 | post-5  |     8 | Tokyo   | 10.6667 |
|  6 | post-6  |     9 | Osaka   | 10.6667 |
|  7 | post-7  |     4 | Tokyo   | 10.6667 |
|  8 | post-8  |    10 | Osaka   | 10.6667 |
|  9 | post-9  |    31 | Fukuoka | 10.6667 |
+----+---------+-------+---------+---------+
```

このようにクエリの中で使うクエリのことをサブクエリと呼ぶので、用語として覚えておいてください。サブクエリを駆使すればかなり複雑なことができますが、その分クエリがこのように長くなったり、速度が遅くなったりするので、データが多いときには注意して使う必要があるという点も注意しておきましょう。

```sql
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg -- sub query
FROM
  posts;
```

### 質問：なぜ SELECT * ,AVG(likes) AS avg FROM posts; で avg のカラムが追加されるのですか？
    
```sql
SELECT * ,AVG(likes) AS avg FROM posts;

```

とすると、テーブルに `avg` のカラムが追加されるのは何故でしょうか。

```sql
INSERT INTO posts(message, likes, area) VALUES

```

の `()` 内に `avg` が追加で入ることでとカラムは追加になるのではないでしょうか。

回答：カラムを追加しているのではなく、AVG(likes) で得られる値を avg として表示しています。

この場合は、テーブルにカラムが追加されているわけではなく`SELECT`の結果に`AVG(likes)`で得られる値を`avg`として表示しております。`SELECT`で表示される結果の列は必ずしもテーブルのカラムに対応しているわけではありません。
### 質問：なぜサブクエリを使うと結果が posts レコード全てに追加されるのですか？
回答：順を追って説明していきます。

`AVG` などの関数は集約関数と呼ばれておりまして、`SELECT *, AVG(likes) AS avg FROM posts;`こちらの場合は全てのレコードを"集約"して平均を求めています、その結果平均を計算した1レコードのみになります。

サブクエリの場合は、まずサブクエリの結果を求めてそれを `posts` のレコード全てに追加のカラムとして結合していると考えていただいて間違いないかと思います。サブクエリの結果は上のクエリと同様に全てのレコードから計算した平均値ですのでまずこれがもとまります。その結果を `posts` のレコード全てに追加していくので、全レコード + 平均値というような結果が返されます。
### 要点まとめ
クエリのなかでクエリを使う方法について見ていきましょう。

- サブクエリ：クエリの中で使うクエリのこと、sub query
- サブクエリを使うときの注意点：サブクエリを駆使すればかなり複雑なことができますが、その分クエリが長くなったり、速度が遅くなったりするので、データが多いときには注意して使う必要がある</details>


<details><summary>#09 相関サブクエリを使ってみよう</summary>

