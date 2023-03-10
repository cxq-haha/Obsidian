# PG配置

pg_hba.conf文件控制允许哪些主机访问服务器

配置PG可远程访问：

```shell
# pg_hba.conf文件中添加 "host all all 10.47.160.137/32 trust"
# 表示允许10.47.160.137/32使用任何用户名连接任数据库而不需要输入密码
```

# 交互式终端psql

## 连接数据库

```shell
psql [option...] [dbname[username]] 

# 选项
-h IP地址
-p 端口
-U 用户名
-d 数据库名
```

## 常用命令

`\q` 命令：退出psql命令行

`\l` 命令：列举数据库

`\c` 命令：选取数据库

`\d 表名` 命令：查看库中某个表结构

`\d`  `\dt` 命令：查看当前数据库中所有表

```shell
\d 查看当前数据库的所有表
\d db_name 查看数据库db_name的所有表
```

`\db` 命令：查看表空间信息

`\dn` 命令：查看所有模式信息

`\du \dg` 命令：查看数据库中所有角色或用户

`\x` 命令：设置查询结果的输出模式

`\?` 命令：查看PG支持的所命令

## 数据类型

### 数值类型

整数类型：
![[Pasted image 20230205210817.png]]

任意精度类型：
![[Pasted image 20230205210836.png]]

浮点类型：
![[Pasted image 20230205210909.png]]

real和double precision都是不准确的、可变精度的数字类型。这意味着：一些值不能准确地转换成内部格式，而是以近似的形式存储的。因此，存储和检索一个值可能出现一些缺失。如果要进行准确的存储和计算（计算货币金额），则应该使用**numeric**类型

序列类型：
![[Pasted image 20230205211046.png]]

货币类型：
![[Pasted image 20230205211104.png]]

money类型可以接受很多输入格式，包括整数和浮点数，以及常用的货币格式，如“$1，000.00”。输出通常是货币格式，不同区域的货币符号有所不同。如果要输出为人民币格式，则需要将lc_monetary参数设置为“zh_CN.utf8”，

```shell
# psql命令行下输入
set lc_monetary="zh_CN.utf8";
```

### 字符串类型

![[Pasted image 20230205211546.png]]

## 二进制类型

提供`bytea`数据类型用于存储二进制字符串

二进制数据类型支持两种用于输入和输出的外部格式：

1. PostgreSQL的转义格式
转义格式是二进制数据类型的传统PostgreSQL格式。它采用将二进制数据表示成ASCII字符序列的方法，而将那些无法用ASCII字符表示的字节转换成特殊的转义语句。
![[Pasted image 20230205212021.png]]

2. 十六进制格式
十六进制格式将二进制数据编码为字符串，每个字节表示两个十六进制数，最高有效位在前。十六进制字符串以“\x”开头（用以和转义格式区分）。

```
select E'\\xDEADBEEF'
```

### 日期和时间类型

![[Pasted image 20230205212221.png]]



