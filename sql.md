# 题目
1. 查找入职最晚员工的所有信息

查找最晚入职员工的所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天(sqlite里面的注释为--,mysql为comment)
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,  -- '员工编号'
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

```sql
SELECT emp_no, birth_date, first_name, last_name, gender, hire_date From employees 
WHERE hire_date = (
    SELECT hire_date FROM employees ORDER BY hire_date
    DESC LIMIT 1
);
```

2. 查找入职员工时间排名倒数第三的员工所有信息

查找入职员工时间排名倒数第三的员工所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

```sql
SELECT emp_no, birth_date, first_name, last_name, gender, hire_date From (
    SELECT emp_no, birth_date, first_name, last_name, gender, hire_date, RANK() OVER (ORDER BY hire_date DESC ) as rank_ From employees
) WHERE rank_ = 3;

select *
from
employees
where hire_date=(
    select distinct(hire_date) from employees order by hire_date desc limit 2,1);
```

3. 查找当前薪水详情以及部门编号

查找各个部门当前(dept_manager.to_date='9999-01-01')领导当前(salaries.to_date='9999-01-01')薪水详情以及其对应部门编号dept_no
(注:输出结果以salaries.emp_no升序排序，并且请注意输出结果里面dept_no列是最后一列)
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL, -- '员工编号',
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL, -- '部门编号'
`emp_no` int(11) NOT NULL, --  '员工编号'
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

emp_no	salary	from_date	to_date	dept_no

```sql
SELECT salaries.emp_no, salaries.salary, salaries.from_date, salaries.to_date, dept_manager.dept_no From salaries JOIN dept_manager ON dept_manager.emp_no = salaries.emp_no
AND salaries.to_date = '9999-01-01' AND dept_manager.to_date = '9999-01-01'
ORDER BY emp_no ASC;
```

4. 查找所有已经分配部门的员工的last_name和first_name以及dept_no

查找所有已经分配部门的员工的last_name和first_name以及dept_no(请注意输出描述里各个列的前后顺序)
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

```sql
SELECT employees.last_name, employees.first_name, dept_emp.dept_no 
FROM dept_emp JOIN employees ON dept_emp.emp_no = employees.emp_no
AND dept_emp.dept_no IS NOT NULL;

```