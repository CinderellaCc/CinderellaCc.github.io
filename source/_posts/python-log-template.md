---
title: python日志模板（即取即用）
tags: python
categories: 笔记
abbrlink: 60595
date: 2023-07-10 16:38:15
---

本文提供了一个简单的python日志模板，及其简单，欢迎使用！

```python
import logging
from logging.handlers import TimedRotatingFileHandler

LOG_PATH = 'logs/XXX.log'  # 日志保存位置

logger = logging.getLogger()
logger.setLevel(logging.INFO)
handler = TimedRotatingFileHandler(filename=LOG_PATH, when="midnight", interval=1, backupCount=7) # backupCount=7 保存过去7天的日志
formatter = logging.Formatter('%(asctime)s %(lineno)s %(levelname)s: %(message)s ')
handler.setFormatter(formatter)
logger.addHandler(handler)

log.info('这个日志太好用啦')
```
