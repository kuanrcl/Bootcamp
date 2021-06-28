# Bootcamp
## Day 1a

Server B: 

* mysql-gpl (3306)
* mysql-commercial (3307, 3308)
* Master

Server A:

* Workbench
* Monitor
* mysql-commercial (3307)
* Slave

## Server B
```
cat /etc/redhat-release
sudo rpm -qa | grep mysql
sudo rpm -qa | grep maria
sudo yum install mysql
ls -l /etc/yum.repos.d/
sudo yum install /workshop/linux/mysql-community/mysql80-community-release-el7-3.noarch.rpm
ls -l /etc/yum.repos.d/
yum repolist all
sudo yum install mysql
sudo rpm -qa|egrep "mysql|maria"
sudo rpm -ivh /workshop/linux/mysql-shell-commercial-8.0.20-1.1.el7.x86_64.rpm
sudo yum install mysql-server
sudo systemctl status mysqld
sudo systemctl start mysqld
sudo systemctl status mysqld
ps -eaf | grep -i mysql
more /etc/my.cnf
grep error /etc/my.cnf
sudo grep 'temporary' /var/log/mysqld.log
mysql -uroot -p -h 127.0.0.1
show databases;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Welcome1!';
status;
show databases;
select user,host from mysql.user where user='root';
CREATE USER 'admin'@'%' IDENTIFIED BY 'Welcome1!';
flush privileges;
show variables like "%version%";
mysql -uadmin -p -h 127.0.0.1
mysql -uadmin -p -h localhost

```

## Server A + Server B 
```
sudo groupadd oracle
sudo useradd -r -g oracle -s /bin/false oracle
sudo usermod -a -G oracle opc

sudo mkdir -p /mysql/ /mysql/etc01 /mysql/data01
sudo mkdir -p /mysql/log01 /mysql/temp01 /mysql/binlog01

sudo mkdir -p /mysql/ /mysql/etc02 /mysql/data02
sudo mkdir -p /mysql/log02 /mysql/temp02 /mysql/binlog02
```

##Server A (Workbench)

```
sudo yum install /workshop/linux/mysql-community/mysql80-community-release-el7-3.noarch.rpm
sudo yum install mysql
cd /workshop/linux/workbench/
sudo yum install atkmm cairomm glibmm24 gtkmm30 libsigc++20 pangcomm
sudo yum install ./mysql-workbench-commercial-8.0.20-1.1.el7.x86_64.rpm

```

## MySQL Commercial on Server A

```
cd /mysql/
sudo tar zxvf /workshop/linux/mysql-commercial-8.0.20-el7-x86_64.tar.gz
sudo ln -s mysql-commercial-8.0.20-el7-x86_64/ mysql-latest
cd /mysql/etc01
sudo vi my.cnf

```

```
[mysqld]
# General configurations
port=3307
mysqlx_port=33070
server_id=10
socket=/mysql/temp01/mysql.sock
mysqlx_socket=/mysql/temp01/mysqlx.sock
user=oracle
# File locations
basedir=/mysql/mysql-latest
plugin-dir=/mysql/mysql-latest/lib/plugin
datadir=/mysql/data01
tmpdir=/mysql/temp01
log-error=/mysql/log01/err_log.log
general_log_file=/mysql/log01/gl_log.log
slow_query_log_file=/mysql/log01/sq_log.log
# Maximum limits
max-connections=200
open-files-limit=5000
# Security setting for file load
secure-file-priv=/mysql/data01
# InnoDB settings
innodb_flush_log_at_trx_commit=1
innodb_buffer_pool_size=512M
# MyISAM settings
key_buffer_size=124M
# Enable binary logs
log-bin=/mysql/binlog01/binlog
binlog-format=row
sync_binlog = 1
master-info-repository=TABLE
relay-log-info-repository=TABLE
gtid-mode=on
enforce-gtid-consistency=true
# Performance Monitor
performance_schema_consumer_events_statements_history_long = ON
# Plugin load example (MySQL Enterprise Thread Pool)
plugin-load=thread_pool.so
```

## MySQL Commercial on Server B

