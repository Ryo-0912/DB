## DBへJSONデータを入れる時に注意すること

=> **JSON形 {”key” : value} keyは必ず文字列(””)で囲む(’’で囲むとエラー)**

[参照URL](https://products.sint.co.jp/topsic/blog/json)

カンマで区切ると、キーと値の組み合わせを複数記述できる。

```
{“key1” : “value1”, “key2” : “value2”}
```
行数が多くなってくると読みにくくなってきます。その場合は改行とインデントを使う。

```
{
    “key1” : “value1”,
    “key2” : “value2”,
    “key3” : “value3”
}
```

```
mysql> use evsdb;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show columns from events;
+-----------------------+---------------+------+-----+-------------------+-------------------+
| Field                 | Type          | Null | Key | Default           | Extra             |
+-----------------------+---------------+------+-----+-------------------+-------------------+
| event_id              | int           | NO   | PRI | NULL              | auto_increment    |
| school_building_cds   | json          | YES  |     | NULL              |                   |
| grade_cds             | json          | YES  |     | NULL              |                   |
| grade_category_cds    | char(2)       | NO   |     | NULL              |                   |
| title                 | varchar(191)  | NO   |     | NULL              |                   |
| body                  | text          | NO   |     | NULL              |                   |
| image_url             | varchar(2048) | YES  |     | NULL              |                   |
| date_or_datetime      | tinyint       | NO   |     | NULL              |                   |
| start_date            | date          | YES  |     | NULL              |                   |
| end_date              | date          | YES  |     | NULL              |                   |
| start_datetime        | datetime      | YES  |     | NULL              |                   |
| end_datetime          | datetime      | YES  |     | NULL              |                   |
| application_deadline  | datetime      | YES  |     | NULL              |                   |
| released_at           | datetime      | NO   |     | NULL              |                   |
| is_hidden             | tinyint       | NO   |     | 0                 |                   |
| created_at            | datetime      | NO   |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED |
| updated_at            | datetime      | NO   |     | CURRENT_TIMESTAMP | DEFAULT_GENERATED |
| application_available | tinyint       | NO   |     | NULL              |                   |
+-----------------------+---------------+------+-----+-------------------+-------------------+
18 rows in set (0.00 sec)

mysql> update events school_building_cds = null;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '= null' at line 1
mysql> update events set school_building_cds = null;
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> update events set school_building_cds = "{1002,1003}";
ERROR 3140 (22032): Invalid JSON text: "Missing a name for object member." at position 1 in value for column 'events.school_building_cds'.
mysql> update events set school_building_cds = "{0:1002,1:1003}";
ERROR 3140 (22032): Invalid JSON text: "Missing a name for object member." at position 1 in value for column 'events.school_building_cds'.
mysql> update events set school_building_cds = json_object(0, 1002);
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> select * from events;
+----------+---------------------+-----------+--------------------+--------+-----------------+-----------+------------------+------------+------------+----------------+--------------+----------------------+---------------------+-----------+---------------------+---------------------+-----------------------+
| event_id | school_building_cds | grade_cds | grade_category_cds | title  | body            | image_url | date_or_datetime | start_date | end_date   | start_datetime | end_datetime | application_deadline | released_at         | is_hidden | created_at          | updated_at          | application_available |
+----------+---------------------+-----------+--------------------+--------+-----------------+-----------+------------------+------------+------------+----------------+--------------+----------------------+---------------------+-----------+---------------------+---------------------+-----------------------+
|        1 | {"0": 1002}         | 15        | 15                 | 試験   | 算数の試験      | NULL      |                1 | 2022-10-19 | 2022-10-22 | NULL           | NULL         | 2022-10-10 00:00:00  | 2022-10-17 17:00:00 |         0 | 2022-10-19 09:00:00 | 2022-10-19 19:00:00 |                     1 |
|        2 | {"0": 1002}         | 16        | 16                 | 試験   | 算数の試験      | NULL      |                1 | 2022-10-19 | 2022-10-22 | NULL           | NULL         | 2022-10-10 00:00:00  | 2022-10-17 17:00:00 |         1 | 2022-10-19 09:00:00 | 2022-10-19 19:00:00 |                     1 |
|        3 | {"0": 1002}         | 12        | 12                 | 試験   | 算数の試験      | NULL      |                1 | 2022-10-19 | 2022-10-22 | NULL           | NULL         | 2022-10-10 00:00:00  | 2022-10-17 17:00:00 |         1 | 2022-10-19 09:00:00 | 2022-10-19 19:00:00 |                     1 |
+----------+---------------------+-----------+--------------------+--------+-----------------+-----------+------------------+------------+------------+----------------+--------------+----------------------+---------------------+-----------+---------------------+---------------------+-----------------------+
3 rows in set (0.00 sec)

```
