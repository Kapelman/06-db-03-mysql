# Домашнее задание к занятию "6.3. MySQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/tree/master/additional/README.md).

## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с данным контейнером.

Решение:

Создадим docker-compose файл
```
vagrant@vagrant:~/docker-compose-yam-mysql$ cat docker-compose.yml
version: '2.1'
services:
  db:
    image: mysql:8.0
    # NOTE: use of "mysql_native_password" is not recommended: https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password
    # (this is just an example, not intended to be a production configuration)
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
    volumes:
      - /home/vagrant/mysql/data:/var/db-data
      - /home/vagrant/mysql/backup:/var/db-backup

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

Передадим дамп базы в папку volume 
```
PS C:\Users\kapli\homeworks\06-db-03-mysql\test_data> scp C:\Users\kapli\homeworks\06-db-03-mysql\test_data\test_dump.sql vagrant@192.168.33.10:/home/vagrant/mysql/backup/
vagrant@192.168.33.10's password:
test_dump.sql                                                                                                                                                                  100% 2125     1.0MB/s   00:00

vagrant@vagrant:~/mysql/backup$ ls -al
total 12
drwxrwxr-x 2 vagrant vagrant 4096 Sep 10 19:32 .
drwxrwxr-x 4 vagrant vagrant 4096 Sep 10 19:27 ..
-rw-rw-r-- 1 vagrant vagrant 2125 Sep 10 19:32 test_dump.sql
```
- соберем сервис
```
vagrant@vagrant:~/docker-compose-yam-mysql$ sudo docker compose build
vagrant@vagrant:~/docker-compose-yam-mysql$ sudo docker compose create
[+] Running 28/28
 ⠿ adminer Pulled                                                                                                                                                                                         110.2s
   ⠿ 213ec9aee27d Pull complete                                                                                                                                                                            11.1s
   ⠿ a600fdbc30cc Pull complete                                                                                                                                                                            15.5s
   ⠿ 0cdd6cb15c0d Pull complete                                                                                                                                                                            18.8s
   ⠿ 8a4c40d8aee7 Pull complete                                                                                                                                                                            21.1s
   ⠿ 77e67522f4fd Pull complete                                                                                                                                                                            33.2s
   ⠿ d181492ef8e9 Pull complete                                                                                                                                                                            39.4s
   ⠿ 0d5c09a73378 Pull complete                                                                                                                                                                            85.9s
   ⠿ 8bb8e21282b9 Pull complete                                                                                                                                                                            87.2s
   ⠿ fdd7ab990e10 Pull complete                                                                                                                                                                            89.0s
   ⠿ d59940a6c65c Pull complete                                                                                                                                                                            91.0s
   ⠿ 8af31b44c60c Pull complete                                                                                                                                                                            92.9s
   ⠿ 8f57d1664c2a Pull complete                                                                                                                                                                            99.7s
   ⠿ eafa31c9e999 Pull complete                                                                                                                                                                           101.8s
   ⠿ d99ebf4176fd Pull complete                                                                                                                                                                           104.9s
   ⠿ dd175257ae8d Pull complete                                                                                                                                                                           105.8s
 ⠿ db Pulled                                                                                                                                                                                              204.8s
   ⠿ 492d84e496ea Pull complete                                                                                                                                                                           105.4s
   ⠿ bbe20050901c Pull complete                                                                                                                                                                           106.0s
   ⠿ e3a5e171c2f8 Pull complete                                                                                                                                                                           106.5s
   ⠿ c2cedd8aa061 Pull complete                                                                                                                                                                           108.8s
   ⠿ d6a485af4cc9 Pull complete                                                                                                                                                                           109.0s
   ⠿ ee16a57baf60 Pull complete                                                                                                                                                                           109.4s
   ⠿ 64bab9180d2a Pull complete                                                                                                                                                                           140.6s
   ⠿ c3aceb7e4f48 Pull complete                                                                                                                                                                           141.0s
   ⠿ 269002e5cf58 Pull complete                                                                                                                                                                           199.1s
   ⠿ d5abeb1bd18e Pull complete                                                                                                                                                                           199.4s
   ⠿ cbd79da5fab6 Pull complete                                                                                                                                                                           200.3s
