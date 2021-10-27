```
1. Ответ:
docker pull postgres:1234
docker volume create vol1
docker volume create vol2
docker run --rm --name pro21ect -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -e -ti -p 5432:5432 -v vol1:/var/lib/postgresql/data -v vol2:/var/lib/postgresql/ postgres:1234

```
``` 
2.Ответ:
test_db=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
(4 rows)

test_db=# \du
                                       List of roles
    Role name     |                         Attributes                         | Member of 
------------------+------------------------------------------------------------+-----------
 postgres         | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 test-admin-user  | Superuser, No inheritance                                  | {}
 test-simple-user | No inheritance                                             | {}

test_db=# \dt
          List of relations
 Schema |  Name   | Type  |  Owner   
--------+---------+-------+----------
 public | clients | table | postgres
 public | orders  | table | postgres
(2 rows)

test_db=# select * from information_schema.table_privileges where grantee in ('test-admin-user','test-simple-user');
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy 
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 postgres | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES
 postgres | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES
 postgres | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO
(8 rows)

test_db=# 

```
```
3.Ответ:
test_db=# insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5

Проверяю:
test_db=# select count (*) from orders;
 count 
-------
     5
(1 row)

test_db=# insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5

Проверяю:
test_db=# select count (*) from clients;
 count 
-------
     5
(1 row)

```
```
4. Ответ:
test_db=# update  clients set booking = 3 where id = 1;
UPDATE 1
test_db=# update  clients set booking = 4 where id = 2;
UPDATE 1
test_db=# update  clients set booking = 5 where id = 3;
UPDATE 1

test_db=# select * from clients;
 id |       lastname       | country | booking 
----+----------------------+---------+---------
  4 | Ронни Джеймс Дио     | Russia  |        
  5 | Ritchie Blackmore    | Russia  |        
  1 | Иванов Иван Иванович | USA     |       3
  2 | Петров Петр Петрович | Canada  |       4
  3 | Иоганн Себастьян Бах | Japan   |       5
(5 rows)

test_db=# select * from clients where booking is not NULL;
 id |       lastname       | country | booking 
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     |       3
  2 | Петров Петр Петрович | Canada  |       4
  3 | Иоганн Себастьян Бах | Japan   |       5
(3 rows)
 
```
```
5. Приведу один из самый информативных, на мой взгляд запросов с использованием директивы EXPLAIN
test_db=# select * from clients where booking is not NULL;
 id |       lastname       | country | booking 
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     |       3
  2 | Петров Петр Петрович | Canada  |       4
  3 | Иоганн Себастьян Бах | Japan   |       5
(3 rows)

test_db=# explain select * from clients where booking is not NULL;
                        QUERY PLAN                         
-----------------------------------------------------------
 Seq Scan on clients  (cost=0.00..18.10 rows=806 width=72)
   Filter: (booking IS NOT NULL)
(2 rows)

Выше можно увидеть число обработанных записей, стоимость и время на операцию

```
```
6. Ответ:
Для начала сделаю дамп на vol2:
root@57b7855b4e87:/# pg_dump -U postgres test_db > /var/lib/postgresql/test_db_dump.sql

root@57b7855b4e87:/# ls -la /var/lib/postgresql/
total 16
drwxr-xr-x  3 postgres postgres 4096 Sep 23 19:03 .
drwxr-xr-x  1 root     root     4096 Sep  3 12:56 ..
drwx------ 19 postgres postgres 4096 Sep 23 16:59 data
-rw-r--r--  1 root     root     2541 Sep 23 19:03 test_db_dump.sql

Затем поднимаю ещё один контейнер для восстановления с тома на котором дамп.
trohi@Ptrohi_1 ~ % docker run --rm --name pro21ect_1 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol2:/var/lib/postgresql/ postgres:1234

Восстанавливаю:
trohi@Ptrohi_1 ~ % docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                       NAMES
1bcde4c5ed12   postgres:1234   "docker-entrypoint.s…"   11 seconds ago   Up 11 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   pro21ect_1
trohi@Ptrohi_1 ~ % docker exec -ti 1bcde4c5ed12 bash
root@1bcde4c5ed12:/# ls -la /var/lib/postgresql/
total 16
drwxr-xr-x  3 postgres postgres 4096 Sep 23 19:03 .
drwxr-xr-x  1 root     root     4096 Sep  3 12:56 ..
drwx------ 19 postgres postgres 4096 Sep 23 19:06 data
-rw-r--r--  1 root     root     2541 Sep 23 19:03 test_db_dump.sql
root@1bcde4c5ed12:/# psql -U postgres -d test_db < /var/lib/postgresql/test_db_dump.sql 
psql: error: FATAL:  database "test_db" does not exist
root@1bcde4c5ed12:/# psql -U postgres
psql (12.8 (Debian 12.8-1.pgdg100+1))
Type "help" for help.

postgres-# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)
postgres=# create database test_db
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
(4 rows)

postgres=# exit
root@1bcde4c5ed12:/# psql -U postgres -d test_db < /var/lib/postgresql/test_db_dump.sql 
SET
SET
SET
SET
SET
 set_config 
------------
 
(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE TABLE
ALTER TABLE
COPY 5
COPY 5
ALTER TABLE
ALTER TABLE
ALTER TABLE
GRANT
GRANT


 
 
```