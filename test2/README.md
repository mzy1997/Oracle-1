# Oracle
第1步：以system登录到pdborcl，创建角色xushen和用户xushen_user，并授权和分配空间：<br>
$ sqlplus system/123@pdborcl<br>
SQL> CREATE ROLE xushen;<br>
Role created.<br>
SQL> GRANT connect,resource,CREATE VIEW TO xushen;<br>
Grant succeeded.<br>
SQL> CREATE USER xushen_user IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;<br>
User created.<br>
SQL> ALTER USER xushen_user QUOTA 50M ON users;<br>
User altered.<br>
SQL> GRANT xushen TO new_user;<br>
Grant succeeded.<br>
SQL> exit<br>
语句“ALTER USER xushen_user QUOTA 50M ON users;”是指授权xushen_user用户访问users表空间，空间限额是50M。<br><br>

第2步：新用户xushen_user连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。<br>
$ sqlplus xushen_user/123@pdborcl<br>
SQL> show user;<br>
USER is "XUSHEN_USER"<br>
SQL> CREATE TABLE mytable (id number,name varchar(50));<br>
Table created.<br>
SQL> INSERT INTO mytable(id,name)VALUES(1,'zhang');<br>
1 row created.<br>
SQL> INSERT INTO mytable(id,name)VALUES (2,'wang');<br>
1 row created.<br>
SQL> CREATE VIEW myview AS SELECT name FROM mytable;<br>
View created.<br>
SQL> SELECT * FROM myview;<br>
NAME<br>
--------------------------------------------------<br>
zhang<br>
wang<br>
SQL> GRANT SELECT ON myview TO hr;<br>
Grant succeeded.<br>
SQL>exit<br>
第3步：用户hr连接到pdborcl，查询xushen_user授予它的视图myview<br><br>
$ sqlplus hr/123@pdborcl<br>
SQL> SELECT * FROM xushen_user.myview;<br>
NAME<br>
--------------------------------------------------<br>
zhang<br>
wang<br>
SQL> exit<br><br>
<br>

以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。<br>

$ sqlplus system/123@pdborcl<br>

SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';<br>

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",<br>
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",<br>
 Round(( total - free )/ total,4)* 100 "使用率%"<br>
 from (SELECT tablespace_name,Sum(bytes)free<br>
        FROM   dba_free_space group  BY tablespace_name)a,<br>
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files<br>
        group  BY tablespace_name)b<br>
 where  a.tablespace_name = b.tablespace_name;<br>
