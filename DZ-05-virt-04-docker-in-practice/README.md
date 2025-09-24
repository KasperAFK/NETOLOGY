# ДЗ по теме "Практическое применение Docker"
## Задание 0
![](./image/DZ555_1.png)

## Задание 1-3
kasper@Ubuntu-555:~/GDZ/shvirtd-example-python$ docker exec -ti shvirtd-example-python-db-1 mysql -uroot -pYtReWq4321
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.4.6 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| virtd              |
+--------------------+
5 rows in set (0.02 sec)

mysql> use virtd;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-----------------+
| Tables_in_virtd |
+-----------------+
| requests        |
+-----------------+
1 row in set (0.01 sec)

mysql> SELECT * from requests LIMIT 10;
+----+---------------------+------------+
| id | request_date        | request_ip |
+----+---------------------+------------+
|  1 | 2025-09-24 19:23:36 | 127.0.0.1  |
|  2 | 2025-09-24 19:23:38 | 127.0.0.1  |
|  3 | 2025-09-24 19:23:40 | 127.0.0.1  |
+----+---------------------+------------+
3 rows in set (0.00 sec)