[+] Running 3/3
 ⠿ Network docker-compose-yam-mysql_default      Created                                                                                                                                                    0.3s
 ⠿ Container docker-compose-yam-mysql-adminer-1  Created                                                                                                                                                    1.0s
 ⠿ Container docker-compose-yam-mysql-db-1       Created                                                                                                                                                    1.0s

- vagrant@vagrant:~/docker-compose-yam-mysql$ sudo docker compose ps
NAME                                 COMMAND                  SERVICE             STATUS              PORTS
docker-compose-yam-mysql-adminer-1   "entrypoint.sh docke…"   adminer             created
docker-compose-yam-mysql-db-1        "docker-entrypoint.s…"   db                  created
```
- запустим сервис

```
vagrant@vagrant:~/docker-compose-yam-mysql$ sudo docker compose up
[+] Running 2/2
 ⠿ Container docker-compose-yam-mysql-adminer-1  Recreated                                                                                                                                                  0.4s
 ⠿ Container docker-compose-yam-mysql-db-1       Recreated                                                                                                                                                  0.4s
Attaching to docker-compose-yam-mysql-adminer-1, docker-compose-yam-mysql-db-1
docker-compose-yam-mysql-adminer-1  | [Sat Sep 10 19:48:04 2022] PHP 7.4.30 Development Server (http://[::]:8080) started
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:04+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.30-1.el8 started.
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:04+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:04+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.30-1.el8 started.
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:05+00:00 [Note] [Entrypoint]: Initializing database files
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:05.330679Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:05.330811Z 0 [Warning] [MY-010918] [Server] 'default_authentication_plugin' is deprecated and will be removed in a future release. Please use authentication_policy instead.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:05.330833Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.30) initializing of server in progress as process 81
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:05.354816Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:08.379795Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:13.927072Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:26+00:00 [Note] [Entrypoint]: Database files initialized
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:26+00:00 [Note] [Entrypoint]: Starting temporary server
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:28.294988Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:28.329809Z 0 [Warning] [MY-010918] [Server] 'default_authentication_plugin' is deprecated and will be removed in a future release. Please use authentication_policy instead.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:28.329845Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.30) starting as process 132
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:28.439631Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:30.738667Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:32.518700Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:32.518911Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:32.524991Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:32.574205Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: /var/run/mysqld/mysqlx.sock
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:32.574349Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.30'  socket: '/var/run/mysqld/mysqld.sock'  port: 0  MySQL Community Server - GPL.
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:32+00:00 [Note] [Entrypoint]: Temporary server started.
docker-compose-yam-mysql-db-1       | '/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
docker-compose-yam-mysql-db-1       | Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
docker-compose-yam-mysql-db-1       | Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
docker-compose-yam-mysql-db-1       | Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
docker-compose-yam-mysql-db-1       | Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
docker-compose-yam-mysql-db-1       | Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.
docker-compose-yam-mysql-db-1       |
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:51+00:00 [Note] [Entrypoint]: Stopping temporary server
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:51.644791Z 10 [System] [MY-013172] [Server] Received SHUTDOWN from user root. Shutting down mysqld (Version: 8.0.30).
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:54.006216Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.30)  MySQL Community Server - GPL.
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:54+00:00 [Note] [Entrypoint]: Temporary server stopped
docker-compose-yam-mysql-db-1       |
docker-compose-yam-mysql-db-1       | 2022-09-10 19:48:54+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.
docker-compose-yam-mysql-db-1       |
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.025515Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.027889Z 0 [Warning] [MY-010918] [Server] 'default_authentication_plugin' is deprecated and will be removed in a future release. Please use authentication_policy instead.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.027930Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.30) starting as process 1
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.053681Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.525122Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.975529Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.975601Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:55.986888Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:56.048919Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
docker-compose-yam-mysql-db-1       | 2022-09-10T19:48:56.054013Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.30'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
```
- Также запустилась web консоль для управления MySql, сервис Adminer

![](Web-mysql.jpg)(Web консоль.jpg)

- зайдем в контейнер и выполним задание
```
vagrant@vagrant:~/docker-compose-yam-mysql$ sudo docker compose ps
NAME                                 COMMAND                  SERVICE             STATUS              PORTS
docker-compose-yam-mysql-adminer-1   "entrypoint.sh docke…"   adminer             running             0.0.0.0:8080->8080/tcp, :::8080->8080/tcp
docker-compose-yam-mysql-db-1        "docker-entrypoint.s…"   db                  running             33060/tcp
vagrant@vagrant:~/docker-compose-yam-mysql$ sudo docker exec -it docker-compose-yam-mysql-db-1 bash
bash-4.4# pwd
/
bash-4.4# whoami
root

