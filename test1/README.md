# Oracle
教材中的查询语句
查询1：

SELECT d.department_name，count(e.job_id)as "部门总人数"，<br>
avg(e.salary)as "平均工资"<br>
from hr.departments d，hr.employees e<br>
where d.department_id = e.department_id<br>
and d.department_name in ('IT'，'Sales')<br>
GROUP BY department_name;<br>

查询2：
SELECT d.department_name，count(e.job_id)as "部门总人数"，<br>
avg(e.salary)as "平均工资"<br>
FROM hr.departments d，hr.employees e<br>
WHERE d.department_id = e.department_id<br>
GROUP BY department_name<br>
HAVING d.department_name in ('IT'，'Sales');<br>
