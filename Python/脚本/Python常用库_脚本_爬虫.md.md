# Python 入门实用教程：常用库、脚本编写与爬虫基础

> 面向大一学生的 Python 进阶入门指南，从零开始掌握实用技能

---

## 目录

1. [Python 常用库概览](#1-python-常用库概览)
2. [数据处理神器：Pandas](#2-数据处理神器pandas)
3. [数值计算基础：NumPy](#3-数值计算基础numpy)
4. [如何编写 Python 脚本](#4-如何编写-python-脚本)
5. [网络爬虫入门](#5-网络爬虫入门)
6. [学习路线建议](#6-学习路线建议)

---

## 1. Python 常用库概览

### 1.1 什么是库（Library）？

**库**就是别人写好的、可以直接调用的代码集合。Python 的强大之处在于拥有极其丰富的第三方库，让你不用"重复造轮子"。

### 1.2 安装库的方法

使用 `pip` 命令安装（在终端/命令行中执行）：

```bash
pip install 库名
# 例如安装 pandas
pip install pandas
# 安装多个库
pip install pandas numpy requests
```

> 💡 **提示**：国内用户建议使用清华镜像源加速下载：
> ```bash
> pip install pandas -i https://pypi.tuna.tsinghua.edu.cn/simple
> ```

### 1.3 大一学生必知的常用库

| 分类 | 库名 | 用途 | 难度 |
|------|------|------|------|
| 数据处理 | **pandas** | 表格数据处理、Excel读写 | ⭐⭐ |
| 数值计算 | **numpy** | 数组运算、数学计算 | ⭐⭐ |
| 网络请求 | **requests** | 发送HTTP请求、爬网页 | ⭐ |
| 网页解析 | **beautifulsoup4** | 解析HTML提取数据 | ⭐ |
| 数据可视化 | **matplotlib** | 画折线图、柱状图等 | ⭐⭐ |
| 自动化 | **selenium** | 模拟浏览器操作 | ⭐⭐⭐ |
| 图片处理 | **pillow** | 图片裁剪、滤镜、格式转换 | ⭐⭐ |

---

## 2. 数据处理神器：Pandas

### 2.1 Pandas 是什么？

Pandas 是 Python 中最流行的**数据分析和处理库**，可以把它理解为"Python 版的 Excel"，但功能更强大、处理速度更快。

### 2.2 核心数据结构

#### DataFrame（数据表）
类似 Excel 的一张工作表，有行有列：

```python
import pandas as pd

# 创建一个简单的 DataFrame
data = {
    '姓名': ['张三', '李四', '王五', '赵六'],
    '年龄': [18, 19, 18, 20],
    '成绩': [85, 92, 78, 95]
}

df = pd.DataFrame(data)
print(df)
```

输出：
```
   姓名  年龄  成绩
0  张三   18   85
1  李四   19   92
2  王五   18   78
3  赵六   20   95
```

#### Series（一列数据）
DataFrame 的某一列就是一个 Series：

```python
# 取一列
print(df['姓名'])
```

### 2.3 读取外部数据

#### 读取 Excel 文件

```python
# 读取 Excel
df = pd.read_excel('学生成绩.xlsx')

# 读取 CSV（更常用，体积更小）
df = pd.read_csv('学生成绩.csv')
```

#### 保存数据

```python
# 保存为 Excel
df.to_excel('结果.xlsx', index=False)

# 保存为 CSV
df.to_csv('结果.csv', index=False, encoding='utf-8-sig')
```

> 💡 `index=False` 表示不保存行号，`utf-8-sig` 编码让 Excel 打开中文不乱码

### 2.4 常用数据操作

#### 查看数据基本信息

```python
df.head()       # 看前5行
df.tail()       # 看后5行
df.shape        # 查看行数和列数 (行, 列)
df.describe()   # 数值列的统计信息（均值、最大最小值等）
df.info()       # 查看每列的数据类型和缺失情况
```

#### 筛选数据

```python
# 筛选成绩大于80分的学生
高分学生 = df[df['成绩'] > 80]

# 多条件筛选（注意每个条件都要用括号）
结果 = df[(df['成绩'] > 80) & (df['年龄'] == 18)]
```

#### 排序

```python
# 按成绩降序排列
df_sorted = df.sort_values('成绩', ascending=False)
```

#### 分组统计

```python
# 按年龄分组，计算每组的平均成绩
统计结果 = df.groupby('年龄')['成绩'].mean()
```

#### 新增列

```python
# 新增一列：成绩等级
df['等级'] = df['成绩'].apply(lambda x: '优秀' if x >= 90 else '良好' if x >= 80 else '及格')
```

### 2.5 Pandas 实战小例子

**任务**：统计班级成绩情况

```python
import pandas as pd

# 1. 读取数据
df = pd.read_csv('班级成绩.csv')

# 2. 查看基本情况
print(f"班级共 {len(df)} 人")
print(f"平均分：{df['总分'].mean():.2f}")
print(f"最高分：{df['总分'].max()}")
print(f"最低分：{df['总分'].min()}")

# 3. 筛选前十名
top10 = df.sort_values('总分', ascending=False).head(10)
print("\n前十名：")
print(top10[['姓名', '总分']])

# 4. 保存结果
top10.to_excel('班级前十名.xlsx', index=False)
print("\n已保存到 班级前十名.xlsx")
```

---

## 3. 数值计算基础：NumPy

### 3.1 NumPy 是什么？

NumPy（Numerical Python）是 Python 科学计算的基础库，提供了**高效的多维数组对象**，Pandas 底层就是基于 NumPy 的。

### 3.2 创建数组

```python
import numpy as np

# 从列表创建
arr = np.array([1, 2, 3, 4, 5])
print(arr)  # [1 2 3 4 5]

# 创建全0数组
zeros = np.zeros((3, 4))  # 3行4列的全0矩阵

# 创建全1数组
ones = np.ones((2, 3))

# 创建连续序列
seq = np.arange(0, 10, 2)  # [0 2 4 6 8]

# 创建等差数列
lin = np.linspace(0, 1, 5)  # [0.   0.25 0.5  0.75 1.  ]
```

### 3.3 数组运算

NumPy 数组支持**向量化运算**，比 Python 列表快得多：

```python
a = np.array([1, 2, 3, 4])
b = np.array([10, 20, 30, 40])

# 对应元素运算
print(a + b)   # [11 22 33 44]
print(a * b)   # [10 40 90 160]
print(a ** 2)  # [ 1  4  9 16]

# 统计函数
print(a.sum())    # 求和 10
print(a.mean())   # 平均值 2.5
print(a.max())    # 最大值 4
print(a.min())    # 最小值 1
```

### 3.4 矩阵操作

```python
# 创建 2x3 矩阵
matrix = np.array([[1, 2, 3],
                   [4, 5, 6]])

print(matrix.shape)  # (2, 3) 形状
print(matrix.T)      # 转置，变成 3x2

# 矩阵乘法
A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])
print(np.dot(A, B))  # 矩阵点乘
```

> 🎯 **大一学习建议**：如果你的专业需要学习线性代数、概率论、数值计算，NumPy 会非常有用。平时数据处理用 Pandas 就够了。

---

## 4. 如何编写 Python 脚本

### 4.1 什么是脚本？

**脚本（Script）**就是保存为 `.py` 文件的 Python 代码，可以直接运行。区别于在交互环境（如 Jupyter）中一行行执行。

### 4.2 脚本的基本结构

一个规范的 Python 脚本通常包含以下部分：

```python
# ==========================================
# 脚本名称：student_analysis.py
# 功能说明：分析学生成绩并生成报告
# 作者：你的名字
# 创建日期：2026-07-14
# ==========================================

# ---------- 1. 导入库 ----------
import pandas as pd
import os

# ---------- 2. 配置参数 ----------
INPUT_FILE = '班级成绩.csv'
OUTPUT_FILE = '成绩分析报告.xlsx'
PASS_LINE = 60  # 及格线

# ---------- 3. 定义函数 ----------
def load_data(file_path):
    """读取成绩数据"""
    if not os.path.exists(file_path):
        print(f"错误：找不到文件 {file_path}")
        return None
    return pd.read_csv(file_path)

def analyze_scores(df):
    """分析成绩数据"""
    result = {
        '总人数': len(df),
        '平均分': df['总分'].mean(),
        '及格率': (df['总分'] >= PASS_LINE).mean() * 100,
        '最高分': df['总分'].max(),
        '最低分': df['总分'].min()
    }
    return result

# ---------- 4. 主程序入口 ----------
if __name__ == '__main__':
    print("=== 成绩分析脚本开始运行 ===")
    
    # 读取数据
    df = load_data(INPUT_FILE)
    if df is None:
        exit(1)  # 出错退出
    
    # 分析数据
    stats = analyze_scores(df)
    
    # 输出结果
    print("\n📊 分析结果：")
    for key, value in stats.items():
        print(f"  {key}: {value:.2f}" if isinstance(value, float) else f"  {key}: {value}")
    
    # 保存结果
    df.to_excel(OUTPUT_FILE, index=False)
    print(f"\n✅ 报告已保存到 {OUTPUT_FILE}")
    print("=== 脚本运行结束 ===")
```

### 4.3 为什么要有 `if __name__ == '__main__':`？

这是 Python 的**最佳实践**：
- 直接运行脚本时，`__name__` 等于 `'__main__'`，主程序会执行
- 被其他文件 `import` 导入时，`__name__` 是文件名，主程序不会执行
- 简单说：**让代码既能独立运行，又能被别人导入使用**

### 4.4 编写脚本的好习惯

#### ① 善用函数，避免重复代码

❌ 不好的写法：
```python
# 到处复制粘贴相似代码
data1 = pd.read_csv('file1.csv')
data1 = data1.dropna()
data1 = data1.sort_values('date')

data2 = pd.read_csv('file2.csv')
data2 = data2.dropna()
data2 = data2.sort_values('date')
```

✅ 好的写法：
```python
def process_csv(filename):
    df = pd.read_csv(filename)
    df = df.dropna()
    df = df.sort_values('date')
    return df

data1 = process_csv('file1.csv')
data2 = process_csv('file2.csv')
```

#### ② 加注释，但不要废话

```python
# 好的注释：解释"为什么这么做"
# 使用utf-8-sig编码，防止Excel打开中文乱码
df.to_csv('output.csv', encoding='utf-8-sig')

# 坏的注释：解释"这是什么"（代码本身已经说明白了）
# 把df保存为csv文件
df.to_csv('output.csv')
```

#### ③ 变量命名要有意义

| ❌ 不好的命名 | ✅ 好的命名 |
|-------------|------------|
| `a`, `b`, `c` | `student_list`, `avg_score` |
| `data1`, `data2` | `raw_data`, `cleaned_data` |
| `func`, `do` | `calculate_average`, `filter_data` |

#### ④ 处理异常情况

```python
# 不要假设文件一定存在
try:
    df = pd.read_csv('data.csv')
except FileNotFoundError:
    print("错误：数据文件不存在，请检查路径")
    exit(1)
```

### 4.5 运行脚本的方法

#### 方法一：命令行运行（最常用）
```bash
python 脚本名.py
# 例如
python student_analysis.py
```

#### 方法二：在 VS Code / PyCharm 中点击运行按钮
IDE 中直接点 ▶ 按钮即可。

### 4.6 命令行参数（进阶）

让脚本接收外部参数，更灵活：

```python
import sys

# sys.argv 是命令行参数列表
# sys.argv[0] 是脚本名，sys.argv[1] 是第一个参数
if len(sys.argv) > 1:
    filename = sys.argv[1]
else:
    filename = 'default.csv'

print(f"正在处理文件：{filename}")
```

运行方式：
```bash
python my_script.py 数据文件.csv
```

---

## 5. 网络爬虫入门

### 5.1 什么是爬虫？

**爬虫**就是用程序自动访问网页、提取数据的技术。简单说：让 Python 代替你手动复制粘贴网页上的信息。

### 5.2 爬虫的法律与道德边界 ⚠️

> **非常重要！大一同学务必了解：**
>
> 1. **遵守 robots.txt**：网站根目录下的 `robots.txt` 规定了哪些页面可以爬
> 2. **控制频率**：不要短时间发大量请求，会把网站搞崩
> 3. **不爬隐私数据**：个人信息、登录后的数据不要碰
> 4. **数据用途**：爬来的数据仅供学习研究，不要商用
> 5. **小网站手下留情**：学生作业、学习练习，尽量爬大站或专门的练习网站

### 5.3 爬虫三步骤

```
发送请求 → 获取网页 → 解析提取数据
```

### 5.4 必备库安装

```bash
pip install requests beautifulsoup4 lxml
```

### 5.5 第一步：发送请求（requests 库）

```python
import requests

# 发送 GET 请求，获取网页内容
url = 'https://quotes.toscrape.com/'  # 专门用来练习爬虫的网站
response = requests.get(url)

# 查看状态码，200 表示成功
print(response.status_code)  # 200

# 查看网页HTML内容
print(response.text)
```

**常见状态码**：
- `200`：成功
- `404`：页面不存在
- `403`：被拒绝访问（反爬了）
- `500`：服务器出错

### 5.6 第二步：解析网页（BeautifulSoup）

HTML 网页是由标签组成的，BeautifulSoup 帮你从一堆标签里找到想要的内容。

#### 基本用法

```python
from bs4 import BeautifulSoup
import requests

url = 'https://quotes.toscrape.com/'
response = requests.get(url)
soup = BeautifulSoup(response.text, 'lxml')  # 解析网页

# 找到所有名言
quotes = soup.find_all('div', class_='quote')

for quote in quotes:
    # 提取名言内容
    text = quote.find('span', class_='text').text
    # 提取作者
    author = quote.find('small', class_='author').text
    print(f"「{text}」 —— {author}")
    print('-' * 50)
```

#### 常用查找方法

| 方法 | 作用 |
|------|------|
| `soup.find('标签名')` | 找到第一个匹配的元素 |
| `soup.find_all('标签名')` | 找到所有匹配的元素，返回列表 |
| `soup.find('div', class_='xxx')` | 按 class 查找 |
| `soup.find(id='xxx')` | 按 id 查找 |
| `element.text` | 获取元素的文本内容 |
| `element['href']` | 获取元素的属性值 |

### 5.7 完整爬虫实例：爬取名言网站

```python
# ==========================================
# 爬虫脚本：quotes_spider.py
# 功能：爬取 quotes.toscrape.com 的名言
# ==========================================

import requests
from bs4 import BeautifulSoup
import csv
import time

def get_quotes_from_page(page_url):
    """从单页提取所有名言"""
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
    }
    
    response = requests.get(page_url, headers=headers)
    soup = BeautifulSoup(response.text, 'lxml')
    
    quotes = soup.find_all('div', class_='quote')
    results = []
    
    for quote in quotes:
        text = quote.find('span', class_='text').text.strip('“”')
        author = quote.find('small', class_='author').text
        tags = [tag.text for tag in quote.find_all('a', class_='tag')]
        
        results.append({
            '内容': text,
            '作者': author,
            '标签': ', '.join(tags)
        })
    
    return results

def main():
    base_url = 'https://quotes.toscrape.com/page/{}/'
    all_quotes = []
    
    # 爬前3页（学习用，不要爬太多）
    for page in range(1, 4):
        print(f"正在爬第 {page} 页...")
        url = base_url.format(page)
        page_quotes = get_quotes_from_page(url)
        all_quotes.extend(page_quotes)
        
        # 礼貌延时，不给服务器太大压力
        time.sleep(1)
    
    # 保存到 CSV
    with open('名言收集.csv', 'w', encoding='utf-8-sig', newline='') as f:
        writer = csv.DictWriter(f, fieldnames=['内容', '作者', '标签'])
        writer.writeheader()
        writer.writerows(all_quotes)
    
    print(f"\n✅ 爬取完成，共收集 {len(all_quotes)} 条名言")
    print("已保存到 名言收集.csv")

if __name__ == '__main__':
    main()
```

### 5.8 常见反爬措施与应对

#### ① 添加请求头（User-Agent）

不添加的话，服务器一看就知道是爬虫：

```python
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36'
}
response = requests.get(url, headers=headers)
```

#### ② 控制爬取速度

```python
import time
time.sleep(1)  # 每次请求间隔1秒
```

#### ③ 遇到登录怎么办？

- 简单的：用 `requests.Session()` 模拟登录
- 复杂的：用 Selenium 模拟真实浏览器（大一阶段了解即可）

### 5.9 爬虫学习路线建议

```
大一入门阶段：
├── 第1周：requests + BeautifulSoup 静态网页
├── 第2周：学会翻页、多页爬取
├── 第3周：保存数据到 CSV / Excel / 数据库
├── 第4周：了解 Selenium 动态网页
└── 进阶：Scrapy 框架、分布式爬虫（大二再学也不迟）
```

### 5.10 推荐练习网站

| 网站 | 特点 |
|------|------|
| quotes.toscrape.com | 名言网站，专为爬虫练习设计 |
| books.toscrape.com | 书店网站，有翻页、分类 |
| 豆瓣电影 Top250 | 经典练习目标（注意控制频率） |

---

## 6. 学习路线建议

### 6.1 大一学年 Python 技能树

```
Python 基础语法（变量、循环、函数、类）
        ↓
    常用库入门
    ├── Pandas（数据处理，最实用）
    ├── NumPy（数值计算，理工科必备）
    ├── Requests + BS4（爬虫，有趣又有用）
    └── Matplotlib（画图，写作业报告用）
        ↓
    脚本编写能力
    ├── 代码规范
    ├── 函数封装
    ├── 文件读写
    └── 异常处理
        ↓
    结合专业应用
    ├── 计算机：算法 + 数据结构
    ├── 数理专业：NumPy + SciPy
    ├── 商科/文科：Pandas 数据分析
    └── 感兴趣方向：爬虫/机器学习/网站开发
```

### 6.2 推荐练习项目（大一水平）

1. **成绩分析器**：读取班级成绩表，统计各项指标，生成 Excel 报告
2. **天气爬虫**：爬取未来7天天气预报，保存到表格
3. **图书管理系统**：用 Python + CSV 做一个简单的图书增删改查
4. **单词本**：读取英文文章，统计词频，生成生词表

### 6.3 学习资源推荐

- **官方文档**：Python、Pandas 官方文档最权威
- **菜鸟教程**：适合入门快速查阅
- **B站视频**：搜索"Python 入门"，找播放量高的跟着敲
- **LeetCode**：学完基础后刷算法题，提升编程能力

---

## 附录：常用代码片段速查

### A.1 Pandas 速查表

```python
import pandas as pd

# 读写
df = pd.read_csv('file.csv')
df = pd.read_excel('file.xlsx')
df.to_csv('out.csv', index=False, encoding='utf-8-sig')

# 查看
df.head()          # 前5行
df.shape           # 形状
df.columns         # 列名

# 选择
df['列名']         # 选一列
df[['列1', '列2']] # 选多列
df.iloc[0]         # 按行号选
df.loc[条件]       # 按条件选

# 处理
df.dropna()        # 删除空值
df.fillna(0)       # 填充空值
df.drop_duplicates() # 去重
df.rename(columns={'旧名': '新名'}) # 改列名
```

### A.2 Requests 速查表

```python
import requests

# GET 请求
resp = requests.get(url, params={'key': 'value'})
# POST 请求
resp = requests.post(url, data={'key': 'value'})

# 响应处理
resp.status_code   # 状态码
resp.text          # 文本内容
resp.json()        # JSON 数据（接口常用）
resp.content       # 二进制内容（下载图片）
```

---

> 📌 **最后提醒**：编程是练出来的，不是看出来的。每个知识点都要自己敲一遍代码，遇到报错是正常的，学会看错误信息、搜索解决方案，才是成长最快的方式。加油！