bash-4.4# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.0.30 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```

- вывод help
```
mysql> \h

For information about MySQL products and services, visit:
   http://www.mysql.com/
For developer information, including the MySQL Reference Manual, visit:
   http://dev.mysql.com/
To buy MySQL Enterprise support, training, or other products, visit:
   https://shop.mysql.com/

List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.
resetconnection(\x) Clean session context.
query_attributes Sets string parameters (name1 value1 name2 value2 ...) for the next query to pick up.
ssl_session_data_print Serializes the current SSL session data to stdout or file

For server side help, type 'help contents'
```
- команда status
```
mysql> status
--------------
mysql  Ver 8.0.30 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          12
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.30 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 31 min 22 sec

Threads: 2  Questions: 5  Slow queries: 0  Opens: 119  Flush tables: 3  Open tables: 38  Queries per second avg: 0.002
```
- создадим базу данных test_db
```
mysql> create database test_db;
Query OK, 1 row affected (0.09 sec)
```
- загрузим dump базы
```
bash-4.4# mysql test_db < test_dump.sql -u root -p
Enter password:
```
- подключимся к db
```
mysql> connect test_db
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Connection id:    19
Current database: test_db

- список таблиц базы данных
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test_db            |
+--------------------+
5 rows in set (0.00 sec)

mysql> SHOW tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)

- price > 300, запрос и результат
mysql> select * from orders where price > 300;
+----+----------------+-------+
| id | title          | price |
+----+----------------+-------+
|  2 | My little pony |   500 |
+----+----------------+-------+
1 row in set (0.00 sec)
```


## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней 
- количество попыток авторизации - 3 
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James"
    
  
Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.
    
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю `test` и 
**приведите в ответе к задаче**.
Решение:

- плагин уже встроен в MySQL по умолчанию
```
bash-4.4# mysql --default-auth=mysql_native_password -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 21
Server version: 8.0.30 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show variables like 'default_authentication_plugin';
+-------------------------------+-----------------------+
| Variable_name                 | Value                 |
+-------------------------------+-----------------------+
| default_authentication_plugin | mysql_native_password |
+-------------------------------+-----------------------+
1 row in set (0.01 sec)
```
- создадим пользователя
```
mysql> CREATE USER 'test'@'localhost'
    -> IDENTIFIED WITH mysql_native_password BY 'test-pass'
    -> WITH MAX_QUERIES_PER_HOUR 100
    -> PASSWORD EXPIRE INTERVAL 180 DAY
    -> FAILED_LOGIN_ATTEMPTS 3
    -> ATTRIBUTE '{"fname": "James", "lname": "Pretty"}';
Query OK, 0 rows affected (0.02 sec)
```
- добавим пользователю гранты
```
mysql> GRANT SELECT ON test_db.* TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.09 sec)
```
- посмотрим аттрибуты и права пользователя 
```
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES
    -> WHERE USER = 'test' AND HOST = 'localhost';
+------+-----------+---------------------------------------+
| USER | HOST      | ATTRIBUTE                             |
+------+-----------+---------------------------------------+
| test | localhost | {"fname": "James", "lname": "Pretty"} |
+------+-----------+---------------------------------------+
1 row in set (0.00 sec)

mysql> show grants for 'test'@'localhost';
+---------------------------------------------------+
| Grants for test@localhost                         |
+---------------------------------------------------+
| GRANT USAGE ON *.* TO `test`@`localhost`          |
| GRANT SELECT ON `test_db`.* TO `test`@`localhost` |
+---------------------------------------------------+
2 rows in set (0.00 sec)
```
## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
- на `InnoDB`