```
cd /mysql/
sudo tar zxvf /workshop/linux/mysql-commercial-8.0.20-el7-x86_64.tar.gz
sudo ln -s mysql-commercial-8.0.20-el7-x86_64/ mysql-latest
cd /mysql/etc01
sudo vi my.cnf (3307)

```

```
[mysqld]
# General configurations
port=3307
mysqlx_port=33070
server_id=10
socket=/mysql/temp01/mysql.sock
mysqlx_socket=/mysql/temp01/mysqlx.sock
user=oracle
# File locations
basedir=/mysql/mysql-latest
plugin-dir=/mysql/mysql-latest/lib/plugin
datadir=/mysql/data01
tmpdir=/mysql/temp01
log-error=/mysql/log01/err_log.log
general_log_file=/mysql/log01/gl_log.log
slow_query_log_file=/mysql/log01/sq_log.log
# Maximum limits
max-connections=200
open-files-limit=5000
# Security setting for file load
secure-file-priv=/mysql/data01
# InnoDB settings
innodb_flush_log_at_trx_commit=1
innodb_buffer_pool_size=512M
# MyISAM settings
key_buffer_size=124M
# Enable binary logs
log-bin=/mysql/binlog01/binlog
binlog-format=row
sync_binlog = 1
master-info-repository=TABLE
relay-log-info-repository=TABLE
gtid-mode=on
enforce-gtid-consistency=true
# Performance Monitor
performance_schema_consumer_events_statements_history_long = ON
# Plugin load example (MySQL Enterprise Thread Pool)
plugin-load=thread_pool.so
```

```
cd /mysql/etc02
sudo vi my.cnf (3317)
```

```
[mysqld]
# General configurations
port=3317
mysqlx_port=33170
mysqlx_socket=/mysql/temp02/mysqlx.sock
server_id=30
socket=/mysql/temp02/mysql.sock
user=oracle
# File locations
basedir=/mysql/mysql-latest
plugin-dir=/mysql/mysql-latest/lib/plugin
datadir=/mysql/data02
tmpdir=/mysql/temp02
log-error=/mysql/log02/err_log.log
general_log_file=/mysql/log02/gl_log.log
slow_query_log_file=/mysql/log02/sq_log.log
# Maximum limits
max-connections=200
open-files-limit=5000
# Security setting for file load
secure-file-priv=/mysql/data02
# InnoDB settings
innodb_flush_log_at_trx_commit=1
innodb_buffer_pool_size=512M
# MyISAM settings
key_buffer_size=124M
# Enable binary logs
log-bin=/mysql/binlog02/binlog
binlog-format=row
sync_binlog = 1
master-info-repository=TABLE
relay-log-info-repository=TABLE
gtid-mode=on
enforce-gtid-consistency=true
# Performance Monitor
performance_schema_consumer_events_statements_history_long = ON
# Plugin load example (MySQL Enterprise Thread Pool)
plugin-load=thread_pool.so
```

```
sudo chown -R oracle:oracle /mysql
sudo chmod -R 750 /mysql
sudo chmod -R 770 /mysql/etc01
sudo chmod -R 770 /mysql/etc02
```


