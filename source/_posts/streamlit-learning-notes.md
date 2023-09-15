---
title: streamlit入门学习笔记
tags: streamlit
abbrlink: 23148
date: 2023-09-15 16:21:16
---

# 安装
``` shell
pip install install streamlit
```

# 启动
```shell
# 方法一
python -m streamlit run your_script.py

# 方法二
streamlit run your_script.py
```

# 使用方法
## `st.write()`的简单使用
使用`st.write()`在网页上画出任何你想展示的内容，包括：text、data、matplotlib plot、charts等
```
# 代码示例
import streamlit as st
import pandas as pd

st.write("Here's our first attempt at using data to create a table:")
st.write(pd.DataFrame({
    'first column': [1, 2, 3, 4],
    'second column': [10, 20, 30, 40]
}))
```
## 使用`st.table()`和`st.dataframe()`画表格
除了用`st.write()`，也可用`st.table()`、`st.dataframe()`等特定函数来制作更加精美的图表。建议用特定函数来实现更复杂的功能，`st.write()`存在一定局限性，示例代码如下：

```python
import streamlit as st
import numpy as np
import pandas as pd

dataframe = pd.DataFrame(
    np.random.randn(10, 20),
    columns=('col %d' % i for i in range(20)))

st.dataframe(dataframe.style.highlight_max(axis=0))
```
```python
import streamlit as st
import numpy as np
import pandas as pd

dataframe = pd.DataFrame(
    np.random.randn(10, 20),
    columns=('col %d' % i for i in range(20)))
st.table(dataframe)
```
## 使用`st.line_chart()`画折线图
```python
import streamlit as st
import numpy as np
import pandas as pd

chart_data = pd.DataFrame(
     np.random.randn(20, 3),
     columns=['a', 'b', 'c'])

st.line_chart(chart_data)
```

## 使用`st.map()`画地图
```python
import streamlit as st
import numpy as np
import pandas as pd

map_data = pd.DataFrame(
    np.random.randn(1000, 2) / [50, 50] + [37.76, -122.4],
    columns=['lat', 'lon'])

st.map(map_data)
```

## 使用`st.slider()`、`st.button()`和`st.selectbox()`画小组件
```python
import streamlit as st
import pandas as pd

df = pd.DataFrame({
    'first column': [1, 2, 3, 4],
    'second column': [10, 20, 30, 40]
    })

option = st.selectbox(
    'Which number do you like best?',
     df['first column'])

'You selected: ', option
```

## 布局
`streamlit`可将所有的菜单元素（如滑动条、选择框、输入框等）统一放置到网页一侧，使网页更加美观。运行如下代码看看效果吧~
```python
import streamlit as st

# Add a selectbox to the sidebar:
add_selectbox = st.sidebar.selectbox(
    'How would you like to be contacted?',
    ('Email', 'Home phone', 'Mobile phone')
)

# Add a slider to the sidebar:
add_slider = st.sidebar.slider(
    'Select a range of values',
    0.0, 100.0, (25.0, 75.0)
)
```

## 进度条
当你的程序运行需要一定时间时，你可将进度条打印出来，代码如下：
```python
import streamlit as st
import time

'Starting a long computation...'

# Add a placeholder
latest_iteration = st.empty()
bar = st.progress(0)

for i in range(100):
  # Update the progress bar with each iteration.
  latest_iteration.text(f'Iteration {i+1}')
  bar.progress(i + 1)
  time.sleep(0.1)

'...and now we\'re done!'
```

# Cache

以下是官网解释：

Streamlit 缓存使您的应用程序即使在从 Web 加载数据、操作大型数据集或执行昂贵的计算时也能保持高性能。

缓存背后的基本思想是存储昂贵的函数调用的结果，并在相同的输入再次发生时返回缓存的结果，而不是在后续运行中调用该函数。

> 我的理解：streamlit app与flask不同，falsk创建的是一个服务，是一个长期运行在后台的程序，程序中一些开销大的计算在服务启动时就计算完毕，后续在不同地方访问服务都无需重新计算，直接获取计算结果，速度非常快（此处不包含一些需要前后端交互导致的计算过程）；而Streamlit app是一个python脚本，每次在浏览器访问页面时都会重新运行该脚本，为此，引入了`cache`功能避免重复计算。

```python
@st.cache_data
def long_running_function(param1, param2):
    return …
```

# 多个页面
Streamlit可创建多个页面，在不同页面间的切换也十分方便，具体操作参考[官方教程](https://docs.streamlit.io/)

# Streamlit的特性
Streamlit是从上到下运行的Python脚本
Streamlit apps are Python scripts that run from top to bottom

每当用户打开浏览器选项卡指向你的应用时，脚本就会被重新执行
Every time a user opens a browser tab pointing to your app, the script is re-executed

当脚本执行时，Streamlit在浏览器中实时绘制其输出
As the script executes, Streamlit draws its output live in a browser

脚本使用Streamlit缓存来避免重新计算昂贵的函数，因此更新发生得非常快
Scripts use the Streamlit cache to avoid recomputing expensive functions, so updates happen very fast

每次用户与小部件交互时，脚本都会重新执行，并且在运行期间将该小部件的输出值设置为新值。
Every time a user interacts with a widget, your script is re-executed and the output value of that widget is set to the new value during that run.

Streamlit可以包含多个页面，这些页面被定义在pages文件夹中单独的.py文件中。
Streamlit apps can contain multiple pages, which are defined in separate .py files in a pages folder.


# 注：
本教程是作者在自学过程中简单记录的笔记，仅做参考，详细教程请移步[官方教程](https://docs.streamlit.io/)

# 参考资料：
Streamlit官方教程：https://docs.streamlit.io/  
Streamlit官方教程API文档：https://docs.streamlit.io/library/api-reference