# MYSQL教程Vol_006
其实在实际的使用过程中会发现，有些时候对于建好的数据库表单需要：

- 增加字段
- 删除字段
- 修改字段

而这些操作又需要保证不影响原本的表单结构。

那么接下来就简单学习一下吧！

## 增加字段

```sql
    ALTER TABLE `table` ADD COLUMN `col_name` INT NOT NULL;
```

**解释说明**

1. table 是要添加字段的数据表的表名。
2. col_name 是要添加的字段的名称。
3. INT NOT NULL 是表明添加的这个字段的类型是 INT 的数据类型，NOT NULL 表明添加的这个字段不能为空。
4. 如果添加的是其它的数据类型，只需要把 INT 改成对应的数据类型即可。
5. 关键字是 ADD 。

## 删除字段

```sql
  ALTER TABLE `table` DROP COLUMN `col_name`;
```

**解释说明**

1. table 是要删除字段的数据表的表名。
2. col_name 是要删除的字段的名称。
3. 关键字是 DROP 。

## 修改字段

### 修改字段类型

```sql
  ALTER TABLE `table` MODIFY `field` NEW-FIELD-TYPE;
```

**解释说明**

1. table 是要修改字段的数据表的表名。
2. field 是要修改的字段的名称。
3. NEW-FIELD-TYPE 要修改的字段的类型。
4. 关键字是 MODIFY 。

### 修改字段名称

```sql
 ALTER TABLE `table` CHANGE `old_name` `new_name` new_type ;
```

**解释说明**

1. table 是要修改字段的数据表的表名。
2. old_name 是要修改的旧字段的名称。
3. new_name 是要修改的新字段的名称。
4. new_type 是新的数据类型。
5. 关键字是 CHANGE 。