## Server B
```
sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --initialize --user=oracle
sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --user=oracle &

ps -ef | grep mysqld
netstat -an | grep 3307
sudo grep ready /mysql/log01/err_log.log
sudo grep 'temporary password' /mysql/log01/err_log.log
/mysql/mysql-latest/bin/mysql_secure_installation -P3307 -h127.0.0.1

mysql -uroot -p -h 127.0.0.1 -P 3307
status

CREATE USER 'admin'@'%' IDENTIFIED BY 'Welcome1!';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;
flush privileges;
select * from information_schema.plugins where plugin_name like 'thread%';
select * from information_schema.plugins where plugin_name like 'thread%'\G

/mysql/mysql-latest/bin/mysqladmin -uadmin -h127.0.0.1 -p -P3307 shutdown
sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --user=oracle &

mysql -u root -p
mysql -u root -p -P3306
mysql -uroot -p -h localhost -P3308
mysql -uroot -p -h 127.0.0.1 -P3307
mysql -uroot -p -h 10.0.2.49 -P3307

mysql -uroot -p -S /mysql/temp01/mysql.sock

ps -ef |grep mysqld
mysql -uroot -p -h 127.0.0.1 -P 3307
select user,host from mysql.user;
mysql -uadmin -p -P3307 
mysql -uadmin -p -P3307 -h 127.0.0.1
mysql -uadmin -p -P3307 -h localhost 
mysql -uadmin -p -P3307 -h 127.0.0.1 < /workshop/databases/world/world.sql
cd /workshop/databases/employees
mysql -uadmin -p -P3307 -h 127.0.0.1 < ./employees.sql
mysql -uroot -p -h 127.0.0.1 -P 3307
SHOW VARIABLES LIKE "%version%";
status;
SELECT table_name, engine FROM INFORMATION_SCHEMA.TABLES WHERE engine <> 'InnoDB';
SELECT table_name, engine FROM INFORMATION_SCHEMA.TABLES WHERE engine = 'InnoDB';
SELECT table_name, engine FROM INFORMATION_SCHEMA.TABLES where engine = 'InnoDB' and table_schema not in ('mysql','information_schema', 'sys');
 SELECT ENGINE, COUNT(*), SUM(DATA_LENGTH)/ 1024 / 1024 AS 'Data MB', SUM(INDEX_LENGTH)/1024 / 1024 AS 'Index MB' FROM information_schema.TABLEs group by engine;
SELECT table_schema AS 'Schema', SUM( data_length ) / 1024 / 1024 AS 'Data MB', SUM( index_length ) / 1024 / 1024 AS 'Index MB', SUM( data_length + index_length ) / 1024 / 1024 AS 'Sum' FROM information_schema.tables GROUP BY table_schema ;
SHOW GLOBAL VARIABLES; 
SHOW MASTER STATUS;
SHOW SLAVE STATUS\G 
SHOW GLOBAL STATUS; 
SHOW FULL PROCESSLIST; 
SHOW ENGINE INNODB STATUS\G 
SHOW FULL PROCESSLIST; 

```



# Day 2a

```
mysql -uadmin -p -h 127.0.0.1 -P 3307
CREATE USER 'appuser'@<myip> IDENTIFIED BY 'Welcome1!';
CREATE USER 'deproot'@<myip> IDENTIFIED BY 'Welcome1!';
GRANT ALL PRIVILEGES ON *.* TO 'deproot'@<myip> WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON world.* TO 'appuser'@<myip>;
SHOW GRANTS FOR 'appuser'@10.0.2.49;

```

```
mysql -u appuser -p -h <myip> -P 3307
USE world;
SELECT * FROM city;
# revoke from appuser

SELECT * FROM city;

```

```
REVOKE SELECT ON world.* FROM 'appuser'@<myip>;
SHOW GRANTS FOR 'appuser'@<myip>';

SHOW VARIABLES LIKE 'validate_password%';
alter user 'appuser'@<myip> identified by 'appuser';
alter user 'appuser'@<myip> identified by 'Welcome';
alter user 'appuser'@<myip>identified by 'We1!';
alter user 'appuser'@<myip> PASSWORD EXPIRE;

```

```
mysql –u appuser -p -h <myip> -P 3307
SHOW DATABASES;
alter user 'appuser'@<myip> identified by 'Welcome1!';
```

## Firewall

```
root

mysql -uadmin -p -P3307 -h127.0.0.1 < /mysql/mysql-latest/share/linux_install_firewall.sql
mysql -uadmin -p -P3307 -h127.0.0.1
SHOW GLOBAL VARIABLES LIKE 'mysql_firewall_mode';
SHOW GLOBAL STATUS LIKE "firewall%";
CREATE USER 'fwtest'@'%' IDENTIFIED BY 'Welcome1!';
GRANT ALL PRIVILEGES ON world.* TO 'fwtest'@'%';
CALL mysql.sp_set_firewall_mode('fwtest@%', 'RECORDING');\G
SELECT * FROM mysql.firewall_users;
SELECT * FROM mysql.firewall_whitelist;

```

