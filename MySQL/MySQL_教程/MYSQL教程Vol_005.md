# MYSQL教程Vol_005
## 问题1：构造一个触发器 audit_log，在向 employees 表中插入一条数据的时候，触发插入相关的数据到 film 表中。

- 分析问题

**原话**：构造一个触发器 audit_log，在向 employees 表中插入一条数据的时候，触发插入相关的数据到 film 表中。

**分析后的语句**：在向 employees 表中添加数据的时候，也向表 film 中添加数据。这个同步的动作，有触发器来自动操作。

- 语句

```sql
    -- 向 employees 表中添加数据 
    INSERT INTO employees VALUES ();
    
    -- 向 film 表中添加数据 
    INSERT INTO film VALUES ();
    
    -- 把这两个动作关联起来？
    -- 通过触发器来构成？
    -- 完整的语句：
    CREATE TRIGGER audit_log AFTER INSERT ON employees FOR EACH ROW
    BEGIN
    		INSERT INTO film
    	VALUES
    		( 01, 'AA', '01AA' );
    END;
    
```

**解释说明**

1. 创建触发器的固定语法：
       CREATE TRIGGER <触发器名称> <触发时机> <触发事件> ON <表名> FOR EACH ROW
       <触发后执行的语句>;
2. 就是记住这种通用的语法结构即可。

## 问题2：删除 salaries 表内 emp_no 重复的记录，只保留最小的 id 对应的记录。

- 分析问题

**原话**：删除 salaries 表内 emp_no 重复的记录，只保留最小的 emp_no  对应的记录。

**分析后的语句**：删除表 salaries 内的数据，数据必须是重复的，条件是只保留最小的 emp_no  对应的记录。

- 语句

```sql
 -- 删除表 salaries 内的数据
    DELETE from salaries ;
    
    -- 只保留最小的 emp_no 对应的记录？
    -- 先把最小的 emp_no 的记录的数据给查出来
    select MIN(d.emp_no) from salaries d;
    -- 这样查出来的数据就一条，显然不满足。
    -- 那么如果显示多条的最小记录数据？
    -- 如果查最小的 emp_no 记录，并且以 emp_no 分组，那么数据也就符合条件了。
    select MIN(d.emp_no) from salaries d GROUP BY d.emp_no;
    
    -- 拿到了以 emp_no 为分组的全部最小记录数据，保留这些数据即可。
    -- 而 UPDATE 或 DELETE 中子查询不能为同一张表，所以应该做适当的改造。
    select * from (select MIN(d.emp_no) from salaries d GROUP BY d.emp_no) as a;
    -- 完整的语句：
    DELETE from salaries where emp_no NOT IN (select * from (select MIN(d.emp_no) from salaries d GROUP BY d.emp_no) as a);
    
```

**解释说明**

1. 在只查询中最好给别名，否则容易出错。

## 问题3：查找排除当前最大、最小 salary 之后的员工的平均工资avg_salary。

- 分析问题

**原话**：查找排除当前最大、最小 salary 之后的员工的平均工资 avg_salary。

**分析后的语句**：找到员工的平均工资，条件是去掉最大值和最小值的员工工资。

- 语句

```sql
    -- 查找员工的平均工资
    select avg(s.salary) as salary from salaries ;
    
    -- 查找工资的最大值
    SELECT MAX(s.salary) from salaries s;
    
    -- 查找工资的最小值
    SELECT MIN(s.salary) from salaries s;
    
    -- 只需要保证要查询的数据条件不在这两个子查询中即可
    -- 完整的语句：
    select avg(s.salary) as salary from salaries s where s.salary not in (SELECT MAX(s.salary) from salaries s) and s.salary not in (SELECT MIN(s.salary) from salaries s);
    
```

**解释说明**

1. 其实还是子查询。


