
```
1. Ответ:
Запускаю:
docker run  --name postgres -d -e POSTGRES_PASSWORD=password -v postgres-data:/var/lib/postgresql/data postgres:13
docker exec -it postgres bash
su - postgres
psql
   
\l[+]   [PATTERN]     Просмотр баз

\c[onnect] {[DBNAME|- USER|- HOST|- PORT|-] | conninfo}  -  Подключение к БД

\dt[S+] [PATTERN]      Просмотр списка таблиц

\d[S+]  NAME           вывод описания сожержимого таблиц

\q                     выход из psql

```
``` 
2.Ответ:

CREATE DATABASE test
postgres-# ;
CREATE DATABASE

root@1e5486c6d7a1:/# psql -U postgres -W test < /home/pro21/postgres/test_dump.sql

postgres=# \c test
You are now connected to database "test" as user "postgres".
test=# 


test=# \dt
         List of relations
 Schema |  Name  | Type  |  Owner   
--------+--------+-------+----------
 public | orders | table | postgres
(1 row)

test=# ANALYZE orders;
ANALYZE

test=# SELECT avg_width FROM pg_stats WHERE tablename = 'orders' ORDER BY avg_width DESC limit 1;
 avg_width 
-----------
        16
(1 row)

```
```
3.Ответ:

test_database=# CREATE TABLE orders_range (
id INT,
title varchar (80),
price INT ) PARTITION BY RANGE(price);
CREATE TABLE

test_database=# CREATE TABLE orders_1 PARTITION OF orders_range FOR VALUES FROM  (499) TO (999999999);
CREATE TABLE
test_database=# CREATE TABLE orders_2 PARTITION OF orders_range FOR VALUES FROM  (0) TO (499);
CREATE TABLE

test_database=# INSERT INTO orders_range SELECT * FROM orders;
INSERT 0 8

Итог:
test_database=# \dt
                  List of relations
 Schema |     Name     |       Type        |  Owner   
--------+--------------+-------------------+----------
 public | orders       | table             | postgres
 public | orders_1     | table             | postgres
 public | orders_2     | table             | postgres
 public | orders_range | partitioned table | postgres
(4 rows)

Исключить "ручное" разбиение при проектировании таблицы orders можно.
```
```
4. Ответ:
Создаю бэкап:
pg_dump test_database > /home/pro21/posgres/test_database_backup.sql

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца title для таблиц test_database?
Использую UNIQUE к нужному столбцу
CREATE TABLE public.orders (
    id integer NOT NULL,
    title character varying(80) UNIQUE NOT NULL,
    price integer DEFAULT 0
)
PARTITION BY RANGE (price);
 
```
