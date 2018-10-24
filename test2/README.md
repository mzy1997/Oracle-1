# Oracle
第1步：以system登录到pdborcl，创建角色xushen和用户xushen_user，并授权和分配空间：
$ sqlplus system/123@pdborcl
SQL> CREATE ROLE xushen;
Role created.
SQL> GRANT connect,resource,CREATE VIEW TO xushen;
Grant succeeded.
SQL> CREATE USER xushen_user IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
User created.
SQL> ALTER USER xushen_user QUOTA 50M ON users;
User altered.
SQL> GRANT xushen TO new_user;
Grant succeeded.
SQL> exit
语句“ALTER USER xushen_user QUOTA 50M ON users;”是指授权xushen_user用户访问users表空间，空间限额是50M。

第2步：新用户xushen_user连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。
$ sqlplus xushen_user/123@pdborcl
SQL> show user;
USER is "XUSHEN_USER"
SQL> CREATE TABLE mytable (id number,name varchar(50));
Table created.
SQL> INSERT INTO mytable(id,name)VALUES(1,'zhang');
1 row created.
SQL> INSERT INTO mytable(id,name)VALUES (2,'wang');
1 row created.
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
View created.
SQL> SELECT * FROM myview;
NAME
--------------------------------------------------
zhang
wang
SQL> GRANT SELECT ON myview TO hr;
Grant succeeded.
SQL>exit
第3步：用户hr连接到pdborcl，查询xushen_user授予它的视图myview
$ sqlplus hr/123@pdborcl
SQL> SELECT * FROM xushen_user.myview;
NAME
--------------------------------------------------
zhang
wang
SQL> exit


以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。

$ sqlplus system/123@pdborcl

SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
