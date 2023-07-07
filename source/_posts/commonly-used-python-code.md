---
title: 常用python代码整理
tags: python
categories: 笔记
abbrlink: 52441
date: 2023-07-07 14:29:54
---

以下代码都是在工作中经常遇到，然而死记硬背下来是不可能的，每次都现查也太麻烦了；因此在这里进行整理，以便以后无数次的使用（长期更新）

## 获取当前可读时间
```python
import time
time.strftime("%Y-%m-%d_%H-%M-%S",time.localtime())
```

## 在输出的excel中插入超链接
```python
=HYPERLINK("https://3g.k.sohu.com/t/n685055087","显示")
```

## 读写json文件
```python
# 写文件
with open(path, "w", encoding="utf-8") as f:
	json.dump(resdict, f, ensure_ascii=False, indent=4) # resdict需要是一个字典
# 读文件
with open("file.json", encoding="utf-8") as f:
	resdict = json.load(f)
```

## 使用pandas读写excel的多个sheet
```python
import pandas as pd
# 写入多个sheet
writer = pd.ExcelWriter('/path/to/XXX.xlsx')
df1.to_excel(writer, "sheet1")
df2.to_excel(writer, "sheet2")
df3.to_excel(writer, "sheet3")
writer.save()
writer.close()

# 读某个sheet
df = pd.read_excel("../path/to/XXX.xlsx", sheet_name="sheet1")

# 写sheet的两种格式
# 方法一
dic = {"key1": list1, "key2": list2}
DataFrame(dic).to_excel("a.xlsx")
# 方法二
lst = [[1,2,3],["a", "b", "c"]]
DataFrame(lst, columns=["数字", "字母"]).to_excel("a.xlsx")

```

## 获取文件相对路径
```python
import os
getModulePath = lambda p: os.path.join(os.path.dirname(__file__), p)
```

## python操作MySQL数据库
参考资料：[点我](https://blog.csdn.net/aijaijgnaw/article/details/124729427)
### 一个不用写SQL语句，只需要填参数的工具类
```python
import pymysql

class Database():
    # **config是指连接数据库时需要的参数,这样只要参数传入正确，连哪个数据库都可以
    # 初始化时就连接数据库
    def __init__(self, **config):
        try:
            # 连接数据库的参数我不希望别人可以动，所以设置私有
            self.__conn = pymysql.connect(**config)
            self.__cursor = self.__conn.cursor()
        except Exception as e:
            print("数据库连接失败：\n", e)

    # 查询一条数据
    # 参数：表名table_name,条件factor_str,要查询的字段field 默认是查询所有字段*
    def select_one(self, table_name, factor_str='', field="*"):
        if factor_str == '':
            sql = f"select {field} from {table_name}"
        else:
            sql = f"select {field} from {table_name} where {factor_str}"
        self.__cursor.execute(sql)
        return self.__cursor.fetchone()

    # 查询多条数据
    # 参数：要查询数据的条数num,表名table_name,条件factor_str,要查询的字段field 默认是查询所有字段*
    def select_many(self, num, table_name, factor_str='', field="*"):
        if factor_str == '':
            sql = f"select {field} from {table_name}"
        else:
            sql = f"select {field} from {table_name} where {factor_str}"
        self.__cursor.execute(sql)
        return self.__cursor.fetchmany(num)

    # 查询全部数据
    # 参数：表名table_name,条件factor_str,要查询的字段field 默认是查询所有字段*
    def select_all(self, table_name, factor_str='', field="*"):
        if factor_str == '':
            sql = f"select {field} from {table_name}"
        else:
            sql = f"select {field} from {table_name} where {factor_str}"
        self.__cursor.execute(sql)
        return self.__cursor.fetchall()

    # 新增数据
    def insert(self,table_name, value):
        sql = f"insert into {table_name} values {value}"
        try:
            self.__cursor.execute(sql)
            self.__conn.commit()
            print("插入成功")
        except Exception as e:
            print("插入失败\n", e)
            self.__conn.rollback()

    # 修改数据
    # 参数：表名，set值(可能是一个，也可能是多个，所以用字典)，条件
    def update(self, table_name, val_obl,change_str):
        sql = f"update {table_name} set"
        # set后面应该是要修改的字段，但是可能会修改多个字段的值，所以遍历一下
        # key对应字段的名，val对应字段的值
        for key, val in val_obl.items():
            sql += f" {key} = {val},"
        # 遍历完的最后面会有一个逗号，所以给它切掉，然后再拼接条件
        # !!!空格很重要
        sql = sql[:-1]+" where "+change_str
        try:
            self.__cursor.execute(sql)
            self.__conn.commit()
            print("修改成功")
        except Exception as e:
            print("修改失败\n", e)
            self.__conn.rollback()

    # 删除数据
    def delete(self,table_name, item):
        sql = f"delete from {table_name} where {item}"
        try:
            self.__cursor.execute(sql)
            self.__conn.commit()
            print("删除成功")
        except Exception as e:
            print("删除失败\n", e)
            self.__conn.rollback()

```

### 调用
```python
# 导包
from mysql_normal_util import Database

# 设置连接数据库的参数
config = {
    "host": "127.0.0.1",
    "port": 3307,
    "database": "lebo",
    "charset": "utf8",
    "user": "root",
    "passwd": "root"
}

# 实例化时就直接传参数
db = Database(**config)

# 查询1条
select_one = db.select_one("user")
print(select_one)

# 查询多条
select_many = db.select_many(3, "user")
print(select_many)

# 查询所有数据(根据条件)
select_all = db.select_all("user", "id>10")
print(select_all)

# 新增一条数据
db.insert("user","(20,'111')")
# 新增多条数据
db.insert("user", "(21,'123'),(22,'456')")

# 修改一个字段的数据
db.update("user", {"name": "222"}, "id=20")
# 修改多个字段的数据
db.update("user", {"id": "23", "name": "12345"}, "id=103")

# 删除数据
db.delete("user", "id=23")

```
### 通过shell命令导出mysql数据
```shell
/usr/bin/mysql -hclientrecmtagro.db.sxhano.com -urecom_tag_ro -p4BhBsLfEMq3m -P3306 --default-character-set=utf8 -Drecom_tag -A -Ne "select * from t_term_info" > "model/sqlModel.txt”
# -h后跟数据库域名
# -u后跟用户
# -p后跟密码
# -D后跟数据库名称
```