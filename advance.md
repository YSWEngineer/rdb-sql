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


<details><summary>#07 UNIONで抽出結果をまとめよう</summary>

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

今実行してあげると、横に平均が表示されている状態です。

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

こちら表の横に area ごとの平均も表示したかった場合、どうするか考えてみましょう。

その場合ですが、最初のレコードを処理しているときには、全体から area が Tokyo のレコードだけを抜き出して、平均を計算してあげれば良さそうです。そして、次のレコードを処理するときには全体から area が Fukuoka のレコードだけ抜き出してあげて、平均を計算してあげればいいですね。そのようにクエリを書いていきましょう。

こちらにもうひとつサブクエリを書いてあげて、 area_avg としてあげましょう。そのうえで WHERE 条件で全体の area と、今処理しているレコードの area が一緒のものを抽出してあげればいいですね。

```sql
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg,
  (SELECT AVG(likes) FROM posts WHERE area = area) AS area_avg
FROM
```

ただこれだと、どっちがどっちかがわからないので、抽出元のテーブルに別名をつけてあげましょう。こちらは t1 、そして今処理しているほうを t2 としてあげましょう。

```sql
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg,
  (SELECT AVG(likes) FROM posts AS t2 WHERE area = area) AS area_avg
FROM
  posts AS t1;
```

そのうえで、こちらの area は t1 の area にしてあげたいので、 テーブル名.カラム名 としてあげます。

こちらは t2 の area にしたいので、このように書いてあげれば OK ですね。

```sql
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg,
  (SELECT AVG(likes) FROM posts AS t2 WHERE t1.area = t2area) AS area_avg
FROM
  posts AS t1;

+----+---------+-------+---------+---------+----------+
| id | message | likes | area    | avg     | area_avg |
+----+---------+-------+---------+---------+----------+
|  1 | post-1  |    12 | Tokyo   | 10.6667 |   8.7500 |
|  2 | post-2  |     8 | Fukuoka | 10.6667 |  19.5000 |
|  3 | post-3  |    11 | Tokyo   | 10.6667 |   8.7500 |
|  4 | post-4  |     3 | Osaka   | 10.6667 |   7.3333 |
|  5 | post-5  |     8 | Tokyo   | 10.6667 |   8.7500 |
|  6 | post-6  |     9 | Osaka   | 10.6667 |   7.3333 |
|  7 | post-7  |     4 | Tokyo   | 10.6667 |   8.7500 |
|  8 | post-8  |    10 | Osaka   | 10.6667 |   7.3333 |
|  9 | post-9  |    31 | Fukuoka | 10.6667 |  19.5000 |
+----+---------+-------+---------+---------+----------+
```

このように大本のクエリと関連付けながら、実行しているサブクエリのことを相関サブクエリと呼ぶので、用語として覚えておくといいでしょう。
### 質問：サブクエリを使ったコードの仕組みがわからない
`SELECT`

`*,`

`(SELECT AVG(likes) FROM posts) AS avg,`

`(SELECT AVG(likes) FROM posts AS t2 WHERE t1.area=t2.area) AS area_avg`

`FROM`

`posts AS t1;`

レッスンで表示されているこのコードの意味が全くわかりませんでした。

area_avgカラムの部分です。

詳しく仕組みを教えていただきたいです。

回答：順序を追って説明していきます。

相関サブクエリはちょっと難しいですよね具体的に値を入れてみると理解の助けになるかもしれません

例えばt1テーブルのid=1の行を考えてみます。つまり`t1.area = 'Tokyo'`が入っていると考えるするとt2.area = 'Tokyo'となる行は以下の4行です

```
| id | message | likes | area    |
+----+---------+-------+---------+
|  1 | post-1  |    12 | Tokyo   |
|  3 | post-3  |    11 | Tokyo   |
|  5 | post-5  |     8 | Tokyo   |
|  7 | post-7  |     4 | Tokyo   |
+----+---------+-------+---------+

```

これらの`likes`の平均が`(12 + 11 + 8 + 4 )/4 = 8.75`となっています。同様にid=2の行を取り出して（`t1.area = 'Fukuoka'`とする）`t2.area = 'Fukuoka'`となる行の平均が得られる。`id = 3 ~ 9`の行も同様に考えていきます。

このような形で、1行につき全ての行を付き合わせて、areaが同じものを抽出し平均を出すというのがこのクエリです。

```sql
(SELECT AVG(likes) FROM posts AS t2 WHERE t1.area = t2.area) AS area_avg

```

`as` でその取り出したカラムにarea_avgという別名をつけているという形ですね。
### 質問：2つのテーブルはどう区別すべきですか？
回答：最初はご自身のわかりやすい考え方で理解しておくといいでしょう。

最初はご自身のわかりやすい考え方で理解しておくのがよいとは思います。

参考までに MySQL がどのようにクエリを処理をしているかのイメージですが、

- まず posts テーブルから全件取得しよう。
- 取得した id=1 のレコードの area の値は Tokyo である。posts テーブルから `area = 'Tokyo'` のレコードを取得して likes の平均を求めよう。
- 取得した id=2 のレコードの area の値は Fukuoka である。posts テーブルから `area = 'Fukuoka'` のレコードを取得して likes の平均を求めよう。
- ... (以下省略)

のような感じです。

posts テーブルのデータは変更されないので、特にコピーしておかなくても SELECT でレコードを取得できます。
### 要点まとめ
クエリを関連付けながら実行できる相関サブクエリについて見ていきます。

- 相関サブクエリ：大元のクエリと関連づけながら、実行しているサブクエリのこと</details>


<details><summary>#10 抽出条件にサブクエリを使ってみよう</summary>

サブクエリはWHEREのあとの抽出条件や抽出元のテーブルにも使うことができます。

postの中から1番大きなlikesを持つレコードを抽出してみましょう。

先ずはレコードを抽出したいので、`SELECT * FROM posts`としてあげます。

その後のWHEREでサブクエリを使いたいのですが、likesが全体の最大値と同じもの、と書いてあげればOKです。

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
  *
FROM
  posts
WHERE
  likes = (SELECT MAX(likes) FROM posts);

+----+---------+-------+---------+
| id | message | likes | area    |
+----+---------+-------+---------+
|  9 | post-9  |    31 | Fukuoka |
+----+---------+-------+---------+
```

このようにサブクエリは抽出条件にも使えるので、使いこなせるようにしておきましょう。
### 質問：サブクエリを使う場合と使わない場合の違いはなんですか？
回答：MAXなどの集約関数はGROUP BYを使わないと正しい結果は得られません。</details>


<details><summary>#11 抽出元にサブクエリを使ってみよう</summary>

areaごとの投稿数を出してみましょう。

COUNTと使い、nという別名を付けて、GROUP BYでareaごと指定してあげます。

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

SELECT area, COUNT(*) AS n FROM posts GROUP BY area;

+---------+---+
| area    | n |
+---------+---+
| Fukuoka | 2 |
| Osaka   | 3 |
| Tokyo   | 4 |
+---------+---+
```

では、ここでこの抽出結果である2,3,4の平均を出したかった場合を考えましょう。

その場合、この結果を別テーブルに切り出して集計する方法もありますが、サブクエリを使う方法もあります。

どうするかというと、この結果をtというテーブルだと考えて、nの平均を出せばいいので、そのように先ずはクエリを組み立ててあげましょう。

nの平均をt(というテーブル)から抽出する、と書きます。

また、ここでtは上にある`SELECT area, COUNT(*) AS n FROM posts GROUP BY area;`と書いたクエリの結果なので、そのままサブクエリとして置き換えてあげます。

但し、サブクエリを抽出元のテーブルとして使った場合、必ず別名を付ける必要があるので、AS tとしてあげてください。

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

SELECT area, COUNT(*) AS n FROM posts GROUP BY area;

SELECT
  AVG(n)
FROM
  (SELECT area, COUNT(*) AS n FROM posts GROUP BY area) AS t; /* クエリを組み立ててその中にサブクエリ作成→
サブクエリを抽出元のテーブルとして使った場合、必ず別名を付ける必要があるので、AS tと付ける */

+---------+---+
| area    | n |
+---------+---+
| Fukuoka | 2 |
| Osaka   | 3 |
| Tokyo   | 4 |
+---------+---+
+--------+
| AVG(n) |
+--------+
| 3.0000 |
+--------+
```

### 質問：SQL文のカンマはどのように作用しているのですか？
回答：

まず、SQL 文を見てみましょう。

```sql
SELECT area, COUNT(*) AS n FROM posts GROUP BY area;

```

こちらですね、このままではわかりにくいので、仮に「｜」をいれてどこまでが部分を形成しているのか見てみましょう。（構文的には｜はないと思ってください）

```sql
｜SELECT  area, COUNT(*) AS n   ｜   FROM posts  ｜ GROUP BY area｜

```

こんな感じになります。つまり、`SELECT`、`FROM`、`GROUP`などの SQL の構文が区切りになっている、ということなのですね。

なのでご質問の `,` は `FROM` の手前まで有効、となります。

次に `GROUP BY` に複数のカラムを入れる方法を紹介しますね。

これはずばり、ご察しのとおり `,` を入れるのです。例を見てみましょう。

```sql
SELECT area,likes, COUNT(*) AS n FROM posts GROUP BY area,likes;

```

こんな感じになります。`GROUP BY area,likes` と `,` で追加されているのがわかりますね。これで`area,likes` が全く同じものを 1 種類とみなしてまとめて、ここでは数を `COUNT` で数えてくれます。

また、`SELECT area,likes,` のように表示するカラムも増やしていることにも注目してください。

さて、ここで宿題です。実は今の状態で上の行を単純に MySQL に入力しても `area` 、`likes` が全く同じレコードがないので試すことができないのです。

なので、`main.sql` をちょっと変更して `area` 、`likes` が一緒であるレコードを追加または変更作成して、上の複数 `GROUP BY` の結果、`area` 、`likes` が一緒であるレコードの数が 2 以上になるようにしてみてください。

ご理解のため、ぜひ挑戦してみてください。もしわからなかったらお気軽にお問い合わせください！</details>


<details><summary>#12 ウィンドウ関数を使ってみよう</summary>

MySQLで最近追加されたウィンドウ関数について見ていきます。

ウィンドウ関数を使うと、テーブルを**パーティション(PARTITION)**と呼ばれる単位で集計してその結果を各レコードの横に追加してくれます。また、きめ細かい単位で集計をすることができるようになります。
### 質問：サブクエリとウィンドウ関数の使い分けを教えてください。
回答：求められる結果が得られえればどちらを使っても大丈夫です。

> サブクエリとウィンドウ関数はどう使い分けるのでしょうか？
> 

こちらは求められる結果が得られればどちらでも良いと思います。

- サブクエリで書くと長くなるのでウィンドウ関数を使う

ということもあるでしょうし、

- ウィンドウ関数を使ってもいいが、現場に慣れている人がいないので、長くなってもわかりやすいサブクエリで書いて保守しやすくする

でもいいと思います。

また一般的にサブクエリを使うとパフォーマンスが落ちたりしますが、データベースのパフォーマンスは利用状況にもよるので、実装しやすい方を使っておいて、運用状況をみながらチューニングしていくという流れになると思います。
### 要点まとめ
PARTITIONやFRAMEを設定して、高度な集計をすることができるウィンドウ関数について見ていきます。

- ウィンドウ関数の説明
- PARTITION：ウィンドウ関数を使用すると、テーブルをPARTITIONと呼ばれる単位で集計してその結果を各レコードの横に追加する。
- FRAME：PRTITIONのサブセットのこと。FRAMEという単位を使用するとさらに細かい集計をすることができる。</details>


<details><summary>#13 PARTITIONを設定してみよう</summary>

早速ウィンドウ関数を使ってみましょう。

コードを実行して表示されたテーブルの右側にlikesの全体の平均とareaごとの平均を出してみましょう。

先ずは**全体の平均**から見ていきます。

ウィンドウ関数では、今まで見てきた集計関数をそのまま使えるので、AVG(likes)としてあげて、ウィンドウ関数として使うならOVERと書いてあげます。また分かりやすくavgという別名を付けてあげます。

そのうえでパーティションを設定するのですが、全体を一つのパーティションにするなら、単なる丸括弧でOKです。

```sql
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

-- ウィンドウ関数を使用
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
  AVG(likes) OVER () AS avg -- 全体を一つのパーティションにするなら、単なる丸括弧でOK
FROM
  posts;

+----+---------+-------+---------+---------+
| id | message | likes | area    | avg     |
+----+---------+-------+---------+---------+
|  4 | post-4  |     3 | Osaka   | 10.6667 |
|  7 | post-7  |     4 | Tokyo   | 10.6667 |
|  5 | post-5  |     8 | Tokyo   | 10.6667 |
|  2 | post-2  |     8 | Fukuoka | 10.6667 |
|  6 | post-6  |     9 | Osaka   | 10.6667 |
|  8 | post-8  |    10 | Osaka   | 10.6667 |
|  3 | post-3  |    11 | Tokyo   | 10.6667 |
|  1 | post-1  |    12 | Tokyo   | 10.6667 |
|  9 | post-9  |    31 | Fukuoka | 10.6667 |
+----+---------+-------+---------+---------+
-- 全体の平均が右側に表示されている
```

次に**areaごとの平均**を見ていきます。

OVERの中でパーティションをしてしてあげればOKです。設定するには、`PARTITION BY area`とすればareaごとにパーティションを区切って平均を集計してくれます。別名はarea_avgにします。

ついでにareaごとのlikesの合計も出してみましょう。

AVGをSUMに変えてあげます。別名も分かりやすくarea_sumとします。

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
  AVG(likes) OVER () AS avg,
  AVG(likes) OVER (PARTITION BY area) AS area_avg,
  SUM(likes) OVER (PARTITION BY area) AS area_sum
FROM
  posts;

+----+---------+-------+---------+---------+----------+----------+
| id | message | likes | area    | avg     | area_avg | area_sum |
+----+---------+-------+---------+---------+----------+----------+
|  2 | post-2  |     8 | Fukuoka | 10.6667 |  19.5000 |       39 |
|  9 | post-9  |    31 | Fukuoka | 10.6667 |  19.5000 |       39 |
|  4 | post-4  |     3 | Osaka   | 10.6667 |   7.3333 |       22 |
|  6 | post-6  |     9 | Osaka   | 10.6667 |   7.3333 |       22 |
|  8 | post-8  |    10 | Osaka   | 10.6667 |   7.3333 |       22 |
|  7 | post-7  |     4 | Tokyo   | 10.6667 |   8.7500 |       35 |
|  3 | post-3  |    11 | Tokyo   | 10.6667 |   8.7500 |       35 |
|  5 | post-5  |     8 | Tokyo   | 10.6667 |   8.7500 |       35 |
|  1 | post-1  |    12 | Tokyo   | 10.6667 |   8.7500 |       35 |
+----+---------+-------+---------+---------+----------+----------+
-- areaごとの平均、areaごとの合計がちゃんと追加されている
```

