# **EPAM University Program**

* # [TASK 2. Database. MySQL ](README.md)
### 1. Download MySQL server for your OS on VM.
```
docker run --name=mysql1 --restart on-failure -d mysql/mysql-server:8.0
Unable to find image 'mysql/mysql-server:8.0' locally
8.0: Pulling from mysql/mysql-server
134439bbc243: Pull complete
24197d57c06a: Pull complete
a8ff14042390: Pull complete
209d472e303b: Pull complete
4158d94acc40: Pull complete
807107bf7d7a: Pull complete
5f5d5a703fe0: Pull complete
Digest: sha256:1b2005199e9dc12d88d5950cd738dfd12172b1224675294646ea9d6031c78408
Status: Downloaded newer image for mysql/mysql-server:8.0
4262afe15b4eed522a0021404ad6d664a64269d792df9d48c314c938660302b3
root@prolegion-work:/home/prolegion/DevOps_Task/2_Databases_Task# docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS                            PORTS                       NAMES
4262afe15b4e   mysql/mysql-server:8.0   "/entrypoint.sh mysq…"   7 seconds ago   Up 6 seconds (health: starting)   3306/tcp, 33060-33061/tcp   mysql1
root@prolegion-work:/home/prolegion/DevOps_Task/2_Databases_Task# docker logs mysql1 2>&1 | grep GENERATED
[Entrypoint] GENERATED ROOT PASSWORD: U&0S@b95UZRYC=,%@D5_2O^thMj2s8v9
root@prolegion-work:/home/prolegion/DevOps_Task/2_Databases_Task# docker exec -it mysql1 mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.31

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```
### 3. Select a subject area and describe the database schema, (minimum 3 tables)
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)

```
### 4. Create a database on the server through the console.
```
mysql> CREATE DATABASE STUDENTS;
Query OK, 1 row affected (0.01 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| STUDENTS           |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.01 sec)

```
### 5. Fill in tables.
```
mysql> use STUDENTS;
Database changed
mysql> create table Students (id INT NOT NULL AUTO_INCREMENT, FirstName MEDIUMTEXT, Secondname MEDIUMTEXT, Description LONGTEXT, Dateofbirthday DATE, Course INT, PRIMARY KEY (id), index (id));
Query OK, 0 rows affected (0.08 sec)

mysql> show tables;
+--------------------+
| Tables_in_STUDENTS |
+--------------------+
| Students           |
+--------------------+
1 row in set (0.00 sec)

mysql> create table Course (id INT NOT NULL AUTO_INCREMENT, Course MEDIUMTEXT, Enrollmentdate DATE, Notice Longtext, PRIMARY KEY (id), index (id));
Query OK, 0 rows affected (0.05 sec)

mysql> create table Tasks (id INT NOT NULL AUTO_INCREMENT, Course INT, Nameoftasks MEDIUMTEXT, Secondname INT, Description Longtext, Reportdate DATE, PRIMARY KEY
    -> (id), index (id));
Query OK, 0 rows affected (0.05 sec)

mysql> show tables;
+--------------------+
| Tables_in_STUDENTS |
+--------------------+
| Course             |
| Students           |
| Tasks              |
+--------------------+
3 rows in set (0.00 sec)

mysql> insert into Course (course, Enrollmentdate, Notice) values ('Devops_2022', '2022-06-28', 'The best course!');
Query OK, 1 row affected (0.04 sec)

mysql> select * from Course;
+----+-------------+----------------+------------------+
| id | Course      | Enrollmentdate | Notice           |
+----+-------------+----------------+------------------+
|  1 | Devops_2022 | 2022-06-28     | The best course! |
+----+-------------+----------------+------------------+
1 row in set (0.00 sec)

mysql> insert into Students (firstname, secondname, dateofbirthday) value ('Anton', 'Antonov', '2000-05-05');
Query OK, 1 row affected (0.01 sec)

