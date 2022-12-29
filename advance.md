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


<details><summary>