```
mysql -ufwtest -p -P3307 -h127.0.0.1
USE world;
SELECT * FROM city limit 25;
SELECT Code, Name, Region FROM country WHERE population > 200000;

```
```

#back to root

SELECT * FROM information_schema.mysql_firewall_whitelist;
CALL mysql.sp_set_firewall_mode('fwtest@%', 'PROTECTING');
SELECT * FROM mysql.firewall_whitelist;

```

```
#back to fwtest
SELECT * FROM city limit 25;
SELECT Code, Name, Region FROM country WHERE population > 200000;
SELECT * FROM countrylanguage;
SELECT Code, Name, Region FROM country WHERE population > 500000;
SELECT Code, Name, Region FROM country WHERE population > 200000 or 1=1;

```

```
root
CALL mysql.sp_set_firewall_mode('fwtest@%', 'DETECTING');
SET GLOBAL log_error_verbosity=3;
```

```
#fwtest

SELECT Code, Name, Region FROM country WHERE population > 200000 or 1=1;
```

```
grep "MY-011191" /mysql/log01/err_log.log

```

```
root
CALL mysql.sp_set_firewall_mode('fwtest@%', 'OFF');
SELECT MODE FROM INFORMATION_SCHEMA.MYSQL_FIREWALL_USERS WHERE USERHOST = 'fwtest@%';
SELECT RULE FROM INFORMATION_SCHEMA.MYSQL_FIREWALL_WHITELIST WHERE USERHOST = 'fwtest@%';
```

## Audit

```
plugin-load=thread_pool.so;audit_log.so
audit_log=FORCE_PLUS_PERMANENT
audit_log_rotate_on_size=20971520

/mysql/mysql-latest/bin/mysqladmin -uadmin -h127.0.0.1 -p -P3307 shutdown
sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --user=oracle &

```

```
mysql -u appuser -p -h <your ip> -P 3307

USE world;
SELECT * FROM city limit 25;
SELECT Code, Name, Region FROM country WHERE population > 200000;
```

## Roles

```
mysql -u root -p -h 127.0.0.1 -P 3307
CREATE USER 'appuser2'@10.0.20.28 IDENTIFIED BY 'Welcome1!';
SHOW GRANTS FOR 'appuser'@10.0.20.28;
CREATE ROLE 'app_read';
GRANT SELECT ON world.* TO 'app_read';
GRANT 'app_read' TO 'appuser'@10.0.20.28, 'appuser2'@10.0.20.28;
SHOW GRANTS FOR 'appuser'@10.0.20.28;

SHOW GRANTS FOR 'appuser2'@10.0.20.28;
```
```
mysql -uappuser -p -P3307 -h[internal ip]

SET ROLE ALL;
```

## Masking

```
mysql -uroot -p -h 127.0.0.1 -P 3307
INSTALL PLUGIN data_masking SONAME 'data_masking.so';
CREATE FUNCTION gen_blacklist RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION gen_dictionary RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION gen_dictionary_drop RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION gen_dictionary_load RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION gen_range RETURNS INTEGER SONAME 'data_masking.so';
CREATE FUNCTION gen_rnd_email RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION gen_rnd_pan RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION gen_rnd_ssn RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION gen_rnd_us_phone RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION mask_inner RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION mask_outer RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION mask_pan RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION mask_pan_relaxed RETURNS STRING SONAME 'data_masking.so';
CREATE FUNCTION mask_ssn RETURNS STRING SONAME 'data_masking.so';

SHOW PLUGINS;

SELECT mask_inner(NAME, 1,1), NAME FROM world.city limit 10;
SELECT mask_outer(NAME, 1,1),NAME FROM world.city limit 10;
SELECT mask_inner(NAME, 1,1, '&'),NAME FROM world.city limit 1;
SELECT gen_range(1, 200);
SELECT gen_rnd_us_phone();
SELECT gen_rnd_email();
```

# Day 1b

