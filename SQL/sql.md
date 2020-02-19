### sql基础

#### 修改表
```sql
#添加列
alter table mytable
add col char(20);

#删除列
alter tablemytable
drop column col;
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

#
```