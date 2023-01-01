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
