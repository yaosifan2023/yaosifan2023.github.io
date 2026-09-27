---
title: MySQL 学习笔记 01：增删改查
date: 2026-09-27 21:30:00
tags: [MySQL, SQL, 数据库]
categories: 数据库
---

## MySQL 学习笔记 01：增删改查

### 前言

这是我第二次学 MySQL 的增删改查了。第一次学的时候只顾着跟着敲命令，敲完就忘；这次重新过一遍，把知识点系统地整理成笔记，用一个学生表贯穿全文，方便以后复习。

> 本篇对应黑马程序员课程 P5–P23（DDL / DML / DQL），完整学习路线和全部章节知识见 [MySQL 课程知识分章总结](/2026/09/27/MySQL课程知识总结/)。

### 准备工作：连接数据库

```sql
-- 在命令行里连接本机 MySQL，回车后输入密码
-- mysql -u root -p

-- 查看当前有哪些数据库
SHOW DATABASES;

-- 创建自己的练习数据库
CREATE DATABASE study CHARACTER SET utf8mb4;

-- 使用这个数据库（后面的操作都针对它）
USE study;
```

### 建表：数据的家

增删改查之前得先有一张表。建一张学生表：

```sql
CREATE TABLE student (
    id      INT PRIMARY KEY AUTO_INCREMENT,  -- 学号，自增
    name    VARCHAR(20) NOT NULL,            -- 姓名，不能为空
    sex     CHAR(1),                         -- 性别
    age     INT,                             -- 年龄
    score   DECIMAL(5, 2),                   -- 成绩，保留两位小数
    birth   DATE                             -- 出生日期
);

-- 查看表结构
DESC student;

-- 查看当前库里的所有表
SHOW TABLES;
```

### 增：INSERT

```sql
-- 插入一条完整数据（要按建表时的列顺序写全）
INSERT INTO student VALUES (1, '张三', '男', 19, 85.5, '2007-03-12');

-- 更推荐：写明列名，顺序随意，也不怕表结构变化
INSERT INTO student (name, sex, age, score) VALUES ('李四', '女', 20, 92.0);

-- 一次插入多条（批量插入效率更高）
INSERT INTO student (name, sex, age, score) VALUES
('王五', '男', 21, 78.0),
('赵六', '女', 19, 66.5),
('孙七', '男', 22, 88.5);
```

### 查：SELECT

查询是用得最多的，也是花样最多的。

```sql
-- 查询所有行所有列
SELECT * FROM student;

-- 只查指定的列
SELECT name, score FROM student;

-- 条件查询 WHERE
SELECT * FROM student WHERE age > 19;
SELECT * FROM student WHERE sex = '女' AND score >= 80;

-- 范围和集合
SELECT * FROM student WHERE age BETWEEN 19 AND 21;
SELECT * FROM student WHERE name IN ('张三', '王五');

-- 模糊查询：_ 匹配一个字符，% 匹配任意多个字符
SELECT * FROM student WHERE name LIKE '张%';

-- 判断空值不能用 = ，要用 IS NULL
SELECT * FROM student WHERE birth IS NULL;

-- 排序：先按成绩降序，成绩相同再按年龄升序
SELECT * FROM student ORDER BY score DESC, age ASC;

-- 分页：取成绩排名的前 3 条
SELECT * FROM student ORDER BY score DESC LIMIT 3;

-- 去重
SELECT DISTINCT sex FROM student;
```

聚合函数配合分组统计：

```sql
-- 常用聚合函数：COUNT、SUM、AVG、MAX、MIN
SELECT COUNT(*) AS 人数,
       AVG(score) AS 平均分,
       MAX(score) AS 最高分,
       MIN(score) AS 最低分
FROM student;

-- 按性别分组统计
SELECT sex, COUNT(*) AS 人数, AVG(score) AS 平均分
FROM student
GROUP BY sex;

-- 分组后筛选用 HAVING（WHERE 是对原始行筛选，HAVING 是对分组结果筛选）
SELECT sex, AVG(score) AS 平均分
FROM student
GROUP BY sex
HAVING AVG(score) > 75;
```

写查询的完整顺序是 `SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ... LIMIT ...`，执行顺序则是 FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT。

### 改：UPDATE

```sql
-- 把 id 为 2 的学生成绩改成 95
UPDATE student SET score = 95 WHERE id = 2;

-- 同时改多个字段
UPDATE student SET age = 20, birth = '2006-08-01' WHERE id = 3;

-- 给所有人的成绩加 5 分（不加 WHERE 就是全表更新！）
UPDATE student SET score = score + 5;
```

### 删：DELETE 与 TRUNCATE

```sql
-- 删除指定行
DELETE FROM student WHERE id = 4;

-- 删除全部数据（一行一行删，可回滚，自增 id 不会重置）
DELETE FROM student;

-- 清空表（直接重建表，速度快，自增 id 从 1 重新开始）
TRUNCATE TABLE student;

-- 连表结构一起删掉
DROP TABLE student;
```

| 操作 | 速度 | WHERE 条件 | 自增值 | 属于 |
|------|------|-----------|--------|------|
| DELETE | 慢，逐行删除 | 支持 | 保留 | DML |
| TRUNCATE | 快，重建表 | 不支持 | 重置为 1 | DDL |
| DROP | 最快 | - | - | DDL |

### 踩过的坑

1. **UPDATE 和 DELETE 不加 WHERE** —— 第二次学习最深的体会：改、删操作执行前先写好 WHERE，或者先用 SELECT 把要影响的行查出来确认一遍再执行。
2. **字符串和日期要加引号**，数字不用：`sex = '男'` 而不是 `sex = 男`。
3. **判断 NULL 只能用 `IS NULL` / `IS NOT NULL`**，写 `= NULL` 不会报错但永远查不到东西，很隐蔽。
4. **列名和表名最好用反引号包起来**（如 `` `name` ``），避免和关键字冲突。
5. 中文乱码一般是字符集问题，建库时指定 `CHARACTER SET utf8mb4` 就基本不会遇到。

### 小结

增删改查对应四类语句：`INSERT`、`SELECT`、`UPDATE`、`DELETE`，再加上 `CREATE TABLE` 建表，就构成了日常操作数据库的基础。这次整理完感觉清晰多了，特别是查询里各种子句的书写顺序和执行顺序。下一步打算学多表查询（JOIN）和子查询，到时候再写一篇。
