# ð»dotinstall MySQLå¥é å¿ç¨ç·¨

**<details><summary>#01 éè¨é¢æ°ãä½¿ã£ã¦ããã</summary>**

ãã¼ã¿éè¨ãè¤æ°ã®ãã¼ãã«ãæ±ã£ã¦ããå¿ç¨ç·¨ã«å¥ã£ã¦ããã¾ããããã³ã¼ãã«ã¤ãã¦ã¯åºç¤ç·¨ã§ä½¿ã£ã posts ãã¼ãã«ãä½¿ã£ã¦ããã¾ããã message ã¯åãããããé£çªã«ãã¦ããã®ã¨ãéè¨ãåæãæ±ã£ã¦ããããã®ã§ãå°ãã¬ã³ã¼ããå¢ããã¦ããã¾ããä»ã9ã¬ã³ã¼ãããç¶æã§ããããããä»åã¯ãèª¬æã®é½åä¸post-5ãpost-8ãNULLã«ãã¦ããã¾ãã

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

ä»åãã¡ãã®ãã¼ã¿ãä½¿ã£ã¦ãéè¨å¦çã«ã¤ãã¦è¦ã¦ããã¾ããã¾ãããã¼ã¿ã®åæ°ãèª¿ã¹ãã«ã¯ COUNT é¢æ°ãä½¿ãã¾ããä» likes ã«ã¯ãµãã¤ NULL ãããã®ã§ããã likes ã® NULL ãé¤ããåæ°ãèª¿ã¹ãã«ã¯ `COUNT(likes)` ã¨ãã¦ãããã° OK ã§ãããããã¯ãåã«å¨ä½ã®è¡æ°ãåå¾ããããªã NULL ã§ã¯ãªããã¨ãä¿è¨¼ããã¦ããä¸»ã­ã¼ã§æ°ãã¦ãããã¨ããã§ãããããããã£ã¦ã `COUNT(id)` ã¨ãã¦ãããã°ããã§ãã­ããããã¯ `SELECT COUNT(*)` ã¨ãã¦ãå¨ä½ã®è¡æ°ãåå¾ãããã¨ãã§ãã¾ãã

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
  
SELECT COUNT(likes) FROM posts; # likesã®æ°ã ããåå¾
SELECT COUNT(id) FROM posts; # idã®æ°ãåå¾
SELECT COUNT(*) FROM posts; # å¨ä½ã®è¡æ°ãåå¾

+--------------+
| COUNT(likes) |
+--------------+
|            7 |
+--------------+
# likesã«ãµãã¤NULLãããã®ã§7
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
# å¨ä½ã®è¡æ°ã¯ã©ã¡ãã9è¡ã«ãªã£ã¦ãã
```

åè¨ãå¹³åãªã©ãæ±ãããã¨ãã§ãã¾ãã

- likesã®åè¨ãæ±ãã¦ã¿ã¾ãããããã®å ´åã¯SUMé¢æ°ãä½¿ãã°OKã§ãã
- å¹³åã ã£ããAVGé¢æ°ã
- æå¤§å¤ã¯MAXé¢æ°ã
- æå°å¤ã¯MINé¢æ°ã

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

SELECT SUM(likes) FROM posts; # SUMã¯åè¨å¤
SELECT AVG(likes) FROM posts; # AVGã¯å¹³åå¤
SELECT MAX(likes) FROM posts; # MAXã¯æå¤§å¤
SELECT MIN(likes) FROM posts; # MINã¯æå°å¤

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

éè¨ã®ããã®é¢æ°ãä½¿ããããã«ãªã£ã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
åè¨ãå¹³åãªã©ãéè¨ã®ããã®é¢æ°ãæ±ãæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- COUNT()ï¼ãã¼ã¿ã®åæ°ãèª¿ã¹ã
- SUM()ï¼åè¨å¤
- AVG()ï¼å¹³åå¤
- MAX()ï¼æå¤§å¤
- MIN()ï¼æå°å¤</details>


**<details><summary>#02 GROUP BYã§ã°ã«ã¼ãåããã</summary>**

ã©ãã§ãã®æç¨¿ããããããè¡¨ãareaã¨ããã«ã©ã ãè¿½å ãã¦ã¿ã¾ãããããã¼ã¿ã®æ¿å¥ããã¦ããã¾ããareaã«ã©ã ãè¶³ãã¦ãé©å½ãªãã¼ã¿ãè¿½å ãã¦ããã¾ããããarea ã¨ãã¦ã 'Tokyo' ã¨ 'Fukuoka' ã¨ 'Osaka' ãè¿½å ããã¨ãã¾ããããã§ã¯ã `SELECT * FROM posts` ã§è¦ã¦ããã¾ãã

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  likes INT,
  area VARCHAR(20), # areaã¨ããã«ã©ã ãè¿½å 
  PRIMARY KEY (id)
);

#areaã«ã©ã ãè¶³ãã¦ãé©å½ãªãã¼ã¿ãè¿½å ãã¦ãã
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

ããã§ãã®ãã¼ã¿ã«ã©ã® area ãå«ã¾ãã¦ããããä¸è¦§ã§è¦ããã£ãã¨ãã¾ãããã®å ´åã DISTINCT ã¨ããã­ã¼ã¯ã¼ããä½¿ãã°ãéè¤ãçãããã¼ã¿ãæ½åºãããã¨ãã§ãã¾ãã`DISTINCT area` ã¨ãã¦ã¿ã¾ãããã

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
SELECT DISTINCT area FROM posts; # DISTINCTã§éè¤ãããã¼ã¿ãæ½åºãããã¨ãå¯è½

+---------+
| area    |
+---------+
| Tokyo   |
| Fukuoka |
| Osaka   |
+---------+
# areaã®ä¸è¦§ãè¡¨ç¤ºããã
```

ããããæä½ãã§ããããã«ãªã£ã¦ããã¾ãããã

æ¬¡ã«ããã®areaæ¯ã®ããã­æ°ã®åè¨ãæ¬²ããã£ãå ´åãã¬ã³ã¼ããã°ã«ã¼ãåãã¦ãããGROUP BYãä½¿ãã¾ããåè¨ãªã®ã§ SUM ã® likes ã¨ãã¦ããã¦ã GROUP BY ã§ area ãã¨ã®åè¨ãªã®ã§ã area ãæå®ãã¦ããã¾ãããã

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
# areaæ¯ã®likesã®åè¨ãè¡¨ç¤ºããã¦ãã
```

ããããéè¨ãã§ããããã«ãªã£ã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
GROPU BYãä½¿ã£ã¦ãã¨ãªã¢ãã¨ã®åè¨ãéè¨ããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- DISTINCTï¼éè¤ãçãããã¼ã¿ãæ½åºãããã¨ãã§ããã
- GROUP BYï¼ã¬ã³ã¼ããã°ã«ã¼ãåãã¦ãããã</details>


**<details><summary>#03 HAVINGã§æ½åºæ¡ä»¶ãæå®ããã</summary>**

ä»ã¯areaæ¯ã®likesã®åè¨ãåºã¦ããç¶æã§ãã

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

SUMã®çµæãã30ããå¤§ããªãã®ã ããæ½åºãããã£ãã¨ãã¾ãããã®å ´åã§ãããé·ããªãã®ã§æ¹è¡ãå¥ããªããæ¸ãã¦ãããã¨ããã®çµæã§ likes ã®åè¨ã 30 ããå¤§ãããã®ãæ½åºãããã®ã§ã WHERE ãä½¿ã£ã¦ããã®ããã«æ¸ãããã¨ããã§ãããå®ã¯ããã§ã¯ãã¾ãããã¾ããã

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
# ã¨ã©ã¼ãè¡¨ç¤ºããã
```

ããã¯ã WHERE ã GROUP BY ããåã«å¦çãããã®ã§ãå¾ãã«æ¸ãã¦ã¯ãããªãã¨ããã«ã¼ã«ãããããã§ãããã GROUP BY ããçµæã«æ¡ä»¶ãä»ãããã£ãã WHERE ã§ã¯ãªãã HAVING ã¨ããå½ä»¤ãä½¿ã£ã¦ãããå¿è¦ãããã¾ãã

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
HAVING # GROUP BYããçµæã«æ¡ä»¶ãã¤ãããå ´åã¯ãWHEREã§ã¯ãªããHAVINGã§å½ä»¤ãããã¨
  SUM(likes) > 30;

+---------+------------+
| area    | SUM(likes) |
+---------+------------+
| Fukuoka |         39 |
| Tokyo   |         35 |
+---------+------------+
# GROUP BYãä½¿ã£ãæã¯ãã®ç¹ã«æ³¨æãã¦ãããã¨
```

WHEREã¨çµã¿åããã¦ä½¿ã£ãä¾ãè¦ã¦ã¿ã¾ããããGROUP BY ããåã«æ¸ãå¿è¦ãããã®ã§ããã¨ãã°ã¨ãããã¨ã§ããã¡ãã§ WHERE likes ã 10 ããå¤§ãããã®ãã¨æ¸ãã¦ããã¾ãããããã®å ´åã§ãããâ  posts ã®å¨ã¦ã®ã¬ã³ã¼ããã likes ã 10 ããå¤§ãããã®ã ããæ½åºãããã¨ã« â¡GROUP BY ãã¦ããã¾ããä»åã ã¨ likes ã 10 ããå¤§ãããã®ãªã®ã§ã Tokyo ã® 12 ã Tokyo ã® 11 ã Fukuoka ã® 31 ãã°ã«ã¼ãåãã¦éè¨ãããã¯ãã§ãã

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
# â  posts ã®å¨ã¦ã®ã¬ã³ã¼ããã likes ã 10 ããå¤§ãããã®ã ããæ½åºãããã¨ã« â¡GROUP BY ãã¦ããã

+---------+------------+
| area    | SUM(likes) |
+---------+------------+
| Fukuoka |         31 |
| Tokyo   |         23 |
+---------+------------+
```

ããããæåãçè§£ãã¦ããã¾ãããã
### è³ªåï¼WHEREå¥ã«SUMãå¥ããã¨ã¨ã©ã¼ã«ãªãã¾ããã
åç­ï¼WHEREå¥ã«SUMãªã©ã®éç´é¢æ°ãæå®ãããã¨ã¯ã§ãã¾ããã

WHEREå¥ã«SUMãªã©ã®éç´é¢æ°ã¯æå®ã§ãã¾ãããéç´ããå¤ãæ¡ä»¶ã¨ãã¦æå®ãããå ´åã«ã¯HAVINGãä½¿ã£ã¦ãã ããã
### è¦ç¹ã¾ã¨ã
GROPU BYããçµæã«æ½åºæ¡ä»¶ãè¨­å®ã§ããHAVINGã«ã¤ãã¦è¦ã¦ããã¾ãã

- HAVINGï¼GROUP BYã§åºããçµæã«æ½åºæ¡ä»¶ãè¨­å®ããå ´åã«ä½¿ã
- WHEREãä½¿ãå ´åã®æ³¨æç¹ï¼WHEREã¯GROUP BYããåã«å¦çãããã®ã§ãGROUP BYããå¾ãã«æ¸ãã¦ã¯ãããªãã¨ããã«ã¼ã«ããã</details>


**<details><summary>#04 IF()ãCASEãæ±ã£ã¦ã¿ãã</summary>**

æ¡ä»¶ã«ãã£ã¦éãå¤ãæ½åºããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãããããã¨ãã°ã likes ã®æ°ã«å¿ãã¦ãã¼ã ãåãã¦ã¿ããã¨ãã£ãã·ã¼ã³ãæ³å®ãã¦ã¿ã¾ããã§ã¯ã 10 ããå¤§ããã£ãã A ãã¼ã ãããä»¥å¤ã¯ B ãã¼ã ã¨ãããå ´åã IF likes ã 10 ããå¤§ããã£ãã A ããããããªãã£ãã B ã¨æ¸ãã¦ããã¾ããåããããã team ã¨ããå¥åã«ãã¦ããã¦ãæ½åºãã¦ã¿ã¾ãããã

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
  IF(likes > 10, 'A', 'B') AS team # likesã®æ°ã10ããå¤ãå ´åã¯Aãã¼ã ãããä»¥å¤ã¯Bãã¼ã 
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

ãããã¯ CASE ã¨ããå½ä»¤ãä½¿ãã¦ããã®å ´åã¯ãããã§ãæ¡ä»¶ãå¢ãããã®ã§ããã¨ãã°ã likes ã 10 ããå¤§ããã£ãã A ãã¼ã ã 10 ä»¥ä¸ã ãã© 5 ããå¤§ããã£ãã B ãã¼ã ãããã¦ããä»¥å¤ã¯ C ãã¼ã ã¨æ¸ãããå ´åã¯ããã®ããã«æ¸ãã¦ãããã° OK ã§ãã

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
    WHEN likes > 10 THEN 'A' # likesã®æ°ã10ããå¤ãå ´åã¯Aãã¼ã 
    WHEN likes > 5 THEN 'B' # likesã®æ°ã10ä»¥ä¸ã ãã©ã5ããå¤ãå ´åã¯Bãã¼ã 
    ELSE 'C' # ããä»¥å¤ã¯Cãã¼ã 
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

ããããå¦çãæ¸ããããã«ãªã£ã¦ããã¾ãããã
### è³ªåï¼IFã®çç¥ããæ¸ãæ¹ããããããã¾ããã
åç­ï¼ãã­ã¥ã¡ã³ããåç§ãã¦ã¿ãã¨è¯ãã§ãããã

`IF ( likes > 10,  'A',  'B' )  AS team  FROM  posts;`

ï¼ã¤ç®ã®æ¡ä»¶å¼(likes > 10)ãçã®å ´åï¼çªç®ã®å¤(âAâ)ããï¼ã¤ç®ã®æ¡ä»¶å¼(likes > 10)ãå½ã®å ´åï¼çªç®(âBâ)ã®å¤ãè¿ãã¾ãã

