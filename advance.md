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


<details><summary>