ウィンドウ関数を使うとサブクエリと違って、すっきり書くことができるので、使いこなせるようになっておきましょう。

それからOVERから後ろの指定ですが、同じようなものがある場合は別名を付けることができます。その場合はWINDOWとしてあげて、`別名 AS PARTITION BY area` を書いてあげればOKです。そうすると、この指定の代わりにwという別名を使うことができるので、`PARTITION BY area`と書いた2行を短く書き換えることができます。

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
  AVG(likes) OVER () AS avg,
  -- AVG(likes) OVER (PARTITION BY area) AS area_avg,
  -- SUM(likes) OVER (PARTITION BY area) AS area_sum
  AVG(likes) OVER w AS area_avg,
  SUM(likes) OVER w AS area_sum
FROM
  posts
WINDOW
  w AS (PARTITION BY area); -- (PARTITION BY area)をwという別名にする
```

こうした操作もできるようになっておいてください。
### 要点まとめ
ウィンドウ関数を使って、PARTITIONを設定する方法について見ていきます。

- OVER()：ウィンドウ関数として使うために必要。
- PARTITION BY：パーティションを設定。</details>


<details><summary>#14 FRAMEを設定してみよう</summary>

今はテーブルの横にareaごとの合計が出ている状態です。

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
  SUM(likes) OVER (PARTITION BY area) AS area_sum
FROM
  posts;

+----+---------+-------+---------+----------+
| id | message | likes | area    | area_sum |
+----+---------+-------+---------+----------+
|  9 | post-9  |    31 | Fukuoka |       39 |
|  2 | post-2  |     8 | Fukuoka |       39 |
|  4 | post-4  |     3 | Osaka   |       22 |
|  6 | post-6  |     9 | Osaka   |       22 |
|  8 | post-8  |    10 | Osaka   |       22 |
|  7 | post-7  |     4 | Tokyo   |       35 |
|  1 | post-1  |    12 | Tokyo   |       35 |
|  3 | post-3  |    11 | Tokyo   |       35 |
|  5 | post-5  |     8 | Tokyo   |       35 |
+----+---------+-------+---------+----------+
```

FRAMEを使って累計を集計してみましょう。

likesの小さい順に並び替えた上で累計を集計していきたいので、`ORDER BY likes` としてあげます。

その上でFRAMEを設定したいのですが、実はデフォルトでパーティションの先頭からそのレコードまでになるので、このままで累計が集計できるはずです。

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
  SUM(likes) OVER (
    PARTITION BY area
    ORDER BY likes -- ORDER BY 列名 :検索結果を並べ替える 今回は昇順
  ) AS area_sum
FROM
  posts;

+----+---------+-------+---------+----------+
| id | message | likes | area    | area_sum |
+----+---------+-------+---------+----------+
|  2 | post-2  |     8 | Fukuoka |        8 |
|  9 | post-9  |    31 | Fukuoka |       39 |
|  4 | post-4  |     3 | Osaka   |        3 |
|  6 | post-6  |     9 | Osaka   |       12 |
|  8 | post-8  |    10 | Osaka   |       22 |
|  7 | post-7  |     4 | Tokyo   |        4 |
|  5 | post-5  |     8 | Tokyo   |       12 |
|  3 | post-3  |    11 | Tokyo   |       23 |
|  1 | post-1  |    12 | Tokyo   |       35 |
+----+---------+-------+---------+----------+
-- パーティションごとにちゃんと累計が集計できているのがわかる
```

FRAMEの設定を変更したい場合も見てあげましょう。

例えば、パーティションの中で前後1行をフレームにしたい場合は`ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING`としてあげればOKです。

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
  SUM(likes) OVER (
    PARTITION BY area
    ORDER BY likes
    ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
  ) AS area_sum
FROM
  posts;

+----+---------+-------+---------+----------+
| id | message | likes | area    | area_sum |
+----+---------+-------+---------+----------+
|  2 | post-2  |     8 | Fukuoka |       39 |
|  9 | post-9  |    31 | Fukuoka |       39 |
|  4 | post-4  |     3 | Osaka   |       12 |
|  6 | post-6  |     9 | Osaka   |       22 |
|  8 | post-8  |    10 | Osaka   |       19 |
|  7 | post-7  |     4 | Tokyo   |       12 |
|  5 | post-5  |     8 | Tokyo   |       23 |
|  3 | post-3  |    11 | Tokyo   |       31 |
|  1 | post-1  |    12 | Tokyo   |       23 |
+----+---------+-------+---------+----------+
```

### 要点まとめ
PARTITIONのなかで並び替えをした後に、FRAMEを設定する方法について見ていきます。

- ORDER BY：検索結果を並べ替える デフォルトやASCは昇順、DESCは降順。
- ROWS BETWEEN ... AND ...：パーティションの中で前後1行をフレームにしたい場合。</details>


<details><summary>#15 RANK()、DENSE_RANK()を使ってみよう</summary>

OVERを使った時だけ使える関数について、よく使うものを見ていきましょう。

では、likesの小さい順に並び替えた後にその連番が欲しいといった場合を考えてみます。その場合、ROW_NUMBER()という関数が使えます。`ROW_NUMBER() OVER (ORDER BY likes)` と書きます。それから、今回パーティションは全体を対象にしているので、パーティションを省略して`ORDER BY`だけになっている点にも注意しましょう。

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
  ROW_NUMBER() OVER (ORDER BY likes) AS num
FROM
  posts;

+----+---------+-------+---------+-----+
| id | message | likes | area    | num |
+----+---------+-------+---------+-----+
|  4 | post-4  |     3 | Osaka   |   1 |
|  7 | post-7  |     4 | Tokyo   |   2 |
|  5 | post-5  |     8 | Tokyo   |   3 |
|  2 | post-2  |     8 | Fukuoka |   4 |
|  6 | post-6  |     9 | Osaka   |   5 |
|  8 | post-8  |    10 | Osaka   |   6 |
|  3 | post-3  |    11 | Tokyo   |   7 |
|  1 | post-1  |    12 | Tokyo   |   8 |
|  9 | post-9  |    31 | Fukuoka |   9 |
+----+---------+-------+---------+-----+
```

例えば連番ではなく、順位が知りたかった場合、likesの数には8が2つあるので、それを考慮して順位をつけたいなら、そのための関数があります。

二つの方法があり、**RANK関数**を使うか、**DENSE_RANK関数**を使えばOKです。

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
  ROW_NUMBER() OVER (ORDER BY likes) AS num,
  RANK() OVER (ORDER BY likes) AS rank, -- 同じ順位の次の順位は飛ばされる
  DENSE_RANK() OVER (ORDER BY likes) AS dense -- 同じ順位があっても次の順位は飛ばされない
FROM
  posts;

+----+---------+-------+---------+-----+------+-------+
| id | message | likes | area    | num | rank | dense |
+----+---------+-------+---------+-----+------+-------+
|  4 | post-4  |     3 | Osaka   |   1 |    1 |     1 |
|  7 | post-7  |     4 | Tokyo   |   2 |    2 |     2 |
|  5 | post-5  |     8 | Tokyo   |   3 |    3 |     3 |
|  2 | post-2  |     8 | Fukuoka |   4 |    3 |     3 |
|  6 | post-6  |     9 | Osaka   |   5 |    5 |     4 |
|  8 | post-8  |    10 | Osaka   |   6 |    6 |     5 |
|  3 | post-3  |    11 | Tokyo   |   7 |    7 |     6 |
|  1 | post-1  |    12 | Tokyo   |   8 |    8 |     7 |
|  9 | post-9  |    31 | Fukuoka |   9 |    9 |     8 |
+----+---------+-------+---------+-----+------+-------+
```

ウィンドウ関数でしか使えない関数もよく使われるので、覚えておきましょう。
### 要点まとめ
OVERを使った時に使える集計関数についてみていきます。

- ROW_NUMBER()：ウィンドウ関数時に連番を付けることができる。
- RANK()：同じ順位の次の順位は飛ばされる。
- DENSE_RANK()：同じ順位があっても次の順位は飛ばされない。</details>


<details><summary>#16 LAG()、LEAD()を扱ってみよう</summary>

n個前、n個後のレコードの値を求めることができる関数について。

1個前のレコードのlikesの値を求めたかったらLAG()を使って、`LAG(likes, 1) OVER (ORDER BY likes) AS lag,` と書けばOKです。今回はパーティションは全体にしてlikesの小さい順で並び替えた上で集計していきましょう。

1個後のレコードのlikesの値は`LEAD()`とすればOKです。

1の場合は省略できるので`LAG(likes) OVER (ORDER BY likes) AS lag,` `LEAD(likes) OVER (ORDER BY likes) AS lead,` このように書いても同じ意味になります。

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
  -- LAG(likes, 1) OVER (ORDER BY likes) AS lag,
  -- LEAD(likes, 1) OVER (ORDER BY likes) AS lead
  LAG(likes) OVER (ORDER BY likes) AS lag,
  LEAD(likes) OVER (ORDER BY likes) AS lead
FROM
  posts;

+----+---------+-------+---------+------+------+
| id | message | likes | area    | lag  | lead |
+----+---------+-------+---------+------+------+
|  4 | post-4  |     3 | Osaka   | NULL |    4 |
|  7 | post-7  |     4 | Tokyo   |    3 |    8 |
|  5 | post-5  |     8 | Tokyo   |    4 |    8 |
|  2 | post-2  |     8 | Fukuoka |    8 |    9 |
|  6 | post-6  |     9 | Osaka   |    8 |   10 |
|  8 | post-8  |    10 | Osaka   |    9 |   11 |
|  3 | post-3  |    11 | Tokyo   |   10 |   12 |
|  1 | post-1  |    12 | Tokyo   |   11 |   31 |
|  9 | post-9  |    31 | Fukuoka |   12 | NULL |
+----+---------+-------+---------+------+------+
```

また、これをうまく使うと、前のレコードからの差分なども表現することができます。

likesから1つ前のlikesを引いてあげれば差分になります。

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
  -- LAG(likes, 1) OVER (ORDER BY likes) AS lag,
  -- LEAD(likes, 1) OVER (ORDER BY likes) AS lead
  -- LAG(likes) OVER (ORDER BY likes) AS lag,
  -- LEAD(likes) OVER (ORDER BY likes) AS lead
  likes - LAG(likes) OVER (ORDER BY likes) AS diff
FROM
  posts;

+----+---------+-------+---------+------+
| id | message | likes | area    | diff |
+----+---------+-------+---------+------+
|  4 | post-4  |     3 | Osaka   | NULL |
|  7 | post-7  |     4 | Tokyo   |    1 |
|  5 | post-5  |     8 | Tokyo   |    4 |
|  2 | post-2  |     8 | Fukuoka |    0 |
|  6 | post-6  |     9 | Osaka   |    1 |
|  8 | post-8  |    10 | Osaka   |    1 |
|  3 | post-3  |    11 | Tokyo   |    1 |
|  1 | post-1  |    12 | Tokyo   |    1 |
|  9 | post-9  |    31 | Fukuoka |   19 |
+----+---------+-------+---------+------+
```

次のレコードで前のレコードからどれだけ増えたかが分かります。

LAG()やLEAD()関数も使えるようになっておきましょう。
### 要点まとめ
n個前、n個後の値を求めることができる関数を見ていきます。

- LAG()：1個前のレコードの値を求める。
- LEAD()：1個後のレコードの値を求める。</details>


<details><summary>#17 トランザクションについて理解しよう</summary>

post-1からlikesを1引いて、post-2からlikesを1足す場合

```sql
UPDATE posts
SET likes = likes - 1
WHERE id = 1;

UPDATE posts
SET likes = likes + 2
WHERE id = 2;
```

ただ、これらのコードを実行する途中で、なんらかの障害が起きて 2 番目の UPDATE が実行されなかったり、もしくは他のユーザーがデータベースを操作していて、 post-2 のデータをうっかり書き換えていたりしていた場合、データの整合性が取れなくなってしまいます。

そこで、こういった途中で他の操作をされたくない一連の処理をする場合、 **START TRANSACTION** と **COMMIT** で囲ってあげれば OK です。

```sql
START TRANSACTION;

UPDATE posts
SET likes = likes - 1
WHERE id = 1;

UPDATE posts
SET likes = likes + 2
WHERE id = 2;

COMMIT;
-- 一連の処理を行うためには START TRANSACTIONとCOMMITで囲う
```

そうすると、これらの処理をしている間は他の処理を受け付けなくなるので、データの整合性が保たれるといった仕組みです。

なお、ひとつの UPDATE を実行して、 post-1 のいいねが変更されたあとに、障害が発生してこちらの処理が途中で止まってしまった場合、 **ROLLBACK** という命令を使うことで途中まで処理が進んでいても、先ほどの変更を取り越すこともできます。

```sql
START TRANSACTION;

UPDATE posts
SET likes = likes - 1
WHERE id = 1;

UPDATE posts
SET likes = likes + 2
WHERE id = 2;

ROLLBACK;
```