mysql> insert into Students (firstname, secondname, dateofbirthday) value ('Ivan', 'Ivanov', '1997-05-11');
Query OK, 1 row affected (0.01 sec)

mysql> update Students, (select id from Course where Course='Devops_2022') as idcourse set Students.Course=idcourse.id where Students.Secondname='Ivanov';
Query OK, 1 row affected (0.04 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> update Students, (select id from Course where Course='Devops_2022') as idcourse set Students.Course=idcourse.id where Students.Secondname='Antonov';
Query OK, 1 row affected (0.04 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> update Students set Description='Nice communication skills' where Secondname='Antonov';
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from Students;
+----+-----------+------------+---------------------------+----------------+--------+
| id | FirstName | Secondname | Description               | Dateofbirthday | Course |
+----+-----------+------------+---------------------------+----------------+--------+
|  1 | Anton     | Antonov    | Nice communication skills | 2000-05-05     |      1 |
|  2 | Ivan      | Ivanov     | NULL                      | 1997-05-11     |      1 |
+----+-----------+------------+---------------------------+----------------+--------+
2 rows in set (0.00 sec)

mysql> insert into Tasks (course, Nameoftasks, Secondname, ReportDate) values ((select id from Course where Course='Devops_2022'), 'Task_2', (select id from Students where Secondname='Antonov'), NOW());
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> select * from Tasks;
+----+--------+-------------+------------+-------------+------------+
| id | Course | Nameoftasks | Secondname | Description | Reportdate |
+----+--------+-------------+------------+-------------+------------+
|  1 |      1 | Task_2      |          1 | NULL        | 2022-11-30 |
+----+--------+-------------+------------+-------------+------------+
1 row in set (0.01 sec)

```
### 6. Construct and execute SELECT operator with WHERE, GROUP BY and ORDER BY.
```
mysql> select Course, count(*) as CourseCount from Students GROUP BY Course;
+--------+-------------+
| Course | CourseCount |
+--------+-------------+
|      1 |           2 |
+--------+-------------+
1 row in set (0.00 sec)

mysql> select Course, count(*) as CourseCount from Students GROUP BY Course ORDER BY Course desc;
+--------+-------------+
| Course | CourseCount |
+--------+-------------+
|      1 |           2 |
+--------+-------------+
1 row in set (0.00 sec)

```
### 7. Execute other different SQL queries DDL, DML, DCL.
```
### DDL ###

mysql> create user 'epam_user_0'@'localhost' identified by '1234556';
Query OK, 0 rows affected (0.02 sec)

mysql> alter table Students add column sex text;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show columns from Students;
+----------------+------------+------+-----+---------+----------------+
| Field          | Type       | Null | Key | Default | Extra          |
+----------------+------------+------+-----+---------+----------------+
| id             | int        | NO   | PRI | NULL    | auto_increment |
| FirstName      | mediumtext | YES  |     | NULL    |                |
| Secondname     | mediumtext | YES  |     | NULL    |                |
| Description    | longtext   | YES  |     | NULL    |                |
| Dateofbirthday | date       | YES  |     | NULL    |                |
| Course         | int        | YES  |     | NULL    |                |
| sex            | text       | YES  |     | NULL    |                |
+----------------+------------+------+-----+---------+----------------+
7 rows in set (0.00 sec)

mysql> create table TEST (id INT);
Query OK, 0 rows affected (0.07 sec)

mysql> rename table TEST to EXAMPLE;
Query OK, 0 rows affected (0.02 sec)

mysql> drop table EXAMPLE;
Query OK, 0 rows affected (0.03 sec)

### DML ###

mysql> delete from Students where FirstName='John Doe';
Query OK, 0 rows affected (0.00 sec)


### DCL ###

mysql> grant select on STUDENTS.Students to 'epam_user_0'@'localhost';
Query OK, 0 rows affected, 1 warning (0.01 sec)


```
### 8. Create a database of new users with different privileges. Connect to the database as a new user and verify that the privileges allow or deny certain actions.
```

```
### 9. Make a selection from the main table DB MySQL.
