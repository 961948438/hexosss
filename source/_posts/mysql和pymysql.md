---
title: mysql和pymysql
date: 2020-08-13 11:41:06
tags:
  - DB-mysql
categories:
  - Database
---
## 一、sql的基本语法
### 1. 说明
  1. 行：记录
  2. 列：字段
  3. 唯一标识每一个行/记录的字段叫做主键：
  4. RDBMS关系型数据库：
  4. mysql的引擎为innodb：
  4. redis适合做缓存数据库、mysql适合做网站、magoose适合做爬虫：
  5. rdbms客户端与服务器端通过sql语句进行通讯，sql是一种结构化查询语言：
    sql语句可以应用在很多的数据库软件上：
  6. DQL是数据查询语言，DML是数据操控语言：sql数据库语言是不区分大小写的：
  7. ubuntu系统中使用如下命令安装数据库服务端和客户端
    $ sudo apt-get install  mysql-client-core-5.6
    $ sudo apt-get install mariadb-client-core-10.0
    然后利用mysql -uroot -p进入数据库
    eixt/quit/ctrl+d退出数据库
  8. 为表定义字段的类型和约束可以规范表的完整性
  9.  sql语句中常用反引号来区分sql关键字：比如 select `name` from ddd;
  10. sql语句中添加注释的三种范式： #、 -- 、 /* 代码块 */
  11. 通常情况下索引会随着主键、外键、唯一键的创建而创建。