### 質問：複数テーブルの場合の動作を教えてください
回答：トランザクション内でデータの更新が発生した時点でその行(またはテーブル)がロックされます。

結論から言いますと、トランザクション内でデータの更新が**発生した時点**でその行（またはテーブル）がロックされます。

※ データの整合性を保つためにテーブル全体をロックせざるを得ない場合はテーブルロックが発生しますが、今回の例のような id=1 のデータだけ更新する場合は、他のデータには影響を及ぼしませんので行ロックとなります。

つまり、

```sql
START TRANSACTION;
```

の時点では何もロックされておらず、

```sql
UPDATE posts SET likes = likes - 1 WHERE id = 1;

```

まで SQL が実行された時点で id=1 の行のみロックされます。（このとき id=2 のデータは他のユーザーから更新可能です。）

したがってテーブルが1つであろうが複数であろうが、更新が発生するまではロックされず、更新が発生して初めてロックされるというわけですね。

なお、トランザクション時にデータがどのようにロックされるかは、実際に試してみるのが最も分かりやすいです。

オンラインターミナルで、

```
mysql -h db -t -u dbuser -pdbpass myapp
```

と実行すると実際に MySQL サーバーにログインできます。

さらにブラウザのタブをもう1つ開いて、オンラインターミナルから同様に MySQL サーバーにログインしてみてください。

そうすると双方のオンラインターミナルから SQL を交互に実行できますので、実際にトランザクションをかけた際にどうなるかを確認できます。</details>


<details><summary>#18 トランザクションを使ってみよう</summary>

#17の例を実装しましょう。

post-1 に付けた最後のいいねが間違いだったとします。それを修正するには id が 1 のレコードに関しては、いいねを 1 減らす、そして id が 2 のレコードについては、いいねを 1 増やすと書いてあげれば OK です。ただ、この途中で邪魔が入って欲しくない場合、トランザクションを使えばいいです。

`START TRANSACTION` としてあげて、処理の終わりで `COMMIT` としてあげれば OK です。

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
  ('post-5', 5),
  ('post-6', 9),
  ('post-7', 4),
  ('post-8', 10),
  ('post-9', 31);

START TRANSACTION;
UPDATE posts SET likes = likes - 1 WHERE id = 1;
UPDATE posts SET likes = likes + 1 WHERE id = 2;
COMMIT;

SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | post-1  |    11 |
|  2 | post-2  |     9 |
|  3 | post-3  |    11 |
|  4 | post-4  |     3 |
|  5 | post-5  |     5 |
|  6 | post-6  |     9 |
|  7 | post-7  |     4 |
|  8 | post-8  |    10 |
|  9 | post-9  |    31 |
+----+---------+-------+
```

途中でなんらかの障害が起きた場合も考えてみましょう。

例えば`UPDATE posts SET likes = likes + 1 WHERE id = 2;` の処理がうまくいかなかったとします。

その場合ですが、 COMMIT ではなくて `ROLLBACK` としてあげれば、こちらの処理はなかったことにできるはずです。

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
  ('post-5', 5),
  ('post-6', 9),
  ('post-7', 4),
  ('post-8', 10),
  ('post-9', 31);

START TRANSACTION;
UPDATE posts SET likes = likes - 1 WHERE id = 1;
-- UPDATE posts SET likes = likes + 1 WHERE id = 2;
-- COMMIT;
ROLLBACK;

SELECT * FROM posts;

+----+---------+-------+
| id | message | likes |
+----+---------+-------+
|  1 | post-1  |    12 |
|  2 | post-2  |     8 |
|  3 | post-3  |    11 |
|  4 | post-4  |     3 |
|  5 | post-5  |     5 |
|  6 | post-6  |     9 |
|  7 | post-7  |     4 |
|  8 | post-8  |    10 |
|  9 | post-9  |    31 |
+----+---------+-------+
```

id が 1 のレコードについて UPDATE をかけたのにも関わらず、 12 のままなのが分かります。
### 要点まとめ
トランザクションの挙動をコードで確認していきます。

- START TRANSACTION：トランザクション開始の指示。この指示以降のSQL文を1つのトランザクションとする。
- COMMIT：トランザクション終了の指示。この指示までを1つのトランザクションとし、変更を確定する。
- ROLLBACK：トランザクション終了の指示。この指示までを1つのトランザクションとし、変更の取り消しを行う。</details>


<details><summary>#19 テーブルを分割してみよう</summary>

今まで見てきたpostsテーブルの投稿にコメントをつけたくなった場合を考えます。

その場合いくつかの管理方法がありますが、ひとつはこちらに**カラムを追加してコメントも管理できるようにする方法**です。

ただ、コメントは複数付けられるのが普通なので、その度にカラムを増やしていってもいいのですが、**いくつ増やせばいいか分からないですし、コメントが付かないレコードも出てくるので、このあたりの領域が無駄**になってしまいます。

また、別の方法としては、**カラムはひとつだけ増やして post-1 に別のコメントが付いたらこのようにレコードを追加するという方法**もあります。

ただ、**この場合 post-1 の箇所が重複しているので、その分容量が大きくなったり、またこの post-1 の文言を変更したくなったら、全てのレコードを更新しなくてはいけないので、処理も遅くなって**しまいます。

そこで、**テーブルを分割してデータを管理しやすくする**、といったテクニックがよく使われます。

今回だと **posts テーブルのほかに comments テーブルを作ってあげて、こちらにはどの post に紐づくかのカラムを持たせて**あげればいいです。また**主キーを設定しておくと、個々のレコードが特定できるようになって便利**かと思います。

**テーブルを分離してあげれば先ほど見たような無駄な領域ができることも無くなり**ますし、たとえば post-1 の文言を更新したくなったときも、この一箇所を更新するだけでいいです

このようにデータを管理しやすくするためにテーブルを分割していくことを**正規化**というので興味がある人は調べてみるといいでしょう。
### 要点まとめ
テーブルを分割してデータを管理しやすくする方法について見ていきます。

- テーブルの分割：データを複数のテーブルに分けて格納した方が、安全、確実にデータを管理しやすい。
- 正規化：矛盾したデータを格納できないよう、テーブルを複数に分割していく作業のこと。</details>


<details><summary>#20 複数のテーブルを扱ってみよう</summary>

posts テーブルですが、レコードを 3 つだけにして、こちらは id と message だけにしておきました。

#19で見た、commentsテーブルを作成します。

先ず、postsテーブルをコピーして、comments、commentにしておきます。

次に、postのidと紐付けるためのカラムを追加します。

