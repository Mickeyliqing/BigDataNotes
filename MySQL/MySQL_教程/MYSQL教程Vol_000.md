# MYSQL教程Vol_000
## 建表语句

### 建表语句_01
```sql
     CREATE TABLE IF NOT EXISTS `employees` (
      `emp_no` INT(11)  NOT NULL,
      `brith_date` DATE NOT NULL,
      `first_name` VARCHAR(14) NOT NULL,
      `last_name` VARCHAR(16) NOT NULL,
      `gender` CHAR(1) not null,
      `hire_date` DATE NOT NULL,
      PRIMARY KEY (`emp_no`)
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into employees (emp_no, brith_date,first_name,last_name, gender,hire_date) values (01,SYSDATE(),'AA','QQ','0',SYSDATE());
    insert into employees (emp_no, brith_date,first_name,last_name, gender,hire_date) values (02,DATE_ADD(NOW(),INTERVAL 1 DAY),'BB','WW','1',DATE_ADD(NOW(),INTERVAL 1 DAY));
    insert into employees (emp_no, brith_date,first_name,last_name, gender,hire_date) values (03,DATE_ADD(NOW(),INTERVAL 2 DAY),'CC','EE','0',DATE_ADD(NOW(),INTERVAL 2 DAY));
    
```
### 建表语句_02
```sql
    CREATE TABLE IF NOT EXISTS `dept_emp` (
      `emp_no` INT(11)  NOT NULL,
      `dept_no` char(4)  NOT NULL,
      `from_date` DATE NOT NULL,
      `to_date` DATE NOT NULL,
      PRIMARY KEY (`emp_no`,`dept_no`)
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into dept_emp (emp_no, dept_no,from_date,to_date) values (01,'01',SYSDATE(),SYSDATE());
    insert into dept_emp (emp_no, dept_no,from_date,to_date) values (02,'02',DATE_ADD(NOW(),INTERVAL 1 DAY),DATE_ADD(NOW(),INTERVAL 1 DAY));
    insert into dept_emp (emp_no, dept_no,from_date,to_date) values (03,'03',DATE_ADD(NOW(),INTERVAL 2 DAY),DATE_ADD(NOW(),INTERVAL 2 DAY));
```

### 建表语句_03
```sql
    CREATE TABLE IF NOT EXISTS `salaries` (
      `emp_no` INT(11)  NOT NULL,
      `salary` INT(11)  NOT NULL,
      `from_date` DATE NOT NULL,
      `to_date` DATE NOT NULL,
      PRIMARY KEY (`emp_no`,`from_date`)
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into salaries (emp_no, salary,from_date,to_date) values (01,10000,SYSDATE(),SYSDATE());
    insert into salaries (emp_no, salary,from_date,to_date) values (02,20000,DATE_ADD(NOW(),INTERVAL 1 DAY),DATE_ADD(NOW(),INTERVAL 1 DAY));
    insert into salaries (emp_no, salary,from_date,to_date) values (03,30000,DATE_ADD(NOW(),INTERVAL 2 DAY),DATE_ADD(NOW(),INTERVAL 2 DAY));
```

### 建表语句_04
```sql
    CREATE TABLE IF NOT EXISTS `dept_manager` (
      `emp_no` INT(11)  NOT NULL,
      `dept_no` char(4)  NOT NULL,
      `to_date` DATE NOT NULL,
      PRIMARY KEY (`emp_no`,`dept_no`)
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into dept_manager (emp_no, dept_no,to_date) values (01,'01',SYSDATE());
    insert into dept_manager (emp_no, dept_no,to_date) values (02,'02',DATE_ADD(NOW(),INTERVAL 1 DAY));
    insert into dept_manager (emp_no, dept_no,to_date) values (03,'03',DATE_ADD(NOW(),INTERVAL 2 DAY));
```

### 建表语句_05
```sql
    CREATE TABLE IF NOT EXISTS `titles` (
      `emp_no` INT(11)  NOT NULL,
      `title` varchar(50)  NOT NULL,
      `from_date` DATE NOT NULL,
      `to_date` DATE NOT NULL
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into titles (emp_no, title,from_date,to_date) values (01,'01',SYSDATE(),SYSDATE());
    insert into titles (emp_no, title,from_date,to_date) values (02,'02',DATE_ADD(NOW(),INTERVAL 1 DAY),DATE_ADD(NOW(),INTERVAL 1 DAY));
    insert into titles (emp_no, title,from_date,to_date) values (03,'03',DATE_ADD(NOW(),INTERVAL 2 DAY),DATE_ADD(NOW(),INTERVAL 2 DAY));
```

### 建表语句_06
```sql
    CREATE TABLE IF NOT EXISTS `film` (
      `filmid` INT(11)  NOT NULL,
      `title` VARCHAR(14) NOT NULL,
      `description` text,
      PRIMARY KEY (`filmid`)
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into film (filmid, title,description) values (01,"AA","01AA");
    insert into film (filmid, title,description) values (02,"BB","02BB");
    insert into film (filmid, title,description) values (03,"CC","03CC");
```

### 建表语句_07
```sql
    CREATE TABLE IF NOT EXISTS `category` (
      `categoryid` INT(11)  NOT NULL,
      `name` VARCHAR(14) NOT NULL,
      `lastupdate` DATE NOT NULL,
      PRIMARY KEY (`categoryid`)
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into category (categoryid, name,lastupdate) values (01,'1',DATE_ADD(NOW(),INTERVAL 1 DAY));
    insert into category (categoryid, name,lastupdate) values (02,'2',DATE_ADD(NOW(),INTERVAL 2 DAY));
    insert into category (categoryid, name,lastupdate) values (03,'3',DATE_ADD(NOW(),INTERVAL 3 DAY));
```

### 建表语句_08
```sql
    CREATE TABLE IF NOT EXISTS `film_category` (
      `filmid` INT(11)  NOT NULL,
      `categoryid` INT(11)  NOT NULL,
      `lastupdate` DATE NOT NULL
    );
    
    --这里插入语句只给出三条实例，多条数据的插入同理。
    insert into film_category (filmid, categoryid,lastupdate) values (01,'01',DATE_ADD(NOW(),INTERVAL 1 DAY));
    insert into film_category (filmid, categoryid,lastupdate) values (02,'02',DATE_ADD(NOW(),INTERVAL 2 DAY));
    insert into film_category (filmid, categoryid,lastupdate) values (03,'03',DATE_ADD(NOW(),INTERVAL 3 DAY));
```

### 建表语句_09
```sql
CREATE TABLE IF NOT EXISTS `department` (  `dept_no` char(4)  NOT NULL,  `dept_name` varchar(50)  NOT NULL);--这里插入语句只给出三条实例，多条数据的插入同理。insert into department (dept_no, dept_name) values (01,'01');insert into department (dept_no, dept_name) values (02,'02');insert into department (dept_no, dept_name) values (03,'03');
```


