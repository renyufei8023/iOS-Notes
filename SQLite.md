#SQLite
###什么是SQLite
SQLite是一款轻型的嵌入式数据库
它占用资源非常的低，在嵌入式设备中，可能只需要几百K的内存就够了
它的处理速度比Mysql、PostgreSQL这两款著名的数据库都还快
###什么是数据库
数据库（Database）是按照数据结构来组织、存储和管理数据的仓库
数据库可以分为2大种类

- 关系型数据库（主流）
- 对象型数据库

###数据库是如何存储数据
数据库的存储结构和excel很像，以表（table）为单位
####数据库存储数据的步骤
1. 新建一张表(table)
2. 添加多个字段（column，列，属性）
3. 添加多行记录（row，record，每行存放多个字段对应的值）
###SQL语句
- 什么是SQL
 - SQL（structured query language）：结构化查询语言
 - SQL是一种对关系型数据库中的数据进行定义和操作的语言
 - SQL语言简洁，语法简单，好学好用
- 什么是SQL语句
 - 使用SQL语言编写出来的句子\代码，就是SQL语句
 - 在程序运行过程中，要想操作（增删改查，CRUD）数据库中的数据，必须使用SQL语句
- SQL语句的特点
 - 不区分大小写
 - 每条语句都必须以分号 ; 结尾
- SQL常见关键字
 - select、insert、update、delete、from、create、where、desc、order、by、group、table、alter、view、index等等
- 数据库中不可以使用关键字来命名表、字段

###SQL语句的种类
- 数据定义语句（DDL：Data Definition Language）
 - 包括create和drop等操作
 - 在数据库中创建新表或删除表(create table或 drop table)
- 数据操作语句（DML：Data Manipulation Language）
 - 包括insert、update、delete等操作
 - 上面的3种操作分别用于添加、修改、删除表中的数据
- 数据查询语句（DQL：Data Query Language）
 - 可以用于查询获得表中的数据
 - 关键字select是DQL（也是所有SQL）用得最多的操作
 - 其他DQL常用的关键字有where，order by，group by和having

####创表

```
格式
create table 表名 (字段名1 字段类, 字段名2 字段类型2,型1…) ;
create table if not exists 表名 (字段名1 字段类型1, 字段名2 字段类型2, …) ;

示例
create table t_student (id integer, name text, age inetger, score real) ;

```
####删表

```
格式
drop table 表名 ;
drop table if exists 表名 ;

示例
drop table t_student ;

```
####字段类型
- SQLite将数据划分为一下几种存储类型
 - integer : 整型值
 - real : 浮点值
 - text : 文本字符串
 - blob : 二进制数据（比如文件）
- 实际上SQLite是没有类型的
 - 就算声明为integer类型，还是能存储字符串文本（主键除外）
 - 建表时声明啥类型或者不声明类型都可以，也就意味着创表语句可以这么写：
create table t_student(name, age);

为了保持良好的编程规范、方便程序员之间的交流，编写建表语句的时候最好加上每个字段的具体类型

####插入数据

```
格式
insert into 表名 (字段1, 字段2, …) values (字段1的值, 字段2的值, …) ;

示例
insert into t_student (name, age) values (‘mj’, 10) ;

注意
数据库中的字符串内容应该用单引号 ’ 括住

```
####更新数据

```
格式
update 表名 set 字段1 = 字段1的值, 字段2 = 字段2的值, … ; 

示例
update t_student set name = ‘jack’, age = 20 ; 

注意
上面的示例会将t_student表中所有记录的name都改为jack，age都改为20

```
####删除数据

```
格式
delete from 表名 ;

示例
delete from t_student ;

注意
上面的示例会将t_student表中所有记录都删掉

```

####条件语句
如果只想更新或者删除某些固定的记录，那就必须在DML语句后加上一些条件

