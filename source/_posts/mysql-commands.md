---
title: MySQL Learning Notes
date: 2018-12-13 15:48:28
tags: database, mysql
categories: database
---

## Data Definition Language (DDL)

### Database

- `SHOW DATABASES;`
- `USE <name of database>;`
- `CREATE DATABASE <name of database>;`
- `DROP DATABASE <name of database>;`

### Table

- `SHOW TABLES`;
- ```Sql
  CREATE TABLE <name of table>(
   ​	<column name1>  <column type1>  <extra>,
     	<column name2>  <column type2>  <extra>,
     	PRIMARY KEY(<a column name>)
   );```
- `DROP TABLE <name of table>;`
- `ALTER TABLE <name of table> ADD <column name> <column type> AFTER <another column name>;`
- `ALTER TABLE <name of table> DROP <column name>;`
- `ALTER TABLE <name of table> MODIFY <column name> <column type>;`
- `ALTER TABLE <name of table> CHANGE <old column name> <new column name> <type of new column>;`
- `ALTER TABLE <name of table> RENAME (TO) <new name of table>;`

**An Example**

```SQL
##################### DATABASE #####################
# show all databases in mysql
SHOW DATABASES;
/*
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| shop               |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
*/

# choose a database
USE shop;
/*
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
*/

# create a new database named "new_set"
CREATE DATABASE new_set;
/*
Query OK, 1 row affected (0.03 sec)
*/

# delete the database named "new_set"
DROP DATABASE new_set;
/*
Query OK, 0 row affected (0.03 sec)
*/

##################### TABLE #####################
# create a table in database "shop"
CREATE TABLE user(
    id INT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT 'id',
    user_name VARCHAR(20) NOT NULL COMMENT '用户名',
    email VARCHAR(50) NOT NULL COMMENT '邮箱',
    age TINYINT UNSIGNED NOT NULL COMMENT '用户年龄',
    fee DECIMAL(10,2) NOT NULL DEFAULT 0.00 COMMENT'余额'，
    created_at TIMESTAMP NOT NULL COMMENT '注册时间',
    PRIMARY KEY(id)
);

# show all tables in database 'shop'
SHOW TABLES;
/*
+----------------+
| Tables_in_shop |
+----------------+
| user           |
+----------------+
1 row in set (0.00 sec)
*/

# show the information of the table
DESC user;

# add one column
ALTER TABLE user ADD password CHAR(32) NOT NULL COMMENT 'Password';

# delete one column
ALTER TABLE user DROP password;

# change the type of one column
ALTER TABLE user MODIFY password CHAR(64) NOT NULL COMMENT 'Password';

# change the name of one column
ALTER TABLE user CHANGE password password1 CHAR(64) NOT NULL COMMENT 'Password1';

# change the name of the table 'user' to 'User'
ALTER TABLE user RENAME User;
ALTER TABLE user RENAME TO User;
```

---

## Data Manipulation Language (DML)

### Insert New Data into Table

- `INSERT INTO <name of table> (<column1>,  <column2>, <column3>) VALUES (<value1>, <value2>, <value3>);`

  - `INSERT INTO <name of table> VALUES (<value1>, <value2>, <value3>);`

    > This will insert a data with values in order with columns.

### Update Data in Table

- `UPDATE <name of table> SET <column1>=<value1>, <column2>=<value2> WHERE <conditions>;`

  - `UPDATE <name of table> SET <column1>=<value1>, <column2>=<value2> ;`

  > This will update the columns of all data in the table.

### Delete Data from Table

- `DELETE FROM <name of table> WHERE <conditions>;`

  - `DELETE FROM <name of table>; `

    > **WARNING**: This would clear all the data of the table, but the id won’t be clear.

- `TRUNCATE <name of table>;`

  > **WARNING**: This would clear all the data of the table, with id also cleared.

**An Example**

```SQL
# show the contents of the table user
SELECT * FROM user;

# insert data
INSERT INTO user(user_name, user_email) VALUES('mike', 'mike@gmail.com');

# update data
UPDATE user SET mobile_phone='13888888888', fee='123.45' WHERE id BETWEEN 2 AND 5;

# delete the data with id=5
DELETE FROM user WHERE id=5;

# delete all the data without id
DELETE FROM user;

# delete all the data, as well as id
TRUNCATE user;
```