## Server B
```
vi my.cnf
gtid-mode=on
enforce-gtid-consistency=true

/mysql/mysql-latest/bin/mysqladmin -uadmin -h127.0.0.1 -p -P3307 shutdown
sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --user=oracle &
mysql -uadmin -p -P3307 -h127.0.0.1
select 1+1;
select 1=0;
select 1=1;
select null=null;

use world;
CREATE TABLE if not exists poi (x Int, y INT, z INT);
alter table poi add id bigint;
ALTER TABLE poi ADD PRIMARY KEY (id);
create table city_part as select * from city;
SELECT count(*) FROM city_part;
SELECT count(*) FROM city;

show create table city\G
show create table city_part\G
CREATE INDEX myidindex ON city_part (ID);

SELECT * FROM INFORMATION_SCHEMA.STATISTICS WHERE table_name = 'city' and table_schema='world'\G
CREATE INDEX myccindex ON city_part (CountryCode);
CREATE INDEX mydistrictindex ON city_part (District);
CREATE INDEX myprefexindex ON city_part(District(3));
SELECT * FROM INFORMATION_SCHEMA.STATISTICS WHERE table_name = 'city_part' and table_schema='world'\G
ALTER TABLE city_part DROP COLUMN Population;
ALTER TABLE city_part DROP COLUMN CountryCode;
OPTIMIZE TABLE city_part;
ANALYZE TABLE city_part;

CREATE TABLE jtest (doc JSON, gencol CHAR(7) AS (doc->"$.b"));
INSERT INTO jtest(doc) VALUE('{"A": "hello", "b": "test", "c": {"hello": 1}}');
INSERT INTO jtest(doc) VALUE('{"b": "hello"}'),('{"c": "help"}');
SELECT * FROM jtest;
SELECT json_extract (doc, "$.b") FROM jtest;
SELECT doc->"$.b" FROM jtest;
SELECT json_extract (doc, "$.c") FROM jtest;
SELECT doc->"$.b" from jtest;
SELECT doc->>"$.b" from jtest;
CREATE INDEX myvirtindex ON jtest(gencol);
SELECT * FROM country;
SELECT * FROM city limit 50;
SELECT * FROM country, city;
SELECT * FROM country, city WHERE Continent="Africa" limit 50;
SELECT * FROM country, city WHERE country.Code=city.CountryCode;
SELECT * FROM country JOIN city ON country.Code=city.CountryCode;
SELECT * FROM country INNER JOIN city ON country.Code=city.CountryCode;
SELECT * FROM country JOIN countrylanguage ON countrylanguage.CountryCode=country.Code;

SELECT * FROM country LEFT JOIN countrylanguage ON countrylanguage.CountryCode=country.Code;
SELECT * FROM country LEFT JOIN countrylanguage ON countrylanguage.CountryCode=country.Code WHERE Continent="Europe";
SELECT * FROM country LEFT JOIN countrylanguage ON countrylanguage.CountryCode=country.Code WHERE Capital is null;
SELECT * FROM country RIGHT JOIN countrylanguage ON countrylanguage.CountryCode=country.Code;

ALTER TABLE world.city_part PARTITION BY HASH (id) PARTITIONS 5;

#uncomment gtid-mode

vi my.cnf

gtid-mode=on
enforce-gtid-consistency=true


/mysql/mysql-latest/bin/mysqladmin -uadmin -h127.0.0.1 -p -P3307 shutdown
sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --user=oracle &
```

```
mysqlsh
\c root@127.0.0.1:33070
session.createSchema('test')
\use test
db.createCollection('posts');
db.posts.add({"title":"MySQL 8.0 rocks", "text":"My first post!", "code": "42"})
db.posts.add({"title":"Polyglot database", "text":"Developing both SQL and NoSQL applications"})

db.posts.add("This is not a valid JSON document")
db.posts.find()
db.posts.find().limit(1)
db.posts.modify("title = 'MySQL 8.0 rocks'").set("title", " MySQL 8.0 rocks!!!")
db.posts.find()
session.sql("desc posts")
session.sql("show create table posts")
session.sql("select * from posts")
db.posts.createIndex('myIndex', {fields: [{field: "$.title", type: "TEXT(20)"}]} )
session.sql("show create table posts")
```

# Day 2b

```
#serverA

cd /workshop/linux/
sudo ./mysqlmonitor-8.0.20.1237-linux-x86_64-installer.bin

#use default, password: Welcome1!

service manager user account: mysqlmem
repository username: service_manager

```

```
#serverB

cd /workshop/linux/agent
sudo ./ mysqlmonitoragent-8.0.20.1237-linux-x86-64bit-installer.bin
sudo /etc/init.d/mysql-monitor-agent start

```