```
条件语句的常见格式
where 字段 = 某个值 ;   // 不能用两个 =
where 字段 is 某个值 ;   // is 相当于 = 
where 字段 != 某个值 ; 
where 字段 is not 某个值 ;   // is not 相当于 != 
where 字段 > 某个值 ; 
where 字段1 = 某个值 and 字段2 > 某个值 ;  // and相当于C语言中的 &&
where 字段1 = 某个值 or 字段2 = 某个值 ;  //  or 相当于C语言中的 ||

```
####DQL语句

```
格式
select 字段1, 字段2, … from 表名 ;
select * from 表名;   //  查询所有的字段

示例
select name, age from t_student ;
select * from t_student ;
select * from t_student where age > 10 ;  //  条件查询

```
####起别名

```
格式(字段和表都可以起别名)
select 字段1 别名 , 字段2 别名 , … from 表名 别名 ; 
select 字段1 别名, 字段2 as 别名, … from 表名 as 别名 ;
select 别名.字段1, 别名.字段2, … from 表名 别名 ;

示例
select name myname, age myage from t_student ;
给name起个叫做myname的别名，给age起个叫做myage的别名

select s.name, s.age from t_student s ;
给t_student表起个别名叫做s，利用s来引用表中的字段

```

####计算记录中的数量

```
格式
select count (字段) from 表名 ;
select count ( * ) from 表名 ;

示例
select count (age) from t_student ;
select count ( * ) from t_student where score >= 60;

```

####排序

```
查询出来的结果可以用order by进行排序
select * from t_student order by 字段 ;
select * from t_student order by age ;

默认是按照升序排序（由小到大），也可以变为降序（由大到小）
select * from t_student order by age desc ;  //降序
select * from t_student order by age asc ;   // 升序（默认）

也可以用多个字段进行排序
select * from t_student order by age asc, height desc ;
先按照年龄排序（升序），年龄相等就按照身高排序（降序）

```
####limit
使用limit可以精确地控制查询结果的数量，比如每次只查询10条数据

```
格式
select * from 表名 limit 数值1, 数值2 ;

示例
select * from t_student limit 4, 8 ;
可以理解为：跳过最前面4条语句，然后取8条记录

```
limit常用来做分页查询，比如每页固定显示5条数据，那么应该这样取数据

```
第1页：limit 0, 5
第2页：limit 5, 5
第3页：limit 10, 5
…
第n页：limit 5*(n-1), 5

```
###约束
####简单约束
建表时可以给特定的字段设置一些约束条件，常见的约束有
- not null ：规定字段的值不能为null
- unique ：规定字段的值必须唯一
- default ：指定字段的默认值

```
create table t_student (id integer, name text not null unique, age integer not null default 1) ;
name字段不能为null，并且唯一
age字段不能为null，并且默认为1

```
####主键约束
如果t_student表中就name和age两个字段，而且有些记录的name和age字段的值都一样时，那么就没法区分这些数据，造成数据库的记录不唯一，这样就不方便管理数据
良好的数据库编程规范应该要保证每条记录的唯一性，为此，增加了主键约束。也就是说，每张表都必须有一个主键，用来标识记录的唯一性
- 什么是主键
 - 主键（Primary Key，简称PK）用来唯一地标识某一条记录
 - 例如t_student可以增加一个id字段作为主键，相当于人的身份证
 - 主键可以是一个字段或多个字段
#####主键的设计原则
1. 主键应当是对用户没有意义的

2. 永远也不要更新主键

3. 主键不应包含动态变化的数据

4. 主键应当由计算机自动生成
#####主键的声明

```
在创表的时候用primary key声明一个主键
create table t_student (id integer primary key, name text, age integer) ;
integer类型的id作为t_student表的主键

主键字段
只要声明为primary key，就说明是一个主键字段
主键字段默认就包含了not null 和 unique 两个约束

如果想要让主键自动增长（必须是integer类型），应该增加autoincrement
create table t_student (id integer primary key autoincrement, name text, age integer) ;

```

