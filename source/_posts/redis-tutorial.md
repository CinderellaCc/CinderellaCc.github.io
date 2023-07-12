---
title: redis教程（python）
tags: redis
categories: 教程
abbrlink: 59688
date: 2023-07-11 10:52:25
---
Redis 是一款开源的、高性能的键值对存储数据库。数据保存在内存里，所以读写速度非常快；支持的键值对数据类型包括：字符串、哈希、列表、集合、有序集合。

下文将列举部分在使用redis过程中的常用方法

# 读取整个redis数据库
```python
import redis

# 示例代码
def read_db_redis():
    rds = redis.Redis(host='XXXX', port=123456, db=1, password='XXXX')
    pipe = rds.pipeline()
    tag_db = {}
    cursor = 0
    while True:
        cursor, keys = rds.scan(cursor, count=10000)
        for tag in keys:
            pipe.get(tag)
        result = pipe.execute()
        for tag, tag_id in zip(*(keys, result)):
            tag, tag_id = tag.decode('utf-8'), tag_id.decode('utf-8')
            tag_db[tag] = tag_id
        if cursor == 0:
            break
    return tag_db

```

# 清空整个redis库
```python
import redis

rds = redis.Redis(host='XXXX', port=123456, db=1, password='XXXX')
rds.flushdb() # 比一个一个delete快多了
```

# redis对5种数据类型的读写操作
```python
import redis # 导入redis

r = redis.Redis(host='XXXX', port=123456, db=1, password='XXXX') # 连接redis
```

## 字符串（String）
```python
# 写入数据
r.set('name', 'Orange')
# 获取数据
print(r.get('name'))

# 输出：b'Orange'
```

## 哈希（Hash）
```python
# 写入数据
r.hset('user:1', 'name', 'Tom')
r.hset('user:1', 'age', '18')
# 获取数据
print(r.hgetall('user1'))

# 输出：{b'name': b'Tom', b'age': b'18'}
```

## 列表（List）
```python
# 写入数据
r.lpush('mylist', 'a')
r.lpush('mylist', 'b')
r.lpush('mylist', 'c')
# 获取数据
print(r.lrange('mylist', 0, -1))

# 输出：[b'c', b'b', a'a']
```

## 集合（Set）
```python
# 写入数据
r.sadd('myset', 'a')
r.sadd('myset', 'b')
r.sadd('myset', 'c')
# 获取数据
print(r.smembers('myset'))

# 输出：{b'a', b'b', b'c'}
```
可以借助集合进行去重，求交集、并集、差集等操作
```python
r.sadd('set1', 1, 2, 3, 4)
r.sadd('set2', 3, 4, 5, 6)

# 求交集
print(r.sinter('set1', 'set2'))

# 求并集
print(r.sunion('set1', 'set2'))

# 求差集
print(r.sdiff('set1', 'set2'))

# 输出：
# {b'3', b'4'}
# {b'1', b'2', b'3', b'4', b'5', b'6'}
# {b'1', b'2'}
```

## 有序集合（Sorted Set）
```python
# 设置有序集合
r.zadd('myzset', {'a': 1, 'b': 2, 'c': 3})

# 获取有序集合
print(r.zrange('myzset', 0, -1, withscores=True))

# 输出：[(b'a', 1.0), (b'b', 2.0), (b'c', 3.0)]
```
有序集合可以用来实现排行榜等功能

```python
r.zadd('rank', {'Tom': 100, 'Jerry': 200, 'Alice': 300})

# 获取排行榜前三名
print(r.zrevrange('rank', 0, 2, withscores=True))

# 输出：[(b'Alice', 300.0), (b'Jerry', 200.0), (b'Tom', 100.0)]
```