## Backup

```
#ServerB

cd /mysql
sudo mkdir -p /mysql/exports
sudo chown oracle:oracle /mysql/exports/
sudo chmod 770 /mysql/exports/
mysqldump -uroot -p -h127.0.0.1 -P3307 --single-transaction --events --routines --flush-logs --all-databases > exports/full.sql
mysqldump -uroot -p -h127.0.0.1 -P3307 --single-transaction --set-gtid-purged=OFF employees > ./exports/employees.sql

mysql -uroot -p -h127.0.0.1 -P3307
show databases;
DROP DATABASE employees;
show databases;

CREATE DATABASE employees;
exit
mysql -uroot -p -h127.0.0.1 -P3307 employees < ./exports/employees.sql
mysql -uroot -p -h127.0.0.1 -P3307
show databases;

```

```
#ServerB

sudo mkdir -p /backupdir/full
sudo mkdir /backupdir/compressed
sudo chown -R oracle:oracle /backupdir
sudo chmod 770 -R /backupdir

mysql -uroot -p -h127.0.0.1 -P3307

CREATE USER 'mysqlbackup'@'%' IDENTIFIED BY 'Welcome1!';
GRANT RELOAD ON *.* TO 'mysqlbackup'@'%';
GRANT BACKUP_ADMIN, SELECT ON *.* TO 'mysqlbackup'@'%';
GRANT CREATE, INSERT, DROP, UPDATE ON mysql.backup_progress TO 'mysqlbackup'@'%';

GRANT CREATE, INSERT, SELECT, DROP, UPDATE ON mysql.backup_history TO 'mysqlbackup'@'%';

GRANT CREATE, INSERT, SELECT, DROP, UPDATE ON mysql.backup_history TO 'mysqlbackup'@'%';

GRANT SUPER ON *.* TO 'mysqlbackup'@'%';

GRANT PROCESS ON *.* TO 'mysqlbackup'@'%';

GRANT CREATE TEMPORARY TABLES on mysql.* TO 'mysqlbackup'@'%';

GRANT SELECT on *.* TO 'mysqlbackup @'%';

```

```
sudo /mysql/mysql-latest/bin/mysqlbackup --port=3307 --host=127.0.0.1 --protocol=tcp --user=mysqlbackup --password --backup-dir=/backupdir/full backup-and-apply-log

sudo /mysql/mysql-latest/bin/mysqlbackup --port=3307 --host=127.0.0.1 --protocol=tcp --user=mysqlbackup --password --backup-dir=/backupdir/compressed --compress backup-and-apply-log

sudo /mysql/mysql-latest/bin/mysqladmin -uadmin -h127.0.0.1 -p -P3307 shutdown
sudo rm -rf /mysql/binlog01/* /mysql/data01/*

sudo /mysql/mysql-latest/bin/mysqlbackup --defaults-file=/mysql/etc01/my.cnf --backup-dir=/backupdir/full/ --datadir=/mysql/data01 --log-bin=/mysql/binlog01/binlog copy-back

sudo chown -R oracle:oracle /mysql/data01 /mysql/binlog01
sudo chmod -R 750 /mysql/data01 /mysql/binlog01

sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --user=oracle &

```

# Day 3a


```
#Server B (Master)
sudo mkdir -p /backupdir
sudo mkdir -p /backupdir/master
sudo chown -R oracle:oracle /backupdir
sudo chmod -R 770 /backupdir

sudo /mysql/mysql-latest/bin/mysqlbackup --port=3307 --host=127.0.0.1 --protocol=tcp --user=admin --password --backup-dir=/backupdir/master backup-and-apply-log

sudo chown -R oracle:oracle /backupdir/master

sudo scp -r -i /workshop/support/sshkeys/id_rsa /backupdir/master/* opc@< privateIP student###-servera >:/backupdir/slave/

sudo scp -i /workshop/support/sshkeys/id_rsa /mysql/etc01/my.cnf opc@< privateIP student###-servera >:/mysql/etc01/


```

