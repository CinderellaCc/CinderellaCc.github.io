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

def setup_rotate_logger(file_path, level=logging.INFO):
    log_fmt = '%(asctime)s \"%(filename)s\" %(process)d %(lineno)s %(levelname)s %(funcName)s: %(message)s '
    formatter = logging.Formatter(log_fmt)
    log = getLogger()
    logfile = os.path.abspath(file_path)
    rotateHandler = ConcurrentRotatingFileHandler(logfile, "a", 512 * 1024 * 1024, 999)
    rotateHandler.setFormatter(formatter)
    log.addHandler(rotateHandler)
    log.setLevel(level)
    log.info('Fancy concurrent logger started!')
    return log

LOG_FILE_PATH = 'path_to_save_log.log'
log = setup_rotate_logger(LOG_FILE_PATH)
```