---

## Data Control Language (DCL)

### Change Password

```SQL
# change password
USE mysql;

# if the version of mysql is 5.7 or older, use:
-- SELECT user, host, authentication_string FROM user;
UPDATE user SET authentication_string=PASSWORD('new_password') WHERE user='root' AND host='localhost';

# elif the version of mysql is less than 5.7, use:
-- SELECT user, host, password FROM user;
UPDATE user SET password=PASSWORD('new_password') WHERE user='root' AND host='localhost';

# then flush the privileges;
FLUSH PRIVILEGES;
EXIT;
```

### Create User

- `CREATE USER <user name>@<user IP> IDENTIFIED BY <user passoword>;`

### Grant Privileges to User

- `GRANT <privilege1>, <privilege2> ON <database>.<table> TO <user>@<user IP>;`

  - Granting all privileges uses `ALL` or `ALL PRIVILEGES` instead of `<privilege1>, <privilege2>`.
  - Granting all tables of a database uses `<database>.*` instead of `<database>.<table>`.
  - Granting all databases uses `*.*` instead of `<database>.<table>`.

- Combining ‘create user’ and ‘grant privileges’ uses:

  `GRANT <privilege1>, <privilege2> ON <database>.<table> TO <new username>@<new user IP> IDENTIFIED BY 'new user password';`

### Revoke Privileges from User

- `REVOKE <privilege1>, <privilege2> ON <database>.<table> FROM <user>@<user IP>; `
  - Revoking privileges from all IP uses `<user>@'%'` instead of `<user>@<user IP>`.

### Show Privileges for User

- `SHOW GRANTS FOR `<user>@<user IP>;

### Delete User

- `DROP USER `<user>@<user IP>;

---

## Data Query Language (DQL)

### Basic Query Display

- `SELECT <column1> (AS <shorthand1>), <column2> (AS <shorthand2>), <column3> (AS <shorthand3>) FROM <table> WHERE <conditions>;`

  - `WHERE <conditions>` could be omitted.

  - Listing all the data in the table uses:

    `SELECT * FROM <table>;`

  **An Example**

  ```SQL
  # list user_name, user_email, mobile_phone of all data from user table.
  SELECT user_name, user_email, mobile_phone FROM user;
  /*
  +-----------+----------------+--------------+
  | user_name | user_email     | mobile_phone |
  +-----------+----------------+--------------+
  | jack      | jack@163.com   | 12555555555  |
  | mike      | mike@gmail.com | 13888888888  |
  | 马克      | haha@gmail.com | 13888888888  |
  +-----------+----------------+--------------+
  */

  # list the item where user_name is 'jack'.
  SELECT user_name, user_emial, mobile_phone FROM user WHERE user_name='jack';
  /*
  +-----------+--------------+--------------+
  | user_name | user_email   | mobile_phone |
  +-----------+--------------+--------------+
  | jack      | jack@163.com | 12555555555  |
  +-----------+--------------+--------------+
  */

  # using shorthands
  SELECT user_name as name, user_email as email FROM user;
  /*
  +------+----------------+
  | name | email          |
  +------+----------------+
  | jack | jack@163.com   |
  | mike | mike@gmail.com |
  | 马克 | haha@gmail.com |
  +------+----------------+
  */
  ```

### Query Filtered Repeated Data

- `SELECT DISTINCT <only one column> FROM <table>;`

### Query Concatenated Columns

