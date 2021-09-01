# MYSQL教程Vol_004
一直在学习查询，今天看点其它的，就当放松一下了。

## 问题1：将 employees 表的所有员工的 last_name 和 first_name 拼接起来作为Name，中间以一个空格区分。

- 分析问题

**原话**：将 employees 表的所有员工的 last_name 和 first_name 拼接起来作为name，中间以一个空格区分。

**分析后的语句**：拼接 last_name 和 first_name ，中间加空格。

- 语句

```sql
    -- 拼接 last_name 和 first_name 
    CONCAT(e.first_name,e.last_name)
    
    -- 中间加空格？
    -- 需要注意的是这里的空格是`' '`这样写的。
    -- 完整的语句：
    select CONCAT(e.first_name,' ',e.last_name) as name from employees e ;
```

**解释说明**

1. SQL 中拼接两个属性字段使用 CONCAT() 关键字。
2. 空格是 ' ' 表示。

## 问题2：创建一个 actor_name 表，将 actor 表中的所有 first_name以及 last_name 导入改表。

- 分析问题

**原话**：创建一个 actor_name 表，将 actor 表中的所有 first_name以及 last_name 导入改表。

**分析后的语句**：创建一个 actor_name 表，这个表应该有两个属性字段，分别是 first_name 和 last_name ，并且这两个属性字段里需要有值，值来至表 actor 。

- 语句

```sql
    -- 创建一个 actor_name 表，字段为 last_name 和 first_name 
    -- 然后在把 actor 表里面的这两个字段的值导出来。
    -- 在把导出的数据导入到新建的 actor_name 表内。
    -- 感觉有点麻烦 ？
    -- 能不能简单点 ？
    -- 完整的语句：
    create table actor_name
    select first_name,last_name from actor;
```

**解释说明**

1. 没什么好说的，格式就是这样的。

## 问题3：针对表 actor 结构创建索引 ， 对 first_name 创建唯一索引，对 last_name 创建普通索引。

- 分析问题

**原话**：针对表 actor 结构创建索引 ， 对 first_name 创建唯一索引，对 last_name 创建普通索引。

**分析后的语句**：对 first_name 创建唯一索引，对 last_name 创建普通索引。

- 语句

```sql
    --创建唯一索引
    CREATE UNIQUE INDEX uniq_idx_firstname ON actor(first_name);
    
    --创建普通索引
    CREATE INDEX idx_lastname ON actor(last_name);
```

**解释说明**

1. 索引：可以简单的理解成是数据库中的一种数据结构，使用索引的目的是为了提高查询速度。
2. 唯一索引：索引的值必须唯一。
3. 普通索引：可以相对唯一索引理解，允许有重复的值。
4. 创建唯一索引的语法：CREATE UNIQUE INDEX <唯一索引值> ON <表名>(列名);
5. 创建普通索引的语法：CREATE INDEX <唯一索引值> ON <表名>(列名);

## 问题4：针对表 actor 创建视图 ，只包含 first_name 和 last_name ，并对这两列重新命名。

- 分析问题

**原话**：针对表 actor 创建视图 ，只包含 first_name 和 last_name ，并对这两列重新命名。

**分析后的语句**：创建一个视图，包含 first_name 和 last_name，但名字不是first_name 和 last_name。

- 语句

```sql
    --创建视图
    CREATE VIEW actor_view(first_name_v,last_name_v)
    AS
    select first_name,last_name from actor;
```

**解释说明**

1. 创建视图的语法 :

```sql
 CREATE VIEW <视图名称> (<视图列名>,<视图列名>，<...>)
       AS
       <select 语句>;
```

## 问题5：针对表 salaries 创建 emp_no 的索引  ，并使用强制索引查询对应的数据。

- 分析问题

**原话**：针对表 salaries 创建 emp_no 的索引  ，并使用强制索引查询对应的数据。

**分析后的语句**：创建 emp_no 的索引  ，然后使用这个索引查询。

- 语句

```sql
    --创建 emp_no 的索引
    CREATE INDEX idx_emp_no ON salaries(emp_no);
    
    -- 使用这个索引强制查询
    FORCE INDEX (idx_emp_no)
    
    -- 完整语句
    select * from salaries FORCE INDEX (idx_emp_no)
```

**解释说明**

1. 使用强制索引查询的语法：FORCE INDEX (<索引名>)