####外键约束
利用外键约束可以用来建立表与表之间的联系，外键的一般情况是：一张表的某个字段，引用着另一张表的主键字段

```
新建一个外键
create table t_student (id integer primary key autoincrement, name text, age integer, class_id integer, constraint fk_student_class foreign key (class_id) references t_class (id));
t_student表中有一个叫做fk_t_student_class_id_t_class_id的外键
这个外键的作用是用t_student表中的class_id字段引用t_class表的id字段

```
###表连接查询
- 什么是表连接查询
 - 需要联合多张表才能查到想要的数据

- 表连接的类型
 - 内连接：inner join 或者 join  （显示的是左右表都有完整字段值的记录）
 - 左外连接：left outer join （保证左表数据的完整性）

##SQLite3
使用SQLite3，需要添加libsqlite3.dylib和导入主头文件
###创建，打开，关闭数据库

```objc
创建或打开数据库
// path是数据库文件的存放路径
sqlite3 *db = NULL;
int result = sqlite3_open([path UTF8String], &db); 

代码解析：
sqlite3_open()将根据文件路径打开数据库，如果不存在，则会创建一个新的数据库。如果result等于常量SQLITE_OK，则表示成功打开数据库
sqlite3 *db：一个打开的数据库实例
数据库文件的路径必须以C字符串(而非NSString)传入

关闭数据库：sqlite3_close(db);

```
###执行不返回数据的SQL语句

```objc
执行创表语句
char *errorMsg = NULL;  // 用来存储错误信息
char *sql = "create table if not exists t_person(id integer primary key autoincrement, name text, age integer);";
int result = sqlite3_exec(db, sql, NULL, NULL, &errorMsg);

代码解析：
sqlite3_exec()可以执行任何SQL语句，比如创表、更新、插入和删除操作。但是一般不用它执行查询语句，因为它不会返回查询到的数据
sqlite3_exec()还可以执行的语句：
开启事务：begin transaction;
回滚事务：rollback;
提交事务：commit;

```
###带占位符插入数据

```objc
char *sql = "insert into t_person(name, age) values(?, ?);";
sqlite3_stmt *stmt;
if (sqlite3_prepare_v2(db, sql, -1, &stmt, NULL) == SQLITE_OK) {
    sqlite3_bind_text(stmt, 1, "母鸡", -1, NULL);
    sqlite3_bind_int(stmt, 2, 27);
}
if (sqlite3_step(stmt) != SQLITE_DONE) {
    NSLog(@"插入数据错误");
}
sqlite3_finalize(stmt);

代码解析：
sqlite3_prepare_v2()返回值等于SQLITE_OK，说明SQL语句已经准备成功，没有语法问题

sqlite3_bind_text()：大部分绑定函数都只有3个参数
第1个参数是sqlite3_stmt *类型
第2个参数指占位符的位置，第一个占位符的位置是1，不是0
第3个参数指占位符要绑定的值
第4个参数指在第3个参数中所传递数据的长度，对于C字符串，可以传递-1代替字符串的长度
第5个参数是一个可选的函数回调，一般用于在语句执行后完成内存清理工作
sqlite_step()：执行SQL语句，返回SQLITE_DONE代表成功执行完毕
sqlite_finalize()：销毁sqlite3_stmt *对象


```
###查询数据

```objc
char *sql = "select id,name,age from t_person;";
sqlite3_stmt *stmt;
if (sqlite3_prepare_v2(db, sql, -1, &stmt, NULL) == SQLITE_OK) {
    while (sqlite3_step(stmt) == SQLITE_ROW) {
        int _id = sqlite3_column_int(stmt, 0);
        char *_name = (char *)sqlite3_column_text(stmt, 1);
        NSString *name = [NSString stringWithUTF8String:_name];
        int _age = sqlite3_column_int(stmt, 2);
        NSLog(@"id=%i, name=%@, age=%i", _id, name, _age);
    }
}
sqlite3_finalize(stmt);
代码解析
sqlite3_step()返回SQLITE_ROW代表遍历到一条新记录
sqlite3_column_*()用于获取每个字段对应的值，第2个参数是字段的索引，从0开始

```
#FMDB
###FMDB核心类
- FMDatabase
 - 一个FMDatabase对象就代表一个单独的SQLite数据库
 - 用来执行SQL语句

