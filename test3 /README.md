实验步骤：<br>
1.在系统表空间USRES，USERS02，USERS03创建orders，order_details两张表，用system给自己账户表空间的使用权限，两个表通过列order_id建立主外键关联<br>。orders表按order_date范围分区进行存储，order_details使用引用分区进行存储。 <br>
创建表代码如下：<br>
CREATE TABLE orders <br>
 (<br>
  order_id NUMBER(10, 0) NOT NULL  primary key<br>
  , customer_name VARCHAR2(40 BYTE) NOT NULL <br>
  , customer_tel VARCHAR2(40 BYTE) NOT NULL <br>
  , order_date DATE NOT NULL <br>
  , employee_id NUMBER(6, 0) NOT NULL <br>
  , discount NUMBER(8, 2) DEFAULT 0 <br>
  , trade_receivable NUMBER(8, 2) DEFAULT 0 <br>
 ) <br>
 TABLESPACE USERS<br>
 PCTFREE 10 INITRANS 1<br>
 STORAGE (   BUFFER_POOL DEFAULT )<br>
 NOCOMPRESS NOPARALLEL<br>
 PARTITION BY RANGE (order_date)<br>
 (<br>
  PARTITION PARTITION_BEFORE_2017 VALUES LESS THAN (<br>
  TO_DATE(' 2017-01-01 00:00:00', 'YYYY-MM-DD HH24:MI:SS',<br>
  'NLS_CALENDAR=GREGORIAN'))<br>
  NOLOGGING<br>
  TABLESPACE USERS<br>
  PCTFREE 10<br>
  INITRANS 1<br>
  STORAGE<br>
 (<br>
  INITIAL 8388608<br>
  NEXT 1048576<br>
  MINEXTENTS 1<br>
  MAXEXTENTS UNLIMITED<br>
  BUFFER_POOL DEFAULT<br>
 )<br>
 NOCOMPRESS NO INMEMORY<br>
 , PARTITION PARTITION_BEFORE_2018 VALUES LESS THAN (<br>
 TO_DATE(' 2018-01-01 00:00:00', 'YYYY-MM-DD HH24:MI:SS',<br>
 'NLS_CALENDAR=GREGORIAN'))<br>
 NOLOGGINGv
 TABLESPACE USERS02v
  PCTFREE 10v
  INITRANS 1v
  STORAGE<br>
 (<br>
  INITIAL 8388608<br>
  NEXT 1048576v
  MINEXTENTS 1<br>
  MAXEXTENTS UNLIMITED<br>
  BUFFER_POOL DEFAULT<br>
 )<br>
 NOCOMPRESS NO INMEMORY<br>
 , PARTITION PARTITION_BEFORE_2019 VALUES LESS THAN (<br>
 TO_DATE(' 2019-01-01 00:00:00', 'YYYY-MM-DD HH24:MI:SS',<br>
 'NLS_CALENDAR=GREGORIAN'))<br>
 NOLOGGING<br>
 TABLESPACE USERS03<br>
  PCTFREE 10<br>
  INITRANS 1<br>
  STORAGE<br>
 (<br>
  INITIAL 8388608<br>
  NEXT 1048576<br>
  MINEXTENTS 1<br>
  MAXEXTENTS UNLIMITED<br>
  BUFFER_POOL DEFAULT<br>
 )<br>
 NOCOMPRESS NO INMEMORY<br>
 );<br><br><br>
创建order_details表:<br>
CREATE TABLE order_details<br>
(<br>
id NUMBER(10, 0) NOT NULL<br>
, order_id NUMBER(10, 0) NOT NULL<br>
, product_id VARCHAR2(40 BYTE) NOT NULL<br>
, product_num NUMBER(8, 2) NOT NULL<br>
, product_price NUMBER(8, 2) NOT NULL<br>
, CONSTRAINT order_details_fk1 FOREIGN KEY  (order_id)<br>
REFERENCES orders  (order_id)<br>
ENABLE<br>
)<br>
TABLESPACE USERS<br>
PCTFREE 10 INITRANS 1<br>
STORAGE (   BUFFER_POOL DEFAULT )v
NOCOMPRESS NOPARALLEL<br>
PARTITION BY REFERENCE (order_details_fk1)<br>
(<br>
PARTITION PARTITION_BEFORE_2016<br>
NOLOGGING<br>
TABLESPACE USERS<br>
PCTFREE 10<br>
 INITRANS 1<br>
 STORAGE<br>
(<br>
 INITIAL 8388608<br>
 NEXT 1048576<br>
 MINEXTENTS 1<br>
 MAXEXTENTS UNLIMITED<br>
 BUFFER_POOL DEFAULT<br>
)<br>
NOCOMPRESS NO INMEMORY,<br>
PARTITION PARTITION_BEFORE_2017<br>
NOLOGGING<br>
TABLESPACE USERS02<br>
PCTFREE 10<br>
 INITRANS 1<br>
 STORAGE<br>
(<br>
 INITIAL 8388608<br>
 NEXT 1048576<br>
 MINEXTENTS 1<br>
 MAXEXTENTS UNLIMITED<br>
 BUFFER_POOL DEFAULT<br>
)<br>
NOCOMPRESS NO INMEMORY,<br>
PARTITION PARTITION_BEFORE_2018<br>
NOLOGGING<br>
TABLESPACE USERS03<br>
PCTFREE 10<br>
 INITRANS 1<br>
 STORAGE<br>
(<br>
 INITIAL 8388608<br>
 NEXT 1048576<br>
 MINEXTENTS 1<br>
 MAXEXTENTS UNLIMITED<br>
 BUFFER_POOL DEFAULT<br>
)<br>
NOCOMPRESS NO INMEMORY<br>
);<br>


<br><br><br>
orders表插入语句:<br>
declare<br>
temp varchar2(20):='aaaa';<br>
i number;<br>
begin<br>
for i in 1..10000 loop<br>
insert into orders(order_id,customer_name,customer_tel,order_date,employee_id,discount ,trade_receivable)values(i,'xx','123',TO_DATE('2016-5-25','YYYY-MM-DD'),i,23,233);<br>
end loop<br>
end;<br>

查询语句：<br>
select * from orders