### 2. 类型
  ![数据类型图](http://fuxiangyou1_admin.gitee.io/image/image/datatype.png)
  > 详细查看： https://www.cnblogs.com/-xlp/p/8617760.html
  字段中常用的数据类型如下：
  + 整数类型：BIT、BOOL、TINY INT、SMALL INT、MEDIUM INT、 INT、 BIG INT
  + 浮点数类型：FLOAT、DOUBLE、DECIMAL
  + 字符串类型：CHAR、VARCHAR、TINY TEXT、TEXT、MEDIUM TEXT、LONGTEXT、TINY BLOB、BLOB、MEDIUM BLOB、LONG BLOB
  + 日期类型：Date、DateTime、TimeStamp、Time、Year等五种格式，其接受的格式如（YYYY-MM-DD HH:MM:SS）
  + 其他数据类型：BINARY、VARBINARY、ENUM、SET、Geometry、Point、MultiPoint、LineString、MultiLineString、Polygon、GeometryCollection等
  对于图片音视频文件不存储在数据库中，而是上传到某个服务器然后在表中存储这些文件的保存路径：

### 3. 约束
  1. 键约束：包含主键约束、外键约束、唯一键约束，
     主键约束通常放置在类型后面，但是复合主键不能这么定义，复合主键 primary key（sid， cid）
     删除表的主键约束非常方便，因为一个表就一个主键或者一个复合主键：
     alter table 表名 drop primary key；
     唯一键约束（unique key）的删除是通过删除索引来实现的；在已有字段上添加唯一键的方法非常简单：
     外键约束对应的字段值对应这其他表的主键、外键、唯一键，但是通常是主键；
     外键约束有五个等级：cascade、set null 、 no action、 restrict、set default；
     cascade 等级 表示我主表依赖字段的记录被删除了，外键字段的记录也要被删除：
     set null等级表示我主表依赖字段的记录被删除了，外键字段对应的值重置为null；
     no action等级表示我主表依赖字段的记录被删除了，此时从表不作为；
     restrict等级，该等级和no action等级一样，此时主表不能随意删除依赖字段的记录，必须先处理从表的外键
         字段后才能删除该主表依赖字段的记录，即此时主表失去了主动权；
     set default等级mysql的innodb引擎不支持的。
     外键定义的语法格式为：
     create table 表名 values （ foreign key（从表中的某字段） references 主表名（被参考的字段：主键）
         on update 更新时的外键约束等级 on delete 删除是的外键约束等级。
      ）
      主键可以定义在字段类型后，外键不可以定义在字段类型后，外键的定义必须占用字段的位置；其语法格式如下；
      create table dds (id int, house int, foreign key(house) references house_1(id));
      外键的约束通常是发生在插入数据的时候，如果插入数据的时候主表还没有创建是不能添加记录，或许可以但也不建议；
      注意外键约束是随机生成的，其存储在我们系统库information_schema.table_constraints的表中；要删除外键必须先
      找出来我们的随机外键名；
      ddd_ibfk_1
  2. 非空not null：此字段不允许为空
  3. check检查约束，用来检查字段值是否为指定范围。 检查约束对于mysql数据库管理系统暂不支持；其格式为：
     create table 表名 （字段1  字段类型 字段约束 check（“1”，“2“））；
  4. 默认default：不填写此值时会使用默认值，如果填写时以填写为准
     注意：如果我们添加记录的时候不想给默认值字段赋值可以通过default来制定：比如
     insert into 表名 values （字段1,default,字段2）；
     通常情况下默认值约束设定后会取消not null约束，我们可以一起设置来解决这个情况：
     alter table 表名 操作 字段名 字段类型 字段约束 default 18 not null；
  5. 自增约束，一个表只能有一个自增约束，因为一个表只有一个维护自增的变量，
     自增的字段类型必须为整数列，自增约束的列必须为键约束列，（主键、外键、唯一键）；
     创建表时自增列定义的语法格式：
     create table 表名 （字段1 int（10） primary key auto_increment ）
     自增列如果我们插入了整数，他会安装其作为自增序号，后面自增数建立在其基础之上；
     当然了如果插入的是0或者null他会自增，自增列是支持负数的，

### 4.  常用操作
  0. 常用的数据库操作（注意语句要以分号的形式写，如果不写分号其会认为语句未完，sql是不区分大小写的：）
  1. show databases；显示连接的所有数据库
  2. select now();显示当前时间
  3. select version();
  4. create database  数据库名  charset='utf8';(注意指定编码字符未utf-8)
  5. show create database 数据库名；（用于显示数据库的创建细节）
  6. drop database 数据库名；删除数据库
  7. 数据库名不支持-中横线，要想认为是一个整齐必须使用类似模板字符串的标识符``即反引号
  8.  select database();显示当前使用或者选择的数据库
  9.  use 数据库名；表示使用或者切换该数据库；
  10. show tables 选择当前选择的数据库里面的所有表
  11. create table yyyy(id int primary key not null auto_increment,name varchar(25))用于创建一个表；方法中
  的每一个字段用逗号隔开，每个字段名后面空隔加上修饰符进行字段修饰；如果是建表后：
  alter table 表名 add primary key （复合键列表）
  12. desc 表名；用于表格形式显示所有的表的字段；
  13. 创建多个字段的表的方法：
  create table  stuednts(
      id int unsigned primary key not null auto_increment,
      name varchar(25),
      age tinyint(3) unsigned,
      high decimal(5,2),
      gender enum("男","女","保密") default "保密",
      cls_id int unsigned
  )
  14. 向表中插入值得方法（一定表得字段得顺序去插入）
  insert into stuednts values(0,'老王',19,190.00,'男',1606);
  15. select * from stuednts （向指定表中查询所有得信息）
### 5. 表结构的简单操作
  14. truncate 表名;用来删除整个表中的数据，其底层比delet from 表名；这个操作要快；
  15. show index from ddd;显示表的索引；
  16. sql得命令行中得注释为--；
  17.  增加删除修改表字段得方法为：alter table stuednts add birthday datetime;
  alter table 表名 add  字段名 字段类型描述（向表中添加字段）
  18. alter table 表明 modify 字段名 字段类型 字段描述；（修改表中字段不重命名版）
  19. alter table 表名 change 原字段名 新字段名 字段类型 字段描述；（修改表中字段重命名版）
  20. alter table 表名 drop 字段名； （字段删除后字段所在得数据也删除了）
  21. drop table 表名；用于删除数据表
  22. show create table 表名
  23. 一般在sql语句中带有名字得比如表名、数据库名、字段名通常带反引号以放出错；
  24. alter table 表名 reanme 新表名
  25. reanme table 旧表名 to 新表名
  26. alter table 表名 add 字段名 字段类型 字段约束 字段位置：用于添加表中的字段，默认是放置到末尾，可以通过
     first表示首个字段，也可以通过after 指定字段让我们添加的字段放置于指定字段之后；
  27. alter table 表名 modify 字段名 新字段类型 新字段约束 字段位置；字段位置可选项有（first，before + 指定字段）
      modify只能修改字段类型约束位置等，
  28. alter table 表名 change 旧字段名 新字段名 字段类型 字段约束 
  29. alter table 表名 drop 字段; 对表字段的删除
  30. drop table 表名；删除表
  31. alter table 表名 drop primary key 删除主键；
  32. alter table 表名 add primary key（已有字段）为已有字段添加主键
  33. alter table 表名 add primary key （已有字段列表） 为表添加复合主键
  34. alter table ddd modify name varchar(20) unique key;为表已有字段添加唯一键
  35. alter table 表名 drop index  索引名；通过删除唯一键对应的缩影 
  36. alter table  表名 modify 字段名 字段类型 not null， 在建表后为某个字段添加非空约束
  37. alter table 表名 modify 字段名 字段类型；通过重新定义字段类型和约束删除某字段的非空约束。
  38. alter table 表名 modify 字段名 字段类型 default 默认值，为已有字段添加配置默认值设置，
  39. alter table  表名 操作 字段名  int primary key auto_increment ；为表添加自增约束
  40. alter table 表名 modify 字段名 字段类型 ；重新定义某个字段不添加自增约束即去除了自增约束；
  42. alter table 表名 add foreign key（从表中的某个字段） references 主表（主表依赖字段）
                                              on update 更新时等级 on delete 删除时等级；
  43. alter table 表名 drop foreign key 约束名，如果不知道的话可以查一查，外键约束名是随机生成的； 
  44. select *   from information_schema.table_constraints where table_name = "需要查询外键的表"
  45. show create table 表名 可以查看创建表的详细细节：包括我们自动生成外键约束名，以便删除；

### 6. 数据库备份
  1. mysql -hlocalhost -P3306 -uroot -p密码  --database 数据库名 > 导出sql文件路径
  例如：mysqldump -hlocalhost -P3306 -uroot -p961948438 --databases desb > C:\Users\dd\Desktop\demo_data\test.sql

### 7. 表数据的的简单增删改查
  1. 插入数据(xxx是表名，插入了一个记录有两个字段值)
    insert into xxx value(1,'121')  注意：此时插入数据得字段值可以为null或者default，只要约束允许；
    注意：枚举类型值可以设定得时候用指定得值，也可以用枚举得下标表示枚举得值；
  2. 部分插入：insert into xxx(字段名) value(字段值)插入单个字段值；
  3. 多行插入：value后面可以接多个记录使得可以一行插入多个；
    insert into xxx value(21,'safsafds'),(31,'asdfsafdsa'),(41,'sdafsdafdsa')
  4. update 表名 set 字段=值 where  字段 = 值  注意：根据唯一字段作为条件去修改字段值，否则将修改整个字段列得值；
  5. 条件查询通过where：select * from xxx where name = '222';select * from  xxx where id>7;
  6. 条件查询指定列（记得用逗号分隔）：select id,name from where name = '222'
  7. 条件查询指定列并起别名(通过)：select id as ID,name as 姓名 from where name = '222'
  8. 物理删除在数据库中真删除和逻辑删除在表中标记删除,即通过特定的字段标记有没有删除：
    物理删除：delete from xxx  where name = '222'
    逻辑删除：update xxx set is_delete = 1 where id=11

## 二、SQL常用函数汇总
### 1. 数学函数

| 函数 |  作用|
| ----------------------------- | ------------------- |
| ABS(x) |  	返回x的绝对值　　SELECT ABS(-1) -- 返回1|
| CEIL(x),CEILING(x) |  	返回大于或等于x的最小整数　　SELECT CEIL(1.5) -- 返回2|
| FLOOR(x) |  返回小于或等于x的最大整数　SELECT FLOOR(1.5) -- 返回1|
| RAND() |  返回0->1的随机数　SELECT RAND() --0.93099315644334|
| RAND(x) |  返回0->1的随机数，x值相同时返回的随机数相同　SELECT RAND(2) --1.5865798029924|
| SIGN(x) |  返回x的符号，x是负数、0、正数分别返回-1、0和1SELECT SIGN(-10) -- (-1)|
| PI() |  返回圆周率(3.141593 SELECT PI() --3.141593|
| TRUNCATE(x,y) |  返回数值x保留到小数点后y位的值 SELECT TRUNCATE(1.23456,3) -- 1.234|
| ROUND(x) |  返回离x最近的整数　　SELECT ROUND(1.23456) --1|
| ROUND(x,y) |  保留x小数点后y位的值，但截断时要进行四舍五入 SELECT ROUND(1.23456,3) -- 1.235|
| POW(x,y).POWER(x,y) |  返回x的y次方　 SELECT POW(2,3) -- 8|
| SQRT(x) |  返回x的平方根　SELECT SQRT(25) -- 5|
| EXP(x) |  返回e的x次方　SELECT EXP(3) -- 20.085536923188|
| MOD(x,y) |  返回x除以y以后的余数 SELECT MOD(5,2) -- 1|
| LOG(x) |  返回自然对数(以e为底的对数) SELECT LOG(20.085536923188) -- 3|
| LOG10(x) |  返回以10为底的对数　 SELECT LOG10(100) -- 2|
| RADIANS(x) |  将角度转换为弧度 SELECT RADIANS(180) -- 3.1415926535898|
| DEGREES(x) |  将弧度转换为角度 SELECT DEGREES(3.1415926535898) -- 180|
| SIN(x) |  求正弦值(参数是弧度)　SELECT SIN(RADIANS(30)) -- 0.5|
| ASIN(x) |  求反正弦值(参数是弧度)|
| COS(x) |  求余弦值(参数是弧度)|
| ACOS(x) |  求反余弦值(参数是弧度)|
| TAN(x) |  求正切值(参数是弧度)|
| ATAN(x) ATAN2(x) |  求反正切值(参数是弧度)|
| COT(x) |  求余切值(参数是弧度)|

### 2. 字符串函数
    字符串函数是MySQL中最常用的一类函数，字符串函数主要用于处理表中的字符串。
| 函数 |  作用|
| ----------------------------- | ------------------- |
| CHAR_LENGTH(s) |  返回字符串s的字符数 SELECT CHAR_LENGTH('你好123') -- 5|
| LENGTH(s) |  返回字符串s的长度 SELECT LENGTH('你好123') -- 9|
| CONCAT(s1,s2,...) |  将字符串s1,s2等多个字符串合并为一个字符串SELECT CONCAT('12','34') -- 1234|
| CONCAT_WS(x,s1,s2,...) |  同CONCAT(s1,s2,...)函数，但是每个字符串直接要加上x SELECT CONCAT_WS('@','12','34') -- 12@34|
| INSERT(s1,x,len,s2) |  将字符串s2替换s1的x位置开始长度为len的字符串 SELECT UPPER('abc') -- ABC|
| LOWER(s),LCASE(s) |  将字符串s的所有字母变成小写字母 SELECT LOWER('ABC') -- abc|
| LEFT(s,n) |  返回字符串s的前n个字符 SELECT LEFT('abcde',2) -- ab|
| RIGHT(s,n) |  返回字符串s的后n个字符 SELECT RIGHT('abcde',2) -- de|
| LPAD(s1,len,s2) |  字符串s2来填充s1的开始处，使字符串长度达到len SELECT LPAD('abc',5,'xx') -- xxabc|
| RPAD(s1,len,s2) |  字符串s2来填充s1的结尾处，使字符串的长度达到len SELECT RPAD('abc',5,'xx') -- abcxx|
| LTRIM(s) |  去掉字符串s开始处的空格|
| RTRIM(s) |  去掉字符串s结尾处的空格|
| TRIM(s) |  去掉字符串s开始和结尾处的空格|
| TRIM(s1 FROM s) |  去掉字符串s中开始处和结尾处的字符串s1 SELECT TRIM('@' FROM '@@abc@@') -- abc|
| REPEAT(s,n) |  将字符串s重复n次 SELECT REPEAT('ab',3) -- ababab|
| SPACE(n) |  返回n个空格|
| REPLACE(s,s1,s2) |  将字符串s2替代字符串s中的字符串s1 SELECT REPLACE('abc','a','x') --xbc|
| STRCMP(s1,s2) |  比较字符串s1和s2|
| SUBSTRING(s,n,len) |  获取从字符串s中的第n个位置开始长度为len的字符串|
| MID(s,n,len) |  同SUBSTRING(s,n,len)|
| LOCATE(s1,s),POSITION(s1 IN s) |  从字符串s中获取s1的开始位置 SELECT LOCATE('b', 'abc') -- 2|
| INSTR(s,s1) |  从字符串s中获取s1的开始位置 SELECT INSTR('abc','b') -- 2|
| REVERSE(s) |  将字符串s的顺序反过来 SELECT REVERSE('abc') -- cba|
| ELT(n,s1,s2,...) |  返回第n个字符串 SELECT ELT(2,'a','b','c') -- b|
| EXPORT_SET(x,s1,s2) |  SELECT EXPORT_SET(5,'Y','N',',',4) -- Y,N,Y,N|
| FIELD(s,s1,s2...) |  返回第一个与字符串s匹配的字符串位置 SELECT FIELD('c','a','b','c') -- 3|
| FIND_IN_SET(s1,s2) |  返回在字符串s2中与s1匹配的字符串的位置|
| MAKE_SET(x,s1,s2) |  SELECT MAKE_SET(1|4,'a','b','c'); -- a,c|
| SUBSTRING_INDEX |  返回从字符串str的第count个出现的分隔符delim之后的子串。|

### 3. 日期时间函数
    MySQL的日期和时间函数主要用于处理日期时间。
| 函数 |  作用|
| ----------------------------- | ------------------- |
| CURDATE(),CURRENT_DATE() |  返回当前日期 SELECT CURDATE() ->2014-12-17|
| CURTIME(),CURRENT_TIME |  返回当前时间 SELECT CURTIME() ->15:59:02 |
| NOW(),CURRENT_TIMESTAMP(),LOCALTIME() |  返回当前日期和时间 SELECT NOW() ->2014-12-17 15:59:02|
| UNIX_TIMESTAMP() |  以UNIX时间戳的形式返回当前时间 SELECT UNIX_TIMESTAMP() ->1418803177|
| UNIX_TIMESTAMP(d) |  将时间d以UNIX时间戳的形式返回 SELECT UNIX_TIMESTAMP('2011-11-11 11:11:11')|
| FROM_UNIXTIME(d) |  将UNIX时间戳的时间转换为普通格式的时间 SELECT FROM_UNIXTIME(1320981071)|
| UTC_DATE() |  返回UTC日期 SELECT UTC_DATE() 2014-12-17|
| UTC_TIME() |  返回UTC时间 SELECT UTC_TIME() 08:01:45	(慢了8小时)|
| MONTH(d) |  返回日期d中的月份值，1->12 SELECT MONTH('2011-11-11 11:11:11')  ->11|
| MONTHNAME(d) |  返回日期当中的月份名称，如Janyary SELECT MONTHNAME('2011-11-11 11:11:11')|
| DAYNAME(d) |  返回日期d是星期几，如Monday,Tuesday SELECT DAYNAME('2011-11-11 11:11:11') ->Friday|
| DAYOFWEEK(d) |  日期d今天是星期几，1星期日，2星期一 SELECT DAYOFWEEK('2011-11-11 11:11:11') >6|
| WEEKDAY(d) |  日期d今天是星期几，0表示星期一，1表示星期二|
| WEEK(d)，WEEKOFYEAR(d) |  计算日期d是本年的第几个星期，范围是0->53 SELECT WEEK('2011-11-11 11:11:11')|
| DAYOFYEAR(d) |  计算日期d是本年的第几天 SELECT DAYOFYEAR('2011-11-11 11:11:11') 315|
| DAYOFMONTH(d) |  计算日期d是本月的第几天 SELECT DAYOFMONTH('2011-11-11 11:11:11') ->11|
| QUARTER(d) |  返回日期d是第几季节，返回1->4 SELECT QUARTER('2011-11-11 11:11:11') ->4|
| HOUR(t) |  返回t中的小时值 SELECT HOUR('1:2:3') ->1|
| MINUTE(t) |  返回t中的分钟值 SELECT MINUTE('1:2:3') ->2 |
| SECOND(t) |  返回t中的秒钟值 SELECT SECOND('1:2:3') ->3|
| EXTRACT(type FROM d) |  从日期d中获取指定的值，type指定返回的值 SELECT EXTRACT(MINUTE FROM '2011-11-11 11:11:11')|
| TIME_TO_SEC(t) |  将时间t转换为秒 SELECT TIME_TO_SEC('1:12:00') ->4320|
| SEC_TO_TIME(s) |  将以秒为单位的时间s转换为时分秒的格式 SELECT SEC_TO_TIME(4320) ->01:12:00|
| TO_DAYS(d) |  计算日期d距离0000年1月1日的天数 ELECT TO_DAYS('0001-01-01 01:01:01') 366|
| FROM_DAYS(n) |  计算从0000年1月1日开始n天后的日期  SELECT FROM_DAYS(1111) ->0003-01-16|
| DATEDIFF(d1,d2) |  计算日期d1->d2之间相隔的天数 SELECT DATEDIFF('2001-01-01','2001-02-02')->-32|
| ADDDATE(d,n) |  计算其实日期d加上n天的日期|
| ADDDATE(d，INTERVAL expr type) |  计算起始日期d加上一个时间段后的日期|
| DATE_ADD(d,INTERVAL expr type) |  同上|
| SUBDATE(d,n) |  日期d减去n天后的日期 SELECT SUBDATE('2011-11-11 11:11:11', 1)->2011-11-10 11:11:11 (默认是天)|
| SUBDATE(d,INTERVAL expr type) |  日期d减去一个时间段后的日期 SELECT SUBDATE('2011-11-11 11:11:11', INTERVAL 5 MINUTE)|
| ADDTIME(t,n) |  时间t加上n秒的时间 SELECT ADDTIME('2011-11-11 11:11:11', 5)|
| SUBTIME(t,n) |  时间t减去n秒的时间  SELECT SUBTIME('2011-11-11 11:11:11', 5) ->2011-11-11 11:11:06 (秒)|
| DATE_FORMAT(d,f) |  按表达式f的要求显示日期d SELECT DATE_FORMAT('2011-11-11 11:11:11','%Y-%m-%d %r')|
| TIME_FORMAT(t,f) |  按表达式f的要求显示时间t SELECT TIME_FORMAT('11:11:11','%r') 11:11:11 AM|
| GET_FORMAT(type,s) |  获得国家地区时间格式函数  select get_format(date,'usa')|

### 4.  条件判断函数

| 函数 |  作用|
| ----------------------------- | ------------------- |
| IF(expr,v1,v2)函数 |  如果表达式expr成立，返回结果v1；否则，返回结果v2。SELECT IF(1 > 0,'正确','错误')|
| 2、IFNULL(v1,v2)函数 |  如果v1的值不为NULL，则返回v1，否则返回v2 SELECT IFNULL(null,'Hello Word')|
| 3、CASE |  略过，详细查资料|

### 5.  系统信息函数
    系统信息函数用来查询MySQL数据库的系统信息
| 函数 |  作用|
| ----------------------------- | ------------------- |
| VERSION() |  返回数据库的版本号 SELECT VERSION()|
| CONNECTION_ID()  |  返回服务器的连接数|
| DATABASE()、SCHEMA |  返回当前数据库名|
| USER()  SYSTEM_USER()、SESSION_USER()|  返回当前用户|
| CHARSET(str) |  返回字符串str的字符集|
| COLLATION(str) |  返回字符串str的字符排列方式|
| LAST_INSERT_ID() |  返回最近生成的AUTO_INCREMENT值|

### 6. 加密函数
| 函数 |  作用|
| ----------------------------- | ------------------- |
| 1、PASSWORD(str) |  PASSWORD(str)用于给用户的密码加密。 SELECT PASSWORD('123') ->*23AE809DDACAF96AF0FD78ED04B6A265E05AA257|
| 2、MD5 |  MD5(str)函数可以对字符串str进行散列，可以用于一些普通的不需要解密的数据加密  SELECT md5('123')202cb962ac59075b964b07152d234b70|
| 3、ENCODE(str,pswd_str)与DECODE(crypt_str,pswd_str) |  ENCODE函数可以使用加密密码pswd_str来加密字符串str，加密结果是二进制数，需要使用BLOB类型的字段保存。该函数与DECODE是一对，需要同样的密码才能够解密。SELECT ENCODE('123','xxoo')->;vx SELECT DECODE(';vx','xxoo') ->123|

### 7. 其他函数
| 函数 |  作用|
| ----------------------------- | ------------------- |
| 1、格式化函数FORMAT(x,n) |  FORMAT(x,n)函数可以将数字x进行格式化，将x保留到小数点后n位。 SELECT FORMAT(3.1415926,3) |
| ASCII(s) |  返回字符串s的第一个字符的ASCII码；|
| BIN（x） |   返回x的二进制编码；|
| HEX(x) |  返回x的十六进制编码|
| OCT(x) |  返回x的八进制编码；|
| CONV(x,f1,f2)  |  返回f1进制数变成f2进制数；|
| INET_ATON(IP) |  )函数可以将IP地址转换为数字表示；IP值需要加上引号；|
| INET_NTOA(n) |  函数可以将数字n转换成IP形式。|
| BENCHMARK(count.expr) |  函数将表达式expr重复执行count此，然后返回执行时间。该函数可以用来判断MySQL处理表达式的速度。|
| CONVERT(s USING cs) |  函数将字符串s的字符集变成cs。|
| CAST(x AS type) |  SELECT CAST('123' AS UNSIGNED INTEGER) + 1 ->124|


## 三、数据库增删改查专题
### 1.  查询相关

  0. 模糊查询的查询条件要使用引号，可以是单引号也可以是双引号，但是不能是反引号；
  1. 去重查询(从students表中去重复查询gender)；
    select distinct gender from stuednts;
  2. 条件查询里面有的运算符有：
    比较运算符> < >= <=  =  !=  <> 
    条件运算符and or not
    注意：select * from xxx where id >= 8 and  id<=10;and运算符左边和右边都是一个整体而不是值；
    注意：select * from xxx where   not id >= 8 and gender = 2;not的用法，用在整体的条件的前面；
  3. 模糊查询为查询条件：
    可以使用like，用%替换0个或者多个字符，用_表示一个字符，
    select * from xxx where name like "_明%"
    可以使用rlike后接正则作为查询条件，
  4. 使用范围查询作为条件 字段 in (范围)  ：注意in前面还可以加not
    select * from xxx  where age  in (11,14)
    可以使用between  and 作为范围查询条件：注意between前面可以加not取反，且between和and是一个整体
    select * from xxx  where age  between 16 and 19
  5. 是否非空作为条件查询（is null / is not null）：
    select * from xxx where name is not null
  6. 在使用了数据库的情况下 通过source 数据库文件名导入数据库
  7. 注意：有时查询条件混淆的时候建议使用小括号明晰一下

### 2.  排序相关
  1.  按照指定条件之后从小到大排序（asc表示从小到大、desc表示从大到小排）：
  此时如果我们排序的那个字段比如age相同的时候系统会按照主键再去排；但是我们可以在by后面接多个字段
  2. 表示当前面字段相同时再按后面的字段排；而不是系统的字段排（多个字段之间使用逗号，）
  elect * from xxx where (age between 15 and  25) and gender = 1 order by age asc
  select * from xxx where (age between 15 and  25) and gender = 1 order by age desc,cls_id desc;


### 3. 聚合、分组（select查询的位置可以放置任何函数或者表达式子）
  聚合函数的用法：
  > count(*)  :查询条件查询后的记录总数：
  select  count(*) as 人数    from xxx where id = 8;
  max(age)：max函数用于查询指定字段的列的最大值：
  select max(age) from xxx;
  sum():函数的用法用于查询指定列的总数；
  select sum(age) from xxx;
  agv（）：函数用于返回指定列的平均值：
  select    avg(age) from xxx;
  round（值，小数点位数）函数用于四舍五入保留指定小数点位数；
  select round(avg(age),1)  from xxx;
  注意：聚合函数不能直接和其他字段列一起查找：可能会报错；但是不一定报错，注意场景
  分组====》group by 字段名
  实际上按照某一字段进行分组就是将每一组折叠起来只显示每一组中的第一个记录；
  分组的意义在于以和聚合函数一起配合使用；
  表示：只查询gender字段，并且按其将其分组；
  select gender,count(*) from xxx group by gender;
  次数的聚合函数count是对分组的每个组进行求值
  +--------+----------+
  | gender | count(*) |
  +--------+----------+
  | 女     |        7 |
  | 男     |        6 |
  +--------+----------+
    2 rows in set (0.00 sec)
  注意：和分组配合使用的聚合函数group_concat(name)的作用显示分组的每一组该字段的值
  select  gender,group_concat(name，age,cls_id)  from xxx  group by gender
  +--------+-----------------------------------------------------------------------------------+
  | gender | group_concat(name,':', age,':')                                                   |
  +--------+-----------------------------------------------------------------------------------+
  | 男     | 小明4:16:,小s:19:,小明dsafsda:11:,小明sadf:64:,小明sdafs:68:,小明sss:78:          |
  | 女     | 小明:11:,小明1:14:,小明2:17:,小明3:19:,小明5:13:,小明sdaf:10:,小明sdafdsafdsa:28: |
  +--------+-----------------------------------------------------------------------------------+
    2 rows in set (0.00 sec)
  3.having的用法，having作为分组的一个条件使用：having是对查出来的结果进行条件判断，而
  where是对原表进行判断；

### 4. 分页limit  start,count：注意
  1. select * from xxx limit 3;  注意只有一个参数表示查询的个数
  2. select * from xxx  limit 2,2; 注意如果有两个参数表示查询的开始个数和查询的总个数，注意不能省略逗号；
  3. 注意：limit 语句只能放在一个sql语句的最后

### 5. 链接查询、多表查询
  链接查询：（链接多个表，取多个表的共有值）
  
  1. 内连接：取多个表的交集：其实际上的处理过程为，从当前表中的每个记录的指定字段去找其他表对应的记录
    合并成为一行记录的过程；如果两个表都有该字段则合并显示，这就是内连接
    select * from xxx inner join xxxc;  这是一个简单的内连接查询；但是该连接没有去重，xxx表的每一个数据
    都对应了xxxc表中的多个记录；可以使用on作为条件
    select * from  xxx inner join xxxc on xxx.cls_id = xxxc.name;
    但是此时还有个问题，就是两个表的相同键都存在，因为我们显示了两个表的所有；正确的方法应该为
    select xxx.*,xxxc.name  from  xxx inner join xxxc on xxx.cls_id = xxxc.name;
    为了方便，我们可以将表通过as起别名；

  2. 外连接，外连接分为左连接和又连接：
    左连接：以join左边的表为基准去其他表去找记录，如果左边的表的指定字段在右边的表没有字段则显示null，
    通常内连接只有两个表都有该字段才会显示一条合并后的记录；

    右连接原理类似、
    连接的返回值通常是一个新表，是一个新的结果，可以通过having 加条件进行筛选；
    （where 通常用于对原表进行条件查询，having通常用于对一个结果或者新表进行条件查找；）
  3. 全外连接；

  4. 什么是自关联使用场景：省市三级联动、
    select *  from areas as privince inner join areas as ticy  on   cith.pid = provice.aid having provice.atitle = 'shandong'
    这就是自关联的使用场景、就是自己内连接自己的表；
  
### 6.  子查询
  1. 查询语句先执行子查询（子查询稍微有点消耗性能）：
  select * from xxx where height = ( select max(height)  from xxx ) ;
  这是一个包含子查询的拆查询语句；将来会先执行子查询语句；
  子查询又分为三类：
  + where型：子查询的结果是作为外部查询的条件进行使用的，
  + from型：子查询的结果是作为外部查询的筛选范围来使用的
  + exist型：子查询的结果是作为外部查询的条件使用的；

### 7.  数据库的设计：

1. 范式（NF）：数据库的范式遵循的几种规范，分为八种，一般前三种就行了
第一范式(1NF)：表中每一个字段的信息不可再拆分：
第二范式(2NF)：一个表中必须有一个主键，且其他非主键必须依赖于我们的主键：
第三范式(3NF)：非主键必须直接依赖于主键、不能存在传递依赖，即不能存在非主键列a依赖于非主键b，
非主键b依赖于主键的情况

2. E-R模型：
    + 一对多
    + 多对一
    + 多对多

## 四、sql的高级语法

### 0. select语句的六大字句：
  且语句之间的顺序只能是1~6;
  + （1）from 子句
  + （2）where子句
  + （3）group by 子句  ：经常和分组函数如count、avg、max等函数配合使用，用来处理分组的一些数据
  + （4）having 子句  ：其后可以再更条件，通常来说是对查询结果进行在筛选处理等等
  + （5）order by子句  ：desc表示降序，默认是升序asc，可以根据多个字段进行排序，前者字段相同采用后者排序方式
  + （6）limit 子句  :limit 后常接两个参数m，n表示从m条开始筛选，筛选n条记录；

### 1. 数据库的视图
  1. 视图试图是从一个或几个基本表(或视图)导出来的表。它与基本表不同，是一个虚表。
  数据库中只存放视图的定义，而不存放视图对应的数据，这些数据仍存放在原来的基本表中。
  所以一旦基本表中的数据发生变化，从视图中查询出来的数据也就随之改变了。
  从这个意义上说，视图就像是一个窗口，透过它可以看到数据库中自己感兴趣的数据及其变化。
  2. 视图的创建方法
  create view vvv_p as  select * from good where cate_name = '台式机';
  3.  数据表视图的好处：
  提高了重用性就像一个函数、对数据重构却不影响程序的运行、
  提高了安全性能，对不同的用户开放、让数据更加清晰；

### 2. 数据库中的事务
  事务的好处：可以防止出现不可避免的意外情况下数据可以恢复到最开始的状态；即要么都成功要么都失败
  所谓事务就是一个操作序列，这些操作要么都执行、要么都不执行，它是一个不可分割的工作单位；
  事务的四大特性：ACID即
  A：原子性 C一致性 I隔久性： D持久性；
  开启事务的方法：start transaction/begin
  提交事务：commit
  回滚事务rollback；回滚后回到事务开始状态；不发生修改；

  注意：mysql客户端是默认开启事务的；
  取消默认开始的事物、即取消自动提交，使得我们这次链接对数据库的操作必须
  手动提交，语法为：set autocommit = false;
  以上方式开启事务范围较广，一下方式只为指定语句开始mysql事务；
  start transaction;
  ····sql语句
  commit; 或者是rollback;
  > 针对不同安全级别的要求，设置了不同的隔离级别:
    1. read uncommitted: 可以读取到未提交的，这是安全等级最低的
      事务1，可以读取到事务2已经修改的部分，但是还有没正式的提交数据，会
      出现脏读、不可重复读、幻读。
    2. read committied：读取到已经提交的数据，事务1只能读取到事务2已经提交的数据，会出现不可重复读、幻读、
    3. repeatbale read：接二觉了不可重复读问题，他会等待事务2已经结束了才能读取到某个记录，从而解决了重复读取值不同
    4. serializable： 解决了幻读问题，即两次事务作用在不同的记录上时，事务1必须等待事务2操作结束了之后才能进行读取；最高安全等级。
    名词解释：
    脏读：事务1读取到了事务2未提交的数据；
    不可重复读：事务1会等待事务2通过rollback或者commit结束了才能读取某个记录，即不可重复读取问题；
    幻读：事务1前后两次读取到的不同记录值不同等，即两个事务作用在不同的记录上，事务2也会等待事务1结束才才能读取
        

  ```
  select @@transaction_isolation;  用于查看当前会话的事务的等级：默认是repeatable read;
  set  transaction_isolation = "read-uncommitted"  -- 设置事务安全等级为read uncommitted等
  ```
  事务的回滚和提交是对数据操作语句DML有效的，对create、alter等数据定义语句DDL是无效的

### 3.  数据库中的索引
索引 ==》索引是一种特殊的文件比如（innodb数据表上的索引是表空间的一个组成部分）
他们包含着对数据表里的所有记录的引用指针；
索引能加快数据库的查询速度；索引的目的在于提高效率；
create  index 索引 on 表明里的字段（字段的最大长度）;
create  index tt on xxxss(num(50));
| Query_ID | Duration   | Query                                                              |
+----------+------------+--------------------------------------------------------------------+
|        1 | 0.00055875 | desc xxxss                                                         |
|        2 | 0.00679775 | select *  from xxxss where num = 'hahahahahhahahhahahha-----49999' |
|        3 | 0.03975900 | create  index tt on xxxss(num(50))                                 |
|        4 | 0.00052425 | select *  from xxxss where num = 'hahahahahhahahhahahha-----49999' |
+----------+------------+--------------------------------------------------------------------+
通过第二次和第四次的比较，通过索引查询效率大大提高；

show index from xxxss显示索引的方法；

注意：创建太多的索引将会影响更新和插入的速度，因为它需要更新文件的索引；所有只有进程查询的表才建议创建索引；

数据库中的索引

### 4. 了解sql账户管理：（保证将来数据的安全）

  1. 通过desc查看数据库用户列表（改表在数据库mysql下）
  select user,host  from user;
  2. 创建账户和授权
  grant  权限列表 on 数据库 to 用户名@访问主机 indetified by 密码
  grant select on xxxss.* to 'zhansan'@'localhost' identified by '123456789' ;
  注意select此时是权限；
  3. 建议不要使用远程登录、非常危险：
  4. 删除数据库用户的方法：
  drop user zhansan@localhost;
  MYSQL的主从：从一个数据库服务器到其他服务器上、在复制数据时、一个服务器充当主服务器、
  mysql服务器之间的主从同步是义域二进制日志机制的，主服务器使用二进制日志来记录数据库的变动情况；
  从服务器通过读取和执行改日志文件来保持和主服务器的一致；
  完成主从服务器之间的同步建议看csdn
  作用：用于数据库的备份；且随时备份；读写分离（写入数据让主服务器响应，读数据让从服务器响应）


## 五、数据库与python的交互
### 1.  mysql知识补充
  1. 切记分组之后的查询字段的函数是对每一组进行操作（分组后的查询是对每一组的查询；）
  select  cate_name,avg(price) from good group by cate_name;
  2. 外键插入的方法：怎么给一张表添加外键（两种种方法）
    通过reference 加其他表的主键比如xxx(id)\CONSTRAINT是约束的意思
    外键一般尽量少使用：会降低数据库的性能；
    cs_id int(30) references classes(id),（包含references 和指定表的字段的字段就是外键；）
    FOREIGN KEY (cs_id) REFERENCES classes(id)
    CONSTRAINT `FK_ID_CS` FOREIGN KEY (cs_id) REFERENCES classes(id)
  3. 如何取消外键：alter table 表名 drop foreign key 外键名称：

### 2.  pymysql使用
1.  window系统下pip3 install pymysql
2. 创建链接数据库connect_t =  connect(多值字典参数)
3. cursor  = connect_t.cursor()创建邮标cursor去操作数据库
4. cursor.execute('select * from xxx;')操作数据库
5. cursor.close()  connect_t.close() 依次关闭游标和链接
6. 注意：处理查询操作必须都要提交我们的链接，connect.commit()

{% codeblock %}
from pymysql import *

def main():
    connect_t =  connect(host='localhost',port=3306,
    user='root',password='961948438',database='python',charset='utf8')
    cursor  = connect_t.cursor()
    cursor.execute('select * from xxx;')
    print('======')
    print(cursor.fetchmany(10))
    cursor.close()
    connect_t.close()
    pass

if __name__ == '__main__':
    main()
{% endcodeblock %}
6. 注意：sql的查询语句不需要利用链接（不是游标cursor）去调用commit提交：
7. 但是其他的增删改操作需要利用链接对象去调用commit方法去确认语句，如果此时不想确认sql操作可以调用
8.  链接对象下的rollback（）方法，但此时递增的数据库表字段可能已经发生了递增；

### 3. 了解sql注入
  1. 什么是sql注意：
  在程序事先定义好的 查询语句中添加额外的SQL语句 ，在管理员不知情的情况下实现非法操作，以此来实现 欺骗数据库服务器执行非授权的任意查询 ，从而进一步得到相应的数据信息。
  SQL注入通俗说就是：
  2. 通过SQL语句找到破绽，进行非法的数据读取。
  python可以通过execute（sql语句，[参数]）方法让execute自行拼接，而不是通过
  sql = ’select * from xxx  %s‘ %  inputname 手动拼接；








## 六、数据库与java的交互
### 一、先大体搞清楚连接时，常用元素都是什么作用
  1. java.sql.Driver 它在MySQL Connector / J 中实现的类的名称 是 com.mysql.jdbc.Driver，也就是驱动程序。你可以把它理解为发动机，没了它，其他都没扯。（8版本后的库中驱动在该包下：com.mysql.cj.jdbc.Driver）
  2. URL 有了发动机之后，你要开始利用它的力量，也就是为它指明前进的方向，而URL就是它的方向。值得一提的是，目前连接URL的通用格式是这样的

  ```
  protocol//[hosts][/database][?properties]
  protocol：一种连接协议，约定的规则。
	hosts：主机名，通常后跟端口号，例如当前主机默认就是localhost:3306，当然也可以多主机连接，这里暂时不表。
	database:数据库嘛
  -- 注意：不带useSSL=false参数可能报错！
  -- 其实应用测试时也就是这样的：
  jdbc:mysql://localhost:3306/test
  ```

  3. username 数据库连接的用户名 passowrd 连接密码
  4. Connection 与数据库建立连接关系,通常为 DriverManager.getConnection（url, username, password）。这里可以很明显的感觉出来，每个元素所产生的作用。
  5. Statement / PreparedStatement ，需要特别注意的是，我们提倡使用后者，也就是预编译语句。PreparedStatement好处：简化Statement中的操作提高执行语句的性能可读性和可维护性更好在代码示例中，语法会详细体现。
  6. 有了PreparedStatement语句之后，我们可以采用以下语句进行执行：executeQuery(String) 查询executeUpdate(String SQL) 更新数据execute(String SQL) 如果你不知道是查询还是修改，可以使用这个语句

### 二、示例代码及注释
  0. 关键字说明：
  > 常用的接口
    1：Connection链接
    2. Statement和PreparedStatement：增删改查
    3. ResultSet：接收和处理查询结果
    4. DriverManager： 驱动管理类。

  > jdbc程序的编写步骤
    1：注册驱动（）
    2. 链接数据库 用到DriverManager 和Connection；
    3. 操作数据库： 数据库的增删改查
    4. 断开链接，关闭对应各种数据资源；jdbc：mysql://localhost:3306/desb

  1. 首先要导入mysql-connector-java 的jar包
  2. 在数据库中准备如下格式的表
      create table user(
        username varchar(10) primary key,
        password varchar(10) not null,
        age int not null,
        gender enum("男","女") not null,
      )
  3. 具体实现代码如下：
  
  ```
  import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import cn.bang.user.domain.User;

public class JdbcUserDao implements UserDao {

	@Override
	public void add(User form) {
		String driverClassName = "com.mysql.jdbc.Driver";	//启动驱动
		String url = "jdbc:mysql://localhost:3306/test";	//设置连接路径
		String username = "root";	//数据库用户名
		String password = "123";	//数据库连接密码
		Connection con = null;		//连接
		PreparedStatement pstmt = null;	//使用预编译语句
		ResultSet rs = null;	//获取的结果集
		try {
			Class.forName(driverClassName); //执行驱动
			con = DriverManager.getConnection(url, username, password); //获取连接
			
			
			String sql = "INSERT INTO USER VALUES(?,?,?,?)"; //设置的预编译语句格式
			pstmt = con.prepareStatement(sql);
			pstmt.setString(1, form.getUsername());
			pstmt.setString(2, form.getPassword());
			pstmt.setInt(3, form.getAge());
			pstmt.setString(4, form.getGender());
			pstmt.executeUpdate();
		} catch (Exception e) {
			throw new RuntimeException(e);
		}finally {
			//关闭资源,倒关
			try {
			if(rs != null) rs.close();
			if(pstmt != null) pstmt.close();
			if(con != null) con.close();  //必须要关
			} catch (Exception e) {
			}
		}
		

	}

	@Override
	public User findByUsername(String username) {
		String driverClassName = "com.mysql.jdbc.Driver";
		String url = "jdbc:mysql://localhost:3306/test";
		String mysqlusername = "root";
		String password = "123";
		Connection con = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			
			Class.forName(driverClassName);
			con = DriverManager.getConnection(url, mysqlusername, password);
			
			
			String sql = "SELECT * FROM USER WHERE username=?";
			pstmt = con.prepareStatement(sql);
			pstmt.setNString(1, username);

			rs = pstmt.executeQuery();
			if(rs == null) {
				return null;
			}
			if(rs.next()) {
			User user = new User();
			user.setUsername(rs.getString("username"));
			user.setPassword(rs.getString("password"));
			user.setAge(rs.getInt("age"));
			user.setGender(rs.getString("gender"));
			
			
			return user;
		} else {
			return null;
		}
		} catch (Exception e) {
			throw new RuntimeException(e);
		}finally {
			//关闭资源,倒关
			try {
			if(rs != null) rs.close();
			if(pstmt != null) pstmt.close();
			if(con != null) con.close();  //必须要关
			} catch (Exception e) {
			}
		}
	}
}

  ```