ãã¡ããåç§ãã¦ã¿ã¦ãã ããã[https://dev.mysql.com/doc/refman/5.6/ja/control-flow-functions.html#function_if](https://dev.mysql.com/doc/refman/5.6/ja/control-flow-functions.html#function_if)

### è¦ç¹ã¾ã¨ã
æ¡ä»¶ã«ãã£ã¦éãå¤ãæ½åºããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- IF()ï¼æ¡ä»¶ã«ãã£ã¦éãå¤ãæ½åºããæ¹æ³
- CASEï¼IFã¨éãããããã§ãæ¡ä»¶ãå¢ãããã¨ãã§ãã</details>


**<details><summary>#05 æ½åºçµæãå¥ã®ãã¼ãã«ã«ããã</summary>**

æ½åºçµæãå¥ãã¼ãã«ã¨ãã¦åãåºãæ¹æ³ãè¦ã¦ããã¾ãã`CREATE TABLE ãã¼ãã«å`ã¨ãããã¨ã« AS ã«ç¶ãã¦æ½åºæ¡ä»¶ãæå®ãã¦ãããã° OK ã§ããä»åã¯ area ã Tokyo ã®ã¬ã³ã¼ãã ããæ½åºãã¦ posts_tokyo ã¨ãããã¼ãã«ã«ãã¦ããã¾ãããã

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

ãã®èãæ¹ãä½¿ãã°ãã¼ãã«ã®ã³ãã¼ãä½ããã¨ãã§ãã¾ãã­ããã¼ãã«åã¯ posts_copy ã¨ãã¦ããã¦ã AS ã«ç¶ããæ½åºæ¡ä»¶ã§ WHERE ãå¤ãã¦ãããã°ãå¨ã¦ã®ã¬ã³ã¼ãã posts_copy ã«ã³ãã¼ãããã¯ãã§ãã

```sql
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
CREATE TABLE posts_copy AS SELECT * FROM posts;
```

ãã¼ã¿ã®æ§é ã ãã³ãã¼ãã¦ä¸­èº«ã®ã¬ã³ã¼ãã¯ãããªãããã¨ããå ´åã¯ LIKE ãã¼ãã«åã¨ãã¦ãããã° OK ã§ãã

```sql
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
CREATE TABLE posts_copy AS SELECT * FROM posts;
CREATE TABLE posts_skeleton LIKE posts;
```

ããããããããã®ãã¼ãã«ãæ¢ã«å­å¨ãã¦ãããã¨ã©ã¼ã«ãªã£ã¦ãã¾ãã®ã§ãå®è¡ããåº¦ã«ããããã®ãã¼ãã«ãæ¶ãã¦ããããã®ã§ DROP TABLE IF EXISTS ãä½¿ã£ã¦ããã¦ããã¾ãããã

```sql
DROP TABLE IF EXISTS posts_tokyo;
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';
CREATE TABLE posts_copy AS SELECT * FROM posts;
CREATE TABLE posts_skeleton LIKE posts;
```

ã§ã¯ã posts_tokyo ã posts_copy ã posts_skeleton ãå­å¨ãã¦ããããåé¤ããã¨æ¸ãã¦ããã¾ãããããã®ããã§ SHOW TABLES ã§ãã¼ãã«ã®ä¸è¦§ãè¡¨ç¤ºãã¦ã¿ã¾ãããããããããããã®ãã¼ãã«ã®ä¸­èº«ãè¡¨ç¤ºãã¦ã¿ã¾ããããposts_tokyo ã®ä¸­èº«ãããã¦ posts_copy ã®ä¸­èº«ãããã¦ skeleton ã®ã»ãã¯ä¸­èº«ãä½ããªãã®ã§ãè¡¨ç¤ºã¯ããã¾ããããä¸å¿ç¢ºããã¦ã¿ã¾ãããã

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

# SELECT * FROM posts_skeleton;ã¯ããã®ãã¼ãã«ãªã®ã§ä½ãè¡¨ç¤ºãããªã
```

3 ã¤ã®ãã¼ãã«ãè¿½å ããã¦ãã¦ãæ¬¡ã Tokyo ã ãã®ãã¼ãã«ããã®æ¬¡ãã³ãã¼ãªã®ã§å¨ã¦ã®ã¬ã³ã¼ããããã¦æå¾ã¯ç©ºã®ãã¼ãã«ã§ä½ãè¡¨ç¤ºããã¦ããªãã®ã§ããã¾ããã£ã¦ããããã§ãã

ãã¼ãã«ããå¥ã®ãã¼ãã«ãä½ãæ¹æ³ãç¥ã£ã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
æ½åºçµæãå¥ãã¼ãã«ã¨ãã¦åãåºãæ¹æ³ãè¦ã¦ããã¾ãã

- CREATE TABLE ... AS ...ï¼ãã¼ãã«ããå¥ãã¼ãã«ãä½æãã
- CREATE TABLE ... LIKE ...ï¼ãã¼ã¿ã®æ§é ã ãã³ãã¼ãã¦ä¸­èº«ã®ã¬ã³ã¼ãã¯ãããªãããã¨ããå ´åã¯`LIKE ãã¼ãã«å`ã¨ãã</details>


<details><summary>#06 VIEWãæ±ã£ã¦ã¿ãã</summary>

ã³ã¼ãã®ã»ãã¯ posts_tokyo ã ãã«ãã¦ããã¾ããã

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

ãã® posts_tokyo ã§ããã posts ãã¼ãã«ããä¸é¨ã®ãã¼ã¿ãæ½åºããå¥ã®ãã¼ãã«ãªã®ã§ãå½ç¶ã§ãããã¡ããæ´æ°ãã¦ã posts_tokyo ãèªåçã«æ´æ°ãããã¨ããè¨³ã§ã¯ããã¾ããã

posts ãã¼ãã«ãæ´æ°ãã¦ããã¾ããid ã 1 ã®ã¬ã³ã¼ãã® likes ã 15 ã«ãã¦ããã¾ãããããã®ããã§ posts ãã¼ãã«ã¨ã posts_tokyo ãã¼ãã«ã®ä¸­èº«ãç¢ºèªãã¦ã¿ã¾ããposts ã®ã»ãã¯æ´æ°ããã¾ããã posts_tokyo ã®ã»ãã¯æ´æ°ãããªãã¯ãã§ãã

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

UPDATE posts SET likes = 15 WHERE id = 1; # idã1ã®ã¬ã³ã¼ãã®likesã®æ°ã15ã«ãã

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
# postsãã¼ãã«ã¯15ã«æ´æ°ããã¦ãã
+----+---------+-------+-------+
| id | message | likes | area  |
+----+---------+-------+-------+
|  1 | post-1  |    12 | Tokyo |
|  3 | post-3  |    11 | Tokyo |
|  5 | post-5  |     8 | Tokyo |
|  7 | post-7  |     4 | Tokyo |
+----+---------+-------+-------+
# posts_tokyoã¯12ã®ã¾ã¾
```

VIEW ã¨ããä»çµã¿ãä½¿ãã°åãã¼ãã«ã¨é£åããä»®æ³çãªãã¼ãã«ãä½ããã¨ãã§ãã¾ããã©ããããã¨ããã¨ã CREATE VIEW ã¨ãã¦ããã¾ããååãåããããã posts_tokyo_view ã«ãã¦ããã¾ãããããã® VIEW ã§ããããã¡ãã®æ½åºæ¡ä»¶ã ããä¿æããä»®æ³çãªãã¼ãã«ã§å®è¡ããåº¦ã«ãåãã¼ã¿ããååº¦å¤ãæ½åºãã¦ãããã¨ããä»çµã¿ã«ãªã£ã¦ãã¾ãã

```sql
DROP TABLE IF EXISTS posts_tokyo;
CREATE TABLE posts_tokyo AS SELECT * FROM posts WHERE area = 'Tokyo';

CREATE VIEW posts_tokyo_view AS SELECT * FROM posts WHERE area = 'Tokyo';

UPDATE posts SET likes = 15 WHERE id = 1;

SELECT * FROM posts;
SELECT * FROM posts_tokyo;
```

ããããæ¢ã«ãã® VIEW ãããã¨ã¨ã©ã¼ã«ãªã£ã¦ãã¾ãã®ã§ããã¡ãã§åé¤ãã¦ããã¾ããããposts_tokyo ã®ã»ãã¯ã³ã¡ã³ãã«ãã¦ããã¦ã posts_tokyo_view ã®ä¸­èº«ãæå¾ã«ç¢ºèªãã¦ããã¾ãã

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
# postsã15
+----+---------+-------+-------+
| id | message | likes | area  |
+----+---------+-------+-------+
|  1 | post-1  |    15 | Tokyo |
|  3 | post-3  |    11 | Tokyo |
|  5 | post-5  |     8 | Tokyo |
|  7 | post-7  |     4 | Tokyo |
+----+---------+-------+-------+
# VIEWã¯å®è¡ããåº¦ã«ããã¼ã¿ãæ½åºããã®ã§ããã¡ãã15ã«ãªã£ã¦ãã
```

ããããVIEWãä½¿ãããªããããã«ãªã£ã¦ããã¾ãããã
### è³ªåï¼VIEWãã¼ãã«ã®å¤ã¯ãã¤æ´æ°ããã¦ããã®ã§ããï¼
åç­ï¼VIEWãåå¾ããåº¦ã«ææ°ã®æå ±ãæ½åºããã¾ãã

`VIEW`ã¯æ½åºæ¡ä»¶ãä¿å­ããä»®æ³çãªãã¼ãã«ã§ãã

å¤ãã®ãã®ãä¿æãã¦ããããã§ã¯ãªããåã®ãã¼ãã«ããæ½åºããæ¡ä»¶ãä¿å­ãã¦ãããã®ã¨èãã¦ãã ããã

ã§ãã®ã§ãåãã¼ãã«ã`UPDATE`ãããã¿ã¤ãã³ã°ã§`VIEW`ã§åç§ãããã®ãæ´æ°ããã¦ãã¾ãã
### è¦ç¹ã¾ã¨ã
æ½åºçµæã ããä¿æãããã¨ã®ã§ããVIEWã«ã¤ãã¦è¦ã¦ããã¾ãã

- VIEWï¼åãã¼ãã«ã¨é£åããä»®æ³çãªãã¼ãã«ãä½ããã¨ãã§ãã
- DROP VIEWï¼ä½ææ¸ã¿ã®ãã¥ã¼ãåé¤ãã</details>


**<details><summary>#07 UNIONã§æ½åºçµæãã¾ã¨ããã</summary>**

UNION ã«ã¤ãã¦è¦ã¦ããããã®ã§ãããä¾ãåºãã¦ããã¾ããlikesã®å¤§ããé ã«ä¸¦ã³æ¿ãã¦ã¿ã¾ãããã`ORDER BY likes DESC` ã§ããã§ãã­ã

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

SELECT * FROM posts ORDER BY likes DESC; # DESCã¯å¤§ãã(å¤ã)é ã«ä¸¦ã³æ¿ããå½ä»¤

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
# DESCã§likesã®å¤ãé ã«ä¸¦ãã§ãã
```

ã§ã¯ãããã§ãã¡ãã®ä¸ä½ 3 åã¨æå¾ã® 1 ã¤ã ããæ½åºãããã£ãã¨ãã¾ãããã®å ´åã®ã¯ã¨ãªãæ¸ãã¦ããã¾ãããã

- ã¾ãä¸ä½3åã¯ããã®ã¾ã¾`LIMIT 3`ã¨ãã¦ãããã°ããã§ãã­ã
- 1çªä¸ã®ã¬ã³ã¼ãã¯ãå°ããé ã«ä¸¦ã³æ¿ãã¦1ååãåºãã¦ãããã°OKã§ãã

SELECT * FROM posts ORDER BY likes DESC;ãã³ã¡ã³ãã«ãã¦ãã³ã¼ããå®è¡ãã¾ãã

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
# ä¸ä½3åã¨æå¾ã®ã¬ã³ã¼ãã®æ½åºãã§ãã¦ãã
```

ããã§ãããããã²ã¨ã¤ã®çµæã¨ãã¦åºãããå ´åãUNIONãä½¿ããã¨ãã§ãã¾ãã

ã©ããããã¨ããã¨ããã¡ãã®ã¯ã¨ãªã¨ãä¸ã®ã¯ã¨ãªãã UNION ALL ã§ãã®ããã«ã¤ãªãã¦ãããã° OK ã§ãã

ãã¡ãã®ã¯ã¨ãªã§ãããããããããã¾ã§ãã²ã¨ã¤ã®ã¯ã¨ãªãªã®ã§ãã»ãã³ã­ã³ã¯æå¾ã«ã²ã¨ã¤ããä»ããªãç¹ã«æ³¨æãã¦ããã¦ãã ããã

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
# çµæãç¸¦ã«ç¹ãã£ã¦ãã
```

UNION ãä½¿ãå ´åããã® 2 ã¤ã®ã¯ã¨ãªã®ã«ã©ã æ°ã¨ãã¼ã¿åãä¸è´ãã¦ããå¿è¦ãããã¾ãããçµæããã®ããã«ç¸¦ã«ç¹ãããã¨ãã«ä¾¿å©ãªã®ã§ãä½¿ãããªããããã«ãã¦ããã¾ãããã
### è³ªåï¼UNION ALLã®åå¾ã®æã()ã§å²ãã¨ãã¨å²ã¾ãªãã¨ãã¨ã®éãã¯ãªãã§ããï¼
åç­ï¼SELECT å¥ã« ORDER BY ã¾ãã¯ LIMIT ãä½¿ãéã¯ã«ãã³ã§å²ããããã§ãªãå ´åã¯ãã®ã¾ã¾è¨è¿°ã§ããããã§ãã

ãªãã¡ã¬ã³ã¹ãåç§ããã¨ããä»¥ä¸ã®è¨è¿°ãè¦ã¤ããã¾ãã

> åãã® SELECT ã« ORDER BY ã¾ãã¯ LIMIT ãé©ç¨ããã«ã¯ããã®å¥ã SELECT ãå²ãæ¬å¼§åã«éç½®ãã¾ããhttps://dev.mysql.com/doc/refman/5.6/ja/union.html
> 

ã©ããã`SELECT`å¥ã«`ORDER BY`ã¾ãã¯`LIMIT`ãä½¿ãéã¯ã«ãã³ã§å²ããããã§ãªãå ´åã¯ãã®ã¾ã¾è¨è¿°ã§ããã¨ãããã¨ã®ããã§ãã
### è¦ç¹ã¾ã¨ã
è¤æ°ã®æ½åºçµæãä¸ã¤ã®çµæã¨ãã¦ã¾ã¨ããæ¹æ³ãè¦ã¦ããã¾ãã

- UNION ALLï¼ã¯ã¨ãªã¨ã¯ã¨ãªãä¸ã¤ã«ç¹ãããã¨ãã§ãã â»ç¹ããã¯ã¨ãªã®ã«ã©ã æ°ã¨ãã¼ã¿åãä¸è´ãã¦ããå¿è¦ããã</details>


**<details><summary>#08 ãµãã¯ã¨ãªãä½¿ã£ã¦ã¿ãã</summary>**

åãã¯å¨ã¦ã®ã¬ã³ã¼ããè¡¨ç¤ºãã¦ã¿ã¾ãããã

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

ããã§ãlikesã®å¹³åãæ¨ªã«è¡¨ç¤ºãããã£ãå ´åãæ®éã«èããã¨ã*ã®ãã¨ã«likesã®å¹³åã`SELECT *, AVG(likes) AS avg FROM posts;`ã¨ãè¡¨ç¤ºããã°ããã¨æãããããã¾ããããããã§ã¯ãã¾ãããã¾ãããAVG é¢æ°ã¯å¨ã¦ã®ã¬ã³ã¼ããéè¨ãã¦ã²ã¨ã¤ã®ã¬ã³ã¼ãã«ãã¦ãã¾ãã®ã§ããã®ãããªçµæã«ãªã£ã¦ãã¾ãã¾ãã

```sql
SELECT *, AVG(likes) AS avg FROM posts;

+------+---------+-------+-------+---------+
| id   | message | likes | area  | avg     |
+------+---------+-------+-------+---------+
|    1 | post-1  |    12 | Tokyo | 10.6667 |
+------+---------+-------+-------+---------+
```

ããã§ãæ¹è¡ãå¥ããªããè¦ãããã¯ã¨ãªãæ¸ãã¦ããã¾ããããã©ããããã¨ããã¨ãSELECTã®ä¸­ã§ããã«SELECTæãä½¿ã£ã¦ãããã°OKã§ããæ®éã® SELECT ã®çµæã«å ãã¦ãã¬ã³ã¼ããã¨ã«éè¨é¢æ°ãä½¿ããã¨ã§ããã¾ãçµæãè¡¨ç¤ºããã¾ãã

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

ãã®ããã«ã¯ã¨ãªã®ä¸­ã§ä½¿ãã¯ã¨ãªã®ãã¨ããµãã¯ã¨ãªã¨å¼ã¶ã®ã§ãç¨èªã¨ãã¦è¦ãã¦ããã¦ãã ããããµãã¯ã¨ãªãé§ä½¿ããã°ããªãè¤éãªãã¨ãã§ãã¾ããããã®åã¯ã¨ãªããã®ããã«é·ããªã£ãããéåº¦ãéããªã£ããããã®ã§ããã¼ã¿ãå¤ãã¨ãã«ã¯æ³¨æãã¦ä½¿ãå¿è¦ãããã¨ããç¹ãæ³¨æãã¦ããã¾ãããã

```sql
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg -- sub query
FROM
  posts;
```

### è³ªåï¼ãªã SELECT * ,AVG(likes) AS avg FROM posts; ã§ avg ã®ã«ã©ã ãè¿½å ãããã®ã§ããï¼
    
```sql
SELECT * ,AVG(likes) AS avg FROM posts;

```

ã¨ããã¨ããã¼ãã«ã«Â `avg`Â ã®ã«ã©ã ãè¿½å ãããã®ã¯ä½æã§ããããã

```sql
INSERT INTO posts(message, likes, area) VALUES

```

ã®Â `()`Â åã«Â `avg`Â ãè¿½å ã§å¥ããã¨ã§ã¨ã«ã©ã ã¯è¿½å ã«ãªãã®ã§ã¯ãªãã§ããããã

åç­ï¼ã«ã©ã ãè¿½å ãã¦ããã®ã§ã¯ãªããAVG(likes) ã§å¾ãããå¤ã avg ã¨ãã¦è¡¨ç¤ºãã¦ãã¾ãã

ãã®å ´åã¯ããã¼ãã«ã«ã«ã©ã ãè¿½å ããã¦ããããã§ã¯ãªã`SELECT`ã®çµæã«`AVG(likes)`ã§å¾ãããå¤ã`avg`ã¨ãã¦è¡¨ç¤ºãã¦ããã¾ãã`SELECT`ã§è¡¨ç¤ºãããçµæã®åã¯å¿ããããã¼ãã«ã®ã«ã©ã ã«å¯¾å¿ãã¦ããããã§ã¯ããã¾ããã
### è³ªåï¼ãªããµãã¯ã¨ãªãä½¿ãã¨çµæã posts ã¬ã³ã¼ãå¨ã¦ã«è¿½å ãããã®ã§ããï¼
åç­ï¼é ãè¿½ã£ã¦èª¬æãã¦ããã¾ãã

`AVG`Â ãªã©ã®é¢æ°ã¯éç´é¢æ°ã¨å¼ã°ãã¦ããã¾ãã¦ã`SELECT *, AVG(likes) AS avg FROM posts;`ãã¡ãã®å ´åã¯å¨ã¦ã®ã¬ã³ã¼ãã"éç´"ãã¦å¹³åãæ±ãã¦ãã¾ãããã®çµæå¹³åãè¨ç®ãã1ã¬ã³ã¼ãã®ã¿ã«ãªãã¾ãã

ãµãã¯ã¨ãªã®å ´åã¯ãã¾ããµãã¯ã¨ãªã®çµæãæ±ãã¦ãããÂ `posts`Â ã®ã¬ã³ã¼ãå¨ã¦ã«è¿½å ã®ã«ã©ã ã¨ãã¦çµåãã¦ããã¨èãã¦ããã ãã¦ééããªããã¨æãã¾ãããµãã¯ã¨ãªã®çµæã¯ä¸ã®ã¯ã¨ãªã¨åæ§ã«å¨ã¦ã®ã¬ã³ã¼ãããè¨ç®ããå¹³åå¤ã§ãã®ã§ã¾ãããããã¨ã¾ãã¾ãããã®çµæãÂ `posts`Â ã®ã¬ã³ã¼ãå¨ã¦ã«è¿½å ãã¦ããã®ã§ãå¨ã¬ã³ã¼ã + å¹³åå¤ã¨ãããããªçµæãè¿ããã¾ãã
### è¦ç¹ã¾ã¨ã
ã¯ã¨ãªã®ãªãã§ã¯ã¨ãªãä½¿ãæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãããã

- ãµãã¯ã¨ãªï¼ã¯ã¨ãªã®ä¸­ã§ä½¿ãã¯ã¨ãªã®ãã¨ãsub query
- ãµãã¯ã¨ãªãä½¿ãã¨ãã®æ³¨æç¹ï¼ãµãã¯ã¨ãªãé§ä½¿ããã°ããªãè¤éãªãã¨ãã§ãã¾ããããã®åã¯ã¨ãªãé·ããªã£ãããéåº¦ãéããªã£ããããã®ã§ããã¼ã¿ãå¤ãã¨ãã«ã¯æ³¨æãã¦ä½¿ãå¿è¦ããã</details>


**<details><summary>#09 ç¸é¢ãµãã¯ã¨ãªãä½¿ã£ã¦ã¿ãã</summary>**

ä»å®è¡ãã¦ãããã¨ãæ¨ªã«å¹³åãè¡¨ç¤ºããã¦ããç¶æã§ãã

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

ãã¡ãè¡¨ã®æ¨ªã« area ãã¨ã®å¹³åãè¡¨ç¤ºãããã£ãå ´åãã©ããããèãã¦ã¿ã¾ãããã

ãã®å ´åã§ãããæåã®ã¬ã³ã¼ããå¦çãã¦ããã¨ãã«ã¯ãå¨ä½ãã area ã Tokyo ã®ã¬ã³ã¼ãã ããæãåºãã¦ãå¹³åãè¨ç®ãã¦ãããã°è¯ãããã§ããããã¦ãæ¬¡ã®ã¬ã³ã¼ããå¦çããã¨ãã«ã¯å¨ä½ãã area ã Fukuoka ã®ã¬ã³ã¼ãã ãæãåºãã¦ããã¦ãå¹³åãè¨ç®ãã¦ãããã°ããã§ãã­ããã®ããã«ã¯ã¨ãªãæ¸ãã¦ããã¾ãããã

ãã¡ãã«ããã²ã¨ã¤ãµãã¯ã¨ãªãæ¸ãã¦ããã¦ã area_avg ã¨ãã¦ããã¾ãããããã®ããã§ WHERE æ¡ä»¶ã§å¨ä½ã® area ã¨ãä»å¦çãã¦ããã¬ã³ã¼ãã® area ãä¸ç·ã®ãã®ãæ½åºãã¦ãããã°ããã§ãã­ã

```sql
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg,
  (SELECT AVG(likes) FROM posts WHERE area = area) AS area_avg
FROM
```

ãã ããã ã¨ãã©ã£ã¡ãã©ã£ã¡ããããããªãã®ã§ãæ½åºåã®ãã¼ãã«ã«å¥åãã¤ãã¦ããã¾ãããããã¡ãã¯ t1 ãããã¦ä»å¦çãã¦ããã»ãã t2 ã¨ãã¦ããã¾ãããã

```sql
SELECT
  *,
  (SELECT AVG(likes) FROM posts) AS avg,
  (SELECT AVG(likes) FROM posts AS t2 WHERE area = area) AS area_avg
FROM
  posts AS t1;
```

ãã®ããã§ããã¡ãã® area ã¯ t1 ã® area ã«ãã¦ããããã®ã§ã ãã¼ãã«å.ã«ã©ã å ã¨ãã¦ããã¾ãã

ãã¡ãã¯ t2 ã® area ã«ãããã®ã§ããã®ããã«æ¸ãã¦ãããã° OK ã§ãã­ã

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

ãã®ããã«å¤§æ¬ã®ã¯ã¨ãªã¨é¢é£ä»ããªãããå®è¡ãã¦ãããµãã¯ã¨ãªã®ãã¨ãç¸é¢ãµãã¯ã¨ãªã¨å¼ã¶ã®ã§ãç¨èªã¨ãã¦è¦ãã¦ããã¨ããã§ãããã
### è³ªåï¼ãµãã¯ã¨ãªãä½¿ã£ãã³ã¼ãã®ä»çµã¿ãããããªã
`SELECT`

`*,`

`(SELECT AVG(likes) FROM posts) AS avg,`

`(SELECT AVG(likes) FROM posts AS t2 WHERE t1.area=t2.area) AS area_avg`

`FROM`

`posts AS t1;`

ã¬ãã¹ã³ã§è¡¨ç¤ºããã¦ãããã®ã³ã¼ãã®æå³ãå¨ããããã¾ããã§ããã

area_avgã«ã©ã ã®é¨åã§ãã

è©³ããä»çµã¿ãæãã¦ããã ãããã§ãã

åç­ï¼é åºãè¿½ã£ã¦èª¬æãã¦ããã¾ãã

ç¸é¢ãµãã¯ã¨ãªã¯ã¡ãã£ã¨é£ããã§ããã­å·ä½çã«å¤ãå¥ãã¦ã¿ãã¨çè§£ã®å©ãã«ãªãããããã¾ãã

ä¾ãã°t1ãã¼ãã«ã®id=1ã®è¡ãèãã¦ã¿ã¾ããã¤ã¾ã`t1.area = 'Tokyo'`ãå¥ã£ã¦ããã¨èããããã¨t2.area = 'Tokyo'ã¨ãªãè¡ã¯ä»¥ä¸ã®4è¡ã§ã

```
| id | message | likes | area    |
+----+---------+-------+---------+
|  1 | post-1  |    12 | Tokyo   |
|  3 | post-3  |    11 | Tokyo   |
|  5 | post-5  |     8 | Tokyo   |
|  7 | post-7  |     4 | Tokyo   |
+----+---------+-------+---------+

```

ãããã®`likes`ã®å¹³åã`(12 + 11 + 8 + 4 )/4 = 8.75`ã¨ãªã£ã¦ãã¾ããåæ§ã«id=2ã®è¡ãåãåºãã¦ï¼`t1.area = 'Fukuoka'`ã¨ããï¼`t2.area = 'Fukuoka'`ã¨ãªãè¡ã®å¹³åãå¾ãããã`id = 3 ~ 9`ã®è¡ãåæ§ã«èãã¦ããã¾ãã

ãã®ãããªå½¢ã§ã1è¡ã«ã¤ãå¨ã¦ã®è¡ãä»ãåããã¦ãareaãåããã®ãæ½åºãå¹³åãåºãã¨ããã®ããã®ã¯ã¨ãªã§ãã

```sql
(SELECT AVG(likes) FROM posts AS t2 WHERE t1.area = t2.area) AS area_avg

```

`as`Â ã§ãã®åãåºããã«ã©ã ã«area_avgã¨ããå¥åãã¤ãã¦ããã¨ããå½¢ã§ãã­ã
### è³ªåï¼2ã¤ã®ãã¼ãã«ã¯ã©ãåºå¥ãã¹ãã§ããï¼
åç­ï¼æåã¯ãèªèº«ã®ããããããèãæ¹ã§çè§£ãã¦ããã¨ããã§ãããã

æåã¯ãèªèº«ã®ããããããèãæ¹ã§çè§£ãã¦ããã®ãããã¨ã¯æãã¾ãã

åèã¾ã§ã« MySQL ãã©ã®ããã«ã¯ã¨ãªãå¦çããã¦ãããã®ã¤ã¡ã¼ã¸ã§ããã

- ã¾ã posts ãã¼ãã«ããå¨ä»¶åå¾ãããã
- åå¾ãã id=1 ã®ã¬ã³ã¼ãã® area ã®å¤ã¯ Tokyo ã§ãããposts ãã¼ãã«ããÂ `area = 'Tokyo'`Â ã®ã¬ã³ã¼ããåå¾ãã¦ likes ã®å¹³åãæ±ãããã
- åå¾ãã id=2 ã®ã¬ã³ã¼ãã® area ã®å¤ã¯ Fukuoka ã§ãããposts ãã¼ãã«ããÂ `area = 'Fukuoka'`Â ã®ã¬ã³ã¼ããåå¾ãã¦ likes ã®å¹³åãæ±ãããã
- ... (ä»¥ä¸çç¥)

ã®ãããªæãã§ãã

posts ãã¼ãã«ã®ãã¼ã¿ã¯å¤æ´ãããªãã®ã§ãç¹ã«ã³ãã¼ãã¦ãããªãã¦ã SELECT ã§ã¬ã³ã¼ããåå¾ã§ãã¾ãã
### è¦ç¹ã¾ã¨ã
ã¯ã¨ãªãé¢é£ä»ããªããå®è¡ã§ããç¸é¢ãµãã¯ã¨ãªã«ã¤ãã¦è¦ã¦ããã¾ãã

- ç¸é¢ãµãã¯ã¨ãªï¼å¤§åã®ã¯ã¨ãªã¨é¢é£ã¥ããªãããå®è¡ãã¦ãããµãã¯ã¨ãªã®ãã¨</details>


<details><summary>#10 æ½åºæ¡ä»¶ã«ãµãã¯ã¨ãªãä½¿ã£ã¦ã¿ãã</summary>

ãµãã¯ã¨ãªã¯WHEREã®ãã¨ã®æ½åºæ¡ä»¶ãæ½åºåã®ãã¼ãã«ã«ãä½¿ããã¨ãã§ãã¾ãã

postã®ä¸­ãã1çªå¤§ããªlikesãæã¤ã¬ã³ã¼ããæ½åºãã¦ã¿ã¾ãããã

åãã¯ã¬ã³ã¼ããæ½åºãããã®ã§ã`SELECT * FROM posts`ã¨ãã¦ããã¾ãã

ãã®å¾ã®WHEREã§ãµãã¯ã¨ãªãä½¿ãããã®ã§ãããlikesãå¨ä½ã®æå¤§å¤ã¨åããã®ãã¨æ¸ãã¦ãããã°OKã§ãã

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

ãã®ããã«ãµãã¯ã¨ãªã¯æ½åºæ¡ä»¶ã«ãä½¿ããã®ã§ãä½¿ãããªããããã«ãã¦ããã¾ãããã
### è³ªåï¼ãµãã¯ã¨ãªãä½¿ãå ´åã¨ä½¿ããªãå ´åã®éãã¯ãªãã§ããï¼
åç­ï¼MAXãªã©ã®éç´é¢æ°ã¯GROUP BYãä½¿ããªãã¨æ­£ããçµæã¯å¾ããã¾ããã</details>


<details><summary>#11 æ½åºåã«ãµãã¯ã¨ãªãä½¿ã£ã¦ã¿ãã</summary>

areaãã¨ã®æç¨¿æ°ãåºãã¦ã¿ã¾ãããã

COUNTã¨ä½¿ããnã¨ããå¥åãä»ãã¦ãGROUP BYã§areaãã¨æå®ãã¦ããã¾ãã

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

ã§ã¯ãããã§ãã®æ½åºçµæã§ãã2,3,4ã®å¹³åãåºãããã£ãå ´åãèãã¾ãããã

ãã®å ´åããã®çµæãå¥ãã¼ãã«ã«åãåºãã¦éè¨ããæ¹æ³ãããã¾ããããµãã¯ã¨ãªãä½¿ãæ¹æ³ãããã¾ãã

ã©ããããã¨ããã¨ããã®çµæãtã¨ãããã¼ãã«ã ã¨èãã¦ãnã®å¹³åãåºãã°ããã®ã§ããã®ããã«åãã¯ã¯ã¨ãªãçµã¿ç«ã¦ã¦ããã¾ãããã

nã®å¹³åãt(ã¨ãããã¼ãã«)ããæ½åºãããã¨æ¸ãã¾ãã

ã¾ããããã§tã¯ä¸ã«ãã`SELECT area, COUNT(*) AS n FROM posts GROUP BY area;`ã¨æ¸ããã¯ã¨ãªã®çµæãªã®ã§ããã®ã¾ã¾ãµãã¯ã¨ãªã¨ãã¦ç½®ãæãã¦ããã¾ãã

ä½ãããµãã¯ã¨ãªãæ½åºåã®ãã¼ãã«ã¨ãã¦ä½¿ã£ãå ´åãå¿ãå¥åãä»ããå¿è¦ãããã®ã§ãAS tã¨ãã¦ããã¦ãã ããã

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
  (SELECT area, COUNT(*) AS n FROM posts GROUP BY area) AS t; /* ã¯ã¨ãªãçµã¿ç«ã¦ã¦ãã®ä¸­ã«ãµãã¯ã¨ãªä½æâ
ãµãã¯ã¨ãªãæ½åºåã®ãã¼ãã«ã¨ãã¦ä½¿ã£ãå ´åãå¿ãå¥åãä»ããå¿è¦ãããã®ã§ãAS tã¨ä»ãã */

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

### è³ªåï¼SQLæã®ã«ã³ãã¯ã©ã®ããã«ä½ç¨ãã¦ããã®ã§ããï¼
åç­ï¼

ã¾ããSQL æãè¦ã¦ã¿ã¾ãããã

```sql
SELECT area, COUNT(*) AS n FROM posts GROUP BY area;

```

ãã¡ãã§ãã­ããã®ã¾ã¾ã§ã¯ãããã«ããã®ã§ãä»®ã«ãï½ããããã¦ã©ãã¾ã§ãé¨åãå½¢æãã¦ããã®ãè¦ã¦ã¿ã¾ããããï¼æ§æçã«ã¯ï½ã¯ãªãã¨æã£ã¦ãã ããï¼

```sql
ï½SELECT  area, COUNT(*) AS n   ï½   FROM posts  ï½ GROUP BY areaï½

```

ãããªæãã«ãªãã¾ããã¤ã¾ãã`SELECT`ã`FROM`ã`GROUP`ãªã©ã® SQL ã®æ§æãåºåãã«ãªã£ã¦ãããã¨ãããã¨ãªã®ã§ãã­ã

ãªã®ã§ãè³ªåã®Â `,`Â ã¯Â `FROM`Â ã®æåã¾ã§æå¹ãã¨ãªãã¾ãã

æ¬¡ã«Â `GROUP BY`Â ã«è¤æ°ã®ã«ã©ã ãå¥ããæ¹æ³ãç´¹ä»ãã¾ãã­ã

ããã¯ãã°ãããå¯ãã®ã¨ããÂ `,`Â ãå¥ããã®ã§ããä¾ãè¦ã¦ã¿ã¾ãããã

```sql
SELECT area,likes, COUNT(*) AS n FROM posts GROUP BY area,likes;

```

ãããªæãã«ãªãã¾ãã`GROUP BY area,likes`Â ã¨Â `,`Â ã§è¿½å ããã¦ããã®ããããã¾ãã­ãããã§`area,likes`Â ãå¨ãåããã®ã 1 ç¨®é¡ã¨ã¿ãªãã¦ã¾ã¨ãã¦ãããã§ã¯æ°ãÂ `COUNT`Â ã§æ°ãã¦ããã¾ãã

ã¾ãã`SELECT area,likes,`Â ã®ããã«è¡¨ç¤ºããã«ã©ã ãå¢ããã¦ãããã¨ã«ãæ³¨ç®ãã¦ãã ããã

ãã¦ãããã§å®¿é¡ã§ããå®ã¯ä»ã®ç¶æã§ä¸ã®è¡ãåç´ã« MySQL ã«å¥åãã¦ãÂ `area`Â ã`likes`Â ãå¨ãåãã¬ã³ã¼ãããªãã®ã§è©¦ããã¨ãã§ããªãã®ã§ãã

ãªã®ã§ã`main.sql`Â ãã¡ãã£ã¨å¤æ´ãã¦Â `area`Â ã`likes`Â ãä¸ç·ã§ããã¬ã³ã¼ããè¿½å ã¾ãã¯å¤æ´ä½æãã¦ãä¸ã®è¤æ°Â `GROUP BY`Â ã®çµæã`area`Â ã`likes`Â ãä¸ç·ã§ããã¬ã³ã¼ãã®æ°ã 2 ä»¥ä¸ã«ãªãããã«ãã¦ã¿ã¦ãã ããã

ãçè§£ã®ããããã²ææ¦ãã¦ã¿ã¦ãã ãããããããããªãã£ãããæ°è»½ã«ãåãåãããã ããï¼</details>


<details><summary>#12 ã¦ã£ã³ãã¦é¢æ°ãä½¿ã£ã¦ã¿ãã</summary>

MySQLã§æè¿è¿½å ãããã¦ã£ã³ãã¦é¢æ°ã«ã¤ãã¦è¦ã¦ããã¾ãã

ã¦ã£ã³ãã¦é¢æ°ãä½¿ãã¨ããã¼ãã«ã**ãã¼ãã£ã·ã§ã³(PARTITION)**ã¨å¼ã°ããåä½ã§éè¨ãã¦ãã®çµæãåã¬ã³ã¼ãã®æ¨ªã«è¿½å ãã¦ããã¾ããã¾ããããç´°ããåä½ã§éè¨ããããã¨ãã§ããããã«ãªãã¾ãã
### è³ªåï¼ãµãã¯ã¨ãªã¨ã¦ã£ã³ãã¦é¢æ°ã®ä½¿ãåããæãã¦ãã ããã
åç­ï¼æ±ããããçµæãå¾ããããã°ã©ã¡ããä½¿ã£ã¦ãå¤§ä¸å¤«ã§ãã

> ãµãã¯ã¨ãªã¨ã¦ã£ã³ãã¦é¢æ°ã¯ã©ãä½¿ãåããã®ã§ããããï¼
> 

ãã¡ãã¯æ±ããããçµæãå¾ãããã°ã©ã¡ãã§ãè¯ãã¨æãã¾ãã

- ãµãã¯ã¨ãªã§æ¸ãã¨é·ããªãã®ã§ã¦ã£ã³ãã¦é¢æ°ãä½¿ã

ã¨ãããã¨ãããã§ããããã

- ã¦ã£ã³ãã¦é¢æ°ãä½¿ã£ã¦ãããããç¾å ´ã«æ£ãã¦ããäººãããªãã®ã§ãé·ããªã£ã¦ããããããããµãã¯ã¨ãªã§æ¸ãã¦ä¿å®ãããããã

ã§ãããã¨æãã¾ãã

ã¾ãä¸è¬çã«ãµãã¯ã¨ãªãä½¿ãã¨ããã©ã¼ãã³ã¹ãè½ã¡ãããã¾ããããã¼ã¿ãã¼ã¹ã®ããã©ã¼ãã³ã¹ã¯å©ç¨ç¶æ³ã«ãããã®ã§ãå®è£ããããæ¹ãä½¿ã£ã¦ããã¦ãéç¨ç¶æ³ãã¿ãªãããã¥ã¼ãã³ã°ãã¦ããã¨ããæµãã«ãªãã¨æãã¾ãã
### è¦ç¹ã¾ã¨ã
PARTITIONãFRAMEãè¨­å®ãã¦ãé«åº¦ãªéè¨ããããã¨ãã§ããã¦ã£ã³ãã¦é¢æ°ã«ã¤ãã¦è¦ã¦ããã¾ãã

- ã¦ã£ã³ãã¦é¢æ°ã®èª¬æ
- PARTITIONï¼ã¦ã£ã³ãã¦é¢æ°ãä½¿ç¨ããã¨ããã¼ãã«ãPARTITIONã¨å¼ã°ããåä½ã§éè¨ãã¦ãã®çµæãåã¬ã³ã¼ãã®æ¨ªã«è¿½å ããã
- FRAMEï¼PRTITIONã®ãµãã»ããã®ãã¨ãFRAMEã¨ããåä½ãä½¿ç¨ããã¨ããã«ç´°ããéè¨ããããã¨ãã§ããã</details>


<details><summary>#13 PARTITIONãè¨­å®ãã¦ã¿ãã</summary>

æ©éã¦ã£ã³ãã¦é¢æ°ãä½¿ã£ã¦ã¿ã¾ãããã

ã³ã¼ããå®è¡ãã¦è¡¨ç¤ºããããã¼ãã«ã®å³å´ã«likesã®å¨ä½ã®å¹³åã¨areaãã¨ã®å¹³åãåºãã¦ã¿ã¾ãããã

åãã¯**å¨ä½ã®å¹³å**ããè¦ã¦ããã¾ãã

ã¦ã£ã³ãã¦é¢æ°ã§ã¯ãä»ã¾ã§è¦ã¦ããéè¨é¢æ°ããã®ã¾ã¾ä½¿ããã®ã§ãAVG(likes)ã¨ãã¦ããã¦ãã¦ã£ã³ãã¦é¢æ°ã¨ãã¦ä½¿ããªãOVERã¨æ¸ãã¦ããã¾ããã¾ãåãããããavgã¨ããå¥åãä»ãã¦ããã¾ãã

ãã®ããã§ãã¼ãã£ã·ã§ã³ãè¨­å®ããã®ã§ãããå¨ä½ãä¸ã¤ã®ãã¼ãã£ã·ã§ã³ã«ãããªããåãªãä¸¸æ¬å¼§ã§OKã§ãã

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

-- ã¦ã£ã³ãã¦é¢æ°ãä½¿ç¨
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
  AVG(likes) OVER () AS avg -- å¨ä½ãä¸ã¤ã®ãã¼ãã£ã·ã§ã³ã«ãããªããåãªãä¸¸æ¬å¼§ã§OK
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
-- å¨ä½ã®å¹³åãå³å´ã«è¡¨ç¤ºããã¦ãã
```

æ¬¡ã«**areaãã¨ã®å¹³å**ãè¦ã¦ããã¾ãã

OVERã®ä¸­ã§ãã¼ãã£ã·ã§ã³ããã¦ãã¦ãããã°OKã§ããè¨­å®ããã«ã¯ã`PARTITION BY area`ã¨ããã°areaãã¨ã«ãã¼ãã£ã·ã§ã³ãåºåã£ã¦å¹³åãéè¨ãã¦ããã¾ããå¥åã¯area_avgã«ãã¾ãã

ã¤ãã§ã«areaãã¨ã®likesã®åè¨ãåºãã¦ã¿ã¾ãããã

AVGãSUMã«å¤ãã¦ããã¾ããå¥åãåãããããarea_sumã¨ãã¾ãã

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
-- areaãã¨ã®å¹³åãareaãã¨ã®åè¨ãã¡ããã¨è¿½å ããã¦ãã
```

ã¦ã£ã³ãã¦é¢æ°ãä½¿ãã¨ãµãã¯ã¨ãªã¨éã£ã¦ããã£ããæ¸ããã¨ãã§ããã®ã§ãä½¿ãããªããããã«ãªã£ã¦ããã¾ãããã

ããããOVERããå¾ãã®æå®ã§ãããåããããªãã®ãããå ´åã¯å¥åãä»ãããã¨ãã§ãã¾ãããã®å ´åã¯WINDOWã¨ãã¦ããã¦ã`å¥å AS PARTITION BY area` ãæ¸ãã¦ãããã°OKã§ããããããã¨ããã®æå®ã®ä»£ããã«wã¨ããå¥åãä½¿ããã¨ãã§ããã®ã§ã`PARTITION BY area`ã¨æ¸ãã2è¡ãç­ãæ¸ãæãããã¨ãã§ãã¾ãã

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
  w AS (PARTITION BY area); -- (PARTITION BY area)ãwã¨ããå¥åã«ãã
```

ããããæä½ãã§ããããã«ãªã£ã¦ããã¦ãã ããã
### è¦ç¹ã¾ã¨ã
ã¦ã£ã³ãã¦é¢æ°ãä½¿ã£ã¦ãPARTITIONãè¨­å®ããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- OVER()ï¼ã¦ã£ã³ãã¦é¢æ°ã¨ãã¦ä½¿ãããã«å¿è¦ã
- PARTITION BYï¼ãã¼ãã£ã·ã§ã³ãè¨­å®ã</details>


<details><summary>#14 FRAMEãè¨­å®ãã¦ã¿ãã</summary>

ä»ã¯ãã¼ãã«ã®æ¨ªã«areaãã¨ã®åè¨ãåºã¦ããç¶æã§ãã

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

FRAMEãä½¿ã£ã¦ç´¯è¨ãéè¨ãã¦ã¿ã¾ãããã

likesã®å°ããé ã«ä¸¦ã³æ¿ããä¸ã§ç´¯è¨ãéè¨ãã¦ããããã®ã§ã`ORDER BY likes` ã¨ãã¦ããã¾ãã

ãã®ä¸ã§FRAMEãè¨­å®ãããã®ã§ãããå®ã¯ããã©ã«ãã§ãã¼ãã£ã·ã§ã³ã®åé ­ãããã®ã¬ã³ã¼ãã¾ã§ã«ãªãã®ã§ããã®ã¾ã¾ã§ç´¯è¨ãéè¨ã§ããã¯ãã§ãã

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
    ORDER BY likes -- ORDER BY åå :æ¤ç´¢çµæãä¸¦ã¹æ¿ãã ä»åã¯æé 
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
-- ãã¼ãã£ã·ã§ã³ãã¨ã«ã¡ããã¨ç´¯è¨ãéè¨ã§ãã¦ããã®ãããã
```

FRAMEã®è¨­å®ãå¤æ´ãããå ´åãè¦ã¦ããã¾ãããã

ä¾ãã°ããã¼ãã£ã·ã§ã³ã®ä¸­ã§åå¾1è¡ããã¬ã¼ã ã«ãããå ´åã¯`ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING`ã¨ãã¦ãããã°OKã§ãã

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

### è¦ç¹ã¾ã¨ã
PARTITIONã®ãªãã§ä¸¦ã³æ¿ããããå¾ã«ãFRAMEãè¨­å®ããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- ORDER BYï¼æ¤ç´¢çµæãä¸¦ã¹æ¿ãã ããã©ã«ããASCã¯æé ãDESCã¯éé ã
- ROWS BETWEEN ... AND ...ï¼ãã¼ãã£ã·ã§ã³ã®ä¸­ã§åå¾1è¡ããã¬ã¼ã ã«ãããå ´åã</details>


<details><summary>#15 RANK()ãDENSE_RANK()ãä½¿ã£ã¦ã¿ãã</summary>

OVERãä½¿ã£ãæã ãä½¿ããé¢æ°ã«ã¤ãã¦ãããä½¿ããã®ãè¦ã¦ããã¾ãããã

ã§ã¯ãlikesã®å°ããé ã«ä¸¦ã³æ¿ããå¾ã«ãã®é£çªãæ¬²ããã¨ãã£ãå ´åãèãã¦ã¿ã¾ãããã®å ´åãROW_NUMBER()ã¨ããé¢æ°ãä½¿ãã¾ãã`ROW_NUMBER() OVER (ORDER BY likes)` ã¨æ¸ãã¾ãããããããä»åãã¼ãã£ã·ã§ã³ã¯å¨ä½ãå¯¾è±¡ã«ãã¦ããã®ã§ããã¼ãã£ã·ã§ã³ãçç¥ãã¦`ORDER BY`ã ãã«ãªã£ã¦ããç¹ã«ãæ³¨æãã¾ãããã

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

ä¾ãã°é£çªã§ã¯ãªããé ä½ãç¥ãããã£ãå ´åãlikesã®æ°ã«ã¯8ã2ã¤ããã®ã§ããããèæ®ãã¦é ä½ãã¤ããããªãããã®ããã®é¢æ°ãããã¾ãã

äºã¤ã®æ¹æ³ãããã**RANKé¢æ°**ãä½¿ããã**DENSE_RANKé¢æ°**ãä½¿ãã°OKã§ãã

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
  RANK() OVER (ORDER BY likes) AS rank, -- åãé ä½ã®æ¬¡ã®é ä½ã¯é£ã°ããã
  DENSE_RANK() OVER (ORDER BY likes) AS dense -- åãé ä½ããã£ã¦ãæ¬¡ã®é ä½ã¯é£ã°ãããªã
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

ã¦ã£ã³ãã¦é¢æ°ã§ããä½¿ããªãé¢æ°ãããä½¿ãããã®ã§ãè¦ãã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
OVERãä½¿ã£ãæã«ä½¿ããéè¨é¢æ°ã«ã¤ãã¦ã¿ã¦ããã¾ãã

- ROW_NUMBER()ï¼ã¦ã£ã³ãã¦é¢æ°æã«é£çªãä»ãããã¨ãã§ããã
- RANK()ï¼åãé ä½ã®æ¬¡ã®é ä½ã¯é£ã°ãããã
- DENSE_RANK()ï¼åãé ä½ããã£ã¦ãæ¬¡ã®é ä½ã¯é£ã°ãããªãã</details>


<details><summary>#16 LAG()ãLEAD()ãæ±ã£ã¦ã¿ãã</summary>

nååãnåå¾ã®ã¬ã³ã¼ãã®å¤ãæ±ãããã¨ãã§ããé¢æ°ã«ã¤ãã¦ã

1ååã®ã¬ã³ã¼ãã®likesã®å¤ãæ±ãããã£ããLAG()ãä½¿ã£ã¦ã`LAG(likes, 1) OVER (ORDER BY likes) AS lag,` ã¨æ¸ãã°OKã§ããä»åã¯ãã¼ãã£ã·ã§ã³ã¯å¨ä½ã«ãã¦likesã®å°ããé ã§ä¸¦ã³æ¿ããä¸ã§éè¨ãã¦ããã¾ãããã

1åå¾ã®ã¬ã³ã¼ãã®likesã®å¤ã¯`LEAD()`ã¨ããã°OKã§ãã

1ã®å ´åã¯çç¥ã§ããã®ã§`LAG(likes) OVER (ORDER BY likes) AS lag,` `LEAD(likes) OVER (ORDER BY likes) AS lead,` ãã®ããã«æ¸ãã¦ãåãæå³ã«ãªãã¾ãã

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

ã¾ããããããã¾ãä½¿ãã¨ãåã®ã¬ã³ã¼ãããã®å·®åãªã©ãè¡¨ç¾ãããã¨ãã§ãã¾ãã

likesãã1ã¤åã®likesãå¼ãã¦ãããã°å·®åã«ãªãã¾ãã

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

æ¬¡ã®ã¬ã³ã¼ãã§åã®ã¬ã³ã¼ãããã©ãã ãå¢ããããåããã¾ãã

LAG()ãLEAD()é¢æ°ãä½¿ããããã«ãªã£ã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
nååãnåå¾ã®å¤ãæ±ãããã¨ãã§ããé¢æ°ãè¦ã¦ããã¾ãã

- LAG()ï¼1ååã®ã¬ã³ã¼ãã®å¤ãæ±ããã
- LEAD()ï¼1åå¾ã®ã¬ã³ã¼ãã®å¤ãæ±ããã</details>


<details><summary>#17 ãã©ã³ã¶ã¯ã·ã§ã³ã«ã¤ãã¦çè§£ããã</summary>

post-1ããlikesã1å¼ãã¦ãpost-2ããlikesã1è¶³ãå ´å

```sql
UPDATE posts
SET likes = likes - 1
WHERE id = 1;

UPDATE posts
SET likes = likes + 2
WHERE id = 2;
```

ãã ããããã®ã³ã¼ããå®è¡ããéä¸­ã§ããªãããã®éå®³ãèµ·ãã¦ 2 çªç®ã® UPDATE ãå®è¡ãããªãã£ããããããã¯ä»ã®ã¦ã¼ã¶ã¼ããã¼ã¿ãã¼ã¹ãæä½ãã¦ãã¦ã post-2 ã®ãã¼ã¿ããã£ããæ¸ãæãã¦ããããã¦ããå ´åããã¼ã¿ã®æ´åæ§ãåããªããªã£ã¦ãã¾ãã¾ãã

ããã§ããããã£ãéä¸­ã§ä»ã®æä½ããããããªãä¸é£ã®å¦çãããå ´åã **START TRANSACTION** ã¨ **COMMIT** ã§å²ã£ã¦ãããã° OK ã§ãã

```sql
START TRANSACTION;

UPDATE posts
SET likes = likes - 1
WHERE id = 1;

UPDATE posts
SET likes = likes + 2
WHERE id = 2;

COMMIT;
-- ä¸é£ã®å¦çãè¡ãããã«ã¯ START TRANSACTIONã¨COMMITã§å²ã
```

ããããã¨ããããã®å¦çããã¦ããéã¯ä»ã®å¦çãåãä»ããªããªãã®ã§ããã¼ã¿ã®æ´åæ§ãä¿ãããã¨ãã£ãä»çµã¿ã§ãã

ãªããã²ã¨ã¤ã® UPDATE ãå®è¡ãã¦ã post-1 ã®ããã­ãå¤æ´ããããã¨ã«ãéå®³ãçºçãã¦ãã¡ãã®å¦çãéä¸­ã§æ­¢ã¾ã£ã¦ãã¾ã£ãå ´åã **ROLLBACK** ã¨ããå½ä»¤ãä½¿ããã¨ã§éä¸­ã¾ã§å¦çãé²ãã§ãã¦ããåã»ã©ã®å¤æ´ãåãè¶ããã¨ãã§ãã¾ãã

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

### è³ªåï¼è¤æ°ãã¼ãã«ã®å ´åã®åä½ãæãã¦ãã ãã
åç­ï¼ãã©ã³ã¶ã¯ã·ã§ã³åã§ãã¼ã¿ã®æ´æ°ãçºçããæç¹ã§ãã®è¡(ã¾ãã¯ãã¼ãã«)ãã­ãã¯ããã¾ãã

çµè«ããè¨ãã¾ãã¨ããã©ã³ã¶ã¯ã·ã§ã³åã§ãã¼ã¿ã®æ´æ°ã**çºçããæç¹**ã§ãã®è¡ï¼ã¾ãã¯ãã¼ãã«ï¼ãã­ãã¯ããã¾ãã

â» ãã¼ã¿ã®æ´åæ§ãä¿ã¤ããã«ãã¼ãã«å¨ä½ãã­ãã¯ããããå¾ãªãå ´åã¯ãã¼ãã«ã­ãã¯ãçºçãã¾ãããä»åã®ä¾ã®ãããª id=1 ã®ãã¼ã¿ã ãæ´æ°ããå ´åã¯ãä»ã®ãã¼ã¿ã«ã¯å½±é¿ãåã¼ãã¾ããã®ã§è¡ã­ãã¯ã¨ãªãã¾ãã

ã¤ã¾ãã

```sql
START TRANSACTION;
```

ã®æç¹ã§ã¯ä½ãã­ãã¯ããã¦ãããã

```sql
UPDATE posts SET likes = likes - 1 WHERE id = 1;

```

ã¾ã§ SQL ãå®è¡ãããæç¹ã§ id=1 ã®è¡ã®ã¿ã­ãã¯ããã¾ããï¼ãã®ã¨ã id=2 ã®ãã¼ã¿ã¯ä»ã®ã¦ã¼ã¶ã¼ããæ´æ°å¯è½ã§ããï¼

ãããã£ã¦ãã¼ãã«ã1ã¤ã§ããããè¤æ°ã§ãããããæ´æ°ãçºçããã¾ã§ã¯ã­ãã¯ããããæ´æ°ãçºçãã¦åãã¦ã­ãã¯ãããã¨ããããã§ãã­ã

ãªãããã©ã³ã¶ã¯ã·ã§ã³æã«ãã¼ã¿ãã©ã®ããã«ã­ãã¯ããããã¯ãå®éã«è©¦ãã¦ã¿ãã®ãæãåãããããã§ãã

ãªã³ã©ã¤ã³ã¿ã¼ããã«ã§ã

```
mysql -h db -t -u dbuser -pdbpass myapp
```

ã¨å®è¡ããã¨å®éã« MySQL ãµã¼ãã¼ã«ã­ã°ã¤ã³ã§ãã¾ãã

ããã«ãã©ã¦ã¶ã®ã¿ãããã1ã¤éãã¦ããªã³ã©ã¤ã³ã¿ã¼ããã«ããåæ§ã« MySQL ãµã¼ãã¼ã«ã­ã°ã¤ã³ãã¦ã¿ã¦ãã ããã

ããããã¨åæ¹ã®ãªã³ã©ã¤ã³ã¿ã¼ããã«ãã SQL ãäº¤äºã«å®è¡ã§ãã¾ãã®ã§ãå®éã«ãã©ã³ã¶ã¯ã·ã§ã³ããããéã«ã©ããªãããç¢ºèªã§ãã¾ãã</details>


<details><summary>#18 ãã©ã³ã¶ã¯ã·ã§ã³ãä½¿ã£ã¦ã¿ãã</summary>

#17ã®ä¾ãå®è£ãã¾ãããã

post-1 ã«ä»ããæå¾ã®ããã­ãééãã ã£ãã¨ãã¾ãããããä¿®æ­£ããã«ã¯ id ã 1 ã®ã¬ã³ã¼ãã«é¢ãã¦ã¯ãããã­ã 1 æ¸ãããããã¦ id ã 2 ã®ã¬ã³ã¼ãã«ã¤ãã¦ã¯ãããã­ã 1 å¢ããã¨æ¸ãã¦ãããã° OK ã§ãããã ããã®éä¸­ã§éªé­ãå¥ã£ã¦æ¬²ãããªãå ´åããã©ã³ã¶ã¯ã·ã§ã³ãä½¿ãã°ããã§ãã

`START TRANSACTION` ã¨ãã¦ããã¦ãå¦çã®çµããã§ `COMMIT` ã¨ãã¦ãããã° OK ã§ãã

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

éä¸­ã§ãªãããã®éå®³ãèµ·ããå ´åãèãã¦ã¿ã¾ãããã

ä¾ãã°`UPDATE posts SET likes = likes + 1 WHERE id = 2;` ã®å¦çããã¾ããããªãã£ãã¨ãã¾ãã

ãã®å ´åã§ããã COMMIT ã§ã¯ãªãã¦ `ROLLBACK` ã¨ãã¦ãããã°ããã¡ãã®å¦çã¯ãªãã£ããã¨ã«ã§ããã¯ãã§ãã

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

id ã 1 ã®ã¬ã³ã¼ãã«ã¤ãã¦ UPDATE ããããã®ã«ãé¢ãããã 12 ã®ã¾ã¾ãªã®ãåããã¾ãã
### è¦ç¹ã¾ã¨ã
ãã©ã³ã¶ã¯ã·ã§ã³ã®æåãã³ã¼ãã§ç¢ºèªãã¦ããã¾ãã

- START TRANSACTIONï¼ãã©ã³ã¶ã¯ã·ã§ã³éå§ã®æç¤ºããã®æç¤ºä»¥éã®SQLæã1ã¤ã®ãã©ã³ã¶ã¯ã·ã§ã³ã¨ããã
- COMMITï¼ãã©ã³ã¶ã¯ã·ã§ã³çµäºã®æç¤ºããã®æç¤ºã¾ã§ã1ã¤ã®ãã©ã³ã¶ã¯ã·ã§ã³ã¨ããå¤æ´ãç¢ºå®ããã
- ROLLBACKï¼ãã©ã³ã¶ã¯ã·ã§ã³çµäºã®æç¤ºããã®æç¤ºã¾ã§ã1ã¤ã®ãã©ã³ã¶ã¯ã·ã§ã³ã¨ããå¤æ´ã®åãæ¶ããè¡ãã</details>


<details><summary>#19 ãã¼ãã«ãåå²ãã¦ã¿ãã</summary>

ä»ã¾ã§è¦ã¦ããpostsãã¼ãã«ã®æç¨¿ã«ã³ã¡ã³ããã¤ããããªã£ãå ´åãèãã¾ãã

ãã®å ´åããã¤ãã®ç®¡çæ¹æ³ãããã¾ãããã²ã¨ã¤ã¯ãã¡ãã«**ã«ã©ã ãè¿½å ãã¦ã³ã¡ã³ããç®¡çã§ããããã«ããæ¹æ³**ã§ãã

ãã ãã³ã¡ã³ãã¯è¤æ°ä»ããããã®ãæ®éãªã®ã§ããã®åº¦ã«ã«ã©ã ãå¢ããã¦ãã£ã¦ãããã®ã§ããã**ããã¤å¢ããã°ãããåãããªãã§ãããã³ã¡ã³ããä»ããªãã¬ã³ã¼ããåºã¦ããã®ã§ããã®ãããã®é åãç¡é§**ã«ãªã£ã¦ãã¾ãã¾ãã

ã¾ããå¥ã®æ¹æ³ã¨ãã¦ã¯ã**ã«ã©ã ã¯ã²ã¨ã¤ã ãå¢ããã¦ post-1 ã«å¥ã®ã³ã¡ã³ããä»ããããã®ããã«ã¬ã³ã¼ããè¿½å ããã¨ããæ¹æ³**ãããã¾ãã

ãã ã**ãã®å ´å post-1 ã®ç®æãéè¤ãã¦ããã®ã§ããã®åå®¹éãå¤§ãããªã£ãããã¾ããã® post-1 ã®æè¨ãå¤æ´ããããªã£ãããå¨ã¦ã®ã¬ã³ã¼ããæ´æ°ããªãã¦ã¯ãããªãã®ã§ãå¦çãéããªã£ã¦**ãã¾ãã¾ãã

ããã§ã**ãã¼ãã«ãåå²ãã¦ãã¼ã¿ãç®¡çãããããã**ãã¨ãã£ããã¯ããã¯ãããä½¿ããã¾ãã

ä»åã ã¨ **posts ãã¼ãã«ã®ã»ãã« comments ãã¼ãã«ãä½ã£ã¦ããã¦ããã¡ãã«ã¯ã©ã® post ã«ç´ã¥ããã®ã«ã©ã ãæããã¦**ãããã°ããã§ããã¾ã**ä¸»ã­ã¼ãè¨­å®ãã¦ããã¨ãåãã®ã¬ã³ã¼ããç¹å®ã§ããããã«ãªã£ã¦ä¾¿å©**ãã¨æãã¾ãã

**ãã¼ãã«ãåé¢ãã¦ãããã°åã»ã©è¦ããããªç¡é§ãªé åãã§ãããã¨ãç¡ããªã**ã¾ããããã¨ãã° post-1 ã®æè¨ãæ´æ°ããããªã£ãã¨ããããã®ä¸ç®æãæ´æ°ããã ãã§ããã§ã

ãã®ããã«ãã¼ã¿ãç®¡çããããããããã«ãã¼ãã«ãåå²ãã¦ãããã¨ã**æ­£è¦å**ã¨ããã®ã§èå³ãããäººã¯èª¿ã¹ã¦ã¿ãã¨ããã§ãããã
### è¦ç¹ã¾ã¨ã
ãã¼ãã«ãåå²ãã¦ãã¼ã¿ãç®¡çããããããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- ãã¼ãã«ã®åå²ï¼ãã¼ã¿ãè¤æ°ã®ãã¼ãã«ã«åãã¦æ ¼ç´ããæ¹ããå®å¨ãç¢ºå®ã«ãã¼ã¿ãç®¡çããããã
- æ­£è¦åï¼çç¾ãããã¼ã¿ãæ ¼ç´ã§ããªãããããã¼ãã«ãè¤æ°ã«åå²ãã¦ããä½æ¥­ã®ãã¨ã</details>


<details><summary>#20 è¤æ°ã®ãã¼ãã«ãæ±ã£ã¦ã¿ãã</summary>

posts ãã¼ãã«ã§ãããã¬ã³ã¼ãã 3 ã¤ã ãã«ãã¦ããã¡ãã¯ id ã¨ message ã ãã«ãã¦ããã¾ããã

#19ã§è¦ããcommentsãã¼ãã«ãä½æãã¾ãã

åããpostsãã¼ãã«ãã³ãã¼ãã¦ãcommentsãcommentã«ãã¦ããã¾ãã

æ¬¡ã«ãpostã®idã¨ç´ä»ããããã®ã«ã©ã ãè¿½å ãã¾ãã

post_id ã§æ´æ°å(INT)ã¨æ¸ãã¾ãã

```sql
DROP TABLE IF EXISTS posts;
CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

DROP TABLE IF EXISTS comments; -- commentsã«æ¸ãç´ã
CREATE TABLE comments ( -- commentsã«æ¸ãç´ã
  id INT NOT NULL AUTO_INCREMENT,
	post_id INT
  comment VARCHAR(140), -- åæ°å½¢ã®commentã«æ¸ãç´ã
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');
```

å¾ã¯ãã¬ã³ã¼ããããã¤ãè¿½å ãã¾ãã

`INSERT INTO posts (message) VALUES
('post-1'),
('post-2'),
('post-3');`

ãã³ãã¼ãã¦æ¸ãæãã¾ãã

comments ãã¼ãã«ã«å¯¾ãã¦ã post_id ã¨ comment ã®å¤ãæ¿å¥ãã¦ããã¾ãããã

ã²ã¨ã¤ç®ã® post ã«å¯¾ãã¦ã³ã¡ã³ãã¨ãã£ãæãã§æ¸ãã¦ããã°ããã§ãã­ã

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

ã§ã¯ 1 çªç®ã® post ã«ããã²ã¨ã¤ã³ã¡ã³ããä»ããã¨ãã¾ãããããããã 3 çªç®ã® post ã«å¯¾ãã¦ 1 ã¤ã®ã³ã¡ã³ããããã¦èª¬æã®ããã« post ã« 4 çªç®ã¯ããã¾ãããããã®ããã« 4 ã¤ç®ã®ã³ã¡ã³ããè¶³ãã¦ã¿ã¾ãããã

ããã§ãpostsã¨commentsã®ä¸­èº«ãç¢ºèªãã¾ãã

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


<details><summary>#21 åé¨çµåãå¤é¨çµåã«ã¤ãã¦è¦ã¦ããã</summary>

postsã¨commentsã®2ã¤ã®ãã¼ãã«ããããpost-1ã«ã¯commentã2ä»¶ãpost-2ã«ã¯commentãªããpost-3ã«ã¯commentã1ä»¶ãããã¦postsã«ç´ã¥ãã¦ããªãcommentã1ä»¶ããç¶æã§ãã

ããã§ã2ã¤ã®ãã¼ãã«ãçµåããã¦ããã¼ã¿ãæ½åºãã¦ããããã®ã§ãããå¤§ãã2ã¤ã®æ¹æ³ãããã¾ãã

1ã¤ç®ã¯**åé¨çµå**ã¨ããæ¹æ³ã§ã2ã¤ã®ãã¼ãã«ã«å±éã®ãã¼ã¿ãåå¾ããæ¹æ³ã§ãã

ä»åã ã¨postsã«ãcommentsã«ããã¼ã¿ãããã®ã¯post-1ã¨post-3ã ããªã®ã§ãåé¨çµåããã¨çµæã¯ãã®ããã«ãªãã¾ãã

postsãã¼ãã«

| id | message |
| --- | --- |
| 1 | post-1 |
| 2 | post-2 |
| 3 | post-3 |

commentsãã¼ãã«

| id | post_id | message |
| --- | --- | --- |
| 1 | 1 | comment-1-1 |
| 2 | 1 | comment-1-2 |
| 3 | 3 | comment-3-1 |
| 4 | 4 | comment-4-1 |

postsãã¼ãã«ã¨commentsãã¼ãã«ãåé¨çµåããçµæ

| id | message | id | post_id | message |
| --- | --- | --- | --- | --- |
| 1 | post-1 | 1 | 1 | comment-1-1 |
| 1 | post-1 | 2 | 1 | comment-1-2 |
| 3 | post-3 | 3 | 3 | comment-3-1 |

2ã¤ç®ã®æ¹æ³ã¯**å¤é¨çµå**ã¨ããæ¹æ³ã§ãã

å¤é¨çµåã«ã¯ãå·¦ã®ãã¼ãã«ãè»¸ã«ãã**å·¦å¤é¨çµå**ãå³ã®ãã¼ãã«ãè»¸ã«ãã**å³å¤é¨çµå**ãããã¾ãã

å·¦å¤é¨çµåã®å ´åã¯ãpostsãè»¸ã«ãã¦ããã«å¯¾å¿ããcommentãããã°åå¾ããã®ã§ãã®ããã«ãªãã¾ãã

postsãã¼ãã«

| id | message |
| --- | --- |
| 1 | post-1 |
| 2 | post-2 |
| 3 | post-3 |

commentsãã¼ãã«

| id | post_id | message |
| --- | --- | --- |
| 1 | 1 | comment-1-1 |
| 2 | 1 | comment-1-2 |
| 3 | 3 | comment-3-1 |
| 4 | 4 | comment-4-1 |

å·¦å¤é¨çµåã®çµæ

| id | message | id | post_id | message |
| --- | --- | --- | --- | --- |
| 1 | post-1 | 1 | 1 | comment-1-1 |
| 1 | post-1 | 2 | 1 | comment-1-2 |
| 2 | post-2 | NULL | NULL | NULL |
| 3 | post-3 | 3 | 3 | comment-3-1 |

commentããªãç®æã¯NULL(ãã«)ã«ãªãã®ã§ãæ³¨æãã¦ããã¾ããããcomment ãã¤ãã¦ãããã©ããã«é¢ãããã**æç¨¿ã®ä¸è¦§ãã¼ã¸ãä½ãã¨ããªã©ã«ä½¿ãã°ããã§ãã­ã**

ä¸æ¹å³å¤é¨çµåã§ãããä»åº¦ã¯ comment ã ãå¨ã¦åå¾ãã¦ãããã«ç´ã¥ããæç¨¿ãããã°åå¾ããã®ã§ãçµæã¯ãã®ããã«ãªãã¾ãã

postsãã¼ãã«

| id | message |
| --- | --- |
| 1 | post-1 |
| 2 | post-2 |
| 3 | post-3 |

commentsãã¼ãã«

| id | post_id | message |
| --- | --- | --- |
| 1 | 1 | comment-1-1 |
| 2 | 1 | comment-1-2 |
| 3 | 3 | comment-3-1 |
| 4 | 4 | comment-4-1 |

å³å¤é¨çµå

| id | message | id | post_id | message |
| --- | --- | --- | --- | --- |
| 1 | post-1 | 1 | 1 | comment-1-1 |
| 1 | post-1 | 2 | 1 | comment-1-2 |
| 3 | post-3 | 3 | 3 | comment-3-1 |
| NULL | NULL | 4 | 4 | comment-4-1 |

è©²å½ããæç¨¿ããªããã°ããã¯NULLã«ãªãã®ã§ããã®ç¹ã«ãæ³¨æãã¦ããã¾ããããããã«**ç´ã¥ããæç¨¿ããããã©ããã«é¢ãããã comment ã®ä¸è¦§ãè¡¨ç¤ºãããã¨ããªã©ã«ä½¿ãã°ããã§ãã­ã**
### è¦ç¹ã¾ã¨ã
2ã¤ã®ãã¼ãã«ãããã¼ã¿ãåå¾ããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- åé¨çµåã®èª¬æï¼çµåãã¹ãç¸æã®è¡ãè¦ã¤ãããªãå ´åã«è¡ãæ¶æ»ãã¦ãã¾ãéå¸¸ã®çµåã®ãã¨ã
- å·¦å¤é¨çµåã®èª¬æï¼ãNULLã®è¡ãçã¿åºãã¦ããå·¦è¡¨ã®å¨è¡ãå¿ãåºåãããå¦çã®ãã¨ã
- å³å¤é¨çµåã®èª¬æï¼ãNULLã®è¡ãæå³åºãã¦ããå³è¡¨ã®å¨è¡ãå¿ãåºåãããå¦çã®ãã¨ã</details>


<details><summary>#22 åé¨çµåãä½¿ã£ã¦ã¿ãã</summary>

åé¨çµåãè¦ã¦ã¿ã¾ãã

posts ã¨ comments ãåé¨çµåãããå ´åã¯ã `posts INNER JOIN comments` ã¨ãã¦ããã¦ãç´ã¥ããã«ã©ã ã ON ã§è¡¨ç¾ãã¦ããã¾ãã

ä»åã ã¨ posts ã® id ã¨ comments ã® post_id ãç´ä»ãã¦ãããã°ããã§ãã

å®ã¯INNERãçç¥ãã¦ãåé¨çµåã«ãªãã®ã§ãåã«JOINã¨æ¸ãã¦ãä¸ã¨åãæå³ã«ãªãã¾ãã

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
  -- posts INNER JOIN comments ON posts.id = comments.post_id; -- åé¨çµå
  posts JOIN comments ON posts.id = comments.post_id; /* postsãã¼ãã«ã¨commentsãã¼ãã«ãåé¨çµåãã
postsãã¼ãã«ã®idã¨commentsãã¼ãã«ã®post_idãç´ä»ããã */
-- ã©ã¡ããåãæå³

+----+---------+----+---------+-------------+
| id | message | id | post_id | comment     |
+----+---------+----+---------+-------------+
|  1 | post-1  |  1 |       1 | comment-1-1 |
|  1 | post-1  |  2 |       1 | comment-1-2 |
|  3 | post-3  |  3 |       3 | comment-3-1 |
+----+---------+----+---------+-------------+
```

ã¾ããç¹å®ã®ã«ã©ã ã ããæ½åºãããå ´åã¯ããã¼ãã«åãä»ãã¦ããã¦ãæ½åºããã° OK ã§ãã

posts ã® id ã¨ posts ã® message ã¨ comments ã® comment ã ãæ½åºãããå ´åã¯ããã®ããã«æ¸ãã¦ãããã° OK ã§ãã

ä½ã id ã¯ä¸¡æ¹ã«ããã®ã§ãã¼ãã«åãä»ããå¿è¦ãããã¾ããã **message ã comment ã¯ããããã®ãã¼ãã«ã«ãããªãã®ã§ãçç¥ãã¦ããããã¨ãã§ãã¾ã**ã

ãããã£ã¦ãã¡ãã¯ã [posts.id](http://posts.id/) ã¨ message ã¨ comment ã ãã§ã OK ã§ãã

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
  posts.id, message, comment -- messageã¨commentã¯ããããã®ãã¼ãã«ã«ãããªãã®ã§çç¥ãå¯è½
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

ããããåé¨çµåãã§ããããã«ãªã£ã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
ã³ã¡ã³ããã¤ãã¦ããæç¨¿ã ããæãåºãæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- INNER JOINï¼INNERãçç¥ãããã¨ãå¯è½ã</details>


<details><summary>#23 å¤é¨çµåãä½¿ã£ã¦ã¿ãã</summary>

å·¦å¤é¨çµåã¯ã`LEFT OUTER JOIN` ã¨ããã°ããã®ã§ãããå®ã¯ `OUTER` ã¯çç¥ã§ããã®ã§ãåã« `LEFT JOIN` ã¨æ¸ãã¦ããã¦ãå·¦å¤é¨çµåã«ãªãã¾ãã

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
  posts LEFT JOIN comments ON posts.id = comments.post_id; -- OUTERãçç¥å¯è½
```

å³å¤é¨çµåã¯RIGHT JOINã«ããã°OKã§ãã

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

å·¦å¤é¨çµåã§ã¯ posts ããå¨ã¦ã¬ã³ã¼ããæ½åºããã¦ãã¦ãããã«é¢é£ãã comment ãããã°åå¾ãã¦ãããã¦ãªããã° NULL ã§åãã¦ããã¾ãã

å³å¤é¨çµåã§ãããã¾ã comments ããå¨ã¦ã®ã¬ã³ã¼ããæ½åºããã¦ãããã«é¢é£ããæç¨¿ãããã°åå¾ãã¦ããªããã° NULL ã§åãã¦ããã¾ãã

ãã®ãããªéããããã®ã§ãç®çã«å¿ãã¦ä½¿ãåããããããã«ãã¦ããã¦ãã ããã
### è¦ç¹ã¾ã¨ã
å·¦å¤é¨çµåã¨å³å¤é¨çµåã«ã¤ãã¦ã¿ã¦ããã¾ãã

- LEFT OUTER JOINï¼OUTERãçç¥ã§ããã**â»ç¾å ´ã§ã¯çç¥å½¢ãå¤ç¨ãã¦ããã**
- RIGHT OUTER JOINï¼OUTERãçç¥ã§ããã</details>


<details><summary>#24 å¤é¨ã­ã¼å¶ç´ãè¨­å®ããã</summary>

ãã¦ãä»ã¾ã§è¦ã¦ãã comments ã®ãã¼ã¿ã§ããã post-4 ããªãã®ã«ãããã«å¯¾ãã¦ comment ãä»ããããã®ã¯å°ããããããããªæ°ããã¾ãã

ããããå¤ãªãã¼ã¿ãå¥ãè¾¼ã¾ãªãããã«ããããã®å¤é¨ã­ã¼å¶ç´ã«ã¤ãã¦è¦ã¦ããã¾ãããã

ã§ã¯ãcomments ãã¼ãã«ã® post_id ã«å¯¾ãã¦ã posts ãã¼ãã«ã® id ã«ãã®å¤ãå­å¨ããªãã£ããå¼¾ãã¦ãããããã«è¨­å®ãã¦ããã¾ãããã

ã©ããããã¨ããã¨ã FOREIGN KEY ã¨ãã¦ããã¦ã post_id ã«å¯¾ãã¦ posts ãã¼ãã«ã® id ãåç§ãã¦ãããã«å¤ããªããã°å¼¾ãã¦ã­ã¨ããã®ããã«æ¸ãã¦ããã¾ãã

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

å¤é¨ã­ã¼ã®å¶ç´ã«ãã£ã¦å¤ãªãã¼ã¿ãè¿½å ã§ããªããªã£ã¦ããã®ãåããã¾ãã

ä»ãå¤é¨ã­ã¼ãè¨­å®ããã¾ããããããããã¨ããã§ç´ä»ããpostsãã¼ãã«ã®ã»ãã«ãå¶éããããã®ã§æ³¨æãã¾ãããã

ä¾ãã°ãä»ä¸çªä¸ã§ posts ãã¼ãã«ãåé¤ãã¦ãã¾ããã posts ãã¼ãã«ã¯ä» comments ãã¼ãã«ã«ç´ã¥ãã¦ããã®ã§ããã®ã¾ã¾åé¤ããã¨ comments ãã¼ãã«ã®ãã¼ã¿ãå®ã¶ãããã«ãªã£ã¦æ´åæ§ãåããªããªã£ã¦ãã¾ãã¾ãã

ãã®ããããã®ã¾ã¾å®è¡ããã¨ã¨ã©ã¼ã«ãªãã¾ãã

ãããç´ãã«ã¯ããã§ posts ãã¼ãã«ãåé¤ããåã«å¤é¨ã­ã¼ã®è¨­å®ãããã¦ãã comments ãã¼ãã«ãåã«åé¤ãã¦ãããã° OK ã§ãã

ä»åã¯ãã¡ãã® DROP TABLE æã posts ãã¼ãã«ãåé¤ããåã«æã£ã¦ãã¦ããã¾ãããã

å¤ãªãã¼ã¿ã¯ã³ã¡ã³ãã«ãã¾ãã

ãããããã¼ã¿ã®ä¸­èº«ãä¸å¿ç¢ºèªãããã®ã§ã SELECT * FROM posts ã SELECT * FROM comments ã¨ãã¦ããã¾ãããã

ããã§ã¨ã©ã¼ãåºãªããªãã¯ãã§ãã

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

### è³ªåï¼**posts ãã¼ãã«ã®åé¤ã¨ comments ãã¼ãã«ã®åé¤ãéã«ããã¨ã¨ã©ã¼ã«ãªãã®ã¯ãªãã§ããï¼**
åç­ï¼**comments ãã¼ãã«ã¯ posts ãã¼ãã«ãå¿è¦ã¨ãã¦ããããã§ãã**

ãcomments ãã¼ãã«ã¯ posts ãã¼ãã«ãå¿è¦ã¨ãã¦ããããããposts ãã¼ãã«ã¯comments ãã¼ãã«ãå¿è¦ã¨ãã¦ããªãããããªã®ã§ãã

å·ä½çã«ç³ãä¸ãã¾ãã­ã

posts ã¯ãsns ã®æç¨¿ãã®ã¤ã¡ã¼ã¸ã§ããã­ãããã¦ comments ãã¼ãã«ã¯ãæç¨¿ã«å¯¾ããã³ã¡ã³ããã®ã¤ã¡ã¼ã¸ã§ããã

æç¨¿ã«å¯¾ããã³ã¡ã³ãã¯ãã©ã®æç¨¿ã«å¯¾ããã³ã¡ã³ããããå¿è¦ã§ããããã

```lisp
FOREIGN KEY (post_id) REFERENCES posts(id)

```

ãã®é¨åã§ãã`post_id`Â ããã©ã®æç¨¿ã«å¯¾ããã³ã¡ã³ããããè¡¨ãã¾ãã

ãã¦ãããã§ããåã« posts ãã¼ãã«ãåé¤ãã¦ãã¾ã£ããã©ããªãã§ãããï¼

comments ãã¼ãã«ã¯æ®ãã®ã§ãããä¸ã®

```lisp
FOREIGN KEY (post_id) REFERENCES posts(id)

```

ã®é¨åãæå³ãæããªããªãã®ããããã¾ãã§ãããããposts ãã¼ãã«ãã¤ã¾ããsns ã®æç¨¿ãããªãã®ã«ããªãæç¨¿ã«å¯¾ããã³ã¡ã³ããã ããããã¨ããããããªç¶æã«ãªãã¾ãã

ãªã®ã§ MySQL ã¯ã¨ã©ã¼ã¨ãªããããªã®ã§ãã

éã ã¨ã©ãã§ãããï¼

commentsãã¤ã¾ããæç¨¿ã«å¯¾ããã³ã¡ã³ããã ããåé¤ããã¦ postsãã¤ã¾ããsns ã®æç¨¿ãã ããããç¶æã§ããããã¯ãsns ã®æç¨¿ãã¯ãæç¨¿ã«å¯¾ããã³ã¡ã³ããããªãã¦ãç¹ã«ãã¼ã¿çã«ã¯åé¡ããã¾ããã­ãããã¦ MySQL ãã¨ã©ã¼ã«ã¯ãªãã¾ããã

ãªã®ã§ãã¼ãã«ãåé¤ããéã¯ããposts ãã¼ãã«ã¯ comments ãã¼ãã«ãå¿è¦ã¨ãã¦ããªããã®ã§ãã¾ãä¸è¦ãª comments ããåé¤ããå¿è¦ããããã¨ãããããªã®ã§ãã­ã
### è¦ç¹ã¾ã¨ã
ãã¼ã¿ã®æ´åæ§ãåãããã«ãå¤é¨ã­ã¼å¶ç´ã«ã¤ãã¦è¦ã¦ããã¾ãã

- å¤é¨ã­ã¼å¶ç´ã®è¨­å®ï¼**å¤é¨ã­ã¼**ã¯ãä»ã®ãã¼ãã«ã®é¢é£è¡ãæãããã®å¤ãæ ¼ç´ãããã¨ã§ãªã¬ã¼ã·ã§ã³ã·ãããçµã¶å½¹å²ãæã£ã¦ããåã®ãã¨ã**å¤é¨ã­ã¼å¶ç´**ã¯ãåç§æ´åæ§ãå´©ãããããªãã¼ã¿æä½ããããã¨ããå ´åã«ã¨ã©ã¼ãçºçãããå¼·å¶çã«å¦çãä¸­æ­ãããå¶ç´ã®ãã¨ã</details>


<details><summary>#25 ãã¼ã¿ã®æ´åæ§ãä¿ã¨ã</summary>

å¤é¨ã­ã¼ãè¨­å®ãããç¶æã§ posts ãã¼ãã«ã®ã»ãã«å¤æ´ãå ãããã£ãå ´åãè¦ã¦ã¿ã¾ãã

ãã¡ãã§ post-3 ãåé¤ãã¦ã¿ã¾ãããã

id ã 3 ã®ã¬ã³ã¼ããåé¤ããã°ããã®ã§ã `DELETE FROM posts WHERE id = 3` ã¨ãã¦ãããã°ããã§ãããã

ãã ããã®å ´å post-3 ã«ç´ã¥ã comment ãå­å¨ãã¦ããã®ã§ããã¼ã¿æ´åæ§ãä¿æããããã«ããã®ã¾ã¾ã§ã¯ãã¾ããããªãã¯ãã§ãã

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

ã§ã¯ãããã§ãã¼ã¿ã®æ´åæ§ãåãããã«ã posts ã®ã»ãã§ã¬ã³ã¼ããåé¤ãããããåããã¦ comments ã®ã»ãã§ãç´ã¥ãã¬ã³ã¼ããåé¤ãããããã«è¨­å®ãã¦ã¿ã¾ãããã

ã©ããããã¨ããã¨ããã¡ãã§ `ON DELETE CASCADE` ã¨ãã¦ããã¾ãã

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

post-3 ãåé¤ããã¦ãã¦ã comment ãããé¢é£ããã¬ã³ã¼ããæ¶ãã¦ããã®ãåããã¾ãã

ãããã DELETE ã ãã§ã¯ãªãã¦ã UPDATE ã«é¢ãã¦ãè¨­å®ãããã¨ãã§ãã¾ãã

`ON UPDATE CASCADE` ã¨ãã¦ã¿ã¾ãããã

ãããã¦ããã¨ã posts ã®ãã¼ã¿ãæ´æ°ãããã¨ããã¡ãã®ç´ã¥ããã¼ã¿ãæ´æ°ãããããã«ãªãã¾ãã

ä»åç´ã¥ãã¦ããã®ã¯ id ãªã®ã§ã id ãæ´æ°ãã¦ã¿ã¾ãããã

id ã 1 ã§ã¯ãªãã¦ã 100 ã ã£ãã¨ãã¦ã¿ã¾ãã

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

id ã 100 ã«ãªã£ã¦ãããã«é¢é£ãããã¼ã¿ã 100 ã§æ´æ°ããã¦ããã®ãåããã¾ãã

å¤é¨ã­ã¼å¶ç´ãä½¿ãã¨ããã®ããã«ãã¼ã¿ã®æ´åæ§ãåããããã«ãªãã®ã§ãæ£ãã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
å¤é¨ã­ã¼ã®è¨­å®ã¨åããã¦ããã¼ã¿ã®æ´åæ§ãåãããã®æ¹æ³ãè¦ã¦ããã¾ãã

- ON DELETE CASCADEï¼ç´ä»ãã¬ã³ã¼ããåé¤ããã
- ON UPDATE CASCADEï¼ç´ä»ãã¬ã³ã¼ããæ´æ°ããã</details>


<details><summary>#26 LAST_INSERT_ID()ãä½¿ã£ã¦ã¿ãã</summary>

ä»ã¯ãã¼ã¿ã®æ´åæ§ãåãã¦ããç¶æã§ãã

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

æ°ããæç¨¿ãè¿½å ããã¨ãã¾ãããã

é©å½ã«ãânew post!âã¨ãã¾ãã

ããã¦ããã®æç¨¿ã«ã³ã¡ã³ããä»ãããã£ãã¨ãã¾ãããã

ãã®å ´åã§ãããINSERTæãä½¿ã£ã¦ãããã°ããã§ãã

post_idã«ã¤ãã¦ã¯ãè¿½å ããæç¨¿ã¯4çªç®ãªã®ã§ã4ã¨ãã¦ãããã°ããã§ãã

post_idã¯4ãcommentã¯ânew commentâã¨ãã¾ãããã

ããã§ãããã®ã§ãããcommentãæ¿å¥ãããã³ã«ä»ã®ããã«æ°ããã®ã¯é¢åã§ãã

ããã§ãMySQLã§ã¯**ç´åã«æ¿å¥ãããã¬ã³ã¼ãã®idãèª¿ã¹ãå½ä»¤ãç¨æããã¦**ãã¦ã`LAST_INSERT_ID`ã¨ããã¨èªåçã«4ãå¥ã£ã¦ããã¾ãã

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

LAST_INSERT_IDãä¾¿å©ãªã®ã§ãæ£ãã¦ããã¦ãã ããã
### è¦ç¹ã¾ã¨ã
ç´åã«æ¿å¥ãããã¬ã³ã¼ãã®IDãèª¿ã¹ãå½ä»¤ã«ã¤ãã¦è¦ã¦ããã¾ãã

- LAST_INSERT_ID()ï¼ç´åã«æ¿å¥ãããã¬ã³ã¼ãã®idãèª¿ã¹ãå½ä»¤ã</details>


<details><summary>#27 ã³ã¡ã³ãã«ã³ã¡ã³ããã¤ããã</summary>

ä»ã®æç¨¿ã¨ã³ã¡ã³ããã©ã®ãããªç¶æãããã³ã¡ã³ãã§ã¾ã¨ãã¦è¦ã¾ãããã

3 ã¤æç¨¿ããã£ã¦ã post-1 ã¨ post-3 ã« comment ãã¤ãã¦ããç¶æã§ãã

post-1 ã«ã¯ 2 ã¤ comment ãã¤ãã¦ãã¦ã post-3 ã«ã¯ 1 ã¤ comment ãã¤ãã¦ãç¶æã§ãã

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

ã§ã¯ããã§ comment ã«å¯¾ãã¦ comment ãä»ããããããã«ãããã£ãã¨ãã¾ãããã

ãã¨ãã° comment-1-2 ã«å¯¾ãã¦ããã« 2 ã¤ comment ãä»ããã¨ãã¾ãã

ããã«comment-1-2-1ã«ãã 1 ã¤ comment ãä»ããã¨ãã¾ãããã

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

ãã®å ´åãã©ããã¼ã¿ãç®¡çãã¦ãããã§ããã comment ãã©ã®è¦ªã comment ã«æã¤ããä¿æãã¦ããã°è¯ãããã§ãã

`parent_id`ã¨ããã«ã©ã ãç¨æãã¾ãã

ãã®ä¸ã§ã¾ãããã® 3 ã¤ã® comment ã§ãããè¦ªã¨ãªã comment ããªãã®ã§ãã¡ãã«é¢ãã¦ã¯ NULL ã¨ãã¦ããã¾ãããã

 comment-1-2 ã«å¯¾ãã¦ 2 ã¤ comment ãä»ããã®ã§ããã¡ãã§ã¬ã³ã¼ããæ¿å¥ãã¦ããã¾ãã

post ã¯ 1 ã«é¢ãããã®ã§ããã¦ã comment ã¯ã¡ãã£ã¨æ´æ°ãã¦ããã¦ãããã¦ parent_id ã§ããã 2 çªç®ã® comment ãªã®ã§ããã¡ãã¯ 2 ã«ãã¦ãããã°ããã§ãããã

ããã« comment-1-2-1 ã«å¯¾ãã¦ããã²ã¨ã¤ comment ãä»ããã®ã§ãã¬ã³ã¼ããæ¿å¥ãã¦ããã¾ãããã

post_id ã¯ 1 ã§ããã¦ããã¡ãã¯æ´æ°ãã¦ããã¦ãããã¦ parent_id ã§ããã 4 çªç®ã® comment ã«ã¤ãã¦ããã®ã§ããã¡ãã 4 ã«ãã¦ãããã°è¯ãã§ãããã

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


<details><summary>#28 ã³ã¡ã³ãã®ã³ã¡ã³ããæ½åºãã¦ã¿ãã</summary>

comment ã« comment ãä»ããããããã«ãªã£ãã®ã§ãæ¬¡ã¯ 2 çªç®ã® comment ã«ä»ãã¦ãããã¹ã¦ã® comment ãæ½åºããæ¹æ³ã«ã¤ãã¦èãã¦ã¿ã¾ãããã

ãã®å ´åãã¾ã 2 çªç®ã® comment ãè¦ªã«æã¤ comment ãæ½åºããã°ããã®ã§ã`SELECT * FROM comments WHERE paren_id = 2;`ãã¨ããã°comment-1-2-1ã¨comment-1-2-2ãåå¾ã§ãã¾ãã

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

ã§ã¯ããã®æ¬¡ã®éå±¤(comment-1-2-1-1)ã® comment ãåå¾ãããã®ã§ãããcomments ãã¼ãã«ãã parent_id ãããã§æ½åºãã id ã¨ä¸è´ãããã®ãæ½åºãã¦ãããã°ããã§ãã

 comments ãã¼ãã«ã¨ãã¡ãã®çµæã comments ãã¼ãã«ã® parent_id ã¨ãã®çµæã® id ã§åé¨çµåãã¦ãããã°è¯ãããã§ãã

çµæã t ã¨ãããã¼ãã«ã ã¨ä»®å®ãã¦ãã¡ãã£ã¨ã¯ã¨ãªãçµã¿ç«ã¦ã¦ã¿ã¾ãããã

commentsããæ½åºãããã®ã§ã`SELECT comments.* FROM commetns JOIN t`ãã¨æ¸ãã¦tãåé¨çµåãã¾ãã

ãã®ä¸ã§ãcommentsãã¼ãã«ã®parent_idã¨tã®idãç´ä»ãã¾ãã

ãã®tã¯ã`SELECT * FROM comments WHERE parent_id = 2`ãã®çµæãªã®ã§ããµãã¯ã¨ãªãä½¿ã

`commetns JOIN (
SELECT * FROM comments WHERE parent_id = 2
) AS t`

ã¨æ¸ãã¾ãã

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

ãã¨ã¯ããããã®çµæãç¸¦ã«ãã£ã¤ããã°ããã®ã§ã `UNION ALL` ãä½¿ã£ã¦ãããã°è¯ãã§ãããã

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

ãã ãè¦ã¦ã®éã SQL ãé·ããªãããã¾ããããã£ã¨æ·±ãéå±¤ã« comment ãä»ãããããã«é·ããªã£ã¦ãã¾ãã¾ãã
### è³ªåï¼ã³ã¡ã³ãã®ã³ã¡ã³ãã®æ½åºã®ä»æ¹ããããã¾ãã
åç­ï¼

ã½ã¼ã¹ã³ã¼ãã«ã³ã¡ã³ããå¥ãã¦èª¬ææã«ãããã³ã°ããã¾ããã®ã§ä¸è¨ã®ã½ã¼ã¹ã³ã¼ãã§ãç¢ºèªãã ããã

```sql
SELECT
  comments.*
FROM
  comments JOIN (
    -- ãcomments ãã¼ãã«ãã parent_id ãããã§æ½åºãã id ã¨ä¸è´ãããã®ã
    -- ã« t ã¨ããååãä»ãã¾ãã
    SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  -- comments ãã¼ãã«ã¨ tã¨ååãä»ãããã¼ãã«ãçµåããã¾ã
  comments.parent_id = t.id;

```

ã¤ã¡ã¼ã¸æ¹§ãã¾ããï¼

### è³ªåï¼UNION ALLã§æ¬å¼§ãå¿è¦ãªã®ã¯ã©ãããã¨ãã§ããï¼
**åç­ï¼ORDER BYã¾ãLIMITãä½¿ãå ´åãSELECTã«æ¬å¼§ãå¿è¦ã«ãªãã¾ãã**

çµè«ããè¨ãã¨ã#07 ã§æ¬å¼§ãå¿ç¨ãªã®ã¯`ORDER BY`Â ã¨Â `LIMIT`Â ãä½¿ã£ã¦ããããã§ãã

`UNION`Â æ§æã§åãã®Â `SELECT`Â ã«Â `ORDER BY`Â ã¾ãã¯Â `LIMIT`Â ãé©ç¨ããå ´åã¯ããããã®Â `SELECT`Â ãæ¬å¼§ã§ãããå¿ç¨ãããã¾ãã

ãã¡ãã #28 ã®æ¹ãæ¬å¼§ã§ããã£ã¦åé¡ããã¾ããããã®æ¹ãã©ãããã©ãã¾ã§ãÂ `SELECT`Â ã®ã¾ã¨ã¾ããããããããã¦ããããããã¾ããã­ã

### è³ªåï¼åé¨çµåã®æåãçè§£ã§ãã¾ããã

```sql
SELECT comments.*
FROM comments JOIN (
  SELECT * FROM comments WHERE parent_id = 2
  ) AS t
ON
  comments.parent_id = t.id;

```

ã®åé¨çµåã«ãã£ã¦ããªããparent_id = 4 ã ããæ½åºãããã®ããããã¾ãããã©ã®æ§ãªæµãã«ãªã£ã¦ããã®ã§ããããï¼
**åç­ï¼**

ã¾ãã`SELECT * FROM comments WHERE parent_id = 2`ãã®ã¯ã¨ãªã®çµæã§ãããä»¥ä¸ã®ããã«ãªã£ã¦ããã¨æãã¾ãã

```
+----+---------+---------------+-----------+
| id | post_id | comment       | parent_id |
+----+---------+---------------+-----------+
|  4 |       1 | comment-1-2-1 |         2 |
|  5 |       1 | comment-1-2-2 |         2 |
+----+---------+---------------+-----------+

```

ããã¦ããã®ã¯ã¨ãªã®çµæã`t`ã¨ãã¦ãcommentsã¨`comments.parent_id = t.id`ãæ¡ä»¶ã«çµåãã¦ããããåé¨çµåã®çµæã`comments.parent_id`ã®å¤ã`t.id`ã®4, 5ã¨ç­ããã¬ã³ã¼ãã®ã¿ãæ®ãã¾ãã`comments.parent_id`ã5ã®ã¬ã³ã¼ãã¯å­å¨ãã¾ããã®ã§ã`comments.parent_id`ã4ã®ã¬ã³ã¼ãã®ã¿ãæ½åºããããã¨ã«ãªãã¾ãã

### è³ªåï¼SELECT *â¦ã§ã¯ãã¡ã§ããï¼
**åç­ï¼UNION ALLã§ã¬ã³ã¼ããé£çµããå ´åãã«ã©ã æ°ãæããå¿è¦ãããã¾ãã**

```sql
SELECT * FROM ...

```

ã¨ããã¨çµæã¯ãµãã¯ã¨ãªã¨ã®åé¨çµåã«ãªãããã

```
+----+---------+-----------------+-----------+----+---------+---------------+-----------+
| id | post_id | comment         | parent_id | id | post_id | comment       | parent_id |
+----+---------+-----------------+-----------+----+---------+---------------+-----------+
|  6 |       1 | comment-1-2-1-1 |         4 |  4 |       1 | comment-1-2-1 |         2 |
+----+---------+-----------------+-----------+----+---------+---------------+-----------+

```

ã¨ãã£ãå½¢ã«ãªãã¾ãã

ãã ä»åã¯UNION ALLã§æåã®SQLã¨é£çµããããã«ã«ã©ã æ°ãæããªãã¨ããã¾ãããããã§`comments`ãã¼ãã«ã®å¤ã ããä½¿ã£ã¦ãã¾ãã
### è¦ç¹ã¾ã¨ã
ã³ã¡ã³ãã®ã³ã¡ã³ããæ½åºãã¦UNION ALLã§ã¾ã¨ããæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- ã³ã¡ã³ãã®ã³ã¡ã³ããæ½åº
- UNION ALLï¼2ã¤ã®SELECTæã®çµæãè¶³ãåãããã</details>


<details><summary>#29 CTEãä½¿ã£ã¦ã¿ãã</summary>

ååè¦ããããªéå±¤ãæ·±ããªã£ã¦ãããã¼ã¿ãå¦çãããå ´åã **CTE** ã¨ããä»çµã¿ãä½¿ãã¾ãã

CTE ã¯ Common Table Expression ã®ç¥ã§ãã¯ã¨ãªåã§ä½¿ããä¸æçãªãã¼ãã«ã®ãã¨ã§ãã

ã¾ã CTE ã«ã¯**åå¸°çãª CTE** ã¨ã**åå¸°çã§ã¯ãªã CTE** ããã£ã¦ãåå¸°çãª CTE ãä»åå®ç¾ããããéå±¤ãæ·±ããªã£ã¦ãããã¼ã¿ãå¦çããããã®æ¹æ³ã«ãªãã¾ãã

åå¸°çã§ã¯ãªã CTE ã¯ãµãã¯ã¨ãªãåãããããæ¸ãæããããã®ãã®ã§ãã

åå¸°çã§ã¯ãªã CTE ãä½¿ãã«ã¯ä¸ã®ã»ãã§ WITH ã¨ãã¦ããã¦ã CTE ã®ååãå¥½ãã«ä»ãã¦ããã¦ã AS ã¨ãã¦ããã¦ã CTE ã¨ããããµãã¯ã¨ãªãããã«æ¸ãã¦ããã¾ãã

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
--   åå¸°çãªCTE
--   åå¸°çã§ã¯ãªãCTE

WITH t AS ( -- æ¬å¼§åã«CTEã¨ããããµãã¯ã¨ãªãæ¸ãã
	SELECT * FROM comments WHERE parent_id = 2
)
SELECT
  comments.*
FROM
  comments JOIN t
ON
  comments.parent_id = t.id;
-- WITHãt.idã¾ã§ãä¸ã¤ã®å½ä»¤ãªã®ã§ãã»ãã³ã­ã³ã¯æå¾ã«ä»ããã

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

åå¸°çã§ã¯ãªã CTE ãä½¿ãã¨ãµãã¯ã¨ãªãä¸ã«æã£ã¦ãã¦ãããåãããããæ¸ããã¨ãã§ããã®ã§ãä½¿ãæ¹ã«æ£ãã¦ããã¾ãããã
###ãè¦ç¹ã¾ã¨ã
ã¯ã¨ãªåã§ä½¿ããä¸æçãªãã¼ãã«ãè¨­å®ã§ããCTEã«ã¤ãã¦è¦ã¦ããã¾ãã

- CTEã®æ¦è¦ï¼éå±¤ãæ·±ããªã£ã¦ãããã¼ã¿ãå¦çãããå ´åã **CTE** ã¨ããä»çµã¿ãä½¿ãã¾ããCTE ã¯ Common Table Expression ã®ç¥ã§ãã¯ã¨ãªåã§ä½¿ããä¸æçãªãã¼ãã«ã®ãã¨ã§ãã
- åå¸°çã§ã¯ãªãCTEï¼ãµãã¯ã¨ãªãåãããããæ¸ãæããããã®ãã®ã</details>


<details><summary>#30 åå¸°çãªCTEãçµã¿ç«ã¦ãã</summary>

åãã©ããããã¨ããã¨ `WITH RECURSIVE` ã¨ãã¦ããã¦ CTE ã®ååãæ¸ãã¦ããã¾ãã

ããã¦æåã«å®è¡ããå¦çã 2 åç®ä»¥éã«åèµ·çã«å®è¡ããå¦çãæ¸ãã¦ UNION ALL ã§ç¹ãã§ããã¾ãã

```sql
WITH RECURSIVE t AS (
  -- n = 1
  UNION ALL
  -- n >= 2
)
```

æåã«å®è¡ããå¦çã§ãã 2 çªç®ã®ã³ã¡ã³ãã«ä»ããæåã®éå±¤ã®ã³ã¡ã³ããåå¾ããã°ããã®ã§ããã®ã¯ã¨ãªããã®ã¾ã¾è²¼ãä»ãã¦ãããã° OK ã§ãããã

```sql
WITH RECURSIVE t AS (
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2
  UNION ALL
  -- n >= 2
)
```

æ¬¡ã«ãã®ä¸ã®éå±¤ãåå¾ããå¦çãªã®ã§ãã¨ãããããã¡ãããã®ã¾ã¾æ¸ãã¦ããã¾ãããã

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

ãããã©ãå¦çããããã§ãããæåã®éå±¤ãåå¾ããã¨ããã t ã«ãã¦ãã¡ãã®å¦çããã¦ããã¾ãã

ã¾ããã¡ãã®å¦çãçµãã£ãããã®çµæã t ã«ãã¦ãçµæãç¡ããªãã¾ã§ãã¡ãã®å¦çãåèµ·çã«å®è¡ãã¦ãããã®ã§ãã©ãã ãéå±¤ãæ·±ããªã£ã¦ããã³ã¡ã³ããåå¾ãã¦ãããã¯ãã§ãã

ãã¨ã¯ãã®çµæãã¾ã¨ãã¦è¡¨ç¤ºãããã®ã§ã`SELECT * FROM t;`ã¨æ¸ãã¾ãã

ã¾ããWITHãSELECT * FROM t;ã¾ã§ãä¸ã¤ã®å½ä»¤ã«ãªãã®ã§ãã»ãã³ã­ã³ã¯æå¾ã«ä¸ã¤ã«ãªãç¹ã«ãæ³¨æãã¦ãã ããã

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
--   åå¸°çãªCTE
--   åå¸°çã§ã¯ãªãCTE

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


<details><summary>#31 éå±¤ãæ·±ãã³ã¡ã³ããæ½åºããã</summary>

åå¸°çãªCTEãæ¸ããã®ã§å®è¡ãã¾ãã

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
--   åå¸°çãªCTE
--   åå¸°çã§ã¯ãªãCTE

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

ããã«æ·±ãéå±¤ã«ã³ã¡ã³ããä»ãã¦ã¿ã¦ããããåå¾ã§ãããè¦ã¦ããã¾ãããã

commentsãã¼ãã«ã«ããä¸ã¤ã¬ã³ã¼ããæ¿å¥ãã¦ããã¾ããânew commentâã¨ãã¦ãparent_idã¯æå¾ã®ã³ã¡ã³ãã«ã¤ããã®ã§6çªç®ã«ãªãã¾ãã

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
--   åå¸°çãªCTE
--   åå¸°çã§ã¯ãªãCTE

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

æ·±ãéå±¤ã®ã³ã¡ã³ãã¾ã§ä¸æ°ã«åå¾ã§ãã¦ãã¾ãã

åå¸°çãªå¦çã¯ããé£ããã®ã§ãååã§å¨ã¦ãçè§£ããå¿è¦ã¯ãªãã®ã§ãããããããéå±¤ãæ·±ããã¼ã¿ãå¦çãããã¨ãã«ã¯CTEãä¾¿å©ã¨ãããã¨ãç¥ã£ã¦ããã¨ããã§ãããã
### è³ªåï¼åå¸°çCTEã®åãããããããã¾ããã
**åç­ï¼**

[https://dotinstall.com/lessons/basic_mysql_advanced/55930](https://dotinstall.com/lessons/basic_mysql_advanced/55930)Â ã®ã¬ãã¹ã³ã§èª¬æããã£ãããã«ãåå¸°çãª CTE ã¯å¦çããåå¸°çãã«ç¹°ãè¿ãã¾ããï¼ãåå¸°ãã¯ãã­ã°ã©ãã³ã°ã§ããç»å ´ããæ¦å¿µã§ããåå¸°ã®æå³ãããããªãã¨ãã¯æ¤ç´¢ãã¦èª¿ã¹ã¦ã¿ã¾ããããï¼

ãã®ããä»¥ä¸ã®ãããªå¦çã«ãªãã¾ãã

- ã¾ãÂ `parent_id = 2`Â ã®ãã¼ã¿ãæ½åºãã âÂ `id=4, 5`Â ã®ãã¼ã¿ãæ½åºããã
- æ¬¡ã«Â `parent_id=4`,Â `parent_id=5`Â ã®ãã¼ã¿ãæ½åºãã âÂ `id=6`Â ã®ãã¼ã¿ãæ½åºããã
- æ¬¡ã«Â `parent_id=6`Â ã®ãã¼ã¿ãæ½åºãã âÂ `id=7, 8`Â ã®ãã¼ã¿ãæ½åºããã
- æ¬¡ã«Â `parent_id=7`,Â `parent_id=8`Â ã®ãã¼ã¿ãæ½åºãã âÂ `id=9`Â ã®ãã¼ã¿ãæ½åºããã
- æ¬¡ã«Â `parent_id=9`Â ã®ãã¼ã¿ãæ½åºãã â æ½åºããããã¼ã¿ã¯ãªãã®ã§å¦ççµäº

ãã®ããã«æ½åºãããçµæãå©ç¨ãã¦ã©ãã©ãæ°ç ç¹ãå¼ã«ãã¼ã¿ãæ½åºãã¾ãã

ãã¤ã³ãã¯Â [https://dotinstall.com/lessons/basic_mysql_advanced/55930](https://dotinstall.com/lessons/basic_mysql_advanced/55930)Â ã®1å28ç§ã®èª¬æã«ããã¾ãã

> ã¾ããã¡ãã®å¦çãçµãã£ãããã®çµæã t ã«ãã¦ãçµæãç¡ããªãã¾ã§ãã¡ãã®å¦çãåèµ·çã«å®è¡ãã¦ãããã®ã§ãã©ãã ãéå±¤ãæ·±ããªã£ã¦ããã³ã¡ã³ããåå¾ãã¦ãããã¯ãã§ãã
> 

åç»ã¨ãããã¦è¦ã¦ããã ãããã®ã§ãããããã®çµæã t ã«ãã¦ãããã¤ã³ãã§ããã¾ãããæåã«å®è¡ããå¦çãã¯1åç®ããå®è¡ããã¾ããã

ã¤ã¾ãã

- 1åç®ã®å®è¡ã¯ãæåã«å®è¡ããå¦çããå®è¡ãã
- 2åç®ã®å®è¡ã¯**1åç®**ã®çµæãåã«ã2åç®ä»¥éã«å®è¡ããå¦çããå®è¡ãã
- 3åç®ã®å®è¡ã¯**2åç®**ã®çµæãåã«ã2åç®ä»¥éã«å®è¡ããå¦çããå®è¡ãã
- 4åç®ã®å®è¡ã¯**3åç®**ã®çµæãåã«ã2åç®ä»¥éã«å®è¡ããå¦çããå®è¡ãã

ã¨ãªãã¾ãã

> æå¾ã®UNION ALLã§ tã¨ãã¦è¡¨ç¤ºãããå¨ã¦ã®ãã¼ã¿ãé£çµãã¦ããã®ã¯ãªãã§ããããï¼
> 

ããã¯ CTE ã®ä»æ§ã§ãã­ãåå¸°çã«å®è¡ããã ``SELECT` æã®çµæããã¹ã¦é£çµãã¾ãã

> æåã«ããã ããåçã®ãåç­ã§ã
> 
> 
> > æ¬¡ã« parent_id=9 ã®ãã¼ã¿ãæ½åºãã â æ½åºããããã¼ã¿ã¯ãªãã®ã§å¦ççµäº
> > 
> 
> ã¨ããã¾ããããã¡ãã¯parent_idã§ã¯ãªããidã®ééãã§ããããï¼
> 

ããããä»¥ä¸ã®ããã«å¦çãããã¦ããã®ã§Â `parent_id`Â ã§æ­£ããã§ãã

> ã¾ã parent_id = 2 ã®ãã¼ã¿ãæ½åºãã
> 

```sql
  -- n = 1
  SELECT * FROM comments WHERE parent_id = 2

```

âÂ `id=4`,Â `id=5`Â ã®ãã¼ã¿ãåå¾ããã¾ãããã¼ã¿ãåå¾ãããã®ã§ååº¦Â `SELECT`Â æãå®è¡ãã¾ãã

> æ¬¡ã« parent_id=4, parent_id=5 ã®ãã¼ã¿ãæ½åºãã
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

âÂ `id=6`Â ã®ãã¼ã¿ãåå¾ããã¾ãããã¼ã¿ãåå¾ãããã®ã§ååº¦Â `SELECT`Â æãå®è¡ãã¾ãã

> æ¬¡ã« parent_id=6 ã®ãã¼ã¿ãæ½åºãã
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

âÂ `id=7`,Â `id=8`Â ã®ãã¼ã¿ãåå¾ããã¾ãããã¼ã¿ãåå¾ãããã®ã§ååº¦Â `SELECT`Â æãå®è¡ãã¾ãã

> æ¬¡ã« parent_id=7, parent_id=8 ã®ãã¼ã¿ãæ½åºãã
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

âÂ `id=9`Â ã®ãã¼ã¿ãåå¾ããã¾ãããã¼ã¿ãåå¾ãããã®ã§ååº¦Â `SELECT`Â æãå®è¡ãã¾ãã

> æ¬¡ã« parent_id=9 ã®ãã¼ã¿ãæ½åºãã
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

â è©²å½ãããã¼ã¿ã¯ããã¾ããã§ãããï¼æ¬¡ã®`SELECT`æã®Â `t`Â ã«ãªããã¼ã¿ããªãããï¼å¦çãçµäºãã¾ãã

åã® SQL ã®å¾åé¨åã¯ã

```sql
  -- n >= 2
  SELECT
    comments.*
  FROM
    comments JOIN t
  ON
    comments.parent_id = t.id

```

ã§ããããã®æå¾ã®Â `comments.parent_id = t.id`Â ã¯ JOIN ã®æ¡ä»¶ã§ãã­ã

- `comments`Â ãã¼ãã«
- ååã®`SELECT`ã®çµæã¬ã³ã¼ã

ã®2ã¤ãçµåãã¦ããããã§ããããã®çµåã®æ¡ä»¶ãã`comments`Â ãã¼ãã«ã®Â `parent_id`Â ã®å¤ã¨ãååã®`SELECT`ã®çµæã¬ã³ã¼ãã®Â `id`Â ã®å¤ãä¸è´ããå ´åãã¨ããæ¡ä»¶ã«ãªãã¾ãã

---

> æ¯è¼æ¼ç®å­INã«ã¤ãã¦ã§ããããã®å ´åã§ãã¨ããcomments.parent_idãã¯ã7ãã¨ã8ãã®ããããã«ç­ããã
> 

ãã®éãã§ãã</details>


<details><summary>#32 TRIGGERãè¨­å®ãã¦ã¿ãã</summary>

ãããã¼ãã«ã§ä½ããã®å¤æ´ãèµ·ããæã«ããããããªã¬ã¼ã«ãã¦ä½ããã®å¦çããããã¨ãã§ããã**ããªã¬ã¼**ã¨ããä»çµã¿ã«ã¤ãã¦è¦ã¦ããã¾ãã

posts ãã¼ãã«ãæ´æ°ãããããä»ã®ãã¼ãã«ã«ã­ã°ãæ®ãããã«ãã¦ããã¾ãããã

postsãã¼ãã«ã«ç¶ãã¦ãlogs ãã¼ãã«ãä½ãã¾ãã

ãã¼ãã«åã¯ logs ã«ãã¦ããã¦ã created ã«ã©ã ãè¿½å ãã¾ãã

DATETIMEã¯DEFAULTã§æ¿å¥ãããæ¥æã«ãã¦ããã¾ãã

```sql
DROP TABLE IF EXISTS comments;
DROP TABLE IF EXISTS posts;

CREATE TABLE posts (
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  PRIMARY KEY (id)
);

CREATE TABLE logs ( -- ãã¼ãã«åã¯logs
  id INT NOT NULL AUTO_INCREMENT,
  message VARCHAR(140),
  created DATETIME DEFAULT NOW(), -- DATETIMEã¯DEFAULTã§æ¿å¥ãããæ¥æã«ãã
  PRIMARY KEY (id)
);

INSERT INTO posts (message) VALUES
  ('post-1'),
  ('post-2'),
  ('post-3');

SELECT * FROM posts;
```

ããªã¬ã¼ã®ä½ãæ¹ã§ãã `CREATE TRIGGER` ã¨ããæãä½¿ãã¾ãã

ãã®ãã¨ã«ãããªã¬ã¼ã®ååãå¥½ãã«ä»ããããã®ã§ãããä»åã¯ posts ãã¢ãããã¼ããããã¨ãã®ããªã¬ã¼ãªã®ã§ã`posts_update_trigger`ã¨ä»ãã¦ããã¾ãããã

æ¬¡ã«ã©ã®ã¿ã¤ãã³ã°ã§ã©ããªå¦çãããããæå®ããã®ã§ãã `AFTER UPDATE ON` ã¨ãã¦ããã¦ posts ãæ´æ°ããããã¨ã«ãã¨æå®ãã¾ãã

æ¬¡ã«æ´æ°ãããã²ã¨ã¤ã²ã¨ã¤ã®è¡ã«å¯¾ãã¦ logs ãã¼ãã«ã«ãã¼ã¿ãæ¿å¥ãã¦ããããã®ã§ãå°ãé·ãã®ã§ãããã®ããã«æ¸ãã¦ãããã° OK ã§ãã

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

å°ãããªã¬ã¼ã¯ã¢ãããã¼ãã ãã«ä½¿ããã¨ããããã§ã¯ãªãã¦ã INSERT ã DELETE ã®ã¿ã¤ãã³ã°ã§ãä½¿ãã¾ãããããã AFTER ã§ã¯ãªãã¦ BEFORE ã¨ããã°å¦çåã«ããªã¬ã¼ãå®è¡ãããã¨ãã§ãã¾ãã

ããªã¬ã¼ã¨ logs ããã§ã«ããã¨ã¨ã©ã¼ã«ãªã£ã¦ãã¾ãã®ã§ãä¸ã®æ¹ãåé¤ãã¦ããã¾ãããã

ãã logs ãã¼ãã«ãå­å¨ãã¦ãããåé¤ãããããªã¬ã¼ãå­å¨ãã¦ãããåé¤ã¨ãããã®ã§ã DROP TRIGGER æãä½¿ã£ã¦ããã¾ãã

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


<details><summary>#33 TRIGGERãä½¿ã£ã¦ã¿ãã</summary>

ããªã¬ã¼ãã¢ãããã¼ããã¦ããã¦ãlogsãã¼ãã«ã«ã­ã°ãæ®ããç¢ºèªãã¾ãã

ä»åã¯idã1ã®ã¬ã³ã¼ãã®messageãæ´æ°ãã¦è¦ã¾ãããã

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

post-1ã®ä¸­èº«ãæ´æ°ããã¦ãã¦ãlogsãã¼ãã«ã«ãUpdatedã¨ããã­ã°ãæ®ã£ã¦ãã¾ãã

ã­ã°ã«ãæ´æ°åãæ´æ°å¾ã®å¤ãå«ããæ¹æ³ãè¦ã¦ããã¾ãããã

ããªã¬ã¼ã§ã¯ **OLD** ã¨ **NEW** ã¨ããç¹æ®ãªã­ã¼ã¯ã¼ããä½¿ããã®ã§ **CONCAT é¢æ°**ãä½¿ã£ã¦ããã®ããã«å¤ãçµã¿ç«ã¦ã¦ããã¾ãã

ããããã¨ message ã®æ´æ°åãæ´æ°å¾ã®å¤ãæ¿å¥ãã¦ããã¾ãã

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
    (CONCAT(OLD.message, ' -> ', NEW.message)); -- CONCATé¢æ°ã¯æå­åãé£çµãã

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

è¨­å®ããã¦ããããªã¬ã¼ã®ä¸è¦§ãè¦ãã«ã¯ã **SHOW TRIGGERS**ãä½¿ãã¾ãã

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

ããªã¬ã¼æå ±ãè¡¨ç¤ºããã¾ãããè¦ã¥ããç¶æã§ãããããªæã¯æå¾ã®ã»ãã³ã­ã³ã**\G**ã«å¤ãã¦ãããã°OKã§ãã

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

ããªã¬ã¼ã®çµæã¨ãã¦ä¸è¡è¡¨ç¤ºããã¦ãã¦ãããããã®é ç®ãè¦ããããªã£ã¦ãã¾ãã

ããããç¢ºèªæ¹æ³ãç¥ã£ã¦ããã¨è¯ãã§ãããã
### è³ªåï¼(CONCAT(OLD.message, '->', NEW.message)) ã¨ããã ãã§ posts ãã¼ãã«ã® message ã®æ°æ§ãè¡¨ç¤ºãããã®ã¯ãªãã§ããï¼
åç­ï¼TRIGGER ãä½æããéã«ã©ã®ãã¼ãã«ã«å¯¾ããæä½ããæå®ã§ããããã§ãã

TRIGGER ãä½æããéã«ã©ã®ãã¼ãã«ã«å¯¾ããæä½ããæå®ããé¨åãããã¾ãã­ä»åã§ãã¨ãã®é¨å

```sql
CREATE TRIGGER
  posts_update_trigger
AFTER UPDATE ON
  posts

```

`posts`Â ãã¼ãã«ã«ä½ãæ´æ°ããã£ããã¨ã«ã¨ããæå®ãããã¾ãã

ããã®æå®ã«ããä»¥ä¸ã®Â `message`Â ã¯Â `posts`Â ãã¼ãã«ã®ãã®ã§ããã¨æå®ããã¦ãã¾ã

```sql
  INSERT INTO
    logs (message)
  VALUES
    -- ('Updated');
    (CONCAT(OLD.message, ' -> ', NEW.message));
```

### è¦ç¹ã¾ã¨ã
TRIGGERã®åä½ç¢ºèªãããå¾ã«ãæ´æ°åå¾ã®å¤ãåå¾ããæ¹æ³ã«ã¤ãã¦ãè¦ã¦ããã¾ãã

- OLD,NEWï¼æ´æ°åãæ´æ°å¾ã®å¤ãæ¿å¥ããã
- SHOW TRIGGERSï¼è¨­å®ããã¦ããããªã¬ã¼ã®ä¸è¦§ãè¦ããã¨ãã§ããã
- \Gï¼è¨­å®ãã¦ããTRIGGERã®ä¸è¦§ãè¦ãããè¡¨ç¤ºãããã</details>


<details><summary>#34 å¤é¨ãã¡ã¤ã«ãããã¼ã¿ãèª­ã¿è¾¼ãã</summary>

å¥ã®ãã¡ã¤ã«ãããã¼ã¿ãèª­ã¿è¾¼ãæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

posts ãã¼ãã«ããã£ã¦ã id ã message ã likes ã area ãè¨­å®ããã¦ãã¾ãã

ã¾ãä»åã¯ã data.csv ã¨ãããã¡ã¤ã«ãç¨æãã¦ããã¾ããã

ä¸è¡ç®ãè¦åºãã§ãããä»¥éã¯ message ã likes ã area ã®ãã¼ã¿ãå¥ã£ã¦ãããã¡ã¤ã«ã§ãã

ãªãããã¼ã¿ãã¡ã¤ã«ã«ã¯ id ã®é£çªãå«ã¾ãã¦ããªãç¹ã«æ³¨æãã¦ããã¾ãããã

ã§ã¯ããã¼ã¿ãèª­ã¿è¾¼ãã§ããããã®ã§ãããåããµã¼ãã«ããå¤é¨ãã¡ã¤ã«ãèª­ã¿è¾¼ãã«ã¯ã **LOAD DATA LOCAL INFILE** ã¨ãã¦ããã¦ããã¡ã¤ã«åãæå®ãã¦ããã¾ãã

ãããã©ã¡ãã®ãã¼ãã«ã«æµãè¾¼ããã **INTO TABLE** ã§æå®ãã¦ããã¾ãããã

ããããããã¡ã¤ã«å½¢å¼ã«å¿ãã¦ããã¤ãã®ãªãã·ã§ã³ãå¿è¦ã§ãã

ã¾ããé ç®ã®åºåããã«ã³ãã ã£ããã **FIELDS TERMINATED BY** ãä½¿ã£ã¦ããã¦ãã ããã

è¡ã®åºåããæå®ãã¦ãããå¿è¦ããã£ã¦ãä»å \n ãæ¹è¡ã¨ãã¦ä½¿ã£ã¦ããã®ã§ã`LINES TERMINATED BY '\nâ`ã¨æ¸ãã¦ããã¾ãã

ãã®ããã§ããã¼ã¿ãã©ã®ãã£ã¼ã«ãã«æ¿å¥ããããæå¾ã«æå®ãã¦ããã¾ãã

message ã likes ã area ã®é çªã ã£ãã®ã§ã`(message, likes, area);`ã¨æ¸ãã° OK ã§ãã

ããã§ãã¼ã¿ãæµãè¾¼ããã¯ããªã®ã§ã SELECT * FROM posts ã§ç¢ºèªãã¦ã¿ã¾ãããã

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
|   1 | message  |     0 | area      | -- è¦åºãè¡ãã¬ã³ã¼ãã¨ãã¦æ¿å¥ããã¦ãã¾ã£ã
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

idã«ã¤ãã¦ã¯èªåã§é£çªãæ¯ããã¾ãã

ä¸çªä¸ãè¦ãã¨å¤ãªãã¼ã¿ãç´ãè¾¼ãã§ãã¾ãã

ãã¼ã¿ã®ä¸è¡ç®ãã¹ã­ããããããªããããã«è¨è¿°ãå¿è¦ã§ããã¡ãã« IGNORE 1 LINES ã¨ãã¦ãããã° OK ã§ãã

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

å¤é¨ãã¡ã¤ã«ã®å½¢å¼ã«ãã£ã¦ã¯ãããã«ãªãã·ã§ã³ãå¿è¦ãªå ´åãããã¾ãããããããæä½ãã§ããããã«ãªã£ã¦ããã¾ãããã
### è³ªåï¼ã«ã³ãåºåãã§ãªããã¡ã¤ã«ã§ã FIELDS TERMINATED BY ãä½¿ããã®ã§ããï¼
åç­ï¼ã¯ããä½¿ãã¾ãã

ä¸è¨ã®CSVåãè¾¼ã¿ã®`FIELDS TERMINATED BY`ã«ã¤ãã¦ã§ãã­ã

```sql
LOAD DATA LOCAL INFILE 'data.csv' INTO TABLE posts
  FIELDS TERMINATED BY ','
  LINES TERMINATED BY '\n'
  IGNORE 1 LINES
  (message, likes, area);

```

ã¯ãã100%ãçè§£ã®éãããåãè¾¼ã¿ããCSVãã¡ã¤ã«ã®åºåãæ¹ãã«ã³ãã§ããã°ãã¨ããæå³ã§OKã§ãï¼

å®ã¯ãã®`LOAD DATAã»ã»`ã§ãããå¯¾è±¡ãCSVã¨ã¯éãããã«ã³ãä»¥å¤ã§åºåããããã¡ã¤ã«ãä¾ãã°

```
post-1 136 Tokyo
post-2 27 Osaka

```

ã®ããã«ã¹ãã¼ã¹åºåãã®ãã¡ã¤ã«ã§ãå¯¾å¿ãããã¨ãã§ãã¾ãã

ãã®ãããªå ´åã¯`FIELDS TERMINATED BY ' 'ï¼ã¹ãã¼ã¹ï¼`ã¨ããã°OKã§ãã­ã

ã¨ãããã¨ã§ãçè§£ã®éã`FIELDS TERMINATED BY`ã¯æ§ããªå½¢å¼ã®ãã¡ã¤ã«ã«å¯¾å¿ããããã«ãåºåãæ¹ãæå®ãã¦ããã¨ããããã§ãã
### è³ªåï¼\nãæ¹è¡ã¨ãã¦ä½¿ã£ã¦ããã¨ããã®ã¯ï¼
åç­ï¼ç°å¢ã«ãã£ã¦ã¯æ¹è¡ãå¥ã®è¡¨ç¾ã«ãªããã¨ãããã¾ãã

è¡ãåºåãããã®æ¹è¡ã³ã¼ãã¯ OS ã«ãã£ã¦ç°ãªã£ã¦ããã`\n`Â ä»¥å¤ã«ä¸»ã«Â `\r`Â ã¨Â `\r\n`Â ãããã¾ããLinux OS ã§ã¯éå¸¸Â `\n`Â ãä½¿ãã¾ãã®ã§ãä»åã¯ãã®æ¹è¡ã³ã¼ããä½¿ç¨ãã¦ããã¾ãã

ãèå³ãããã¾ããããæ¹è¡ã³ã¼ããã§ Google æ¤ç´¢ãã¦ã¿ã¦ãã ããã

**åç­2ï¼**

```
LINES TERMINATED BY '\n'
```

ã¯è¡ã®çµããã®æå­ãæå®ãã¦ãã¾ããï¼ããã¯ä½ãæå®ããªãã¨

```
'\n'
```

ãæå®ããããã®ã¨ãã¦æ±ãããã®ã§ï¼å¥ãã¦ãå¥ããªãã¦ãå¤åããªããã¨æãã¾ãï¼

ä¾ãã°Windowsã§ä½æããããã¡ã¤ã«ã¯è¡æ«ã

```
\r\n
```

ã«ãªã£ã¦ãããã¨ãããã®ã§ï¼ãããã£ãå ´åã«

```
LINES TERMINATED BY '\r\n'
```

ã¨æå®ããå ´åãããã¾ãã
### è¦ç¹ã¾ã¨ã
å¥ã®ãã¡ã¤ã«ãããã¼ã¿ãèª­ã¿è¾¼ãæ¹æ³ã«ã¤ãã¦è¦ã¦ããã¾ãã

- LOAD DATA LOCAL INFILEï¼åããµã¼ãã«ããå¤é¨ãã¡ã¤ã«ãèª­ã¿è¾¼ãã
- FIELDS TERMINATEDï¼èª­ã¿è¾¼ããã¼ã¿ã®é ç®ã®åºåããã«ã³ã(,)ã®å ´åã«ä½¿ãã
- LINES TERMINATEDï¼è¡ã®åºåããæå®ããã
- IGNORE n LIKESï¼ãã¼ã¿ã®nè¡ç®ãã¹ã­ããããã</details>


<details><summary>#35 ã¤ã³ããã¯ã¹ãä½¿ã£ã¦ã¿ãã</summary>

posts

| id | messagae | likes | area |
| --- | --- | --- | --- |
| 1 | post-1 | 136 | Tokyo |
| 2 | post-2 | 27 | Osaka |
| 3 | post-3 | 125 | Osaka |
| â¦ |  |  |  |
| 34 | post-34 | 33 | Kyoto |
| 35 | post-35 | 28 | Tokyo |

ä»ãä¾ãã°ãããã£ã posts ãã¼ãã«ããã£ãã¨ãã¦ããã®ãã¼ãã«ãã area ã Kyoto ã®ã¬ã³ã¼ããæ½åºãããã£ãã¨ãã¾ãã

ãã®å ´åãMySQLã§ã¯åé ­ããé çªã«ä¸ã¤ãã¤è¦ã¦ãã£ã¦ãåè´ãããã®ãæ¢ãã¦ããã¾ãã

åªããã®æ¹æ³ã ã¨å¨ã¦ã®ã¬ã³ã¼ããæåããæå¾ã¾ã§è¦ã¦ãããã¨ã«ãªãã®ã§ãã¬ã³ã¼ãæ°ãè¨å¤§ã«ãªã£ã¦ããã¨ãããæéããããã¨ããåé¡ãããã¾ãã

ããã§ããã¼ã¿ãã¼ã¹ã§ã¯ããæ¤ç´¢ãããã«ã©ã ã«å¯¾ãã¦**ã¤ã³ããã¯ã¹**ã¨ãããã®ãä»ãããã¨ãã§ãã¾ãã

ã¤ã³ããã¯ã¹ã¯ãåãã¼ã¿ã¨ã¯å¥ã«ä¿æããã**ç´¢å¼ã®ãããªãã¼ã¿**ã§ãã©ã®ãã¼ã¿ãã©ãã«ããã®ããããè¤éãªãã¼ã¿æ§é ã§ä¿æããã¦ãã¾ãã

ãã®ãã¼ã¿æ§é ã¯ã**ãããããå¤ãæ´åãããããã§ç¯å²ãã¨ã«åºåããã¦ãããããä¸ããé çªã«ã²ã¨ã¤ãã¤è¦ã¦ãããããããã£ã¨å¹ççã«æ¤ç´¢ãããã¨ãã§ããã¨ããä»çµã¿ã«ãªã£ã¦**ãã¾ãã

ã¤ã³ããã¯ã¹ãä½ãã¨æ¤ç´¢ã¯æ©ããªãã®ã§ããã**éã«ãã¼ã¿ã®æ¿å¥ãæ´æ°ãããã¦åé¤ãããã¨ãã¤ã³ããã¯ã¹ã®ãã¼ã¿ããã¡ãã¡åæ§ç¯ããªãã¦ã¯ãªãã¾ãã**ã

ã¾ãã**ã¤ã³ããã¯ã¹ã®åããã¼ã¿ãã¼ã¹ã«å¿è¦ãªå®¹éãå¢ãã¦ãã¾ãã¨ãããã¡ãªãã**ãããã¾ãã

ãããã£ã¦ã**ã¤ã³ããã¯ã¹ã¯ãã¼ã¿ãã¼ã¹ã®éç¨ç¶æ³ãè¦ãªãããå¿è¦ãªã«ã©ã ã«å¯¾ãã¦ä»ãå¤ããã§ããããã«ãªã£ã¦ããã¨ãã**ã§ãããã

ä¸»ã­ã¼ã«é¢ãã¦ã¯ **PRIMARY** ã¨ããã¤ã³ããã¯ã¹ãå®ã¯èªåçã«çæããã¾ãã

ãããã£ã¦ããã®ä¾ã ã¨ id ãä½¿ã£ãæ¤ç´¢ã¯ãã§ã«é«éã«åä½ããã¨ãããã¨ãç¥ã£ã¦ããã¨ããã§ãããã
### è¦ç¹ã¾ã¨ã
æ¤ç´¢ãé«éåãããã¨ãã§ããã¤ã³ããã¯ã¹ã«ã¤ãã¦è¦ã¦ããã¾ãã

- ã¤ã³ããã¯ã¹ã®æ¦è¦
    - åãã¼ã¿ã¨ã¯å¥ã«ä¿æããã**ç´¢å¼ã®ãããªãã¼ã¿**ã§ãã©ã®ãã¼ã¿ãã©ãã«ããã®ããããè¤éãªãã¼ã¿æ§é ã§ä¿æããã¦ããã
    - ã¤ã³ããã¯ã¹ã¯ãæå®ããåã«å¯¾ãã¦ä½ãããã
    - ã¤ã³ããã¯ã¹ãå­å¨ããåã«å¯¾ãã¦æ¤ç´¢ãè¡ãããå ´åãDBMSã¯èªåçã«ã¤ã³ããã¯ã¹ä½¿ç¨ãè©¦ã¿ããããé«éã«ãªããã¨ãå¤ãã
- ã¤ã³ããã¯ã¹è¨­å®æã®æ³¨æç¹
    - éã«ãã¼ã¿ã®æ¿å¥ãæ´æ°ãããã¦åé¤ãããã¨ãã¤ã³ããã¯ã¹ã®ãã¼ã¿ããã¡ãã¡åæ§ç¯ããªãã¦ã¯ãªããªãã
    - ç´¢å¼æå ±ãä¿æããããã«ãã¤ã³ããã¯ã¹ã®åãã¼ã¿ãã¼ã¹ã«å¿è¦ãªå®¹éãå¢ãã¦ãã¾ãã</details>


<details><summary>#36 EXPLAINã§ã¯ã¨ãªãåæããã</summary>

åãã¯ã¤ã³ããã¯ã¹ã§ä»è¨­å®ããã¦ãããã®ãç¢ºèªãã¾ãã

ååèª¬æããéããä¸»ã­ã¼ã«ã¯èªåçã«ã¤ã³ããã¯ã¹ãä½ããã¦ããã¯ããªã®ã§ããã®æå ±ãåºã¦ããã¯ãã§ãã

`SHOW INDEX FROM ãã¼ãã«å`ã¨æ¸ãã¾ãã

è¦ã¦ã¿ãã¨ãæ¨ªã«é·ãè¦ã¥ããã®ã§`\G`ãä½¿ãã¾ãã

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

-- \Gãä½¿ç¨
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

id ã®ãã£ã¼ã«ãã« PRIMARY ã¨ããã¤ã³ããã¯ã¹ãè¨­å®ããã¦ãã¾ãã

ããããå®éã« id ãä½¿ã£ãæ¤ç´¢ã§ããã®ã¤ã³ããã¯ã¹ãä½¿ããã¦ãããã©ãããç¢ºèªããæ¹æ³ãè¦ã¦ããã¾ãã

ã§ã¯ id ãä½¿ã£ãæ¤ç´¢ã¨ãã¦ã `SELECT * FROM posts WHERE id = 30` ãä½¿ã£ã¦ã¿ã¾ãããã

ãã®ããã«ã¯ã¨ãªãæ¸ãã¦ããã¦ããã®ã¯ã¨ãªã§ã©ã®ã¤ã³ããã¯ã¹ãä½¿ããã¦ãããã¯ãåé ­ã« **EXPLAIN** ãä»ãã¦ãããã°èª¿ã¹ããã¨ãã§ãã¾ããçµæã¯å°ãé·ããªãã®ã§ã \G ã«ãã¦ããã¾ãããã

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

è¦ã¦ãããã¨ããããããã¾ãããè¦ãã¹ãé ç®ã¯ **key** ã¨ **rows** ã§ã key ã¯ã¯ã¨ãªã§å®éã«ä½¿ãããã¤ã³ããã¯ã¹ã®ååã rows ã¯æ¤ç´¢å¯¾è±¡ã¨ãªãã¬ã³ã¼ãæ°ã®è¦ç©ããã§ãã

ã¾ãã¯ããã®ããã«ã¤ã³ããã¯ã¹ã«ã¤ãã¦ç¢ºèªã§ããããã«ãªã£ã¦ããã¾ãããã
### è¦ç¹ã¾ã¨ã
è¨­å®ããã¦ããã¤ã³ããã¯ã¹ãç¢ºèªããå¾ã«EXPLAINã®ä½¿ãæ¹ã«ã¤ãã¦è¦ã¦ããã¾ãã

- SHOW INDEXï¼INDEXã®ç¢ºèªãè¡ããSHOW INDEX FROM ãã¼ãã«å
- EXPLAINï¼ã¯ã¨ãªã®å®è¡æ¹æ³ãèª¿ã¹ã¦ãè¡¨ç¤ºããã</details>


<details><summary>#37 ã¤ã³ããã¯ã¹ãè¨­å®ãã¦ã¿ãã</summary>

ã¤ã³ããã¯ã¹ãè¨­å®ããã¦ããªãã¯ã¨ãªã§ãç¢ºèªãã¾ãã

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

-- SHOW INDEX FROM posts\G
-- EXPLAIN SELECT * FROM posts WHERE id = 30\G

EXPLAIN SELECT * FROM posts WHERE area = 'Kyoto'\G

*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: posts
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 200
        Extra: Using where
```

key ãè¦ãã¨ NULL ã«ãªã£ã¦ãã¦ãã¤ã³ããã¯ã¹ãä½¿ããã¦ããªããã¨ããããã¾ãã

ã¾ãã rows ã¯ 200 ä»¶ã«ãªã£ã¦ããã®ã§ãæåããæå¾ã¾ã§è¦ã¦ããæ¤ç´¢çµæãè¿ãã¦ããã®ã§ãä½éãªã¯ã¨ãªã§ãããã¨ããããã¾ãã

ä»åãã¨ãªã¢ã§é »ç¹ã«æ¤ç´¢ããããã«ãªã£ãã¨ãã¦ã¤ã³ããã¯ã¹ãè¨­å®ãã¦ããã¾ãããã

CREATE TABLE ã§è¨­å®ãããã¨ãã§ããã®ã§ããããã¨ããä»ãå¤ããããã¨ãå¤ãã®ã§ **ALTER TABLE** ãä½¿ã£ã¦ããã¾ãããã

ã¤ã³ããã¯ã¹ãè¿½å ããã«ã¯ **ADD INDEX** ã¨ãã¦ããã¦ãã¤ã³ããã¯ã¹ã®ååãä»ãã¦ããã¾ãã

ãã®ããã§ãã©ã®ã«ã©ã ã«å¯¾ãã¦ã®ã¤ã³ããã¯ã¹ããæå®ãã¦ãããã° OK ã§ãã

ããã¦ã**SHOW INDEX**ã§ç¢ºèªãã¾ãã

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

-- SHOW INDEX FROM posts\G
-- EXPLAIN SELECT * FROM posts WHERE id = 30\G

ALTER TABLE posts ADD INDEX index_area(area);
SHOW INDEX FROM posts\G
EXPLAIN SELECT * FROM posts WHERE area = 'Kyoto'\G

*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: posts
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 200
        Extra: Using where
~ $ mysql -h db -t -u dbuser -pdbpass myapp < main.sql
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
*************************** 2. row ***************************
        Table: posts
   Non_unique: 1
     Key_name: index_area
 Seq_in_index: 1
  Column_name: area
    Collation: A
  Cardinality: 28
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: posts
         type: ref
possible_keys: index_area
          key: index_area
      key_len: 83
          ref: const
         rows: 9
        Extra: Using index condition
```

ã¤ã³ããã¯ã¹ã®ä¸è¦§ã§ã¯ PRIMARY ã¨ area ã«ã©ã ã«ã¤ã³ããã¯ã¹ãè¨­å®ããã¦ãã¦ã EXPLAIN ã®çµæãè¦ãã¨ä»åº¦ã¯ key ã§ã¡ããã¨ index_area ãä½¿ããã¦ããã®ããããã¾ããã rows ãå¤§å¹ã«æ¸ã£ã¦ããã®ã§ãã®ã¯ã¨ãªãé«éã«åä½ããããã«ãªã£ãã®ããããã¾ãã

ã¨ãªã¢ã§ã®æ¤ç´¢ãéããªã¨æããããã«ãªã£ããããã®ããã«ã¤ã³ããã¯ã¹ãè¨­å®ãã¦ãããã¨ããã§ãããã

ã¤ã³ããã¯ã¹ãå¤ãæ¹æ³ã«ã¤ãã¦ãè¦ã¦ããã¾ãããã

ãã®å ´åã¯ã **ALTER TABLE** ã® **DROP INDEX** ãä½¿ã£ã¦ããã¾ãã

ã¡ããã¨å¤ãããã©ãã **SHOW INDEX** ã§ç¢ºèªãã¦ã¿ã¾ãã

ã¤ã³ããã¯ã¹ãè¿½å ãã¦ããã®ã§ããããã®ãã¨ã«ã¤ã³ããã¯ã¹ãå¤ãã¦ããã®ã§ SHOW INDEX ã§ã¯ PRIMARY KEY ã ããè¡¨ç¤ºãããã¯ãã§ãã

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

-- SHOW INDEX FROM posts\G
-- EXPLAIN SELECT * FROM posts WHERE id = 30\G

ALTER TABLE posts ADD INDEX index_area(area);
-- SHOW INDEX FROM posts\G
-- EXPLAIN SELECT * FROM posts WHERE area = 'Kyoto'\G

ALTER TABLE posts DROP INDEX index_area;
SHOW INDEX FROM posts\G

*************************** 1. row ***************************
        Table: posts
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: id
    Collation: A
  Cardinality: 199
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment:
```

ã¤ã³ããã¯ã¹ã«ã¤ãã¦ã¯ãã£ã¨å¥¥ãæ·±ãã®ã§ããã**ãã¼ã¿ãå¢ãã¦ãã¦ããã©ã¼ãã³ã¹ãè½ã¡ã¦ããã EXPLAIN ãä½¿ã£ã¦èª¿æ»ãã¤ã¤ãé©åã«ã¤ã³ããã¯ã¹ãè¨­å®ãã¦ããã**ã¨ããã§ãããã
### è¦ç¹ã¾ã¨ã
ã¤ã³ããã¯ã¹ãè¨­å®ããæ¹æ³ã¨ãå¤ãæ¹æ³ã«ã¤ãã¦ã¿ã¦ããã¾ãã

- ALTER TABLE ... ADD INDEX â¦ï¼ã¤ã³ããã¯ã¹ãè¿½å ã
- ALTER TABLE ... DROP INDEX â¦ï¼ã¤ã³ããã¯ã¹ãå¤ãã</details>						      
