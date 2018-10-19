# Oracle
![image](https://github.com/shengxu123456uuuu/Oracle/blob/master/test1/1.png)

教材中的查询语句
查询1：

SELECT d.department_name，count(e.job_id)as "部门总人数"，<br>
avg(e.salary)as "平均工资"<br>
from hr.departments d，hr.employees e<br>
where d.department_id = e.department_id<br>
and d.department_name in ('IT'，'Sales')<br>
GROUP BY department_name;<br>

![image](https://github.com/shengxu123456uuuu/Oracle/blob/master/test1/2.png)<br>

查询2：
SELECT d.department_name，count(e.job_id)as "部门总人数"<br>
avg(e.salary)as "平均工资"<br>
FROM hr.departments d，hr.employees e<br>
WHERE d.department_id = e.department_id<br>
GROUP BY department_name<br>
HAVING d.department_name in ('IT'，'Sales');<br>


实验分析:通过对以上两个sql语句运行，查询结果相同，但后者所用时间更少，因此更优,优化指导可以增加一个索引,通过索引搜索更快。<br><br>

实验代码：
SELECT d.department_name，count(e.job_id)as "部门总人数"<br>
avg(e.salary)as "平均工资"<br>
FROM hr.departments d，hr.employees e<br>
WHERE d.department_id = e.department_id<br>
INDEX UNIQUE  SCAN d.department_name<br>
