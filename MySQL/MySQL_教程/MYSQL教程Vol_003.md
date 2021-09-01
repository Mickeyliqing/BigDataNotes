# MYSQL教程Vol_003
## 问题_01：获取所有部门中当前员工薪水最高的相关信息，给出dept_no , emp_no 以及其对应的 salary。

- 分析问题

**原话**：获取所有部门中当前员工薪水最高的相关信息，给出 dept_no,
emp_no 以及其对应的 salary。

**分析后的语句**：查找员工的dept_no，emp_no 和 salary，条件是所有部门，薪水最高。

- 查询语句

```sql
    -- 查找员工的 dept_no，emp_no 和 salary
    select a.dept_no from dept_emp a 
    select b.emp_no, max(b.salary) from salaries b
    
    -- 如何把这两张表给关联起来？
    -- 寻找这两张表的关系。
    -- 可以通过建表语句，寻找表结构信息。
    -- 发现在这两张表中存在 `a.emp_no = b.emp_no` 这样的一种关联
    -- 完整的语句：
    select a.dept_no, b.emp_no ,max(b.salary) from dept_emp a LEFT JOIN salaries b on a.emp_no = b.emp_no GROUP BY dept_no ;
```

**解释说明**

1. 这里使用 GROUP BY 分组语句，将每个部门分成一个组，然后在使用max() 函数查出每个组内薪水最高的。


## 问题_02：从 titles 表获取按照 title 进行分组，每组个数大于等于 2，给出 title 以及对应的数目 t。

- 分析问题

**原话**：从 titles 表获取按照 title 进行分组，每组个数大于等于 2，给出 title 以及对应的数目 t。

**分析后的语句**：查找 title 和 title 的数目，条件 title 的数目大于 2 。

- 查询语句

```sql
    -- 查找 title 和 title 的数目
    select t.title,COUNT(t.title) as ti from titles
    
    -- 如何分组？
    -- 使用 GROUP BY 关键字
    -- 如何判断分组后的数目大于 2 ？
    -- 如何在使用 GROUP BY 关键字分组后判断分组数目大于 2 ？
    -- 使用关键字 HAVING 
    -- 完整的语句：
    select t.title, COUNT(t.title) as ti from titles t GROUP BY t.title HAVING ti >= 2;
```

**解释说明**

1. GROUP BY HAVING  这是采用 GROUP BY 分组后在进行判断的完整语法结构。
2. 其中 HAVING 必须在 GROUP BY 之后使用。
3. 有 GROUP BY 才能使用 HAVING 。HAVING 不能单独使用。

## 问题_03：查找 employees 表所有 emp_no 为奇数，且 last_name 不为Mary 的员工信息，并按照 hire_date 逆序排列。

- 分析问题

**原话**：查找 employees 表所有 emp_no 为奇数，且 last_name 不为Mary 的员工信息，并按照 hire_date 逆序排列。

**分析后的语句**：查找员工信息从 employees 表，条件是 emp_no  为奇数，last_name  不等于 Mary ，并按照 hire_date 逆序排序。

- 查询语句

```sql
    -- 查找查找员工信息从 employees 表
    select e.* from employees e
    
    -- emp_no  为奇数？
    -- 结合数学上求奇数的思路，对2取余数为1。
    -- 那是否可以设计成 emp_no % 2 = 1 ？
    -- last_name  不等于 Mary ？
    -- ‘不等于’ 。采用操作符 '!=' 。
    -- hire_date 逆序排序 ?
    -- ORDER BY DESC 关键字。
    --完整的语句：
    select e.* from employees e where e.emp_no % 2 = 1 and e.last_name != 'Mary ' ORDER BY e.hire_date DESC;
```

**解释说明**

1. 不等于 != 一定要用这样的格式，中间不能有空格。写成 ! = 这样是不正确的。


## 问题_04：获取薪水第二多的员工的 emp_no 以及其对应的薪水 salary。

- 分析问题

**原话**：获取薪水第二多的员工的 emp_no 以及其对应的薪水 salary。

**分析后的语句**：查找员工的 emp_no 和 salary ，条件是 salary 为第二。

- 查询语句

```sql
    -- 查找员工的 emp_no 和 salary
    select s.emp_no, s.salary from salaries s
    
    -- salary 为第二？
    -- 平时遇到的都是最大或者最小，比最大的小一点点怎么判断设计？
    -- 先看如何取最大值，一般使用 max(salary)。
    -- 如果把最大值取出来，’SELECT MAX(salary) from salaries‘。
    -- 工资表剩余的工资都是小于这个最大值的数据，只是这个时候没显示。
    -- 如何让这些数据显示 ？
    -- 先添加条件判断 ’s.salary < (SELECT MAX(salary) from salaries)‘。那么前面的查询就简单了。’select * from salaries s where s.salary < (SELECT MAX(salary) from salaries)‘。
    -- 然后在从这些比最大值小的数据中取最大值，那么这个第二次取到的最大值不就是第二的数据了。只需要把上面的语句改成 ’select MAX(salary) from salaries s where s.salary < (SELECT MAX(salary) from salaries)‘ 即可。
    -- 整个查询的条件是 salary 为第二，而第二的数据也已经查出来了，那么只需要在条件判断里设计成 salary 等于刚刚查出来的数据即可。
    -- 完整的语句：
    select s.emp_no, s.salary from salaries s where s.salary = (select MAX(salary) from salaries s where s.salary < (SELECT MAX(salary) from salaries));
```

**解释说明**

1. 这里的难点其实就是如何找出第二的数据。认真看上面的分析思路，会发现涉及到了很对的子查询。其实对子查询而言，就从最里层开始分析，在复杂的子查询也是有最基础的查询拼接而成的。
2. 找到最基础的查询，先把满足条件的一部分数据查询出来。
3. 在找到各个查询的关联，到这一步问题基本上就解决的差不多了。




