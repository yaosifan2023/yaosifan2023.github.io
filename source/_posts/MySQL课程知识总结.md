---
title: MySQL 课程知识分章总结
date: 2026-09-27 23:30:00
tags: [MySQL, SQL, 数据库, 学习笔记]
categories: 数据库
cover: /img/mysql-course-cover.jpg
---

## MySQL 课程知识分章总结

跟着 B 站黑马程序员的 MySQL 全套课程学习（[课程地址](https://www.bilibili.com/video/BV1Kr4y1i7ru/)，共 195 集约 30 小时）。这篇文章把课程知识按章节整理：第一节是学习路线，后面每一节对应课程的一章。每章末尾附对应的视频分 P。

## 第一节：学习路线

![MySQL 学习路线图](/img/mysql-roadmap.svg)

- **基础篇（P1–57）**：概述、SQL 四类语句、函数、约束、多表查询、事务
- **进阶篇（P58–152）**：存储引擎、索引与优化、视图、存储过程、触发器、锁、InnoDB 底层
- **运维篇（P153–195）**：日志、主从复制、分库分表、读写分离

> 我的进度：P1–P23 已完成（✅ 对应 [MySQL 学习笔记 01：增删改查](/2026/09/27/MySQL学习笔记01-增删改查/)），从 DCL 开始继续往后学。

## 第二节：基础篇

### 2.1 数据库概述

- **数据库（DB）**：存储数据的仓库；**数据库管理系统（DBMS）**：操纵和管理数据库的软件（如 MySQL）；**SQL**：操作关系型数据库的编程语言。
- 关系型数据库：数据建立在关系模型上，由多张相互连接的**二维表**组成。常见的有 MySQL、Oracle、SQL Server、PostgreSQL。
- MySQL 数据模型：一个数据库服务器上可以建多个数据库，每个数据库有多张表，表中是行数据。
- SQL 语句分类（课程主线）：

| 分类 | 全称 | 作用 |
|------|------|------|
| DDL | Data Definition Language | 定义数据库对象：库、表、字段 |
| DML | Data Manipulation Language | 对表中数据增删改 |
| DQL | Data Query Language | 查询表中数据 |
| DCL | Data Control Language | 控制用户和权限 |

视频：[P1–P4](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=1)

### 2.2 DDL——数据定义

**库操作：**

```sql
CREATE DATABASE IF NOT EXISTS study;   -- 创建
SHOW DATABASES;                        -- 查询所有库
USE study;                             -- 切换库
SELECT DATABASE();                     -- 查看当前库
DROP DATABASE IF EXISTS study;         -- 删除
```

**表操作：**

```sql
CREATE TABLE student (
    id    INT PRIMARY KEY AUTO_INCREMENT COMMENT '学号',
    name  VARCHAR(20) NOT NULL COMMENT '姓名',
    age   INT COMMENT '年龄'
) COMMENT '学生表';

SHOW TABLES;      -- 查询当前库所有表
DESC student;     -- 查询表结构
SHOW CREATE TABLE student;  -- 查询建表语句

ALTER TABLE student ADD nickname VARCHAR(20);       -- 加字段
ALTER TABLE student MODIFY age INT DEFAULT 0;       -- 改类型
ALTER TABLE student CHANGE nickname nick VARCHAR(30); -- 改字段名和类型
ALTER TABLE student DROP COLUMN nick;               -- 删字段
ALTER TABLE student RENAME TO stu;                  -- 改表名
DROP TABLE IF EXISTS stu;                           -- 删表
TRUNCATE TABLE stu;  -- 删除表并重建（属 DDL）
```

**常用数据类型：**

| 类型 | 说明 |
|------|------|
| TINYINT / INT / BIGINT | 整数，按范围选 |
| FLOAT / DOUBLE / DECIMAL(M,D) | 小数，金额用 DECIMAL 精确存储 |
| CHAR(n) / VARCHAR(n) | 定长 / 变长字符串 |
| TEXT / BLOB | 长文本 / 二进制（如图片，一般不存表里） |
| DATE / TIME / DATETIME / TIMESTAMP | 日期 / 时间 / 两者 / 时间戳 |

设计原则：字段能用小类型就不用大的，变长字符串用 VARCHAR。

视频：[P5–P11](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=5)

### 2.3 DML——数据操作（增删改）

```sql
INSERT INTO student (name, age) VALUES ('张三', 19), ('李四', 20);  -- 批量插入
UPDATE student SET age = 20 WHERE name = '张三';  -- 改（必须带 WHERE）
DELETE FROM student WHERE id = 2;                 -- 删（必须带 WHERE）
```

要点：`DELETE` 逐行删除可回滚、自增不重置；`TRUNCATE` 重建表、自增归零。详细示例和踩坑见我的[笔记 01](/2026/09/27/MySQL学习笔记01-增删改查/)。

视频：[P12–P14](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=12)

### 2.4 DQL——数据查询

```sql
SELECT 字段列表 FROM 表名
WHERE 条件              -- > < = != BETWEEN IN LIKE IS NULL
GROUP BY 分组字段
HAVING 分组后条件
ORDER BY 排序字段 DESC
LIMIT 起始索引, 记录数;
```

- **聚合函数**：`COUNT`、`SUM`、`AVG`、`MAX`、`MIN`，忽略 NULL。
- **书写顺序** SELECT→FROM→WHERE→GROUP BY→HAVING→ORDER BY→LIMIT；**执行顺序** FROM→WHERE→GROUP BY→HAVING→SELECT→ORDER BY→LIMIT。
- 判断空值只能 `IS NULL` / `IS NOT NULL`；`LIKE` 中 `%` 任意多字符、`_` 单字符。

详细语法和示例见我的[笔记 01](/2026/09/27/MySQL学习笔记01-增删改查/)。

视频：[P15–P23](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=15)

### 2.5 DCL——用户与权限

```sql
-- 用户管理（host 为 % 表示任意主机）
CREATE USER 'user1'@'%' IDENTIFIED BY '123456';
ALTER USER 'user1'@'%' IDENTIFIED WITH mysql_native_password BY 'abc123';
DROP USER 'user1'@'%';

-- 权限控制
SHOW GRANTS FOR 'user1'@'%';                      -- 查权限
GRANT ALL ON study.* TO 'user1'@'%';              -- 授予 study 库全部权限
REVOKE ALL ON study.* FROM 'user1'@'%';           -- 撤销
```

常用权限：`ALL`、`SELECT`、`INSERT`、`UPDATE`、`DELETE`、`ALTER`、`DROP`。

视频：[P24–P26](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=24)

### 2.6 函数

| 分类 | 常用函数 |
|------|---------|
| 字符串 | `CONCAT(s1,s2)`、`UPPER` / `LOWER`、`LPAD` / `RPAD(n,长度,填充)`、`TRIM`、`SUBSTRING(s,start,len)`、`REPLACE`、`LENGTH` |
| 数值 | `CEIL`、`FLOOR`、`MOD`、`RAND`、`ROUND(x,d)` |
| 日期 | `CURDATE`、`CURTIME`、`NOW`、`YEAR` / `MONTH` / `DAY`、`DATE_ADD(d,INTERVAL n DAY)`、`DATEDIFF(d1,d2)` |
| 流程 | `IF(cond,a,b)`、`IFNULL(x,默认值)`、`CASE WHEN cond1 THEN v1 ... ELSE vn END` |

```sql
-- 案例：工号补齐 5 位 + 根据分数评级
SELECT LPAD(id, 5, '0') AS 工号,
       CASE WHEN score >= 90 THEN '优秀'
            WHEN score >= 60 THEN '及格'
            ELSE '不及格' END AS 评级
FROM student;
```

视频：[P27–P31](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=27)

### 2.7 约束

| 约束 | 关键字 | 作用 |
|------|--------|------|
| 非空 | NOT NULL | 不能为 NULL |
| 唯一 | UNIQUE | 值不能重复 |
| 主键 | PRIMARY KEY | 非空且唯一，一行身份标识 |
| 自增 | AUTO_INCREMENT | 从 1 起自动递增（配合主键） |
| 默认 | DEFAULT | 插入时不指定则用默认值 |
| 检查 | CHECK(8.x 生效) | 自定义检查条件 |
| 外键 | FOREIGN KEY | 关联另一张表，保证一致性 |

```sql
-- 建表后添加外键
ALTER TABLE emp ADD CONSTRAINT fk_dept_id
    FOREIGN KEY (dept_id) REFERENCES dept(id)
    ON UPDATE CASCADE ON DELETE CASCADE;  -- 级联更新/删除
```

外键删除更新行为：`CASCADE`（级联）、`SET NULL`、`NO ACTION` / `RESTRICT`（默认，不允许）、`SET DEFAULT`。

视频：[P32–P36](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=32)

### 2.8 多表查询

**多表关系**：一对多（多的方加外键）、多对多（建中间表存两个外键）、一对一（用于拆表，外键加 UNIQUE）。

```sql
-- 内连接：查两表交集
SELECT e.name, d.name FROM emp e INNER JOIN dept d ON e.dept_id = d.id;

-- 外连接：左外包含左表全部行（右表无匹配填 NULL）
SELECT e.*, d.name FROM emp e LEFT JOIN dept d ON e.dept_id = d.id;

-- 自连接：一张表当两张用（如查员工及其上级）
SELECT a.name AS 员工, b.name AS 领导 FROM emp a, emp b WHERE a.manager_id = b.id;

-- 联合查询：UNION 去重、UNION ALL 不去重（列数类型要一致）
SELECT name FROM emp WHERE age < 20
UNION ALL
SELECT name FROM emp WHERE salary > 10000;
```

**子查询**（SQL 语句中嵌套 SELECT）：

| 类型 | 特征 | 用法 |
|------|------|------|
| 标量子查询 | 返回单个值 | `WHERE salary > (SELECT AVG(salary) FROM emp)` |
| 列子查询 | 返回一列 | 配合 `IN`、`NOT IN`、`ANY`、`SOME`、`ALL` |
| 行子查询 | 返回一行 | `WHERE (salary, manager_id) = (SELECT ...)` |
| 表子查询 | 返回多行多列 | `FROM (SELECT ...) t` 再查 |
| EXISTS | 有结果返回 1 | `WHERE EXISTS (SELECT ...)` |

视频：[P37–P50](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=37)

### 2.9 事务

一组操作要么全部成功、要么全部失败回滚。

```sql
START TRANSACTION;  -- 或 BEGIN
UPDATE account SET money = money - 100 WHERE name = 'A';
UPDATE account SET money = money + 100 WHERE name = 'B';
COMMIT;   -- 提交
-- ROLLBACK;  回滚
```

**四大特性（ACID）**：原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）。

**并发事务问题**：脏读（读到别人未提交的数据）、不可重复读（前后两次读同一行结果不同）、幻读（前后两次查询行数不同）。

**四种隔离级别**（从低到高）：

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|---------|------|-----------|------|
| READ UNCOMMITTED | 有 | 有 | 有 |
| READ COMMITTED | 无 | 有 | 有 |
| REPEATABLE READ（默认） | 无 | 无 | 有* |
| SERIALIZABLE | 无 | 无 | 无 |

```sql
SELECT @@TRANSACTION_ISOLATION;                       -- 查看隔离级别
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED; -- 设置
```

视频：[P51–P57](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=51)

## 第三节：进阶篇

### 3.1 存储引擎

- MySQL 体系结构：连接层 → 服务层 → 引擎层（数据存储提取，**可插拔**）→ 存储层。
- `SHOW ENGINES;` 查看支持的引擎；建表时可指定 `ENGINE=InnoDB`。

| 对比项 | InnoDB（默认） | MyISAM | Memory |
|--------|---------------|--------|--------|
| 事务 | 支持 | 不支持 | 不支持 |
| 行级锁 | 支持 | 只有表锁 | 只有表锁 |
| 外键 | 支持 | 不支持 | 不支持 |
| 适用场景 | 高并发、一致性要求高 | 读多写少 | 临时表、缓存 |

视频：[P58–P65](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=58)

### 3.2 索引

**为什么快**：把无序数据变成有序的查找结构，避免全表扫描。MySQL InnoDB 采用 **B+ 树**（非叶子节点只存键、叶子节点存数据并用双向链表连接，矮胖结构磁盘 IO 少、范围查询友好）；Memory 支持 hash 索引。

**分类**：主键索引（聚簇索引，叶子存整行）、唯一索引、常规索引、全文索引（二级索引叶子存主键值，查到后**回表**）。

```sql
CREATE INDEX idx_emp_name ON emp(name);       -- 创建
SHOW INDEX FROM emp;                          -- 查看
DROP INDEX idx_emp_name ON emp;               -- 删除
```

**索引使用原则**：
- 为 WHERE、ORDER BY、GROUP BY 涉及的列建索引；区分度高的列适合
- 联合索引遵守**最左前缀法则**，从最左列开始且不跳列
- **失效场景**：索引列上做运算或函数、隐式类型转换（字符串列用数字查）、`LIKE '%xx'` 前置模糊、`OR` 两侧有非索引列
- **覆盖索引**（查询字段都在索引里）避免回表，`EXPLAIN` 的 Extra 显示 `Using index`
- SQL 提示：`... FROM emp USE INDEX(idx_name)` / `IGNORE INDEX` / `FORCE INDEX`

视频：[P66–P88](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=66)

### 3.3 性能分析

```sql
SHOW GLOBAL STATUS LIKE 'Com_______';  -- 查看增删改查访问频次
SHOW VARIABLES LIKE 'slow_query_log';  -- 慢查询日志开关
SHOW PROFILES;                         -- 每条 SQL 耗时
EXPLAIN SELECT ...;                    -- 执行计划（最重要）
```

`EXPLAIN` 关键列：**id**（大表先执行）、**type**（system > const > eq_ref > ref > range > index > ALL，ALL 全表扫描要优化）、**key**（实际用到的索引）、**rows**、**Extra**（`Using index` 好，`Using filesort` / `Using temporary` 要优化）。

视频：[P74–P78](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=74)

### 3.4 SQL 优化

- **INSERT**：批量插入 values 多行、手动事务（多条 insert 包在事务里）、主键顺序插入、大批量用 `LOAD DATA LOCAL INFILE`。
- **ORDER BY**：让排序走索引（`Using index`），否则文件排序 `Using filesort`；按联合索引顺序排。
- **GROUP BY**：同样利用索引，分组字段满足最左前缀。
- **LIMIT 深分页**：`LIMIT 1000000,10` 很慢，优化为覆盖索引 + 子查询定位起始 id：`WHERE id >= (SELECT id FROM t ORDER BY id LIMIT 1000000,1)`。
- **COUNT**：`COUNT(*)` ≈ `COUNT(1)` > `COUNT(主键)` > `COUNT(字段)`（不统计 NULL）。
- **UPDATE**：WHERE 字段必须有索引，否则行锁升级为表锁。

视频：[P89–P96](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=89)

### 3.5 视图

虚拟表，简化查询、安全（只暴露部分字段）、逻辑独立。

```sql
CREATE OR REPLACE VIEW stu_v AS SELECT id, name FROM student WHERE id <= 20;
-- 检查选项：WITH CASCADED CHECK OPTION（默认，级联检查） / WITH LOCAL CHECK OPTION
ALTER VIEW stu_v AS SELECT ...;
DROP VIEW IF EXISTS stu_v;
```

视图要可更新（不含聚合、DISTINCT、GROUP BY 等）才能 INSERT / UPDATE，且受检查选项约束。

视频：[P97–P101](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=97)

### 3.6 存储过程

事先编译好存在数据库里的 SQL 逻辑集合。

```sql
CREATE PROCEDURE p1(IN score INT, OUT grade VARCHAR(10))
BEGIN
    DECLARE i INT DEFAULT 0;      -- 声明变量
    SET i = 1;                    -- 赋值
    -- 用户变量：SET @a = 1;  系统变量：@@autocommit
    IF score >= 60 THEN SET grade = '及格';
    ELSE SET grade = '不及格';
    END IF;
    -- 循环：WHILE ... DO ... END WHILE; / REPEAT ... UNTIL ... END REPEAT; / LOOP + LEAVE
    -- 游标：DECLARE c CURSOR FOR SELECT ...; OPEN c; FETCH c INTO ...; CLOSE c;
    -- 条件处理：DECLARE EXIT HANDLER FOR NOT FOUND ...
END;

CALL p1(80, @g);   -- 调用
SELECT @g;
DROP PROCEDURE IF EXISTS p1;
```

参数模式：`IN` 输入、`OUT` 输出、`INOUT` 皆可。

视频：[P102–P115](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=102)

### 3.7 触发器

在 INSERT / UPDATE / DELETE 之前或之后自动执行的逻辑，通过 `NEW` / `OLD` 引用变更前后的行数据。

```sql
CREATE TRIGGER trg_insert AFTER INSERT ON emp
FOR EACH ROW
BEGIN
    INSERT INTO log(content) VALUES(CONCAT('新增员工：', NEW.name));
END;
-- UPDATE 可用 NEW / OLD；DELETE 只有 OLD
SHOW TRIGGERS;
DROP TRIGGER trg_insert;
```

视频：[P116–P120](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=116)

### 3.8 锁

| 粒度 | 内容 | 备注 |
|------|------|------|
| 全局锁 | `FLUSH TABLES WITH READ LOCK`，整库只读 | 用于逻辑备份；InnoDB 备份可改用 `mysqldump --single-transaction` |
| 表级锁 | 表锁（读锁/写锁）、**元数据锁 MDL**（自动加，防结构冲突）、**意向锁**（表级，快速判断表里是否有行锁） |
| 行级锁（InnoDB） | **记录锁 Record Lock**（锁索引记录）、**间隙锁 Gap Lock**（锁区间防插入）、**临键锁 Next-Key Lock**（记录+间隙，RR 默认） | 行锁锁的是索引，无索引时退化为锁全表记录 |

视频：[P121–P127](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=121)

### 3.9 InnoDB 核心原理

- **逻辑存储结构**：表空间 → 段 → 区（1MB，64 页）→ 页（16KB，磁盘管理最小单元）→ 行。
- **架构**：内存（缓冲池、change buffer、日志缓冲）+ 磁盘（系统表空间、独立表空间、redo log、undo log、双写缓冲）+ 后台线程。
- **事务原理**：原子性/持久性靠 **redo log**（崩溃恢复，WAL 先写日志）+ **undo log**（回滚、MVCC 基础）；隔离性靠**锁 + MVCC**。
- **MVCC 多版本并发控制**：三要素——表隐藏字段（`DB_TRX_ID` 事务 id、`DB_ROLL_PTR` 回滚指针）、undo log 版本链、**ReadView**（快照）。RC 每次查询生成新 ReadView（所以可重复读不了）；RR 只在第一次查询生成（所以可重复读）。

视频：[P128–P151](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=128)

## 第四节：运维篇

### 4.1 日志

| 日志 | 作用 | 开关 |
|------|------|------|
| 错误日志 | 启动/运行报错 | 默认开，`SHOW VARIABLES LIKE 'log_error'` |
| 二进制日志 binlog | 所有 DDL/DML（不含查询），用于**主从复制和数据恢复** | 默认开 |
| 查询日志 | 全部操作记录 | 默认关 |
| 慢查询日志 | 超过 `long_query_time` 的 SQL | 默认关，排查慢 SQL 必开 |

视频：[P153–P157](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=153)

### 4.2 主从复制

- **原理**：主库写 binlog → 从库 IO 线程拉取写入**中继日志 relay log** → 从库 SQL 线程重放，实现数据一致。
- 搭建：主库开 binlog 并创建复制账号（`GRANT REPLICATION SLAVE`），从库 `CHANGE MASTER TO ...` + `START SLAVE`。
- **复制延迟**：从库单线程重放是瓶颈，可并行复制、分库分表缓解。

视频：[P158–P162](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=158)

### 4.3 分库分表

单表数据量过大（一般千万级）或单库压力过大时拆分：

- **垂直拆分**：按业务拆库、按字段冷热拆表。
- **水平拆分**：按行拆到多张表/多个库，需要**分片键**。
- 中间件 **MyCat**：应用无感知地把 SQL 路由到对应分片；分片算法有范围、取模、一致性 hash、枚举、固定 hash、按日期等。
- 问题：跨分片 JOIN、分布式事务、全局唯一 ID。

视频：[P163–P187](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=163)

### 4.4 读写分离

- 写走主库、读走从库，配合主从复制使用；通过 MyCat 或程序中间层实现路由。
- 双主双从架构：两台互为主从，任一宕机另一台接管，提高可用性。

视频：[P188–P195](https://www.bilibili.com/video/BV1Kr4y1i7ru/?p=188)

## 我的配套笔记

| 笔记 | 对应章节 | 状态 |
|------|---------|------|
| [MySQL 学习笔记 01：增删改查](/2026/09/27/MySQL学习笔记01-增删改查/) | 2.2–2.4（DDL / DML / DQL） | ✅ 已完成 |
| 函数与约束 | 2.6–2.7 | 📅 计划中 |
| 多表查询与事务 | 2.8–2.9 | 📅 计划中 |
