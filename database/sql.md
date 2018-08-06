<h1 id="1">一、概述</h1>
结构化查询语言（Structed Query Language，SQL）是关系数据库的标准语言，也是一个通用的、功能极强的关系数据库语言。其功能不仅仅是查询。而是包括数据库模式创建、数据库的插入与修改、数据库安全性完整性定义与控制等一系列功能。
<h1 id="2">二、数据定义</h1>
一个关系数据库管理系统的实例中可以建立多个数据库，一个数据库中可以建立多个模式，一个模式下通常包括多个表、视图和索引等数据库对象。
<h2 id="2.1">模式</h2>

### 定义模式
SQL中，模式定义语句如下：

    CREATE SCHEMA <模式名> AUTHORIZATION <用户名>;
    例：CREATE SCHEMA "TEST" AUTHORIZATION ZHANG;

如果没有指定模式名，那么模式名隐含为用户名。
### 删除模式

    DROP SCHEMA <模式名> <CASCADE|RESTRICT>;
    例：DROP SCHEMA ZHANG CASCADE;

其中CASCADE和RESTRICT两者必选其一。CASCADE(级联)，表示在删除模式的同时把该模式中所有数据库对象全部删除;RESTRICT(限制)，表示如果该模式中已经定义了下属的数据库对象（如表、视图等），则拒绝该删除语句的执行。
<h2 id="2.2">基本表</h2>

### 定义基本表

    CREATE TABLE Student
        (Sno CHAR(9) PRIMARY KEY,   /*列级完整性约束条件，Sno是主键*/
        Sname CHAR(20) UNIQUE,      /*Sname取唯一值*/
        Ssex CHAR(2),
        Sage AMALLINT,
        Sdept CHAR(20)
        );
    
### 修改基本表
    
    ALTER TABLE <表名>
    [ADD [COLUMN] <新列名> <数据类型> [完整性约束]]
    [ADD <表级完整性约束>]
    [DROP [COLUMN] <列名> [CASCADE|RESTRICT]]
    [DROP CONSTRAINT <完整性约束名> [RESTRICT|CASCADE]]
    [ALTER COLUMN <列名> <数据类型>];
添加列

    ALTER TABLE Student ADD S_entrance DATE;

删除表

    DROP TABLE <表名> [REESTRICT|CASCADE];

