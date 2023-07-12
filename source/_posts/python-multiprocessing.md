---
title: python多进程模板
tags: python
categories: 笔记
abbrlink: 32258
date: 2023-07-12 17:26:28
---

在工作中，我们经常会用到多进程来处理数据，从而提高工作效率。下文是一个多进程模板，可以按照具体需求进行修改，欢迎使用~

```python
from multiprocessing import Pool


if __name__ == '__main__':
from multiprocessing import Pool
import os
import time

def func(data):
    print(f'进程{os.getpid()}开始处理数据{data}')
    # 这里写你的数据处理代码
    time.sleep(1) 
    print(f'进程{os.getpid()}结束处理数据{data}')


def split_data(data_list, cpu_worker_num):
    ''' 将数据列表分成cpu_worker_num份，例如cpu_worker_num=3，data_list=[1,2,3,4,5,6,7,8,9,10]，返回[[1, 2, 3, 4], [5, 6, 7], [8, 9, 10]]

    '''
    chunk_size = int(len(data_list)/cpu_worker_num) + 1 
    return [data_list[x:x + chunk_size] for x in range(0, len(data_list), chunk_size)]


if __name__ == '__main__':
    # 进程数
    cpu_worker_num = 8
    # 待处理的数据列表
    # 例如，要处理多个文件，该列表可以是文件路径列表，即让每个进程分别处理一部分文件；也可以是任何你要传给单个进程的参数列表
    data_list = [1, 2, 3, 4, 5, 6, 7, 8]
    # 创建进程池
    pool = Pool(cpu_worker_num)
    # 将数据列表分成cpu_worker_num份，分别传给cpu_worker_num个进程处理
    # 每个进程都会调用func函数，参数是data_list的其中一份
    pool_data_list = split_data(data_list, cpu_worker_num)
    # 进程池中的每个进程都会调用func函数，参数是pool_data_list的其中一份
    pool.map(func, pool_data_list)
    # 关闭进程池
    pool.close()
    # 等待进程池中的所有进程结束
    pool.join()

```