- FMResultSet
 - 使用FMDatabase执行查询后的结果集

- FMDatabaseQueue
 - 用于在多线程中执行多个查询或更新，它是线程安全的
###打开数据库
通过指定SQLite数据库文件路径来创建FMDatabase对象

```objc
FMDatabase *db = [FMDatabase databaseWithPath:path];
if (![db open]) {
    NSLog(@"数据库打开失败！");
}

```
文件路径有三种情况
- 具体文件路径
 - 如果不存在会自动创建
- 空字符串@""
 - 会在临时目录创建一个空的数据库
 - 当FMDatabase连接关闭时，数据库文件也被删除
- nil
 - 会创建一个内存中临时数据库，当FMDatabase连接关闭时，数据库会被销毁

###执行更新
在FMDB中，除查询以外的所有操作，都称为“更新”，create、drop、insert、update、delete等
使用executeUpdate:方法执行更新

```objc
- (BOOL)executeUpdate:(NSString*)sql, ...
- (BOOL)executeUpdateWithFormat:(NSString*)format, ...
- (BOOL)executeUpdate:(NSString*)sql withArgumentsInArray:(NSArray *)arguments

[db executeUpdate:@"UPDATE t_student SET age = ? WHERE name = ?;", @20, @"Jack"]

```
 
 ###执行查询
 
```objc
查询方法
- (FMResultSet *)executeQuery:(NSString*)sql, ...
- (FMResultSet *)executeQueryWithFormat:(NSString*)format, ...
- (FMResultSet *)executeQuery:(NSString *)sql withArgumentsInArray:(NSArray *)arguments

示例
// 查询数据
FMResultSet *rs = [db executeQuery:@"SELECT * FROM t_student"];

// 遍历结果集
while ([rs next]) {
    NSString *name = [rs stringForColumn:@"name"];
    int age = [rs intForColumn:@"age"];
    double score = [rs doubleForColumn:@"score"];
}

```
###FMDatabaseQueue
FMDatabase这个类是线程不安全的，如果在多个线程中同时使用一个FMDatabase实例，会造成数据混乱等问题，为了保证线程安全，FMDB提供方便快捷的FMDatabaseQueue类

```objc
FMDatabaseQueue的创建
FMDatabaseQueue *queue = [FMDatabaseQueue databaseQueueWithPath:path];

简单使用
[queue inDatabase:^(FMDatabase *db) {
    [db executeUpdate:@"INSERT INTO t_student(name) VALUES (?)", @"Jack"];
    [db executeUpdate:@"INSERT INTO t_student(name) VALUES (?)", @"Rose"];
    [db executeUpdate:@"INSERT INTO t_student(name) VALUES (?)", @"Jim"];
    
    FMResultSet *rs = [db executeQuery:@"select * from t_student"];
    while ([rs next]) {
        // …
    }
}];

使用事务
[queue inTransaction:^(FMDatabase *db, BOOL *rollback) {
    [db executeUpdate:@"INSERT INTO t_student(name) VALUES (?)", @"Jack"];
    [db executeUpdate:@"INSERT INTO t_student(name) VALUES (?)", @"Rose"];
    [db executeUpdate:@"INSERT INTO t_student(name) VALUES (?)", @"Jim"];
    
    FMResultSet *rs = [db executeQuery:@"select * from t_student"];
    while ([rs next]) {
        // …
    }
}];

事务回滚
*rollback = YES;

```



