# SQL基础笔记

## 高级数据过滤
### Where子句
#### 四种操作符
* AND操作符
* OR操作符
* IN操作符：和OR操作符能完成相同的功能，并且语法比OR操作符更家清晰。IN操作符的执行速度也一般比OR执行更快。并且，最重要的是能够**嵌套包含其他SELECT子句**
* NOT操作符：WHERE子句中NOT操作符用来否定其后面条件的关键字。
  ```SQL
  select name,email from USER where id not in (1,2,3)
  ```

## 通配符进行过滤
### LIKE操作符
Like是一个谓词，可以结合通配符进行模糊匹配。常见的通配符如下：
* 百分号（%）通配符：用来表示字符出现的任意次数。
* 下划线（_）通配符：用来表示匹配单个字符。
* 方括号（[]）通配符：匹配指定位置的一个字符，MySQL使用正则表达式的REGEXP关键字来使用这种通配符。例如：
  ```sql
  select * from user where name REGEXP '^[t]';
  ```

## 计算字段
计算字段指的是使用同一张表中的其他列表达式计算而来的，不存在于实际真实的数据库库表中。
### 拼接字段
在MySQL中，拼接字段需要借助concat实现。例如：
```sql
select concat(name,'(',email,')') from user;
```

## CASE表达式
### 基本用法：
简单表达式：
```sql
CASE column：
  WHEN condition1 THEN value1
  WHEN condition2 THEN value2
ELSE
  value3
END
```

搜索表达式：
```sql
CASE 
  WHEN column=condition1 THEN value1
  WHEN column=condition2 THEN value2
ELSE
  value3
END
```