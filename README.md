```
1. Ответ:
docker run --name mysql -v mysql-db:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123 -d mysql:8
docker exec -it mysql bash
apt update && apt install -y curl
cd /tmp
curl -LO https://raw.githubusercontent.com/netology-code/virt-homeworks/master/06-db-03-mysql/test_data/test_dump.sql
mysql -p
mysql> create database test_db;
mysql> use test_db;
mysql> source test_dump.sql
\s
show tables;
select * from orders where price > 300;
```
``` 
2.Ответ:
create user test identified with mysql_native_password by 'test-pass' with max_connections_per_hour 100 password expire interval 180 day failed_login_attempts 3 attribute '{"fname": "James", "lname": "Pretty"}';
grant select on test_db.* to test;
select * from INFORMATION_SCHEMA.USER_ATTRIBUTES where user='test';
```
```
3.Ответ:
set profiling=1;
select * from orders;
show profiles;
show table status where name = 'test_db';
alter table orders engine = MyISAM;
select * from orders;
show profiles;

InnoDB: 0.00025900
MyISAM: 0.00022600
```
```
4. Ответ:
Смотрю посредством docker stats ОЗУ для контейнера MySQL на машине 15.643GB. Соответственно 30% будет 4805MB.

vim /etc/mysql/conf.d/10-custom.cnf

[mysql]
innodb_flush_method = O_DSYNC
innodb_flush_log_at_trx_commit = 2
query_cache_size = 0
innodb-file-per-table = ON
innodb_log_buffer_size = 1M
innodb_buffer_pool_size = 4805M
innodb_log_file_size = 100M

 
```
