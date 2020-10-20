# 介绍
> DB - 数据库（仓库）
>
> DBMS - 数据库管理系统（MySQL、Oracle、DB2、SqlServer）
>
> SQL - 所有数据库软件都支持的数据库语言
>
> show tables from 库名;
>
> mysql --version
>
> select version();
>
> 单行注释：#注释文字
>
> 单行注释：-- 注释文字（一定要有空格）
>
> 多行注释：/\* 注释文字 \*/
>
> SQL = DQL(查询) + DML(增删改) + DOL + TCL(事务控制)


# DQL语言
## 1.基本查询
### select查询
> select 可以查询的内容：表中字段、常量值、表达式、函数
>
> select 100; select 'john; 查询常量值

> select 10\*20; 查询表达式

> select version(); 查询函数

### 使用as或空格起别名
> 1 便于理解 2 查询的字段有重名的情况，使用别名进行区分

> select 10\*20 as 结果;

> select last_name 姓, first_name 名 from employees;

### distinct去重
> 只需要在字段前加上distinct关键字

> select distinct id from employees;

### + 的作用
> MySQL中的+号只有一个功能：运算符号
> 1. '123' + 90 在相加的时候会试图将 '123'转换为数字，转换成功则进行加法运算
> 2. 但是如果 '123' 换为 'join' 则会转换失败，转换失败则将字符型数值转换为0进行运算
> 3. 如果一方为 null，null+90 --> 结果为肯定为 null

### CONCAT 和 IFNULL
> concat 字符串拼接函数，只要有一个为null结果一定为null
>
> select concat(first_name, ':', last_name) from employees;
>
> ifnull 如果为null返回一个值
>
> ifnull(first_name, 0); 如果first_name为空则返回0，否则返回原本的值




## 2.条件查询

### 按条件表达式筛选
> 条件运算符：> < = != <> <=>

### 按逻辑表达式筛选
> && || ! and or not

### 模糊查询
> like / between and / in / is null
>
> like 一般和通配符搭配使用 - % 包含任意多个字符，_ 任意单个字符
>
> between and 两端闭区间
>
> in('a', 'b', 'c')  集合中不可用模糊查询
>
> 普通的 = 不可用于判断 null，应该用is null。或者用 <=>




## 3.排序查询
##### select 查询列表 from 表 where 筛选条件 order by 排序列表 asc/desc
> order by 一般放在查询语句的最后（除了limit外）




## 4.常见函数
**select 函数名(实参列表) [from 表]**

### 字符函数
> length(); 求字符串占用字节长度，utf-8汉字占用3个字节
>
> concat(); 字符串拼接
>
> upper(), lower(); 大小写转换函数
>
> substr('李莫愁爱上了陆展元', 4, 3); ==> '爱上了' 起始索引为1，从4开始截取3个字符长度
>
> isstr('杨不悔爱上了殷六侠', '殷六侠'); ==> 7 返回子串第一次出现的索引
>
> trim('    张翠山    '); 去掉两头空格。trim('aaaa张a翠山aaa');去掉两头 'a'
>
> lpad('殷素素', 10, '*'); 用指定的字符实现所填充指定长度. rpad()右填充
>
> replace('张无忌爱上了周芷若', '周芷若', '赵敏'); 字符串替换(全部)

### 数学函数
> round(); 绝对值四舍五入
>
> ceil(); 向上取整，返回>=该参数的最小整数
>
> floor(); 向下取整，返回<=该参数的最大整数
>
> truncate(1.6999, 1); ==> 1.6 截断（小数点后保留几位）
>
> mod(); 取模

### 日期函数
> now(); 返回当前系统日期+时间
>
> curdate(); 返回当前系统日期，不包含时间
>
> curtime(); 返回当前系统时间，不包含日期
>
> year(); month(); day();...获取指定部分，参数是一个时间
>
> str_to_date('4-3 1999', '%c-%d %Y'); 将字符转换为日期
>
> date_format(now(), '%y年%m月%d日'); 日期转换为字符串
>
> datediff(); 日期之差

### 其他函数
> version(); 查看MySQL版本
>
> datebase(); 查看当前数据库
>
> user(); 查看当前用户

### 流程控制函数
> if(10<5, '大', '小') ; 等同if else的效果
>
> case(); 等同switch分支语句
>
> case 要判断的表达式
>
> when 常量1 then 语句1
>
> when 常量2 then 语句2
>
> else 语句n
>
> end

### 分组函数(聚合函数/统计函数)
> sum - 求和  和distint搭配使用，sum(distinct salary); 去重求和(salary为工资字段)
>
> avg - 平均值
>
> max - 最大值
>
> min - 最小值
>
> count - 计算非空值的个数