```
# Server A (Slave)

sudo mkdir -p /backupdir
sudo mkdir -p /backupdir/slave
sudo chown -R oracle:oracle /backupdir
sudo chmod -R 770 /backupdir

/mysql/mysql-latest/bin/mysqladmin -uadmin -h127.0.0.1 -p -P3307 shutdown
cd /mysql
sudo rm -rf binlog01/*
sudo rm -rf data01/*
sudo rm -rf log01/*
sudo rm -rf temp01/*
sudo cd /
ls -l

sudo chown -R oracle:oracle /backupdir

cd /backupdir/
ls -l

sudo /mysql/mysql-latest/bin/mysqlbackup --defaults-file=/mysql/etc01/my.cnf --backup-dir=/backupdir/slave/ --datadir=/mysql/data01 --log-bin=/mysql/binlog01/binlog copy-back
sudo chown -R oracle:oracle /mysql
sudo chown oracle:oracle /mysql/etc01/my.cnf

sudo vi /mysql/etc01/my.cnf
# server_id=20

sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc01/my.cnf --user=oracle &
```

```
# Server B (master)

mysql -uroot -p -h127.0.0.1 -P3307
CREATE USER 'repl'@'%' identified with mysql_native_password by 'Welcome1!';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
```
```
# Server A (slave)

mysql -uroot -p -h127.0.0.1 -P3307
SHOW MASTER STATUS;
show variables like 'gtid_purged';

change master to master_host='<private IP of student###-serverb', master_port=3307, master_user='repl', master_password='Welcome1!', master_auto_position=1;
start slave;
SHOW SLAVE STATUS\G
SHOW GLOBAL VARIABLES LIKE 'GTID_EXECUTED';
```

```
# Server B (Master)

CREATE DATABASE newdb;
USE newdb;
CREATE TABLE t1 (c1 int primary key);
INSERT INTO t1 VALUES(1);
INSERT INTO t1 VALUES(2);
create DATABASE employees;
```

```
# Server A (slave)
SHOW DATABASES;
SELECT * FROM newdb.t1;

```

# Day 3b

```
# Server B (Primary)
mysql -uadmin -p -h127.0.0.1 -P3307

SELECT table_schema, table_name, engine, table_rows,
(index_length+data_length)/1024/1024 AS sizeMB
FROM information_schema.tables
WHERE engine != 'innodb'
AND table_schema NOT IN
('information_schema', 'mysql', 'performance_schema');


select tables.table_schema, tables.table_name, tables.engine, tables.table_rows
from information_schema.tables
left join (
select table_schema, table_name
from information_schema.statistics
group by table_schema, table_name, index_name
having
sum(
case
when non_unique = 0
and nullable != 'YES' then 1
else 0
end
) = count(*)
) puks
on tables.table_schema = puks.table_schema
and tables.table_name = puks.table_name
where puks.table_name is null
and tables.table_type = 'BASE TABLE' and engine='InnoDB';

```

```
# Server A

mysql -uadmin -p -h127.0.0.1 -P3307
stop slave;
reset slave;
```