Решение:
```
mysql> set profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SHOW VARIABLES like 'profiling';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| profiling     | ON    |
+---------------+-------+
1 row in set (0.01 sec)
```
- посмотрим на шаги выволнения запроса, время выполнения каждого шага, утилизацию CPU  и т.д.
```
mysql> show profile all;
+----------------------+----------+----------+------------+-------------------+---------------------+--------------+---------------+---------------+-------------------+-------------------+-------------------+-------+-----------------------+----------------------+-------------+
| Status               | Duration | CPU_user | CPU_system | Context_voluntary | Context_involuntary | Block_ops_in | Block_ops_out | Messages_sent | Messages_received | Page_faults_major | Page_faults_minor | Swaps | Source_function       | Source_file          | Source_line |
+----------------------+----------+----------+------------+-------------------+---------------------+--------------+---------------+---------------+-------------------+-------------------+-------------------+-------+-----------------------+----------------------+-------------+
| starting             | 0.000292 | 0.000133 |   0.000158 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 1 |     0 | NULL                  | NULL                 |        NULL |
| checking permissions | 0.000042 | 0.000019 |   0.000022 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | check_access          | sql_authorization.cc |        2147 |
| Opening tables       | 0.000247 | 0.000114 |   0.000135 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 4 |     0 | open_tables           | sql_base.cc          |        5796 |
| init                 | 0.000016 | 0.000006 |   0.000007 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | execute               | sql_select.cc        |         564 |
| System lock          | 0.000019 | 0.000009 |   0.000011 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | mysql_lock_tables     | lock.cc              |         331 |
| optimizing           | 0.000011 | 0.000005 |   0.000006 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | optimize              | sql_optimizer.cc     |         296 |
| optimizing           | 0.000017 | 0.000008 |   0.000009 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | optimize              | sql_optimizer.cc     |         296 |
| statistics           | 0.000070 | 0.000032 |   0.000038 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 1 |     0 | optimize              | sql_optimizer.cc     |         624 |
| preparing            | 0.000043 | 0.000020 |   0.000023 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | optimize              | sql_optimizer.cc     |         708 |
| statistics           | 0.000015 | 0.000006 |   0.000008 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | optimize              | sql_optimizer.cc     |         624 |
| preparing            | 0.000061 | 0.000028 |   0.000033 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 1 |     0 | optimize              | sql_optimizer.cc     |         708 |
| executing            | 0.004677 | 0.004885 |   0.000000 |                 1 |                   1 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | ExecuteIteratorQuery  | sql_union.cc         |        1186 |
| end                  | 0.000036 | 0.000031 |   0.000000 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | execute               | sql_select.cc        |         597 |
| query end            | 0.000037 | 0.000037 |   0.000000 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | mysql_execute_command | sql_parse.cc         |        4763 |
| closing tables       | 0.000021 | 0.000020 |   0.000000 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | mysql_execute_command | sql_parse.cc         |        4826 |
| freeing items        | 0.000320 | 0.000323 |   0.000000 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | dispatch_sql_command  | sql_parse.cc         |        5281 |
| cleaning up          | 0.000058 | 0.000055 |   0.000000 |                 0 |                   0 |            0 |             0 |             0 |                 0 |                 0 |                 0 |     0 | dispatch_command      | sql_parse.cc         |        2381 |
+----------------------+----------+----------+------------+-------------------+---------------------+--------------+---------------+---------------+-------------------+-------------------+-------------------+-------+-----------------------+----------------------+-------------+
17 rows in set, 1 warning (0.00 sec)
```
- узнать текущий поисковый движок можно следующей командой
```
mysql> SHOW VARIABLES like 'default_storage_engine';
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| default_storage_engine | InnoDB |
+------------------------+--------+
1 row in set (0.00 sec)
```
- поменяем поисковый движок и сравним время выполения 
```
mysql> select * from orders where price > 300;
+----+----------------+-------+
| id | title          | price |
+----+----------------+-------+
|  2 | My little pony |   500 |
+----+----------------+-------+
1 row in set (0.00 sec)
```
```
mysql> show profiles;
+----------+------------+--------------------------------------------------------------+
| Query_ID | Duration   | Query                                                        |
+----------+------------+--------------------------------------------------------------+
|       11 | 0.00251700 | SHOW VARIABLES like 'engine*'                                |
|       12 | 0.00228500 | SHOW VARIABLES like 'engine'                                 |
|       13 | 0.00021400 | show engines                                                 |
|       14 | 0.00013200 | SET SESSION profiling = 1                                    |
|       15 | 0.00039375 | SELECT DATABASE()                                            |
|       16 | 0.00011125 | SELECT DATABASE()                                            |
|       17 | 0.00009600 | SELECT * FROM INFORMATION_SCHEMA.PROFILING WHERE QUERY_ID=   |
|       18 | 0.00043975 | SELECT * FROM INFORMATION_SCHEMA.PROFILING WHERE QUERY_ID=1  |
|       19 | 0.00015200 | SET SESSION profiling = 1                                    |
|       20 | 0.00016400 | SELECT DATABASE()                                            |
|       21 | 0.00037000 | select * from orders where price > 300                       |
|       22 | 0.00067725 | SELECT * FROM INFORMATION_SCHEMA.PROFILING WHERE QUERY_ID=21 |
|       23 | 0.00012375 | show variable like 'default_storage_engine'                  |
|       24 | 0.00180225 | SHOW VARIABLES like 'default_storage_engine'                 |
|       25 | 0.00024125 | select * from orders where price > 300                       |
+----------+------------+--------------------------------------------------------------+
15 rows in set, 1 warning (0.00 sec)

mysql> alter table orders engine = MyISAM;
Query OK, 5 rows affected (0.06 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> select * from orders where price > 300;
+----+----------------+-------+
| id | title          | price |
+----+----------------+-------+
|  2 | My little pony |   500 |
+----+----------------+-------+
1 row in set (0.00 sec)
```
- время выполнения увеличилось, значение столбца Duration для строки 27 больше строки 25.
```
mysql> show profiles;
+----------+------------+--------------------------------------------------------------+
| Query_ID | Duration   | Query                                                        |
+----------+------------+--------------------------------------------------------------+
|       13 | 0.00021400 | show engines                                                 |
|       14 | 0.00013200 | SET SESSION profiling = 1                                    |
|       15 | 0.00039375 | SELECT DATABASE()                                            |
|       16 | 0.00011125 | SELECT DATABASE()                                            |
|       17 | 0.00009600 | SELECT * FROM INFORMATION_SCHEMA.PROFILING WHERE QUERY_ID=   |
|       18 | 0.00043975 | SELECT * FROM INFORMATION_SCHEMA.PROFILING WHERE QUERY_ID=1  |
|       19 | 0.00015200 | SET SESSION profiling = 1                                    |
|       20 | 0.00016400 | SELECT DATABASE()                                            |
|       21 | 0.00037000 | select * from orders where price > 300                       |
|       22 | 0.00067725 | SELECT * FROM INFORMATION_SCHEMA.PROFILING WHERE QUERY_ID=21 |
|       23 | 0.00012375 | show variable like 'default_storage_engine'                  |
|       24 | 0.00180225 | SHOW VARIABLES like 'default_storage_engine'                 |
|       25 | 0.00024125 | select * from orders where price > 300                       |
|       26 | 0.05775350 | alter table orders engine = MyISAM                           |
|       27 | 0.00082025 | select * from orders where price > 300                       |
+----------+------------+--------------------------------------------------------------+
15 rows in set, 1 warning (0.00 sec)
```
## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):
- Скорость IO важнее сохранности данных
- Нужна компрессия таблиц для экономии места на диске
- Размер буффера с незакомиченными транзакциями 1 Мб
- Буффер кеширования 30% от ОЗУ
- Размер файла логов операций 100 Мб