## 5. 分组查询
##### select 分组函数,列 from 表 [where 筛选条件] group by 分组的列表 [order by 子句];
> select max(salary),job_id from employees group by location_id;
>
> select avg(salary),department_id from employees where email like '%a%' group by department_id;
>
> 分组前筛选: 这里使用的 where 都紧跟在 from 后面，筛选的是原始表;
>
> 分组后筛选: 如果想要在筛选的结果中再次进行筛选，则应该在 group by 后使用 having 字段.
>
> 多字段分组查询，多字段之间没有顺序之分



## 6. 连接查询/多表查询

### 分类
> 按年代分类：
> > sql92标准:仅仅支持内连接
> > sql99标准:

> 按功能分:
> > 内连接:
> > > 等值连接
> > > 非等值连接
> > > 自连接
> > 外连接:
> > > 左外连接
> > > 右外连接
> > > 全外连接
> > 交叉连接

## sql92标准
> 等值连接
> > 1. 多表等值连接的结果为多表的交集部分
> > 2. 多表的顺序没有要求
> > 3. 一般需要为表起别名，起了别名后只能使用别名
> > 4. 可以搭配排序、分组、筛选来使用
> > select name,boyName from boys,beauty where beauty.boyfriend_id=boys.id;

> 非等值连接
> > select salary,grade_level from employees e,job_grades g where salary between g.lsal and g.hsal;

> 自连接
> > select e.employee_id,e.last_name,m.employee_id,m.last_name
> > from employees e,employees m
> > where e.manager_id=m.employee_id;

### sql99标准
> select 查询列表 from 表1 别名 inner join 表2 别名 on 连接条件; ==> inner 可以省略

> 外连接 ==> 查询一个表中有而另一个表中没有的字段
> 分为主从表
> > 查询结果为主表中的所有记录，如果从表中有与主表相匹配的则显示匹配值，否则显示null
> > 外连接查询的结果 = 内连接结果 + 主表中有而从表中没有的记录
> 左外连接：left join 左边的是主表
> 右外连接：right join 右边的是主表
> 左外和右外交换两个表的顺序，可以实现同样的效果

## 7. 子查询
##### 出现其他语句中的select语句，称为子查询或内查询

### 分类
> 按子查询出现的位置：
> > select 后面> ：仅仅支持标量子查询
> > from 后面> ：支持表子查询
> > where 或 having 后面> ：标量子查询、列子查询、行子查询 ❤❤❤
> > exists 后面（相关子查询）> ：表子查询
> 按结果集的行数不同：
> > 标量子查询（结果集只有一行一列）
> > 列子查询（结果集只有一列多行）
> > 行子查询（结果集有一行多列）
> > 表子查询（结果集一般为多行多列）

### where 或 having 后面
> 1.子查询放在小括号内
> 2.子查询一般放在条件的右侧
> 3.标量子查询，一般搭配着单行操作符使用 < > =


## 8. 分页查询
> select 查询列表
> from 表
> 【type join 表2
> on 连接条件
> where 筛选条件
> group by 分组字段
> having 分组后的筛选
> order by 排序的字段】
> limit offset，size;

> offset 要显示的条目的起始索引（起始索引从0开始）
> size 要显示的条目个数
> limit 语句放在查询语句的最后

## 9. 联合查询
> union 联合：将多条查询语句的结果合并成一个结果
> 当查询内容来自多个表，表之间没有任何关系，但查询信息一样的时候使用。
> 1. 要求多条查询语句的列数是一致的
> 2. 自动去重，想要不去重应该使用 union all
> 3. 格式
> > 查询语句1
> > union
> > 查询语句


# DML 语言
##### 对数据进行操作
> 插入 insert、修改 update、删除 delete

## 1. 插入 insert
##### insert into 表名(列名...) values(值...);
> 支持一次性插入多行。
> 列的顺序可以进行调换，但是必须和 values 完全对应。
> 列数和值的个数必须一致。
> 不提供列名 ==> 默认所有列，并且 必须按顺序写。
##### insert into 表名 set 列名=值,列名=值,...



## 2. 修改 update
##### update 表名 set 列=值, 列=值, ... where 筛选条件;
> 不加 where 将会修改所有列。
> 支持修改多表记录：
> update 表1 别名 inner|left|right join 表2 别名 on 连接条件 set 列=值... where 筛选条件。


## 3. 删除 delete
##### delete from 表名 where 筛选条件;
> 删除的是一整行，delete from 删除整个表 不会删除表结构。




# DDL 数据定义语言
##### 对数据和表管理的操作
> 库的管理&&表的管理
> > 创建 create、修改 alter、删除 drop

### 1. 库的创建
> create database if not exists 库名;

### 2. 库的修改
> 更改库的字符集：
> > alter database 库名 character set gbk;

### 3. 库的删除
> drop database if exists 库名;

### 4. 表的创建
> create table 表名(列名 类型 [(长度)约束],...);

