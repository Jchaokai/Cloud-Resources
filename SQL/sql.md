### sql基础

#### 修改
```sql
#添加列
alter table mytable
add col1 char(20);

#删除列
alter tablemytable
drop column col1;
```
#### 插入
```sql
#普通插入
insert into mytable(col1,col2)
values(val1,val2);

#插入检索出来的数据
insert into mytable(col1,col2)
select col1,col2
from mytable2;

#将一个表插入到一个新表
create table newtable as
select * from mytables;
```

#### 排序

```sql
ASC ：升序（默认）
DESC ：降序
可以按多个列进行排序，并且为每个列指定不同的排序方式：

SELECT *
FROM mytable
ORDER BY col1 DESC, col2 ASC;