post_id で整数型(INT)と書きます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments; -- commentsに書き直す
CREATE TABLE comments ( -- commentsに書き直す
  id INT NOT NULL AUTO_INCREMENT,
	post_id INT
  comment VARCHAR(140), -- 単数形のcommentに書き直す
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');
```

後は、レコードをいくつか追加します。

`INSERT INTO posts (message) VALUES
('post-1'),
('post-2'),
('post-3');`

をコピーして書き換えます。

comments テーブルに対して、 post_id と comment の値を挿入していきましょう。

ひとつ目の post に対してコメントといった感じで書いていけばいいですね。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments;
CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');
  
INSERT INTO posts (message) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-1'),
  (1, 'comment-1-1'),
  (1, 'comment-1-1');
```

では 1 番目の post にもうひとつコメントが付いたとしましょう。それから 3 番目の post に対して 1 つのコメント、そして説明のために post に 4 番目はありませんが、このように 4 つ目のコメントを足してみましょう。

ここで、postsとcommentsの中身を確認します。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments;
CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');
  
INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1'),
  (4, 'comment-4-1');

SELECT * FROM posts;
SELECT * FROM comments;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-------------+
| id | post_id | comment     |
+----+---------+-------------+
|  1 |       1 | comment-1-1 |
|  2 |       1 | comment-1-2 |
|  3 |       3 | comment-3-1 |
|  4 |       4 | comment-4-1 |
+----+---------+-------------+
```

</details>


<details><summary>#21 内部結合、外部結合について見ていこう</summary>

postsとcommentsの2つのテーブルがあり、post-1にはcommentが2件、post-2にはcommentなし、post-3にはcommentが1件、そしてpostsに紐づいていないcommentが1件ある状態です。

ここで、2つのテーブルを結合させて、データを抽出していきたいのですが、大きく2つの方法があります。

1つ目は**内部結合**という方法で、2つのテーブルに共通のデータを取得する方法です。

今回だとpostsにもcommentsにもデータがあるのはpost-1とpost-3だけなので、内部結合すると結果はこのようになります。

postsテーブル

| id | message |
| --- | --- |
| 1 | post-1 |
| 2 | post-2 |
| 3 | post-3 |

commentsテーブル

| id | post_id | message |
| --- | --- | --- |
| 1 | 1 | comment-1-1 |
| 2 | 1 | comment-1-2 |
| 3 | 3 | comment-3-1 |
| 4 | 4 | comment-4-1 |

postsテーブルとcommentsテーブルを内部結合した結果

| id | message | id | post_id | message |
| --- | --- | --- | --- | --- |
| 1 | post-1 | 1 | 1 | comment-1-1 |
| 1 | post-1 | 2 | 1 | comment-1-2 |
| 3 | post-3 | 3 | 3 | comment-3-1 |

2つ目の方法は**外部結合**という方法です。

外部結合には、左のテーブルを軸にした**左外部結合**、右のテーブルを軸にした**右外部結合**があります。

左外部結合の場合は、postsを軸にしてそれに対応するcommentがあれば取得するのでこのようになります。

postsテーブル

| id | message |
| --- | --- |
| 1 | post-1 |
| 2 | post-2 |
| 3 | post-3 |

commentsテーブル

| id | post_id | message |
| --- | --- | --- |
| 1 | 1 | comment-1-1 |
| 2 | 1 | comment-1-2 |
| 3 | 3 | comment-3-1 |
| 4 | 4 | comment-4-1 |

左外部結合の結果

| id | message | id | post_id | message |
| --- | --- | --- | --- | --- |
| 1 | post-1 | 1 | 1 | comment-1-1 |
| 1 | post-1 | 2 | 1 | comment-1-2 |
| 2 | post-2 | NULL | NULL | NULL |
| 3 | post-3 | 3 | 3 | comment-3-1 |

commentがない箇所はNULL(ナル)になるので、注意しておきましょう。comment がついているかどうかに関わらず、**投稿の一覧ページを作るときなどに使えばいいですね。**

一方右外部結合ですが、今度は comment だけ全て取得して、そこに紐づいた投稿があれば取得するので、結果はこのようになります。

postsテーブル

| id | message |
| --- | --- |
| 1 | post-1 |
| 2 | post-2 |
| 3 | post-3 |

commentsテーブル

| id | post_id | message |
| --- | --- | --- |
| 1 | 1 | comment-1-1 |
| 2 | 1 | comment-1-2 |
| 3 | 3 | comment-3-1 |
| 4 | 4 | comment-4-1 |

右外部結合

| id | message | id | post_id | message |
| --- | --- | --- | --- | --- |
| 1 | post-1 | 1 | 1 | comment-1-1 |
| 1 | post-1 | 2 | 1 | comment-1-2 |
| 3 | post-3 | 3 | 3 | comment-3-1 |
| NULL | NULL | 4 | 4 | comment-4-1 |

該当する投稿がなければそこはNULLになるので、その点にも注意しておきましょう。そこに**紐づいた投稿があるかどうかに関わらず、 comment の一覧を表示したいときなどに使えばいいですね。**
### 要点まとめ
2つのテーブルからデータを取得する方法について見ていきます。

- 内部結合の説明：結合すべき相手の行が見つからない場合に行が消滅してしまう通常の結合のこと。
- 左外部結合の説明：「NULLの行を生み出しても、左表の全行を必ず出力する」処理のこと。
- 右外部結合の説明：「NULLの行を意味出しても、右表の全行を必ず出力する」処理のこと。</details>


<details><summary>#22 内部結合を使ってみよう</summary>

内部結合を見てみます。

posts と comments を内部結合したい場合は、 `posts INNER JOIN comments` としてあげて、紐づけるカラムを ON で表現してあげます。

今回だと posts の id と comments の post_id を紐付けてあげればいいです。

実はINNERを省略しても内部結合になるので、単にJOINと書いても上と同じ意味になります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments;
CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1'),
  (4, 'comment-4-1');

SELECT
  *
FROM
  -- posts INNER JOIN comments ON posts.id = comments.post_id; -- 内部結合
  posts JOIN comments ON posts.id = comments.post_id; /* postsテーブルとcommentsテーブルを内部結合し、
postsテーブルのidとcommentsテーブルのpost_idを紐付けせよ */
-- どちらも同じ意味

+----+---------+----+---------+-------------+
| id | message | id | post_id | comment     |
+----+---------+----+---------+-------------+
|  1 | post-1  |  1 |       1 | comment-1-1 |
|  1 | post-1  |  2 |       1 | comment-1-2 |
|  3 | post-3  |  3 |       3 | comment-3-1 |
+----+---------+----+---------+-------------+
```

また、特定のカラムだけを抽出したい場合は、テーブル名を付けてあげて、抽出すれば OK です。

posts の id と posts の message と comments の comment だけ抽出したい場合は、このように書いてあげれば OK です。

但し id は両方にあるのでテーブル名を付ける必要がありますが、 **message や comment はそれぞれのテーブルにしかないので、省略してあげることもできます**。

したがってこちらは、 [posts.id](http://posts.id/) と message と comment だけでも OK です。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments;
CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1'),
  (4, 'comment-4-1');

SELECT
  -- *
  -- posts.id, posts.message, comments.comment
  posts.id, message, comment -- messageとcommentはそれぞれのテーブルにしかないので省略が可能
FROM
  -- posts INNER JOIN comments ON posts.id = comments.post_id;
  posts JOIN comments ON posts.id = comments.post_id;

+----+---------+-------------+
| id | message | comment     |
+----+---------+-------------+
|  1 | post-1  | comment-1-1 |
|  1 | post-1  | comment-1-2 |
|  3 | post-3  | comment-3-1 |
+----+---------+-------------+
```

こうした内部結合もできるようになっておきましょう。
### 要点まとめ
コメントがついている投稿だけを抜き出す方法について見ていきます。

- INNER JOIN：INNERを省略することも可能。</details>


<details><summary>#23 外部結合を使ってみよう</summary>

左外部結合は、`LEFT OUTER JOIN` とすればいいのですが、実は `OUTER` は省略できるので、単に `LEFT JOIN` と書いてあげても左外部結合になります。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments;
CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1'),
  (4, 'comment-4-1');

SELECT
  *
FROM
  posts JOIN comments ON posts.id = comments.post_id;
  
SELECT
  *
FROM
  -- posts LEFT OUTER JOIN comments ON posts.id = comments.post_id;
  posts LEFT JOIN comments ON posts.id = comments.post_id; -- OUTERを省略可能
```

右外部結合はRIGHT JOINにすればOKです。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments;
CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1'),
  (4, 'comment-4-1');

SELECT
  *
FROM
  posts JOIN comments ON posts.id = comments.post_id;
  
SELECT
  *
FROM
  -- posts LEFT OUTER JOIN comments ON posts.id = comments.post_id;
  posts LEFT JOIN comments ON posts.id = comments.post_id;
  
SELECT
  *
FROM
  posts RIGHT JOIN comments ON posts.id = comments.post_id;

+----+---------+----+---------+-------------+
| id | message | id | post_id | comment     |
+----+---------+----+---------+-------------+
|  1 | post-1  |  1 |       1 | comment-1-1 |
|  1 | post-1  |  2 |       1 | comment-1-2 |
|  3 | post-3  |  3 |       3 | comment-3-1 |
+----+---------+----+---------+-------------+
+----+---------+------+---------+-------------+
| id | message | id   | post_id | comment     |
+----+---------+------+---------+-------------+
|  1 | post-1  |    1 |       1 | comment-1-1 |
|  1 | post-1  |    2 |       1 | comment-1-2 |
|  3 | post-3  |    3 |       3 | comment-3-1 |
|  2 | post-2  | NULL |    NULL | NULL        |
+----+---------+------+---------+-------------+
+------+---------+----+---------+-------------+
| id   | message | id | post_id | comment     |
+------+---------+----+---------+-------------+
|    1 | post-1  |  1 |       1 | comment-1-1 |
|    1 | post-1  |  2 |       1 | comment-1-2 |
|    3 | post-3  |  3 |       3 | comment-3-1 |
| NULL | NULL    |  4 |       4 | comment-4-1 |
+------+---------+----+---------+-------------+
```

左外部結合では posts から全てレコードが抽出されていて、それに関連する comment があれば取得して、そしてなければ NULL で埋めてくれます。

右外部結合ですが、まず comments から全てのレコードが抽出されて、それに関連する投稿があれば取得して、なければ NULL で埋めてくれます。

このような違いがあるので、目的に応じて使い分けられるようにしておいてください。
### 要点まとめ
左外部結合と右外部結合についてみていきます。

- LEFT OUTER JOIN：OUTERを省略できる。**※現場では省略形を多用している。**
- RIGHT OUTER JOIN：OUTERを省略できる。</details>


<details><summary>#24 外部キー制約を設定しよう</summary>

さて、今まで見てきた comments のデータですが、 post-4 がないのに、それに対して comment が付けられるのは少しおかしいような気がします。

こうした変なデータが入り込まないようにするための外部キー制約について見ていきましょう。

では、comments テーブルの post_id に対して、 posts テーブルの id にその値が存在しなかったら弾いてくれるように設定しておきましょう。

どうするかというと、 FOREIGN KEY としてあげて、 post_id に対して posts テーブルの id を参照して、そこに値がなければ弾いてねと、このように書いてあげます。

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments;
CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1'),
  (4, 'comment-4-1');

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1452 (23000) at line 22: Cannot add or update a child row: a foreign key 
fails (`myapp`.`comments`, CONSTRAINT `comments_ibfk_1` FOREIGN KEY (`post_id`) REFERENCES `posts` (`id`))
```

外部キーの制約によって変なデータが追加できなくなっているのが分かります。

今、外部キーが設定されましたが、そうするとここで紐付けたpostsテーブルのほうにも制限がかかるので注意しましょう。

例えば、今一番上で posts テーブルを削除していますが、 posts テーブルは今 comments テーブルに紐づいているので、このまま削除すると comments テーブルのデータが宙ぶらりんになって整合性が取れなくなってしまいます。

そのため、このまま実行するとエラーになります。

これを直すにはここで posts テーブルを削除する前に外部キーの設定がされている comments テーブルを先に削除してあげれば OK です。

今回はこちらの DROP TABLE 文を posts テーブルを削除する前に持ってきてあげましょう。

変なデータはコメントにします。

それからデータの中身も一応確認したいので、 SELECT * FROM posts 、 SELECT * FROM comments としておきましょう。

これでエラーが出なくなるはずです。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');
  -- (4, 'comment-4-1');
  
SELECT * FROM posts;
SELECT * FROM comments;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-------------+
| id | post_id | comment     |
+----+---------+-------------+
|  1 |       1 | comment-1-1 |
|  2 |       1 | comment-1-2 |
|  3 |       3 | comment-3-1 |
+----+---------+-------------+
```

### 質問：**posts テーブルの削除と comments テーブルの削除を逆にするとエラーになるのはなぜですか？**
回答：**comments テーブルは posts テーブルを必要としているからです。**

「comments テーブルは posts テーブルを必要としている」が、「posts テーブルはcomments テーブルを必要としていない」からなのです。

具体的に申し上げますね。

posts は「sns の投稿」のイメージでしたね。そして comments テーブルは「投稿に対するコメント」のイメージでした。

投稿に対するコメントは「どの投稿に対するコメントか」が必要です。これが

```lisp
FOREIGN KEY (post_id) REFERENCES posts(id)

```

この部分です。`post_id` が「どの投稿に対するコメントか」を表します。

さて、ここでもし先に posts テーブルを削除してしまったらどうなるでしょう？

comments テーブルは残るのですが、上の

```lisp
FOREIGN KEY (post_id) REFERENCES posts(id)

```

の部分が意味を持たなくなるのがわかりますでしょうか。posts テーブル、つまり「sns の投稿」がないのに「ない投稿に対するコメント」だけがあるというおかしな状態になります。

なので MySQL はエラーとなるわけなのです。

逆だとどうでしょう？

comments、つまり「投稿に対するコメント」だけが削除されて posts、つまり「sns の投稿」だけがある状態です。これは「sns の投稿」は「投稿に対するコメント」がなくても特にデータ的には問題ありませんね。そして MySQL もエラーにはなりません。

なのでテーブルを削除する際は、「posts テーブルは comments テーブルを必要としていない」ので、まず不要な comments から削除する必要がある、というわけなのですね。
### 要点まとめ
データの整合性を取るために、外部キー制約について見ていきます。

- 外部キー制約の設定：**外部キー**は、他のテーブルの関連行を指すための値を格納することでリレーションシップを結ぶ役割を担っている列のこと。**外部キー制約**は、参照整合性が崩れるようなデータ操作をしようとした場合にエラーを発生させ、強制的に処理を中断させる制約のこと。</details>


<details><summary>#25 データの整合性を保とう</summary>

外部キーが設定された状態で posts テーブルのほうに変更を加えたかった場合を見てみます。

こちらで post-3 を削除してみましょう。

id が 3 のレコードを削除すればいいので、 `DELETE FROM posts WHERE id = 3` としてあげればいいでしょう。

ただ、この場合 post-3 に紐づく comment が存在しているので、データ整合性を保持するために、このままではうまくいかないはずです。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');

DELETE FROM posts WHERE id = 3;

SELECT * FROM posts;
SELECT * FROM comments;

~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
ERROR 1451 (23000) at line 28: Cannot delete or update a parent 
row: a foreign key constraint fails (`myapp`.`comments`, CONSTRAINT `comments_ibfk_1` 
FOREIGN KEY (`post_id`) REFERENCES `posts` (`id`))
```

では、ここでデータの整合性を取るために、 posts のほうでレコードが削除されたら、合わせて comments のほうでも紐づくレコードが削除されるように設定してみましょう。

どうするかというと、こちらで `ON DELETE CASCADE` としてあげます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');

DELETE FROM posts WHERE id = 3;

SELECT * FROM posts;
SELECT * FROM comments;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
+----+---------+
+----+---------+-------------+
| id | post_id | comment     |
+----+---------+-------------+
|  1 |       1 | comment-1-1 |
|  2 |       1 | comment-1-2 |
+----+---------+-------------+
```

post-3 が削除されていて、 comment からも関連するレコードが消えているのが分かります。

それから DELETE だけではなくて、 UPDATE に関しても設定することができます。

`ON UPDATE CASCADE` としてみましょう。

こうしておくと、 posts のデータが更新されると、こちらの紐づくデータも更新されるようになります。

今回紐づいているのは id なので、 id を更新してみましょう。

id が 1 ではなくて、 100 だったとしてみます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');

DELETE FROM posts WHERE id = 3;
UPDATE posts SET id = 100 WHERE id = 1;

SELECT * FROM posts;
SELECT * FROM comments;

+-----+---------+
| id  | message |
+-----+---------+
|   2 | post-2  |
| 100 | post-1  |
+-----+---------+
+----+---------+-------------+
| id | post_id | comment     |
+----+---------+-------------+
|  1 |     100 | comment-1-1 |
|  2 |     100 | comment-1-2 |
+----+---------+-------------+
```

id が 100 になって、それに関連するデータも 100 で更新されているのが分かります。

外部キー制約を使うと、このようにデータの整合性が取れるようになるので、慣れておきましょう。
### 要点まとめ
外部キーの設定と合わせて、データの整合性を取るための方法を見ていきます。

- ON DELETE CASCADE：紐付くレコードを削除する。
- ON UPDATE CASCADE：紐付くレコードを更新する。</details>


<details><summary>#26 LAST_INSERT_ID()を使ってみよう</summary>

今はデータの整合性が取れている状態です。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');

SELECT * FROM posts;
SELECT * FROM comments;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-------------+
| id | post_id | comment     |
+----+---------+-------------+
|  1 |       1 | comment-1-1 |
|  2 |       1 | comment-1-2 |
|  3 |       3 | comment-3-1 |
+----+---------+-------------+
```

新しく投稿を追加したとしましょう。

適当に、’new post!’とします。

そして、この投稿にコメントを付けたかったとしましょう。

その場合ですが、INSERT文を使ってあげればいいです。

post_idについては、追加した投稿は4番目なので、4としてあげればいいです。

post_idは4、commentは’new comment’としましょう。

これでもいいのですが、commentを挿入するたびに今のように数えるのは面倒です。

そこで、MySQLでは**直前に挿入されたレコードのidを調べる命令が用意されて**いて、`LAST_INSERT_ID`とすると自動的に4が入ってくれます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');
  
INSERT INTO posts (message) VALUES
  ('new post!');

-- INSERT INTO comments (post_id, comment) VALUES
--   (4, 'new comment');

INSERT INTO comments (post_id, comment) VALUES
  (LAST_INSERT_ID(), 'new comment');

SELECT * FROM posts;
SELECT * FROM comments;
```

LAST_INSERT_IDも便利なので、慣れておいてください。
### 要点まとめ
直前に挿入されたレコードのIDを調べる命令について見ていきます。

- LAST_INSERT_ID()：直前に挿入されたレコードのidを調べる命令。</details>


<details><summary>#27 コメントにコメントをつけよう</summary>

今の投稿とコメントがどのような状態か、をコメントでまとめて見ましょう。

3 つ投稿があって、 post-1 と post-3 に comment がついている状態です。

post-1 には 2 つ comment がついていて、 post-3 には 1 つ comment がついてる状態です。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');
  
/*
post-1
  comment-1-1
  comment-1-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;
```

ではここで comment に対して comment が付けられるようにしたかったとしましょう。

たとえば comment-1-2 に対してさらに 2 つ comment が付いたとします。

さらにcomment-1-2-1にもう 1 つ comment が付いたとしましょう。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment) VALUES
  (1, 'comment-1-1'),
  (1, 'comment-1-2'),
  (3, 'comment-3-1');
  
/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;
```

その場合、どうデータを管理していくかですが、 comment がどの親を comment に持つかを保持しておけば良さそうです。

`parent_id`というカラムを用意します。

その上でまず、この 3 つの comment ですが、親となる comment がないのでこちらに関しては NULL としておきましょう。

 comment-1-2 に対して 2 つ comment を付けたので、こちらでレコードを挿入してあげます。

post は 1 に関するものでよくて、 comment はちょっと更新してあげて、そして parent_id ですが、 2 番目の comment なので、こちらは 2 にしてあげればいいでしょう。

さらに comment-1-2-1 に対してもうひとつ comment を付けたので、レコードを挿入してあげましょう。

post_id は 1 でよくて、こちらは更新してあげて、そして parent_id ですが、 4 番目の comment についているので、こちらを 4 にしてあげれば良いでしょう。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4);
  
/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  1 |       1 | comment-1-1     |      NULL |
|  2 |       1 | comment-1-2     |      NULL |
|  3 |       3 | comment-3-1     |      NULL |
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
```

</details>


<details><summary>#28 コメントのコメントを抽出してみよう</summary>

comment に comment が付けられるようになったので、次は 2 番目の comment に付いているすべての comment を抽出する方法について考えてみましょう。

その場合、まず 2 番目の comment を親に持つ comment を抽出すればいいので、`SELECT * FROM comments WHERE paren_id = 2;`　とすればcomment-1-2-1とcomment-1-2-2を取得できます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4);

/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

SELECT * FROM comments WHERE parent_id = 2;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  1 |       1 | comment-1-1     |      NULL |
|  2 |       1 | comment-1-2     |      NULL |
|  3 |       3 | comment-3-1     |      NULL |
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
+----+---------+---------------+-----------+
| id | post_id | comment       | parent_id |
+----+---------+---------------+-----------+
|  4 |       1 | comment-1-2-1 |         2 |
|  5 |       1 | comment-1-2-2 |         2 |
+----+---------+---------------+-----------+
```

では、その次の階層(comment-1-2-1-1)の comment を取得したいのですが、comments テーブルから parent_id がここで抽出した id と一致するものを抽出してあげればいいです。

 comments テーブルとこちらの結果を comments テーブルの parent_id とこの結果の id で内部結合してあげれば良さそうです。

結果を t というテーブルだと仮定して、ちょっとクエリを組み立ててみましょう。

commentsから抽出したいので、`SELECT comments.* FROM commetns JOIN t`　と書いてtを内部結合します。

その上で、commentsテーブルのparent_idとtのidを紐付けます。

このtは、`SELECT * FROM comments WHERE parent_id = 2`　の結果なので、サブクエリを使い

`commetns JOIN (
SELECT * FROM comments WHERE parent_id = 2
) AS t`

と書きます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4);

/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

SELECT * FROM comments WHERE parent_id = 2;

SELECT
  comments.*
FROM
  comments JOIN (
    SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  comments.parent_id = t.id;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  1 |       1 | comment-1-1     |      NULL |
|  2 |       1 | comment-1-2     |      NULL |
|  3 |       3 | comment-3-1     |      NULL |
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
+----+---------+---------------+-----------+
| id | post_id | comment       | parent_id |
+----+---------+---------------+-----------+
|  4 |       1 | comment-1-2-1 |         2 |
|  5 |       1 | comment-1-2-2 |         2 |
+----+---------+---------------+-----------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
```

あとは、これらの結果を縦にくっつければいいので、 `UNION ALL` を使ってあげれば良いでしょう。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4);

/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

SELECT * FROM comments WHERE parent_id = 2
UNION ALL
SELECT
  comments.*
FROM
  comments JOIN (
    SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  comments.parent_id = t.id;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  1 |       1 | comment-1-1     |      NULL |
|  2 |       1 | comment-1-2     |      NULL |
|  3 |       3 | comment-3-1     |      NULL |
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
```

ただ、見ての通り SQL が長くなりすぎますし、もっと深い階層に comment が付いたらさらに長くなってしまいます。
### 質問：コメントのコメントの抽出の仕方がわかりません
回答：

ソースコードにコメントを入れて説明文にマッピングさせましたので下記のソースコードでご確認ください。

```sql
SELECT
  comments.*
FROM
  comments JOIN (
    -- 「comments テーブルから parent_id がここで抽出した id と一致するもの」
    -- に t という名前を付けます。
    SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  -- comments テーブルと tと名前を付けたテーブルを結合させます
  comments.parent_id = t.id;

```

イメージ湧きますか？

### 質問：UNION ALLで括弧が必要なのはどういうときですか？
**回答：ORDER BYまたLIMITを使う場合、SELECTに括弧が必要になります。**

結論から言うと、#07 で括弧が必用なのは`ORDER BY` と `LIMIT` を使っているからです。

`UNION` 構文で個々の `SELECT` に `ORDER BY` または `LIMIT` を適用する場合はそれぞれの `SELECT` を括弧でくくる必用があります。

もちろん #28 の方も括弧でくくって問題ありません。その方がどこからどこまでが `SELECT` のまとまりかわかりやすくていいかもしれませんね。

### 質問：内部結合の挙動が理解できません。

```sql
SELECT comments.*
FROM comments JOIN (
  SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  comments.parent_id = t.id;

```

の内部結合によって　なぜ、parent_id = 4 だけが抽出されたのかわかりません。どの様な流れになっているのでしょうか？
**回答：**

まず、`SELECT * FROM comments WHERE parent_id = 2`このクエリの結果ですが、以下のようになっていると思います。

```
+----+---------+---------------+-----------+
| id | post_id | comment       | parent_id |
+----+---------+---------------+-----------+
|  4 |       1 | comment-1-2-1 |         2 |
|  5 |       1 | comment-1-2-2 |         2 |
+----+---------+---------------+-----------+

```

そして、このクエリの結果を`t`として、commentsと`comments.parent_id = t.id`を条件に結合しているため内部結合の結果、`comments.parent_id`の値が`t.id`の4, 5と等しいレコードのみが残ります。`comments.parent_id`が5のレコードは存在しませんので、`comments.parent_id`が4のレコードのみが抽出されることになります。

### 質問：SELECT *…ではダメですか？
**回答：UNION ALLでレコードを連結する場合、カラム数を揃える必要があります。**

```sql
SELECT * FROM ...

```

とすると結果はサブクエリとの内部結合になるため、

```
+----+---------+-----------------+-----------+----+---------+---------------+-----------+
| id | post_id | comment         | parent_id | id | post_id | comment       | parent_id |
+----+---------+-----------------+-----------+----+---------+---------------+-----------+
|  6 |       1 | comment-1-2-1-1 |         4 |  4 |       1 | comment-1-2-1 |         2 |
+----+---------+-----------------+-----------+----+---------+---------------+-----------+

```

といった形になります。

ただ今回はUNION ALLで最初のSQLと連結するためにカラム数を揃えないといけません。そこで`comments`テーブルの値だけを使っています。
### 要点まとめ
コメントのコメントを抽出してUNION ALLでまとめる方法について見ていきます。

- コメントのコメントを抽出
- UNION ALL：2つのSELECT文の結果を足し合わせる。</details>


<details><summary>#29 CTEを使ってみよう</summary>

前回見たような階層が深くなっていくデータを処理したい場合、 **CTE** という仕組みが使えます。

CTE は Common Table Expression の略で、クエリ内で使える一時的なテーブルのことです。

また CTE には**再帰的な CTE** と、**再帰的ではない CTE** があって、再帰的な CTE が今回実現したい、階層が深くなっていくデータを処理するための方法になります。

再帰的ではない CTE はサブクエリを分かりやすく書き換えるためのものです。

再帰的ではない CTE を使うには上のほうで WITH としてあげて、 CTE の名前を好きに付けてあげて、 AS としてあげて、 CTE としたいサブクエリをここに書いてあげます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4);

/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

SELECT * FROM comments WHERE parent_id = 2
UNION ALL
SELECT
  comments.*
FROM
  comments JOIN (
    SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  comments.parent_id = t.id;
  
-- CTE (Common Table Expression)
--   再帰的なCTE
--   再帰的ではないCTE

WITH t AS ( -- 括弧内にCTEとしたいサブクエリを書く。
	SELECT * FROM comments WHERE parent_id = 2
)
SELECT
  comments.*
FROM
  comments JOIN t
ON
  comments.parent_id = t.id;
-- WITH〜t.idまでが一つの命令なので、セミコロンは最後に付ける。

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  1 |       1 | comment-1-1     |      NULL |
|  2 |       1 | comment-1-2     |      NULL |
|  3 |       3 | comment-3-1     |      NULL |
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
```

再帰的ではない CTE を使うとサブクエリを上に持ってきて、やや分かりやすく書くことができるので、使い方に慣れておきましょう。
###　要点まとめ
クエリ内で使える一時的なテーブルを設定できるCTEについて見ていきます。

- CTEの概要：階層が深くなっていくデータを処理したい場合、 **CTE** という仕組みが使えます。CTE は Common Table Expression の略で、クエリ内で使える一時的なテーブルのことです。
- 再帰的ではないCTE：サブクエリを分かりやすく書き換えるためのもの。</details>


<details><summary>#30 再帰的なCTEを組み立てよう</summary>

先ずどうするかというと `WITH RECURSIVE` としてあげて CTE の名前を書いてあげます。

そして最初に実行する処理、 2 回目以降に再起的に実行する処理を書いて UNION ALL で繋いであげます。

```sql
WITH RECURSIVE t AS (
  -- n = 1
  UNION ALL
  -- n >= 2
)
```

最初に実行する処理ですが 2 番目のコメントに付いた最初の階層のコメントを取得すればいいので、このクエリをそのまま貼り付けてあげれば OK でしょう。

```sql
WITH RECURSIVE t AS (
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2
  UNION ALL
  -- n >= 2
)
```

次にその下の階層を取得する処理なので、とりあえずこちらをそのまま書いてあげましょう。

```sql
WITH RECURSIVE t AS (
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2
  UNION ALL
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN (
      SELECT * FROM comments WHERE parent_id = 2
    ) AS t
  ON
    comments.parent_id = t.id
)
```

これがどう処理されるかですが、最初の階層を取得するとそれを t にしてこちらの処理をしてくれます。

またこちらの処理が終わったらその結果を t にして、結果が無くなるまでこちらの処理を再起的に実行してくれるので、どれだけ階層が深くなっても、コメントを取得してくれるはずです。

あとはその結果をまとめて表示したいので、`SELECT * FROM t;`と書きます。

また、WITH〜SELECT * FROM t;までが一つの命令になるので、セミコロンは最後に一つになる点にも注意してください。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4);

/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

SELECT * FROM comments WHERE parent_id = 2
UNION ALL
SELECT
  comments.*
FROM
  comments JOIN (
    SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  comments.parent_id = t.id;

-- CTE (Common Table Expression)
--   再帰的なCTE
--   再帰的ではないCTE

WITH RECURSIVE t AS (
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2
  UNION ALL
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id = t.id
)
SELECT * FROM t;
```

</details>


<details><summary>#31 階層が深いコメントを抽出しよう</summary>

再帰的なCTEが書けたので実行します。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4);

/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

-- CTE (Common Table Expression)
--   再帰的なCTE
--   再帰的ではないCTE

WITH RECURSIVE t AS (
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2
  UNION ALL
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id = t.id
)
SELECT * FROM t;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  1 |       1 | comment-1-1     |      NULL |
|  2 |       1 | comment-1-2     |      NULL |
|  3 |       3 | comment-3-1     |      NULL |
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
+----+---------+-----------------+-----------+
+------+---------+-----------------+-----------+
| id   | post_id | comment         | parent_id |
+------+---------+-----------------+-----------+
|    4 |       1 | comment-1-2-1   |         2 |
|    5 |       1 | comment-1-2-2   |         2 |
|    6 |       1 | comment-1-2-1-1 |         4 |
+------+---------+-----------------+-----------+
```

さらに深い階層にコメントを付けてみて、それも取得できるか見てあげましょう。

commentsテーブルにもう一つレコードを挿入してあげます。’new comment’として、parent_idは最後のコメントにつけるので6番目になります。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INT NOT NULL AUTO_INCREMENT,
  post_id INT,
  comment VARCHAR(140),
  parent_id INT,
  PRIMARY KEY (id),
  FOREIGN KEY (post_id) REFERENCES posts(id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

INSERT INTO comments (post_id, comment, parent_id) VALUES
  (1, 'comment-1-1', NULL),
  (1, 'comment-1-2', NULL),
  (3, 'comment-3-1', NULL),
  (1, 'comment-1-2-1', 2),
  (1, 'comment-1-2-2', 2),
  (1, 'comment-1-2-1-1', 4),
  (1, 'new comment', 6);

/*
post-1
  comment-1-1
  comment-1-2
    comment-1-2-1
      comment-1-2-1-1
    comment-1-2-2
post-2
post-3
  comment-3-1
*/

SELECT * FROM posts;
SELECT * FROM comments;

-- CTE (Common Table Expression)
--   再帰的なCTE
--   再帰的ではないCTE

WITH RECURSIVE t AS (
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2
  UNION ALL
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id = t.id
)
SELECT * FROM t;

+----+---------+
| id | message |
+----+---------+
|  1 | post-1  |
|  2 | post-2  |
|  3 | post-3  |
+----+---------+
+----+---------+-----------------+-----------+
| id | post_id | comment         | parent_id |
+----+---------+-----------------+-----------+
|  1 |       1 | comment-1-1     |      NULL |
|  2 |       1 | comment-1-2     |      NULL |
|  3 |       3 | comment-3-1     |      NULL |
|  4 |       1 | comment-1-2-1   |         2 |
|  5 |       1 | comment-1-2-2   |         2 |
|  6 |       1 | comment-1-2-1-1 |         4 |
|  7 |       1 | new comment     |         6 |
+----+---------+-----------------+-----------+
+------+---------+-----------------+-----------+
| id   | post_id | comment         | parent_id |
+------+---------+-----------------+-----------+
|    4 |       1 | comment-1-2-1   |         2 |
|    5 |       1 | comment-1-2-2   |         2 |
|    6 |       1 | comment-1-2-1-1 |         4 |
|    7 |       1 | new comment     |         6 |
+------+---------+-----------------+-----------+
```

深い階層のコメントまで一気に取得できています。

再帰的な処理はやや難しいので、初回で全てを理解する必要はないのですが、こうした階層が深いデータを処理したいときにはCTEが便利ということを知っておくといいでしょう。
### 質問：再帰的CTEの動きがよくわかりません。
**回答：**

[https://dotinstall.com/lessons/basic_mysql_advanced/55930](https://dotinstall.com/lessons/basic_mysql_advanced/55930) のレッスンで説明があったように、再帰的な CTE は処理を「再帰的」に繰り返します。（「再帰」はプログラミングでよく登場する概念です。再帰の意味がわからないときは検索して調べてみましょう。）

そのため以下のような処理になります。

- まず `parent_id = 2` のデータを抽出する → `id=4, 5` のデータが抽出される
- 次に `parent_id=4`, `parent_id=5` のデータを抽出する → `id=6` のデータが抽出される
- 次に `parent_id=6` のデータを抽出する → `id=7, 8` のデータが抽出される
- 次に `parent_id=7`, `parent_id=8` のデータを抽出する → `id=9` のデータが抽出される
- 次に `parent_id=9` のデータを抽出する → 抽出されるデータはないので処理終了

このように抽出された結果を利用してどんどん数珠繋ぎ式にデータを抽出します。

ポイントは [https://dotinstall.com/lessons/basic_mysql_advanced/55930](https://dotinstall.com/lessons/basic_mysql_advanced/55930) の1分28秒の説明にあります。

> またこちらの処理が終わったらその結果を t にして、結果が無くなるまでこちらの処理を再起的に実行してくれるので、どれだけ階層が深くなっても、コメントを取得してくれるはずです。
> 

動画とあわせて見ていただきたいのですが、「その結果を t にして」がポイントです。また、「最初に実行する処理」は1回目しか実行されません。

つまり、

- 1回目の実行は「最初に実行する処理」を実行する
- 2回目の実行は**1回目**の結果を元に「2回目以降に実行する処理」を実行する
- 3回目の実行は**2回目**の結果を元に「2回目以降に実行する処理」を実行する
- 4回目の実行は**3回目**の結果を元に「2回目以降に実行する処理」を実行する

となります。

> 最後のUNION ALLで tとして表示された全てのデータが連結しているのはなぜでしょうか？
> 

これは CTE の仕様ですね。再帰的に実行された ``SELECT` 文の結果をすべて連結します。

> 最初にいただいた先生のご回答で、
> 
> 
> > 次に parent_id=9 のデータを抽出する → 抽出されるデータはないので処理終了
> > 
> 
> とありますが、こちらはparent_idではなく、idの間違えでしょうか？
> 

いいえ、以下のように処理がされているので `parent_id` で正しいです。

> まず parent_id = 2 のデータを抽出する
> 

```sql
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2

```

→ `id=4`, `id=5` のデータが取得されます。データが取得されたので再度 `SELECT` 文を実行します。

> 次に parent_id=4, parent_id=5 のデータを抽出する
> 

```sql
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id IN (4, 5)

```

→ `id=6` のデータが取得されます。データが取得されたので再度 `SELECT` 文を実行します。

> 次に parent_id=6 のデータを抽出する
> 

```sql
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id = 6

```

→ `id=7`, `id=8` のデータが取得されます。データが取得されたので再度 `SELECT` 文を実行します。

> 次に parent_id=7, parent_id=8 のデータを抽出する
> 

```sql
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id IN (7, 8)

```

→ `id=9` のデータが取得されます。データが取得されたので再度 `SELECT` 文を実行します。

> 次に parent_id=9 のデータを抽出する
> 

```sql
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id = 9

```

→ 該当するデータはありませんでした。（次の`SELECT`文の `t` になるデータがないため）処理を終了します。

元の SQL の後半部分は、

```sql
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id = t.id

```

であり、この最後の `comments.parent_id = t.id` は JOIN の条件ですね。

- `comments` テーブル
- 前回の`SELECT`の結果レコード

の2つを結合しているわけですが、その結合の条件が「`comments` テーブルの `parent_id` の値と、前回の`SELECT`の結果レコードの `id` の値が一致する場合」という条件になります。

---

> 比較演算子INについてですが、この場合ですと【「comments.parent_id」は「7」と「8」のいずれかに等しい】
> 

その通りです。</details>


<details><summary>#32 TRIGGERを設定してみよう</summary>

あるテーブルで何らかの変更が起きた時に、それをトリガーにして何らかの処理をすることができる、**トリガー**という仕組みについて見ていきます。

posts テーブルが更新されたら、他のテーブルにログが残るようにしてあげましょう。

postsテーブルに続いて、logs テーブルを作ります。

テーブル名は logs にしてあげて、 created カラムも追加します。

DATETIMEはDEFAULTで挿入された日時にしておきます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE logs ( -- テーブル名はlogs
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  created DATETIME DEFAULT NOW(), -- DATETIMEはDEFAULTで挿入された日時にする
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

SELECT * FROM posts;
```

トリガーの作り方ですが `CREATE TRIGGER` という文を使います。

そのあとに、トリガーの名前を好きに付けられるのですが、今回は posts がアップデートされたときのトリガーなので、`posts_update_trigger`と付けておきましょう。

次にどのタイミングでどんな処理をするかを指定するのですが `AFTER UPDATE ON` としてあげて posts が更新されたあとに、と指定します。

次に更新されたひとつひとつの行に対して logs テーブルにデータを挿入していきたいので、少し長いのですがこのように書いてあげれば OK です。

```sql
CREATE TRIGGER
  posts_update_trigger
AFTER UPDATE ON
  posts
FOR EACH ROW
  INSERT INTO
    logs (message)
  VALUES
    ('Updated');
```

尚、トリガーはアップデートだけに使えるというわけではなくて、 INSERT や DELETE のタイミングでも使えますし、ここも AFTER ではなくて BEFORE とすれば処理前にトリガーを実行することができます。

トリガーと logs がすでにあるとエラーになってしまうので、上の方を削除しておきましょう。

もし logs テーブルが存在していたら削除、もしトリガーが存在していたら削除としたいので、 DROP TRIGGER 文を使ってあげます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;
DROP TABLE IF EXISTS logs;
DROP TRIGGER IF EXISTS posts_update_trigger;
```

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;
DROP TABLE IF EXISTS logs;
DROP TRIGGER IF EXISTS posts_update_trigger;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE logs (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  created DATETIME DEFAULT NOW(),
  PRIMARY KEY (id)
);

CREATE TRIGGER
  posts_update_trigger
AFTER UPDATE ON
  posts
FOR EACH ROW
  INSERT INTO
    logs (message)
  VALUES
    ('Updated');

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

SELECT * FROM posts;
```

</details>


<details><summary>#33 TRIGGERを使ってみよう</summary>

トリガーをアップデートしてあげて、logsテーブルにログが残るか確認します。

今回はidが1のレコードのmessageを更新して見ましょう。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;
DROP TABLE IF EXISTS logs;
DROP TRIGGER IF EXISTS posts_update_trigger;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE logs (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  created DATETIME DEFAULT NOW(),
  PRIMARY KEY (id)
);

CREATE TRIGGER
  posts_update_trigger
AFTER UPDATE ON
  posts
FOR EACH ROW
  INSERT INTO
    logs (message)
  VALUES
    ('Updated');

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');
UPDATE posts SET message = 'post-1 updated' WHERE id = 1;

SELECT * FROM posts;
SELECT * FROM logs;

+----+----------------+
| id | message        |
+----+----------------+
|  1 | post-1 updated |
|  2 | post-2         |
|  3 | post-3         |
+----+----------------+
+----+---------+---------------------+
| id | message | created             |
+----+---------+---------------------+
|  1 | Updated | 2022-07-15 16:38:20 |
+----+---------+---------------------+
```

post-1の中身が更新されていて、logsテーブルにもUpdatedというログが残っています。

ログに、更新前、更新後の値を含める方法も見ておきましょう。

トリガーでは **OLD** と **NEW** という特殊なキーワードが使えるので **CONCAT 関数**を使って、このように値を組み立ててあげます。

そうすると message の更新前、更新後の値を挿入してくれます。

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;
DROP TABLE IF EXISTS logs;
DROP TRIGGER IF EXISTS posts_update_trigger;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE logs (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  created DATETIME DEFAULT NOW(),
  PRIMARY KEY (id)
);

CREATE TRIGGER
  posts_update_trigger
AFTER UPDATE ON
  posts
FOR EACH ROW
  INSERT INTO
    logs (message)
  VALUES
    -- ('Updated');
    (CONCAT(OLD.message, ' -> ', NEW.message)); -- CONCAT関数は文字列を連結する

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');
UPDATE posts SET message = 'post-1 updated' WHERE id = 1;

SELECT * FROM posts;
SELECT * FROM logs;

+----+----------------+
| id | message        |
+----+----------------+
|  1 | post-1 updated |
|  2 | post-2         |
|  3 | post-3         |
+----+----------------+
+----+--------------------------+---------------------+
| id | message                  | created             |
+----+--------------------------+---------------------+
|  1 | post-1 -> post-1 updated | 2022-07-15 16:47:16 |
+----+--------------------------+---------------------+
```

設定されているトリガーの一覧を見るには、 **SHOW TRIGGERS**を使います。

```sql
SELECT * FROM posts;
SELECT * FROM logs;

SHOW TRIGGERS;

+----+--------------------------+---------------------+
+----------------------+--------+-------+---------------------------------------------------------------------------------------------+--------+------------------------+-------------------------------------------------------------------------------------------+----------+----------------------+----------------------+--------------------+
| Trigger              | Event  | Table | Statement                                                                                   | Timing | Created                | sql_mode                                                                                  | Definer  | character_set_client | collation_connection | Database Collation |
+----------------------+--------+-------+---------------------------------------------------------------------------------------------+--------+------------------------+-------------------------------------------------------------------------------------------+----------+----------------------+----------------------+--------------------+
| posts_update_trigger | UPDATE | posts | INSERT INTO
    logs (message)
  VALUES
    
    (CONCAT(OLD.message, ' -> ', NEW.message)) | AFTER  | 2022-07-15 16:51:13.66 | STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION | dbuser@% | utf8mb4              | utf8mb4_general_ci   | utf8mb4_general_ci |
+----------------------+--------+-------+---------------------------------------------------------------------------------------------+--------+------------------------+-------------------------------------------------------------------------------------------+----------+----------------------+----------------------+--------------------+
```

トリガー情報が表示されますが、見づらい状態です。そんな時は最後のセミコロンを**\G**に変えてあげればOKです。

```sql
SELECT * FROM posts;
SELECT * FROM logs;

-- SHOW TRIGGERS;
SHOW TRIGGERS\G

*************************** 1. row ***************************
             Trigger: posts_update_trigger
               Event: UPDATE
               Table: posts
           Statement: INSERT INTO
    logs (message)
  VALUES
    
    (CONCAT(OLD.message, ' -> ', NEW.message))
              Timing: AFTER
             Created: 2022-07-15 16:54:08.54
            sql_mode: STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
             Definer: dbuser@%
character_set_client: utf8mb4
collation_connection: utf8mb4_general_ci
  Database Collation: utf8mb4_general_ci
```

トリガーの結果として一行表示されていて、それぞれの項目が見やすくなっています。

こうした確認方法も知っておくと良いでしょう。
### 質問：(CONCAT(OLD.message, '->', NEW.message)) とするだけで posts テーブルの message の新旧が表示されるのはなぜですか？
回答：TRIGGER を作成する際にどのテーブルに対する操作かを指定できるためです。

TRIGGER を作成する際にどのテーブルに対する操作かを指定する部分がありますね今回ですとこの部分

```sql
CREATE TRIGGER
  posts_update_trigger
AFTER UPDATE ON
  posts

```

`posts` テーブルに何か更新があったあとにという指定があります。

ここの指定により以下の `message` は `posts` テーブルのものであると指定されています

```sql
  INSERT INTO
    logs (message)
  VALUES
    -- ('Updated');
    (CONCAT(OLD.message, ' -> ', NEW.message));
```

### 要点まとめ
TRIGGERの動作確認をした後に、更新前後の値を取得する方法についても見ていきます。

- OLD,NEW：更新前、更新後の値を挿入する。
- SHOW TRIGGERS：設定されているトリガーの一覧を見ることができる。
- \G：設定しているTRIGGERの一覧が見やすく表示される。</details>


<details><summary>#34 外部ファイルからデータを読み込もう</summary>

別のファイルからデータを読み込む方法について見ていきます。

posts テーブルがあって、 id 、 message 、 likes 、 area が設定されています。

また今回は、 data.csv というファイルを用意しておきました。

一行目が見出しで、それ以降は message 、 likes 、 area のデータが入っているファイルです。

なお、データファイルには id の連番が含まれていない点に注意しておきましょう。

では、データを読み込んでいきたいのですが、同じサーバにある外部ファイルを読み込むには、 **LOAD DATA LOCAL INFILE** としてあげて、ファイル名を指定してあげます。

それをどちらのテーブルに流し込むかを **INTO TABLE** で指定してあげましょう。

それから、ファイル形式に応じていくつかのオプションが必要です。

まず、項目の区切りがカンマだったら、 **FIELDS TERMINATED BY** を使ってあげてください。

行の区切りも指定してあげる必要があって、今回 \n を改行として使っているので、`LINES TERMINATED BY '\n’`と書いてあげます。

そのうえで、データをどのフィールドに挿入するかを最後に指定してあげます。

message 、 likes 、 area の順番だったので、`(message, likes, area);`と書けば OK です。

これでデータが流し込めるはずなので、 SELECT * FROM posts で確認してみましょう。

```sql
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

LOAD DATA LOCAL INFILE 'data.csv' INTO TABLE posts
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  (message, likes, area);
  
SELECT * FROM posts;

+-----+----------+-------+-----------+
| id  | message  | likes | area      |
+-----+----------+-------+-----------+
|   1 | message  |     0 | area      | -- 見出し行がレコードとして挿入されてしまった
|   2 | post-1   |   136 | Tokyo     |
|   3 | post-2   |    27 | Osaka     |
|   4 | post-3   |   125 | Osaka     |
|   5 | post-4   |    40 | Kanazawa  |
|   6 | post-5   |    19 | Wakayama  |
|   7 | post-6   |   101 | Wakayama  |
|   8 | post-7   |   125 | Osaka     |
|   9 | post-8   |    67 | Kanazawa  |
|  10 | post-9   |   137 | Tokyo     |
|  11 | post-10  |    77 | Kumamoto  |
|  12 | post-11  |     4 | Kanazawa  |
|  13 | post-12  |   167 | Kagoshima |
|  14 | post-13  |   101 | Nara      |
|  15 | post-14  |   170 | Kumamoto  |
|  16 | post-15  |   169 | Nagoya    |
|  17 | post-16  |    59 | Wakayama  |
|  18 | post-17  |    97 | Kumamoto  |
|  19 | post-18  |   179 | Nagoya    |
|  20 | post-19  |    26 | Kumamoto  |
|  21 | post-20  |    60 | Mito      |
|  22 | post-21  |   147 | Osaka     |
|  23 | post-22  |    14 | Hiroshima |
|  24 | post-23  |   192 | Fukuoka   |
|  25 | post-24  |   124 | Kumamoto  |
|  26 | post-25  |    45 | Wakayama  |
|  27 | post-26  |    16 | Niigata   |
|  28 | post-27  |     1 | Wakayama  |
|  29 | post-28  |    14 | Wakayama  |
|  30 | post-29  |   128 | Wakayama  |
|  31 | post-30  |    46 | Fukuoka   |
|  32 | post-31  |    32 | Mito      |
|  33 | post-32  |    17 | Niigata   |
|  34 | post-33  |    30 | Tokyo     |
|  35 | post-34  |    25 | Saitama   |
|  36 | post-35  |   113 | Kyoto     |
|  37 | post-36  |   193 | Tokyo     |
|  38 | post-37  |   184 | Kyoto     |
|  39 | post-38  |   178 | Kumamoto  |
|  40 | post-39  |    50 | Niigata   |
|  41 | post-40  |    14 | Nagoya    |
|  42 | post-41  |    90 | Osaka     |
|  43 | post-42  |    34 | Hiroshima |
|  44 | post-43  |   152 | Mito      |
|  45 | post-44  |    16 | Wakayama  |
|  46 | post-45  |    48 | Nagoya    |
|  47 | post-46  |    64 | Tokyo     |
|  48 | post-47  |    94 | Hiroshima |
|  49 | post-48  |   111 | Kanazawa  |
|  50 | post-49  |    30 | Nagoya    |
|  51 | post-50  |    24 | Mito      |
|  52 | post-51  |   153 | Kanazawa  |
|  53 | post-52  |   115 | Niigata   |
|  54 | post-53  |   129 | Nara      |
|  55 | post-54  |    29 | Kumamoto  |
|  56 | post-55  |   121 | Niigata   |
|  57 | post-56  |   153 | Kagoshima |
|  58 | post-57  |    31 | Kumamoto  |
|  59 | post-58  |   120 | Kumamoto  |
|  60 | post-59  |   198 | Mito      |
|  61 | post-60  |   104 | Kumamoto  |
|  62 | post-61  |   172 | Saitama   |
|  63 | post-62  |   197 | Saitama   |
|  64 | post-63  |    88 | Kanazawa  |
|  65 | post-64  |   144 | Nagoya    |
|  66 | post-65  |    45 | Niigata   |
|  67 | post-66  |   194 | Nagoya    |
|  68 | post-67  |   102 | Hiroshima |
|  69 | post-68  |    60 | Fukuoka   |
|  70 | post-69  |    81 | Kanazawa  |
|  71 | post-70  |   190 | Fukuoka   |
|  72 | post-71  |   102 | Niigata   |
|  73 | post-72  |    38 | Kagoshima |
|  74 | post-73  |   159 | Mito      |
|  75 | post-74  |   111 | Kumamoto  |
|  76 | post-75  |   186 | Mito      |
|  77 | post-76  |   124 | Kanazawa  |
|  78 | post-77  |    66 | Hiroshima |
|  79 | post-78  |    77 | Tokyo     |
|  80 | post-79  |   122 | Kumamoto  |
|  81 | post-80  |   162 | Kanazawa  |
|  82 | post-81  |   108 | Tokyo     |
|  83 | post-82  |   156 | Hiroshima |
|  84 | post-83  |   133 | Kumamoto  |
|  85 | post-84  |    31 | Fukuoka   |
|  86 | post-85  |    37 | Tokyo     |
|  87 | post-86  |   165 | Niigata   |
|  88 | post-87  |   161 | Saitama   |
|  89 | post-88  |   137 | Fukuoka   |
|  90 | post-89  |    84 | Mito      |
|  91 | post-90  |   186 | Osaka     |
|  92 | post-91  |    74 | Nara      |
|  93 | post-92  |     2 | Mito      |
|  94 | post-93  |    10 | Nagoya    |
|  95 | post-94  |   135 | Kanazawa  |
|  96 | post-95  |   199 | Nagoya    |
|  97 | post-96  |    46 | Wakayama  |
|  98 | post-97  |    62 | Nagoya    |
|  99 | post-98  |   108 | Kanazawa  |
| 100 | post-99  |    18 | Wakayama  |
| 101 | post-100 |     6 | Nagoya    |
| 102 | post-101 |   179 | Niigata   |
| 103 | post-102 |    83 | Nagoya    |
| 104 | post-103 |   118 | Kanazawa  |
| 105 | post-104 |   116 | Nara      |
| 106 | post-105 |   140 | Fukuoka   |
| 107 | post-106 |   132 | Kumamoto  |
| 108 | post-107 |    36 | Tokyo     |
| 109 | post-108 |   131 | Fukuoka   |
| 110 | post-109 |    59 | Kagoshima |
| 111 | post-110 |   174 | Tokyo     |
| 112 | post-111 |   199 | Osaka     |
| 113 | post-112 |   166 | Kumamoto  |
| 114 | post-113 |   121 | Niigata   |
| 115 | post-114 |    44 | Fukuoka   |
| 116 | post-115 |   162 | Tokyo     |
| 117 | post-116 |    24 | Fukuoka   |
| 118 | post-117 |   134 | Kumamoto  |
| 119 | post-118 |    95 | Nara      |
| 120 | post-119 |   135 | Saitama   |
| 121 | post-120 |   102 | Tokyo     |
| 122 | post-121 |   137 | Nara      |
| 123 | post-122 |   192 | Kagoshima |
| 124 | post-123 |    51 | Nagoya    |
| 125 | post-124 |    29 | Kyoto     |
| 126 | post-125 |   155 | Kagoshima |
| 127 | post-126 |    21 | Nagoya    |
| 128 | post-127 |   161 | Hiroshima |
| 129 | post-128 |    35 | Nagoya    |
| 130 | post-129 |   138 | Fukuoka   |
| 131 | post-130 |    49 | Kagoshima |
| 132 | post-131 |   185 | Kagoshima |
| 133 | post-132 |    65 | Osaka     |
| 134 | post-133 |   135 | Saitama   |
| 135 | post-134 |   195 | Nara      |
| 136 | post-135 |   180 | Kyoto     |
| 137 | post-136 |   188 | Hiroshima |
| 138 | post-137 |   161 | Mito      |
| 139 | post-138 |   155 | Kanazawa  |
| 140 | post-139 |    96 | Tokyo     |
| 141 | post-140 |    79 | Osaka     |
| 142 | post-141 |   175 | Kanazawa  |
| 143 | post-142 |     1 | Kagoshima |
| 144 | post-143 |    50 | Wakayama  |
| 145 | post-144 |    60 | Hiroshima |
| 146 | post-145 |   100 | Saitama   |
| 147 | post-146 |    38 | Osaka     |
| 148 | post-147 |   176 | Kumamoto  |
| 149 | post-148 |   129 | Wakayama  |
| 150 | post-149 |   165 | Nara      |
| 151 | post-150 |   170 | Mito      |
| 152 | post-151 |   109 | Nagoya    |
| 153 | post-152 |   166 | Kumamoto  |
| 154 | post-153 |    62 | Kagoshima |
| 155 | post-154 |    42 | Nara      |
| 156 | post-155 |   104 | Kanazawa  |
| 157 | post-156 |   112 | Nara      |
| 158 | post-157 |   165 | Saitama   |
| 159 | post-158 |     8 | Nara      |
| 160 | post-159 |    32 | Saitama   |
| 161 | post-160 |   180 | Mito      |
| 162 | post-161 |    23 | Nagoya    |
| 163 | post-162 |    44 | Kagoshima |
| 164 | post-163 |   165 | Tokyo     |
| 165 | post-164 |   175 | Niigata   |
| 166 | post-165 |    61 | Kagoshima |
| 167 | post-166 |   192 | Kyoto     |
| 168 | post-167 |    78 | Tokyo     |
| 169 | post-168 |    89 | Osaka     |
| 170 | post-169 |   149 | Kyoto     |
| 171 | post-170 |   129 | Wakayama  |
| 172 | post-171 |   188 | Tokyo     |
| 173 | post-172 |   170 | Fukuoka   |
| 174 | post-173 |   112 | Kanazawa  |
| 175 | post-174 |   147 | Tokyo     |
| 176 | post-175 |    28 | Hiroshima |
| 177 | post-176 |   186 | Wakayama  |
| 178 | post-177 |    68 | Fukuoka   |
| 179 | post-178 |    84 | Tokyo     |
| 180 | post-179 |   105 | Kyoto     |
| 181 | post-180 |    88 | Nara      |
| 182 | post-181 |   194 | Niigata   |
| 183 | post-182 |   126 | Kagoshima |
| 184 | post-183 |    78 | Kyoto     |
| 185 | post-184 |   162 | Kyoto     |
| 186 | post-185 |    69 | Mito      |
| 187 | post-186 |    72 | Mito      |
| 188 | post-187 |    18 | Osaka     |
| 189 | post-188 |    59 | Hiroshima |
| 190 | post-189 |   127 | Nagoya    |
| 191 | post-190 |    98 | Kanazawa  |
| 192 | post-191 |   115 | Hiroshima |
| 193 | post-192 |    36 | Wakayama  |
| 194 | post-193 |   199 | Nara      |
| 195 | post-194 |    46 | Hiroshima |
| 196 | post-195 |   105 | Nagoya    |
| 197 | post-196 |    38 | Saitama   |
| 198 | post-197 |   163 | Niigata   |
| 199 | post-198 |    41 | Kagoshima |
| 200 | post-199 |   104 | Nagoya    |
| 201 | post-200 |    40 | Osaka     |
+-----+----------+-------+-----------+
```

idについては自動で連番が振られます。

一番上を見ると変なデータが紛れ込んでいます。

データの一行目をスキップしたいなら、さらに記述が必要で、こちらに IGNORE 1 LINES としてあげれば OK です。

```sql
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

LOAD DATA LOCAL INFILE 'data.csv' INTO TABLE posts
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  IGNORE 1 LINES
  (message, likes, area);
  
SELECT * FROM posts;

+-----+----------+-------+-----------+
| id  | message  | likes | area      |
+-----+----------+-------+-----------+
|   1 | post-1   |   136 | Tokyo     |
|   2 | post-2   |    27 | Osaka     |
|   3 | post-3   |   125 | Osaka     |
|   4 | post-4   |    40 | Kanazawa  |
|   5 | post-5   |    19 | Wakayama  |
|   6 | post-6   |   101 | Wakayama  |
|   7 | post-7   |   125 | Osaka     |
|   8 | post-8   |    67 | Kanazawa  |
|   9 | post-9   |   137 | Tokyo     |
|  10 | post-10  |    77 | Kumamoto  |
|  11 | post-11  |     4 | Kanazawa  |
|  12 | post-12  |   167 | Kagoshima |
|  13 | post-13  |   101 | Nara      |
|  14 | post-14  |   170 | Kumamoto  |
|  15 | post-15  |   169 | Nagoya    |
|  16 | post-16  |    59 | Wakayama  |
|  17 | post-17  |    97 | Kumamoto  |
|  18 | post-18  |   179 | Nagoya    |
|  19 | post-19  |    26 | Kumamoto  |
|  20 | post-20  |    60 | Mito      |
|  21 | post-21  |   147 | Osaka     |
|  22 | post-22  |    14 | Hiroshima |
|  23 | post-23  |   192 | Fukuoka   |
|  24 | post-24  |   124 | Kumamoto  |
|  25 | post-25  |    45 | Wakayama  |
|  26 | post-26  |    16 | Niigata   |
|  27 | post-27  |     1 | Wakayama  |
|  28 | post-28  |    14 | Wakayama  |
|  29 | post-29  |   128 | Wakayama  |
|  30 | post-30  |    46 | Fukuoka   |
|  31 | post-31  |    32 | Mito      |
|  32 | post-32  |    17 | Niigata   |
|  33 | post-33  |    30 | Tokyo     |
|  34 | post-34  |    25 | Saitama   |
|  35 | post-35  |   113 | Kyoto     |
|  36 | post-36  |   193 | Tokyo     |
|  37 | post-37  |   184 | Kyoto     |
|  38 | post-38  |   178 | Kumamoto  |
|  39 | post-39  |    50 | Niigata   |
|  40 | post-40  |    14 | Nagoya    |
|  41 | post-41  |    90 | Osaka     |
|  42 | post-42  |    34 | Hiroshima |
|  43 | post-43  |   152 | Mito      |
|  44 | post-44  |    16 | Wakayama  |
|  45 | post-45  |    48 | Nagoya    |
|  46 | post-46  |    64 | Tokyo     |
|  47 | post-47  |    94 | Hiroshima |
|  48 | post-48  |   111 | Kanazawa  |
|  49 | post-49  |    30 | Nagoya    |
|  50 | post-50  |    24 | Mito      |
|  51 | post-51  |   153 | Kanazawa  |
|  52 | post-52  |   115 | Niigata   |
|  53 | post-53  |   129 | Nara      |
|  54 | post-54  |    29 | Kumamoto  |
|  55 | post-55  |   121 | Niigata   |
|  56 | post-56  |   153 | Kagoshima |
|  57 | post-57  |    31 | Kumamoto  |
|  58 | post-58  |   120 | Kumamoto  |
|  59 | post-59  |   198 | Mito      |
|  60 | post-60  |   104 | Kumamoto  |
|  61 | post-61  |   172 | Saitama   |
|  62 | post-62  |   197 | Saitama   |
|  63 | post-63  |    88 | Kanazawa  |
|  64 | post-64  |   144 | Nagoya    |
|  65 | post-65  |    45 | Niigata   |
|  66 | post-66  |   194 | Nagoya    |
|  67 | post-67  |   102 | Hiroshima |
|  68 | post-68  |    60 | Fukuoka   |
|  69 | post-69  |    81 | Kanazawa  |
|  70 | post-70  |   190 | Fukuoka   |
|  71 | post-71  |   102 | Niigata   |
|  72 | post-72  |    38 | Kagoshima |
|  73 | post-73  |   159 | Mito      |
|  74 | post-74  |   111 | Kumamoto  |
|  75 | post-75  |   186 | Mito      |
|  76 | post-76  |   124 | Kanazawa  |
|  77 | post-77  |    66 | Hiroshima |
|  78 | post-78  |    77 | Tokyo     |
|  79 | post-79  |   122 | Kumamoto  |
|  80 | post-80  |   162 | Kanazawa  |
|  81 | post-81  |   108 | Tokyo     |
|  82 | post-82  |   156 | Hiroshima |
|  83 | post-83  |   133 | Kumamoto  |
|  84 | post-84  |    31 | Fukuoka   |
|  85 | post-85  |    37 | Tokyo     |
|  86 | post-86  |   165 | Niigata   |
|  87 | post-87  |   161 | Saitama   |
|  88 | post-88  |   137 | Fukuoka   |
|  89 | post-89  |    84 | Mito      |
|  90 | post-90  |   186 | Osaka     |
|  91 | post-91  |    74 | Nara      |
|  92 | post-92  |     2 | Mito      |
|  93 | post-93  |    10 | Nagoya    |
|  94 | post-94  |   135 | Kanazawa  |
|  95 | post-95  |   199 | Nagoya    |
|  96 | post-96  |    46 | Wakayama  |
|  97 | post-97  |    62 | Nagoya    |
|  98 | post-98  |   108 | Kanazawa  |
|  99 | post-99  |    18 | Wakayama  |
| 100 | post-100 |     6 | Nagoya    |
| 101 | post-101 |   179 | Niigata   |
| 102 | post-102 |    83 | Nagoya    |
| 103 | post-103 |   118 | Kanazawa  |
| 104 | post-104 |   116 | Nara      |
| 105 | post-105 |   140 | Fukuoka   |
| 106 | post-106 |   132 | Kumamoto  |
| 107 | post-107 |    36 | Tokyo     |
| 108 | post-108 |   131 | Fukuoka   |
| 109 | post-109 |    59 | Kagoshima |
| 110 | post-110 |   174 | Tokyo     |
| 111 | post-111 |   199 | Osaka     |
| 112 | post-112 |   166 | Kumamoto  |
| 113 | post-113 |   121 | Niigata   |
| 114 | post-114 |    44 | Fukuoka   |
| 115 | post-115 |   162 | Tokyo     |
| 116 | post-116 |    24 | Fukuoka   |
| 117 | post-117 |   134 | Kumamoto  |
| 118 | post-118 |    95 | Nara      |
| 119 | post-119 |   135 | Saitama   |
| 120 | post-120 |   102 | Tokyo     |
| 121 | post-121 |   137 | Nara      |
| 122 | post-122 |   192 | Kagoshima |
| 123 | post-123 |    51 | Nagoya    |
| 124 | post-124 |    29 | Kyoto     |
| 125 | post-125 |   155 | Kagoshima |
| 126 | post-126 |    21 | Nagoya    |
| 127 | post-127 |   161 | Hiroshima |
| 128 | post-128 |    35 | Nagoya    |
| 129 | post-129 |   138 | Fukuoka   |
| 130 | post-130 |    49 | Kagoshima |
| 131 | post-131 |   185 | Kagoshima |
| 132 | post-132 |    65 | Osaka     |
| 133 | post-133 |   135 | Saitama   |
| 134 | post-134 |   195 | Nara      |
| 135 | post-135 |   180 | Kyoto     |
| 136 | post-136 |   188 | Hiroshima |
| 137 | post-137 |   161 | Mito      |
| 138 | post-138 |   155 | Kanazawa  |
| 139 | post-139 |    96 | Tokyo     |
| 140 | post-140 |    79 | Osaka     |
| 141 | post-141 |   175 | Kanazawa  |
| 142 | post-142 |     1 | Kagoshima |
| 143 | post-143 |    50 | Wakayama  |
| 144 | post-144 |    60 | Hiroshima |
| 145 | post-145 |   100 | Saitama   |
| 146 | post-146 |    38 | Osaka     |
| 147 | post-147 |   176 | Kumamoto  |
| 148 | post-148 |   129 | Wakayama  |
| 149 | post-149 |   165 | Nara      |
| 150 | post-150 |   170 | Mito      |
| 151 | post-151 |   109 | Nagoya    |
| 152 | post-152 |   166 | Kumamoto  |
| 153 | post-153 |    62 | Kagoshima |
| 154 | post-154 |    42 | Nara      |
| 155 | post-155 |   104 | Kanazawa  |
| 156 | post-156 |   112 | Nara      |
| 157 | post-157 |   165 | Saitama   |
| 158 | post-158 |     8 | Nara      |
| 159 | post-159 |    32 | Saitama   |
| 160 | post-160 |   180 | Mito      |
| 161 | post-161 |    23 | Nagoya    |
| 162 | post-162 |    44 | Kagoshima |
| 163 | post-163 |   165 | Tokyo     |
| 164 | post-164 |   175 | Niigata   |
| 165 | post-165 |    61 | Kagoshima |
| 166 | post-166 |   192 | Kyoto     |
| 167 | post-167 |    78 | Tokyo     |
| 168 | post-168 |    89 | Osaka     |
| 169 | post-169 |   149 | Kyoto     |
| 170 | post-170 |   129 | Wakayama  |
| 171 | post-171 |   188 | Tokyo     |
| 172 | post-172 |   170 | Fukuoka   |
| 173 | post-173 |   112 | Kanazawa  |
| 174 | post-174 |   147 | Tokyo     |
| 175 | post-175 |    28 | Hiroshima |
| 176 | post-176 |   186 | Wakayama  |
| 177 | post-177 |    68 | Fukuoka   |
| 178 | post-178 |    84 | Tokyo     |
| 179 | post-179 |   105 | Kyoto     |
| 180 | post-180 |    88 | Nara      |
| 181 | post-181 |   194 | Niigata   |
| 182 | post-182 |   126 | Kagoshima |
| 183 | post-183 |    78 | Kyoto     |
| 184 | post-184 |   162 | Kyoto     |
| 185 | post-185 |    69 | Mito      |
| 186 | post-186 |    72 | Mito      |
| 187 | post-187 |    18 | Osaka     |
| 188 | post-188 |    59 | Hiroshima |
| 189 | post-189 |   127 | Nagoya    |
| 190 | post-190 |    98 | Kanazawa  |
| 191 | post-191 |   115 | Hiroshima |
| 192 | post-192 |    36 | Wakayama  |
| 193 | post-193 |   199 | Nara      |
| 194 | post-194 |    46 | Hiroshima |
| 195 | post-195 |   105 | Nagoya    |
| 196 | post-196 |    38 | Saitama   |
| 197 | post-197 |   163 | Niigata   |
| 198 | post-198 |    41 | Kagoshima |
| 199 | post-199 |   104 | Nagoya    |
| 200 | post-200 |    40 | Osaka     |
+-----+----------+-------+-----------+
```

外部ファイルの形式によっては、さらにオプションが必要な場合もありますが、こうした操作もできるようになっておきましょう。
### 質問：カンマ区切りでないファイルでも FIELDS TERMINATED BY が使えるのですか？
回答：はい、使えます。

下記のCSV取り込みの`FIELDS TERMINATED BY`についてですね。

```sql
LOAD DATA LOCAL INFILE 'data.csv' INTO TABLE posts
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  IGNORE 1 LINES
  (message, likes, area);

```

はい。100%ご理解の通り、「取り込みたいCSVファイルの区切り方がカンマであれば」という意味でOKです！

実はこの`LOAD DATA・・`ですが、対象がCSVとは限らず、カンマ以外で区切られたファイル、例えば

```
post-1 136 Tokyo
post-2 27 Osaka

```

のようにスペース区切りのファイルでも対応することができます。

そのような場合は`FIELDS TERMINATED BY ' '（スペース）`とすればOKですね。

ということでご理解の通り`FIELDS TERMINATED BY`は様々な形式のファイルに対応するために、区切り方を指定しているというわけです。
### 質問：\nを改行として使っているというのは？
回答：環境によっては改行が別の表現になることもあります。

行を区切るための改行コードは OS によって異なっており、`\n` 以外に主に `\r` と `\r\n` があります。Linux OS では通常 `\n` を使いますので、今回はその改行コードを使用しております。

ご興味がありましたら「改行コード」で Google 検索してみてください。

**回答2：**

```
LINES TERMINATED BY '\n'
```

は行の終わりの文字を指定していますが，これは何も指定しないと

```
'\n'
```

が指定されたものとして扱われるので，入れても入れなくても変化がないかと思います．

例えばWindowsで作成されたファイルは行末が

```
\r\n
```

になっていることもあるので，そういった場合に

```
LINES TERMINATED BY '\r\n'
```

と指定する場合もあります。
### 要点まとめ
別のファイルからデータを読み込む方法について見ていきます。

- LOAD DATA LOCAL INFILE：同じサーバにある外部ファイルを読み込む。
- FIELDS TERMINATED：読み込むデータの項目の区切りがカンマ(,)の場合に使う。
- LINES TERMINATED：行の区切りを指定する。
- IGNORE n LIKES：データのn行目をスキップする。</details>


<details><summary>#35 インデックスを使ってみよう</summary>

posts

| id | messagae | likes | area |
| --- | --- | --- | --- |
| 1 | post-1 | 136 | Tokyo |
| 2 | post-2 | 27 | Osaka |
| 3 | post-3 | 125 | Osaka |
| … |  |  |  |
| 34 | post-34 | 33 | Kyoto |
| 35 | post-35 | 28 | Tokyo |

今、例えばこういった posts テーブルがあったとして、このテーブルから area が Kyoto のレコードを抽出したかったとします。

その場合、MySQLでは先頭から順番に一つずつ見ていって、合致するものを探していきます。

只、この方法だと全てのレコードを最初から最後まで見ていくことになるので、レコード数が膨大になってくるとすごく時間がかかるという問題があります。

そこで、データベースではよく検索されるカラムに対して**インデックス**というものを付けることができます。

インデックスは、元データとは別に保持される**索引のようなデータ**で、どのデータがどこにあるのか、やや複雑なデータ構造で保持されています。

このデータ構造は、**あらかじめ値を整列させたうえで範囲ごとに区切られているため、上から順番にひとつずつ見ていくよりも、ずっと効率的に検索することができるという仕組みになって**います。

インデックスを作ると検索は早くなるのですが、**逆にデータの挿入や更新、そして削除をすると、インデックスのデータをいちいち再構築しなくてはなりません**。

また、**インデックスの分、データベースに必要な容量が増えてしまうというデメリット**もあります。

したがって、**インデックスはデータベースの運用状況を見ながら、必要なカラムに対して付け外しができるようになっておくといい**でしょう。

主キーに関しては **PRIMARY** というインデックスが実は自動的に生成されます。

したがって、この例だと id を使った検索はすでに高速に動作するということを知っておくといいでしょう。
### 要点まとめ
検索を高速化することができるインデックスについて見ていきます。

- インデックスの概要
    - 元データとは別に保持される**索引のようなデータ**で、どのデータがどこにあるのか、やや複雑なデータ構造で保持されている。
    - インデックスは、指定した列に対して作られる。
    - インデックスが存在する列に対して検索が行われた場合、DBMSは自動的にインデックス使用を試みるため、高速になることが多い。
- インデックス設定時の注意点
    - 逆にデータの挿入や更新、そして削除をすると、インデックスのデータをいちいち再構築しなくてはならない。
    - 索引情報を保持するために、インデックスの分データベースに必要な容量が増えてしまう。</details>


<details><summary>#36 EXPLAINでクエリを分析しよう</summary>

先ずはインデックスで今設定されているものを確認します。

前回説明した通り、主キーには自動的にインデックスが作られているはずなので、その情報が出てくるはずです。

`SHOW INDEX FROM テーブル名`と書きます。

見てみると、横に長く見づらいので`\G`を使います。

```sql
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

LOAD DATA LOCAL INFILE 'data.csv' INTO TABLE posts
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  IGNORE 1 LINES
  (message, likes, area);
  
SHOW INDEX FROM posts;

+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| posts |          0 | PRIMARY  |            1 | id          | A         |          25 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+

-- \Gを使用
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

LOAD DATA LOCAL INFILE 'data.csv' INTO TABLE posts
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  IGNORE 1 LINES
  (message, likes, area);
  
SHOW INDEX FROM posts\G

*************************** 1. row ***************************
        Table: posts
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: id
    Collation: A
  Cardinality: 33
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment:
```

id のフィールドに PRIMARY というインデックスが設定されています。

それから実際に id を使った検索で、このインデックスが使われているかどうか、確認する方法も見ておきます。

では id を使った検索として、 `SELECT * FROM posts WHERE id = 30` を使ってみましょう。

このようにクエリを書いてあげて、このクエリでどのインデックスが使われているかは、先頭に **EXPLAIN** を付けてあげれば調べることができます。結果は少し長くなるので、 \G にしてあげましょう。

```sql
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20),
  PRIMARY KEY (id)
);

LOAD DATA LOCAL INFILE 'data.csv' INTO TABLE posts
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  IGNORE 1 LINES
  (message, likes, area);
  
SHOW INDEX FROM posts\G
EXPLAIN SELECT * FROM posts WHERE id = 30\G

*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: posts
         type: const
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: const
         rows: 1
        Extra:
```

見てあげるといろいろありますが、見るべき項目は **key** と **rows** で、 key はクエリで実際に使われたインデックスの名前、 rows は検索対象となるレコード数の見積もりです。

まずは、このようにインデックスについて確認できるようになっておきましょう。
### 要点まとめ
設定されているインデックスを確認した後にEXPLAINの使い方について見ていきます。

- SHOW INDEX：INDEXの確認を行う。SHOW INDEX FROM テーブル名
- EXPLAIN：クエリの実行方法を調べて、表示する。</details>


<details><summary>#37 インデックスを設定してみよう</summary>