- `SELECT CONCAT(<column1>, <column2>, .........., <columnN>) AS <concated_name> FROM <table>;`

  - `SELECT CONCAT_WS(<seperator>, <column1>, <column2>, ..., <columnN>) AS <concated_name> FROM <table>`;

  **An Example**

  ```SQL
  SELECT CONCAT(user_name, user_emial, mobile_phone) AS 'user_name_email_phone' FROM user;
  /*
  +-------------------------------+
  | user_name_email_phone         |
  +-------------------------------+
  | jackjack@163.com12555555555   |
  | mikemike@gmail.com13888888888 |
  | 马克haha@gmail.com13888888888 |
  +-------------------------------+
  */

  # Add a seperator.
  SELECT CONCAT_WS('::', user_name, user_emial, mobile_phone) AS 'user_name_email_phone' FROM user;
  /*
  +-----------------------------------+
  | user_name_email_phone             |
  +-----------------------------------+
  | jack::jack@163.com::12555555555   |
  | mike::mike@gmail.com::13888888888 |
  | 马克::haha@gmail.com::13888888888 |
  +-----------------------------------+
  */
  ```

### Query by Fuzzy Search

- `SELECT <column1>, <column2> FROM <table> WHERE <one column> LIKE <%something%>;`

  > `Where <one column> LIKE <%something%>` could only be used when the size of the dataset is not so large. If the dataset is so large that we don’t use `LIKE` command which would costs several seconds, one way is using [Sphinx](https://en.wikipedia.org/wiki/Sphinx_(search_engine)).

  **An Example**

  ```SQL
  SELECT user_name, user_email FROM user WHERE user_email like '%gmail%';
  /*
  +-----------+----------------+
  | user_name | user_email     |
  +-----------+----------------+
  | mike      | mike@gmail.com |
  | 马克      | haha@gmail.com |
  +-----------+----------------+
  */
  ```

### Query by Sorting Data

- **Ascending**: `SELECT <column1>, <column2> FROM <table> ORDER BY <one column> (ASC);`

- **Descending**: `SELECT <column1>, <column2> FROM <table> ORDER BY <one column> DESC;`

### Query by using Aggregate Function

- **Count**: `SELECT COUNT(<column>) FROM <table>;`
- **Sum**: `SELECT SUM(<column>) FROM <table>;`
- **Average**: `SELECT AVG(<column>) FROM <table>;`
- **Maximum**: `SELECT MAX(<column>) FROM <table>;`
- **Minimum**: `SELECT MIN(<column>) FROM <table>;`

### Group Query

- `SELECT COUNT(*) FROM <table> GROUP BY <one column>;`

 ```SQL
# Group by 'mobile_phone'.
SELECT mobile_phone, COUNT(*) as totals FROM user GROUP BY mobile_phone;
/*
+--------------+--------+
| mobile_phone | totals |
+--------------+--------+
| 12555555555  | 1      |
| 13888888888  | 2      |
+--------------+--------+
*/
 ```

- `SELECT <one column> FROM <table> GROUP BY <one column> HAVING COUNT(*)<conditions> ;`

```SQL
# Group by age and display the age(s) that are counted to be bigger than 2.
SELECT age FROM user GROUP BY age HAVING COUNT(*)>2;
/*
+-----+
| age |
+-----+
| 0   |
| 21  |
| 23  |
+-----+
*/
```

### Query by connecting two tables

- **Inner join: ** `SELECT <table 1>.<one column>, <table 2>.<one column> from <table 1>, <table 2> where <conditions>;  `

  - another form: `SELECT <table 1>.<one column>, <table 2>.<one column> from <table 1> INNER JOIN <table 2> where <conditions>;`

- **Left join: ** `SELECT <table 1>.<one column>, <table 2>.<one column> from <table 1> LEFT JOIN <table 2> where <conditions>;`

  > Left join means to show the whole selected column of the left table (table 1), and then join the selected column of the right table (table 2).

- **Right join: ** `SELECT <table 1>.<one column>, <table 2>.<one column> from <table 1> RIGHT JOIN <table 2> where <conditions>;`

  > Right join is just the opposite with the left join, which is to show the whole selected column from the right table (table 2), and then join the selected column from the left table (table 1).

- **Union all: ** `SELECT <column> FROM <table 1> UNION ALL SELECT <column> FROM <table 2>;`
- **Sub query: ** `SELECT <column 1> FROM <table 1> where <column 2> in (SELECT <column 2> FROM <table 2>);`

### Limit Query

- `SELECT <column> FROM <table> LIMIT <number>; `

  ```SQL
  SELECT id, name, age FROM student LIMIT 3;
  /*
  | id | name | age |
  +----+------+-----+
  | 1  | wang | 16  |
  | 2  | hong | 17  |
  | 3  | li   | 17  |
  +----+------+-----+
  */
  ```

  > Getting the length of the table could use:
  >
  > `SELECT id FROM student ORDER BY id DESC LIMIT 1; `
  >
  > instead of:
  >
  > `SELECT count(*) FROM student;`
  >
  > Because the former one is far more efficient.



---

## Transaction

- **Start transaction**: `START TRANSACTION;`
- **Roll back:** `ROLLBACK;`
- **Commit the transaction:** `COMMIT;`

**An example**

```SQL
# Using transaction to achieve a transfer service.

USE shop;
START TRANSACTION;
UPDATE user SET fee=fee-100 WHERE id=2;
UPDATE user SET fee=fee+100 WHERE id=3;

# If the transaction goes wrong
ROLLBACK;
# If the transaction accomplished
COMMIT;
```

---

## View

View is used as a query table.

- **Create a view**:  `CREATE VIEW <name of view> (column1, column2, ...) AS <SELECT commands>;`
- **Show the creating information:** `SHOW CREATE VIEW <name of view>;`
- **Alter a view:** `ALTER VIEW <name of view> (column1, column2, ...) AS <SELECT commands>;`
- **Query the view:** `SELECT * FROM <name of view>;`
- **Delete a view:** `DROP VIEW <name of view>;`

**An example**

```SQL
# Create a view named 'test_view'
create view test_view(id, name, mark) as select s.id, s.name, m.mark from student as s, mark as m where s.id=m.stu_id;

# Show the creating information
show create view test_view\G;
/*
***************************[ 1. row ]***************************
View                 | test_view
Create View          | CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `test_view` AS select `s`.`id` AS `id`,`s`.`name` AS `name`,`m`.`mark` AS `mark` from (`student` `s` join `mark` `m`) where (`s`.`id` = `m`.`stu_id`)
character_set_client | utf8
collation_connection | utf8_general_ci
*/

# Query the view
select * from test_view;
/*
+----+---------+------+
| id | name    | mark |
+----+---------+------+
| 2  | bruce   | 66   |
| 4  | timothy | 86   |
| 6  | mike    | 90   |
+----+---------+------+
3 rows in set
*/

# Delete the view
drop view test_view;
```

---

## Trigger

Trigger a series of operations before or after some operations (insert, update, delete) on a table.

- **Create a trigger:**

```SQL
CREATE TRIGGER <name of trigger> AFTER|BEFORE INSERT|UPDATE|DELETE ON <name of table> FOR EACH ROW
BEGIN
		SQL commands
END
```

- **Show the creating information:** `SHOW CREATE TRIGGER <name of trigger>;`
- **Delete the trigger:** `DROP TRIGGER <name of trigger>;`

**An example**

```SQL
# The total_num table shows the numbers of rows of each table.
select * from student;	# student table
/*
+----+-----------+-----+
| id | name      | age |
+----+-----------+-----+
| 1  | jack      | 18  |
| 2  | bruce     | 19  |
| 3  | emma      | 19  |
| 4  | timothy   | 20  |
| 5  | chayatish | 20  |
| 6  | mike      | 20  |
| 7  | alice     | 18  |
| 8  | hong      | 18  |
| 9  | 王        | 18  |
| 10 | 李        | 18  |
| 11 | hey       | 19  |
+----+-----------+-----+
*/

select * from mark;		# mark table
/*
+----+------+--------+
| id | mark | stu_id |
+----+------+--------+
| 1  | 66   | 2      |
| 2  | 86   | 4      |
| 3  | 90   | 6      |
| 4  | 85   | 7      |
+----+------+--------+
*/

select * from total_num;	# total number of each table
/*
+----+---------+-----+
| id | type    | num |
+----+---------+-----+
| 1  | student | 11  |
| 2  | mark    | 4   |
+----+---------+-----+
*/

# Define triggers that could sync the total_num table to student table and mark table.

# Increase the number of student after insert a student.
create trigger after_insert_stu after insert on student for each row
begin
	update total_num set num=num+1 where type='student';
end;

# Increase the number of mark after insert a mark.
create trigger after_insert_mark after insert on mark for each row
begin
	update total_num set num=num+1 where type='mark';
end;

# Decrease the number of student after delete a student.
create trigger after_delete_stu after delete on student for each row
begin
	update total_num set num=num-1 where type='student';
end;

# Decrease the number of mark after delete a mark.
create trigger after_delete_mark after delete on mark for each row
begin
	update total_num set num=num-1 where type='mark';
end;


# Test of the triggers
select * from total_num;
/*
+----+---------+-----+
| id | type    | num |
+----+---------+-----+
| 1  | student | 11  |
| 2  | mark    | 4   |
+----+---------+-----+
*/
insert into student(name, age) values('tish', 17);
select * from total_num;
/*
+----+---------+-----+
| id | type    | num |
+----+---------+-----+
| 1  | student | 12  |
| 2  | mark    | 4   |
+----+---------+-----+
*/
```

---

## Import and export file

- **Export SQL file:**
  - `$ mysqldump -u <username> -p <password> <database> <table name> > <filename.sql>`
  - Using navicat: `Dump SQL File `

- **Import SQL file:**

  - `$ mysql -u <username> -p <password> <database> < <filename.sql>`
  - Using navicat: `Execute SQL File`

---

## Optimization of MySQL

- Explain the query: `explain <query command>;`

```SQL
# use shop
explain select * from student where id=10\G;
/*
id            | 1
select_type   | SIMPLE
table         | student
partitions    | <null>
type          | const
possible_keys | PRIMARY
key           | PRIMARY
key_len       | 4
ref           | const
rows          | 1
filtered      | 100.0
Extra         | <null>
*/

explain select * from student where name='王'\G;
/*
id            | 1
select_type   | SIMPLE
table         | student
partitions    | <null>
type          | ALL
possible_keys | <null>
key           | <null>
key_len       | <null>
ref           | <null>
rows          | 13
filtered      | 10.0
Extra         | Using where
*/
```

- Profiles of the query commands:
  - Open the `profiling`: `set profiling=ON;`
  - Show profiles: `show profiles;`
  - Show the detailed duration of a query: `show profile for query <Query_ID>;`

```SQL
show variables like "%profil%";
/*
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| have_profiling         | YES   |
| profiling              | OFF   |
| profiling_history_size | 15    |
+------------------------+-------+
*/

# open the profiling
set profiling=ON;

# check whether profiling is on
show variables like "%profil%";
/*
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| have_profiling         | YES   |
| profiling              | ON    |
| profiling_history_size | 15    |
+------------------------+-------+
*/

# execute a few queries
select * from student where id=3;
select * from student where name='王';

# show profiles
show profiles;
/*
+----------+------------+---------------------------------------+
| Query_ID | Duration   | Query                                 |
+----------+------------+---------------------------------------+
| 1        | 0.000132   | SHOW WARNINGS                         |
| 2        | 0.00154275 | show variables like '%profil%'        |
| 3        | 3.85e-05   | SHOW WARNINGS                         |
| 4        | 0.000186   | select * from student where id=3      |
| 5        | 0.000284   | select * from student where name='王' |
+----------+------------+---------------------------------------+
*/

# show the detailed information of query 5;
show profile for query 5;
/*
+----------------------+----------+
| Status               | Duration |
+----------------------+----------+
| starting             | 0.000070 |
| checking permissions | 0.000009 |
| Opening tables       | 0.000017 |
| init                 | 0.000025 |
| System lock          | 0.000010 |
| optimizing           | 0.000010 |
| statistics           | 0.000016 |
| preparing            | 0.000013 |
| executing            | 0.000004 |
| Sending data         | 0.000051 |
| end                  | 0.000005 |
| query end            | 0.000008 |
| closing tables       | 0.000008 |
| freeing items        | 0.000019 |
| cleaning up          | 0.000019 |
+----------------------+----------+
*/
```

> If there is one status that costs too much time, perhaps it’s where the problem exists, and then you could come up with some solutions to it.