Приведите в ответе измененный файл `my.cnf`.


Решение:

Файл поместим в директорию показанную ниже:
```
bash-4.4# pwd
/etc/mysql/conf.d
bash-4.4# ls -al
total 16
drwxr-xr-x 1 root root 4096 Sep 11 01:37 .
drwxr-xr-x 1 root root 4096 Aug 30 21:37 ..
-rw-r--r-- 1 root root  331 Sep 11 01:37 my.cnf
```
- Переменные в MySQL имеют следующие значения:
```
mysql> show variables like 'innodb_log_file_size'
    -> ;
+----------------------+----------+
| Variable_name        | Value    |
+----------------------+----------+
| innodb_log_file_size | 50331648 |
+----------------------+----------+
1 row in set (0.02 sec)

mysql> show variables like 'innodb_file_per_table';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_file_per_table | ON    |
+-----------------------+-------+
1 row in set (0.00 sec)

mysql> show variables like 'innodb_flush_method';
+---------------------+-------+
| Variable_name       | Value |
+---------------------+-------+
| innodb_flush_method | fsync |
+---------------------+-------+
1 row in set (0.01 sec)

mysql> show variables like 'innodb_flush_log_at_trx_commit';
+--------------------------------+-------+
| Variable_name                  | Value |
+--------------------------------+-------+
| innodb_flush_log_at_trx_commit | 1     |
+--------------------------------+-------+
1 row in set (0.01 sec)

mysql> show variables like 'innodb_log_buffer_size';
+------------------------+----------+
| Variable_name          | Value    |
+------------------------+----------+
| innodb_log_buffer_size | 16777216 |
+------------------------+----------+
1 row in set (0.10 sec)

mysql> show variables like 'innodb_buffer_pool_size';
+-------------------------+-----------+
| Variable_name           | Value     |
+-------------------------+-----------+
| innodb_buffer_pool_size | 134217728 |
+-------------------------+-----------+
1 row in set (0.01 sec)
```
- доступная память на виртуальном хосте 6 гб, они все доступны контейнеру, ОЗУ 6 гб, 30% это 2 гб
```
vagrant@vagrant:~$ sudo docker stats --no-stream
CONTAINER ID   NAME                                 CPU %     MEM USAGE / LIMIT     MEM %     NET I/O          BLOCK I/O       PIDS
7dfeb4fcf83e   docker-compose-yam-mysql-db-1        9.13%     533.6MiB / 5.809GiB   8.97%     93.7MB / 351kB   254kB / 775MB   65
e22ac2e8c5ec   docker-compose-yam-mysql-adminer-1   0.02%     13.09MiB / 5.809GiB   0.22%     164kB / 54.3kB   475kB / 0B      1
```

- Итоговый файл ниже:
```
[mysqld]
#  fast i\o
innodb_flush_method=fsync
innodb_log_file_size=64M
innodb_flush_log_at_trx_commit=2

# one table -> on file
innodb_file_per_table=ON

# a buffer for transactions is 1 mbyte
innodb_log_buffer_size=1M

# 30% from operational memory=2GB
innodb_buffer_pool_size=2GB

# log file size
innodb_log_file_size=100M
```


### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
