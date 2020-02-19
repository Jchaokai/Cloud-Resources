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
```
### 通配符
```sql
/*通配符也是用在过滤语句中，但它只能用于文本字段。 

 % 匹配(>=0)个任意字符；
 _ 匹配(==1)个任意字符；
 [ ] 可以匹配集合内的字符，例如 [ab] 将匹配字符 a 或者 b。用脱字符 ^ 可以对其进行否定，也就是不匹配集合内的字符。

使用 Like 来进行通配符匹配。*/

SELECT *
FROM mytable
WHERE col LIKE '[^AB]%'; -- 不以 A 和 B 开头的任意文本
#不要滥用通配符，通配符位于开头处匹配会非常慢。
```
#### 
CONCAT() 用于连接两个字段。许多数据库会使用空格把一个值填充为列宽，因此连接的结果会出现一些不必要的空格，使用 TRIM() 可以去除首尾空格。

SELECT CONCAT(TRIM(col1), '(', TRIM(col2), ')') AS concat_col
FROM mytable;