```
# Server B

mysqlsh
dba.checkInstanceConfiguration('admin@student116-serverb:3307')
dba.configureInstance('admin@student116-serverb:3307')
dba.checkInstanceConfiguration('admin@student116-serverb:3307')
\connect admin@student116-serverb:3307
var cluster = dba.createCluster('testCluster' , {ipWhitelist: "10.0.0.0/16", localAddress:"student116-serverb:13307" })
cluster.status()
dba.checkInstanceConfiguration('admin@student116-servera:3307')
dba.configureInstance('admin@student116-servera:3307')

cluster.addInstance('admin@student116-servera:3307', {ipWhitelist: "10.0.0.0/16", localAddress:"student116-servera:13307" })
cluster.status()

sudo mkdir -p /backupdir/node3
sudo chown oracle:oracle /backupdir/node3
sudo chmod 770 /backupdir/node3

sudo mkdir –p /backupdir/node1
sudo chown oracle:oracle /backupdir/node1
sudo chmod 770 /backupdir/node1

sudo /mysql/mysql-latest/bin/mysqlbackup --port=3307 --host=127.0.0.1 --protocol=tcp --user=admin --password --backup-dir=/backupdir/node1 backup-and-apply-log
sudo scp -r -i /workshop/support/sshkeys/id_rsa /backupdir/node1/* opc@student116-serverb:/backupdir/node3/

sudo chmod -R 770 /mysql/etc02
sudo scp -r -i /workshop/support/sshkeys/id_rsa /mysql/etc01/my.cnf opc@secondary2:/mysql/etc02/
sudo chown -R oracle:oracle /mysql/etc02

vi /mysql/etc02/my.cnf
[mysqld]
# General configurations
port=3317
mysqlx_port=33170
mysqlx_socket=/mysql/temp02/mysqlx.sock
server_id=30
socket=/mysql/temp02/mysql.sock
user=oracle
# File locations
basedir=/mysql/mysql-latest
plugin-dir=/mysql/mysql-latest/lib/plugin
datadir=/mysql/data02
tmpdir=/mysql/temp02
log-error=/mysql/log02/err_log.log
general_log_file=/mysql/log02/gl_log.log
slow_query_log_file=/mysql/log02/sq_log.log
# Maximum limits
max-connections=200
open-files-limit=5000
# Security setting for file load
secure-file-priv=/mysql/data02
# InnoDB settings
innodb_flush_log_at_trx_commit=1
innodb_buffer_pool_size=512M
# MyISAM settings
key_buffer_size=124M
# Enable binary logs
log-bin=/mysql/binlog02/binlog
binlog-format=row
sync_binlog = 1
master-info-repository=TABLE
relay-log-info-repository=TABLE
gtid-mode=on
enforce-gtid-consistency=true
# Performance Monitor
performance_schema_consumer_events_statements_history_long = ON
# Plugin load example (MySQL Enterprise
plugin-load=thread_pool.so
```

```
# Server B

sudo /mysql/mysql-latest/bin/mysqlbackup --defaults-file=/mysql/etc02/my.cnf --backup-dir=/backupdir/secondary2/ --datadir=/mysql/data02 --log-bin=/mysql/binlog02/binlog copy-back

sudo chown -R oracle:oracle /mysql/data02 /mysql/binlog02
chown -R oracle:oracle /mysql/etc02/my.cnf

vi my.cnf
#server_id=30

sudo /mysql/mysql-latest/bin/mysqld --defaults-file=/mysql/etc02/my.cnf --user=oracle &
cat /backupdir/secondary2/meta/backup_gtid_executed.sql

mysql -uadmin -p -h127.0.0.1 -P3317
RESET MASTER;
RESET SLAVE;
source /backupdir/secondary2/meta/backup_gtid_executed.sql
```

```
# Server B

mysqlsh
dba.checkInstanceConfiguration('admin@secondary2:3317')
dba.configureInstance('admin@secondary2:3317')
\connect admin@primary:3307
var cluster = dba.getCluster('testCluster')
cluster.addInstance('admin@secondary2:3317', {ipWhitelist: '10.0.0.0/16', localAddress:"secondary2:13317" })
cluster.status()

\sql SELECT * FROM performance_schema.replication_group_members;

sudo yum -y install /workshop/linux/mysql-router-commercial-8.0.20-1.1.el7.x86_64.rpm
sudo chmod 777 /mysql
sudo mysqlrouter --bootstrap admin@primary:3307 --user=mysqlrouter --directory /mysql/mysqlrouter
sudo chmod 600 /mysql/mysqlrouter/data/keyring
sudo chmod 600 /mysql/mysqlrouter/ mysqlrouter.key
sudo /mysql/mysqlrouter/start.sh

```

```
#Server B

mysql -uadmin -p -h127.0.0.1 -P6446
SELECT @@hostname, @@port;
use newdb;
SHOW TABLES;
CREATE TABLE newtable (c1 int primary key);
INSERT INTO newtable VALUES(1);
INSERT INTO newtable VALUES(2);
SELECT * from newtable;
exit

mysql -uadmin -p -h127.0.0.1 -P6447
SELECT @@hostname, @@port;
use newdb;
SHOW TABLES;
SELECT * from newtable;
INSERT INTO newtable VALUES(3);
SELECT * from newtable;
show variables like '%read_only';
exit

```

Test Cluster failover