### 5. 表的修改
##### alter table 表名 add|drop|modify|change column 列名 【列类型 约束】;
> 修改列名(新的列名必须加上类型)
> > alter table 表名 change column 列名 新的列名 新类型;
> 修改列的类型或约束
> > alter table 表名 modify column 列名 新类型;
> 添加新列
> > alter table 表名 add column 列名 类型;
> 删除列
> > alter table 表名 drop column 列名;
> 修改表名
> > alter table 表名 rename to 新表名;

### 6. 表的删除
> drop table if exists 表名;

### 7. 表的复制
> 仅仅复制表的结构
> > create table copy like author;
> 复制表的结构+数据
> > create table copy2 select * from author;
> 只复制部分表
> > create table copy3 select 列名1,列名2 from 表名 where 筛选条件;

### 8. 表的约束
> 分类：六大约束
> > not null：非空，保证该字段的值不为空
> > default：默认值
> > primary key：主键，用于保证该字段的值具有唯一性，并且非空
> > unique：唯一键，用于保证该字段的值具有唯一性，可以为空
> > foreign key：外键，用于限制两个表的关系，用于保证该字段的值必须来自主表的关联列的值，在从表中添加外键约束，用于引用主表中某列的值
> > auto_increment：自增长

> 添加约束的时机：
> > 1.创建表时
> > 2.修改表时

> 约束的添加分类
> > 1.列级约束
> > > 六大约束语法都支持，但外键没有效果
> > 2.表级约束
> > > 除了非空、默认，其他的都支持

> 主键和唯一键对比
> > > 唯一性> 是否允许为空> 一个表中可以有多少> 是否允许组合
> 主键> > √> > > ×> > 至多一个> > > > √
> 唯一> > √> > > √> > 可以多个> > > > √

> 外键：
> > 1. 要求在从表设置外键关系
> > 2. 从表的外键列类型和主表的关联列的类型要求一致或兼容
> > 3. 主表的关联列必须是一个key（一般是主键或者唯一键）
> > 4. 插入数据时先插入主表后插入从表，删除数据时正好相反


# TCL 事务控制语言
#####一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行。

### ACID 特性
> 1. 原子性：事务要么全部发生，要么全部不发生
> 2. 一致性：事务必须使数据从一个一致性状态转换到另外一个一致性状态
> 3. 隔离性：一个事务的执行不受其他事务的干扰
> 4. 持久性：事务一旦被提交，对数据库的影响就是永久性的

### 并发带来的问题
> 1. 脏读：对于两个事务T1 T2，T1读取了已经被T2更新但是还没有被提交的字段之后，若T2回滚，T1读取的内容就是临时无效的。
> 2. 不可重复读：对于两个进行中的事务T1 T2，T1读取了一个字段，然后T2更新了该字段之后，T1再次读取同一个字段，值就不同了。
> 3. 幻读：对于两个事务T1 T2，T1从一个表中读取了一个字段，然后T2在该表中插入了新行，T1再次读取同一个表就会发现多出了几行。和脏读类似，脏读侧重的是更新数据，幻读侧重的是插入数据。

### 事务的隔离级别
>  > > > > > > 脏读> > 幻读> > 不可重复读
> read uncommitted:> > √> >   √> > >   √
> read committed: > > ×> >   √> > >   √
> repeatable read:> > ×> >   ×> > >   √
> serializable:> > > ×> >   ×> > >   ×

> mysql 中默认 第三个隔离级别 repeatable read



### 事务的创建
> 隐式事务：事务没有明显的开启和结束的标记，比如insert update delete

> 显示事务：事务具有明显的开启和结束的标记
> 前提：必须先设置自动提交功能为禁用

> 步骤1：开启事务
> > set autocommit=0;
> > start transaction;
> 步骤2：编写事务中的sql语句(savepoint a;设置保存点 rollback to a;回滚到保存点)
> 步骤3：结束任务

> commit; 提交事务
> rollback; 回滚事务

# 视图
##### create view 视图名 as 查询语句;


### 变量、存储过程、函数、分支结构、循环结构



## 字符集
> 查看字符集：show variables like 'character%';
> 默认字符集使用：latin1

## MyISAM 和 InnoDB 对比
> 主外键：MyISAM 不支持、InnoDB 支持
> 事务： MyISAM 不支持、InnoDB 支持
> 行表锁：MyISAM 表锁、InnoDB 行锁 适合并发的操作
> 缓存： MyISAM 只缓存索引 不缓存真实数据、InnoDB 不仅缓存索引还要缓存真实数据 对内存要求高
> 表空间：MyISAM 小、InnoDB 大
> 关注点：MyISAM 性能、InnoDB 事务

## 性能下降 sql 慢的原因
> 查询语句写的太烂（有索引但是不用）
> 索引失效

## 机读sql语句 ==> 从from开始

## 索引
> 官方：索引（Index）是帮助MySQL高效获取数据的数据结构

> 除了数据外，数据库系统还维护这满足特定查找算法的数据结构，在数据结构上实现高级查找算法，这种数据结构就是索引。

