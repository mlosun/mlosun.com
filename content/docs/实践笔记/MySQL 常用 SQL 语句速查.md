---
created: 2025-05-09
title: MySQL 常用 SQL 语句速查
tags: [MySQL, 数据库]
description: MySQL 常用 SQL 语句速查，涵盖数据库、数据表、数据行的增删改查及常用数据类型。
---

## 数据库操作

```sql
/* 创建数据库 */
CREATE DATABASE 数据库名称;

/* 删除数据库 */
DROP DATABASE 数据库名称;

/* 查看所有数据库 */
SHOW DATABASES;

/* 查看当前数据库的字符集等信息 */
SHOW CREATE DATABASE 数据库名称;

/* 修改数据库的字符集等属性 */
ALTER DATABASE 数据库名称 CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

## 数据表操作

```sql
/* 创建数据表 */
CREATE TABLE 数据表名称 (字段1 数据类型1, 字段2 数据类型2, ...);

/* 删除数据表 */
DROP TABLE 数据表名称;

/* 查看当前数据库中的所有数据表 */
SHOW TABLES;

/* 查看数据表的结构 */
DESCRIBE 数据表名称;

/* 查看数据表的创建语句 */
SHOW CREATE TABLE 数据表名称;

/* 添加新列 */
ALTER TABLE 数据表名称 ADD COLUMN 新字段 数据类型;

/* 修改列的数据类型 */
ALTER TABLE 数据表名称 MODIFY COLUMN 字段1 新数据类型;

/* 重命名列 */
ALTER TABLE 数据表名称 CHANGE COLUMN 字段1 新字段名 数据类型;

/* 删除列 */
ALTER TABLE 数据表名称 DROP COLUMN 字段1;
```

## 数据行操作

```sql
/* 插入数据行 */
INSERT INTO 数据表名称 (字段1, 字段2, ...) VALUES (值1, 值2, ...);

/* 查询数据行 */
SELECT 字段1, 字段2, ... FROM 数据表名称 WHERE 字段1 = '条件值1';

/* 更新数据行 */
UPDATE 数据表名称 SET 字段1 = '新值1', 字段2 = '新值2', ... WHERE 字段1 = '条件值1';

/* 删除数据行 */
DELETE FROM 数据表名称 WHERE 字段1 = '条件值1';
```

## 条件与排序

```sql
/* 条件查询 */
SELECT * FROM 数据表 WHERE 字段1 = '值';
SELECT * FROM 数据表 WHERE 字段1 LIKE '%关键词%';
SELECT * FROM 数据表 WHERE 字段1 > 100 AND 字段2 = '值';

/* 排序 */
SELECT * FROM 数据表 ORDER BY 字段1 DESC;        /* 降序 */
SELECT * FROM 数据表 ORDER BY 字段1 ASC;         /* 升序（默认） */

/* 分页 */
SELECT * FROM 数据表 LIMIT 10;                    /* 取前 10 条 */
SELECT * FROM 数据表 LIMIT 10 OFFSET 20;          /* 跳过 20 条，取 10 条 */
SELECT * FROM 数据表 LIMIT 20, 10;                /* 同上，简写形式 */
```

## 聚合与分组

```sql
/* 聚合函数 */
SELECT COUNT(*) FROM 数据表;                      /* 统计行数 */
SELECT SUM(金额字段) FROM 数据表;                  /* 求和 */
SELECT AVG(价格字段) FROM 数据表;                  /* 平均值 */
SELECT MAX(年龄字段) FROM 数据表;                  /* 最大值 */
SELECT MIN(年龄字段) FROM 数据表;                  /* 最小值 */

/* 分组统计 */
SELECT 分类字段, COUNT(*) FROM 数据表 GROUP BY 分类字段;
SELECT 分类字段, SUM(金额) FROM 数据表 GROUP BY 分类字段 HAVING SUM(金额) > 1000;
```

## 索引

```sql
/* 创建索引 */
CREATE INDEX 索引名称 ON 数据表 (字段1);
CREATE INDEX 索引名称 ON 数据表 (字段1, 字段2);   /* 联合索引 */

/* 删除索引 */
DROP INDEX 索引名称 ON 数据表;

/* 查看索引 */
SHOW INDEX FROM 数据表;
```

## 用户管理

```sql
/* 创建用户 */
CREATE USER '用户名'@'localhost' IDENTIFIED BY '密码';
CREATE USER '用户名'@'%' IDENTIFIED BY '密码';     /* 允许远程登录 */

/* 授权 */
GRANT ALL PRIVILEGES ON 数据库名.* TO '用户名'@'localhost';
GRANT SELECT, INSERT, UPDATE ON 数据库名.* TO '用户名'@'%';

/* 刷新权限 */
FLUSH PRIVILEGES;

/* 查看用户 */
SELECT User, Host FROM mysql.user;

/* 修改密码 */
ALTER USER '用户名'@'localhost' IDENTIFIED BY '新密码';

/* 删除用户 */
DROP USER '用户名'@'localhost';
```

## 常用数据类型

### 数值类型

| 数据类型 | 说明 | 取值范围 | 适用场景 |
| --- | --- | --- | --- |
| `INT` | 普通整数 | −21亿 ～ 21亿 | 用户ID、文章数量、年份 |
| `BIGINT` | 大整数 | −922亿亿 ～ 922亿亿 | 订单号、日志ID、超大数据量 |
| `FLOAT` | 单精度浮点数 | 约 7 位有效数字 | 科学计算、百分比 |
| `DOUBLE` | 双精度浮点数 | 约 15 位有效数字 | 科学计算、高精度要求 |
| `DECIMAL` | 定点数（精确） | 取决于定义 | **金额、价格**（必须用这个，避免精度丢失） |

> `FLOAT(M,D)` / `DOUBLE(M,D)` / `DECIMAL(M,D)` 中的 `(M,D)` 表示总长度 M 位，其中 D 位是小数。例如 `DECIMAL(10,2)` 总长 10 位，小数 2 位，整数 8 位。

### 字符串类型

| 数据类型 | 说明 | 最大长度 | 适用场景 |
|----------|------|---------|--------|
| `VARCHAR` | 可变长度字符串 | 65535 字符 | **用户名、邮箱、标题**（短文本首选） |
| `TEXT` | 长文本 | 65535 字符 | 文章正文、评论内容、日志详情 |

### 日期和时间类型

| 数据类型 | 说明 | 取值范围 | 适用场景 |
| --- | --- | --- | --- |
| `DATETIME` | 日期和时间 | 1000 ～ 9999 年 | **创建时间、更新时间**（通用场景） |
| `TIMESTAMP` | 时间戳 | 1970 年起 | 记录日志时间，受时区影响（自动转换为当前时区） |

> `DATETIME` 和 `TIMESTAMP` 的区别：`TIMESTAMP` 会自动转换为当前时区；`DATETIME` 不受时区影响。日常场景首选 `DATETIME`。

### JSON 类型

| 数据类型 | 说明 | 适用场景 |
|----------|------|--------|
| `JSON` | 存储 JSON 格式数据 | 配置信息、动态属性、非结构化数据存储 |
