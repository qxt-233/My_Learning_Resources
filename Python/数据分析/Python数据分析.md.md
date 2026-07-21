# Python 数据分析三剑客：NumPy + Pandas + Matplotlib 常用知识手册

> 面向大一学生的数据分析入门指南，覆盖最常用的核心操作

---

## 目录

1. [NumPy：数值计算基础](#1-numpy数值计算基础)
2. [Pandas：表格数据处理](#2-pandas表格数据处理)
3. [Matplotlib：数据可视化](#3-matplotlib数据可视化)
4. [三库联动实战案例](#4-三库联动实战案例)
5. [速查表附录](#5-速查表附录)

---

## 1. NumPy：数值计算基础

### 1.1 NumPy 是什么？

NumPy（Numerical Python）是 Python 科学计算的**底层基础库**，核心是 `ndarray`（多维数组），比 Python 原生列表运算速度快几十倍。Pandas、Matplotlib 底层都依赖 NumPy。

**安装**：
```bash
pip install numpy
```

**导入（行业标准写法）**：
```python
import numpy as np
```

### 1.2 核心概念：ndarray 数组

| 特点 | 说明 |
|------|------|
| 同类型 | 数组内所有元素类型必须一致（都是 int / 都是 float） |
| 连续内存 | 数据在内存中连续存储，运算速度极快 |
| 向量化运算 | 不用写循环，直接对整个数组做数学运算 |

### 1.3 创建数组的常用方法

#### 方法一：从列表转换
```python
# 一维数组
arr1 = np.array([1, 2, 3, 4, 5])
print(arr1)  # [1 2 3 4 5]

# 二维数组（矩阵）
arr2 = np.array([[1, 2, 3],
                 [4, 5, 6]])
print(arr2.shape)  # (2, 3) → 2行3列
```

#### 方法二：生成序列数组
```python
# 类似 range，生成连续整数
a = np.arange(0, 10)      # [0 1 2 3 4 5 6 7 8 9]
b = np.arange(0, 10, 2)   # [0 2 4 6 8]，步长为2

# 生成等差数列（首尾都包含）
c = np.linspace(0, 1, 5)  # [0.   0.25 0.5  0.75 1.  ]
```

#### 方法三：特殊数组
```python
# 全0数组
zeros = np.zeros((3, 4))      # 3行4列全0

# 全1数组
ones = np.ones((2, 3))        # 2行3列全1

# 单位矩阵
eye = np.eye(3)               # 3阶单位矩阵

# 随机数组（0~1均匀分布）
rand = np.random.rand(2, 3)

# 随机整数
rand_int = np.random.randint(0, 100, size=(3, 3))  # 0~99的3x3随机整数
```

### 1.4 数组属性速查

```python
arr = np.array([[1, 2, 3], [4, 5, 6]])

arr.shape    # 形状：(2, 3)
arr.ndim     # 维度：2
arr.size     # 元素总数：6
arr.dtype    # 数据类型：int64
```

### 1.5 数组运算（向量化，不用写循环）

#### 数组与数字运算
```python
arr = np.array([1, 2, 3, 4])

print(arr + 10)   # [11 12 13 14]，每个元素+10
print(arr * 2)    # [2 4 6 8]，每个元素×2
print(arr ** 2)   # [ 1  4  9 16]，每个元素平方
```

#### 数组与数组运算（对应位置计算）
```python
a = np.array([1, 2, 3])
b = np.array([10, 20, 30])

print(a + b)   # [11 22 33]
print(a * b)   # [10 40 90]
```

### 1.6 常用统计函数

```python
arr = np.array([1, 2, 3, 4, 5])

arr.sum()      # 求和：15
arr.mean()     # 平均值：3.0
arr.max()      # 最大值：5
arr.min()      # 最小值：1
arr.std()      # 标准差
arr.var()      # 方差
np.median(arr) # 中位数
```

**二维数组按行/列统计**：
```python
arr = np.array([[1, 2, 3], [4, 5, 6]])

arr.sum(axis=0)   # 按列求和：[5 7 9]
arr.sum(axis=1)   # 按行求和：[6 15]
```

### 1.7 索引与切片

#### 一维数组（和列表几乎一样）
```python
arr = np.array([10, 20, 30, 40, 50])

print(arr[0])      # 10，取第一个元素
print(arr[1:4])    # [20 30 40]，切片
print(arr[-1])     # 50，倒数第一个
```

#### 二维数组（行, 列）
```python
arr = np.array([[1, 2, 3],
                [4, 5, 6],
                [7, 8, 9]])

print(arr[0, 1])      # 2，第0行第1列
print(arr[0, :])      # [1 2 3]，第0行全部
print(arr[:, 1])      # [2 5 8]，第1列全部
print(arr[0:2, 0:2])  # 左上角2x2子矩阵
```

### 1.8 形状变换

```python
arr = np.arange(6)  # [0 1 2 3 4 5]

# 改变形状（元素总数必须一致）
arr2 = arr.reshape(2, 3)
print(arr2)
# [[0 1 2]
#  [3 4 5]]

# 展平为一维
flat = arr2.flatten()  # [0 1 2 3 4 5]

# 转置
t = arr2.T  # 3行2列
```

### 1.9 布尔索引（条件筛选）

```python
arr = np.array([1, 2, 3, 4, 5, 6])

# 选出大于3的元素
result = arr[arr > 3]
print(result)  # [4 5 6]
```

---

## 2. Pandas：表格数据处理

### 2.1 Pandas 是什么？

Pandas 是 Python 最流行的**表格数据处理库**，相当于"Python 版 Excel"，但处理速度更快、自动化能力更强。

**安装**：
```bash
pip install pandas openpyxl
```

**导入**：
```python
import pandas as pd
```

### 2.2 两个核心数据结构

| 结构 | 说明 | 类比 |
|------|------|------|
| **Series** | 一维数据，带索引 | Excel 的一列 |
| **DataFrame** | 二维表格，多行多列 | Excel 的一张工作表 |

### 2.3 创建 DataFrame

#### 从字典创建（最常用）
```python
data = {
    '姓名': ['张三', '李四', '王五', '赵六', '钱七'],
    '班级': ['一班', '一班', '二班', '二班', '一班'],
    '数学': [85, 92, 78, 95, 88],
    '英语': [90, 85, 82, 78, 95]
}

df = pd.DataFrame(data)
print(df)
```

输出：
```
   姓名  班级  数学  英语
0  张三  一班   85   90
1  李四  一班   92   85
2  王五  二班   78   82
3  赵六  二班   95   78
4  钱七  一班   88   95
```

### 2.4 读写文件（工作中最常用）

#### 读取文件
```python
# 读取 CSV
df = pd.read_csv('学生成绩.csv')

# 读取 Excel
df = pd.read_excel('学生成绩.xlsx', sheet_name='Sheet1')
```

#### 保存文件
```python
# 保存为 CSV（utf-8-sig 防止 Excel 中文乱码）
df.to_csv('结果.csv', index=False, encoding='utf-8-sig')

# 保存为 Excel
df.to_excel('结果.xlsx', index=False)
```

> 💡 `index=False` 表示不保存行号，否则会多出一列索引

### 2.5 数据查看

```python
df.head()        # 前5行
df.tail(3)       # 后3行
df.shape         # 形状：(行数, 列数)
df.columns       # 所有列名
df.dtypes        # 每列的数据类型
df.info()        # 详细信息（类型、缺失值数量）
df.describe()    # 数值列的统计摘要（均值、最大最小等）
```

### 2.6 选择数据

#### 选列
```python
# 选一列，返回 Series
df['姓名']

# 选多列，返回 DataFrame
df[['姓名', '数学']]
```

#### 按行号选（iloc）
```python
df.iloc[0]       # 第1行
df.iloc[0:3]     # 前3行
df.iloc[0, 1]    # 第0行第1列的值
```

#### 条件筛选（最常用）
```python
# 数学成绩大于90的学生
df[df['数学'] > 90]

# 多条件筛选（每个条件必须加括号，& 表示且，| 表示或）
df[(df['数学'] > 80) & (df['英语'] > 85)]

# 按值筛选
df[df['班级'].isin(['一班', '三班'])]
```

### 2.7 排序

```python
# 按数学成绩降序排列
df.sort_values('数学', ascending=False)

# 多列排序：先按班级，再按数学成绩
df.sort_values(['班级', '数学'], ascending=[True, False])
```

### 2.8 新增与修改列

```python
# 新增总分列
df['总分'] = df['数学'] + df['英语']

# 新增平均分列
df['平均分'] = df['总分'] / 2

# 批量修改：所有人数学+5分
df['数学'] = df['数学'] + 5

# 条件修改：数学大于90标记为优秀
df['数学等级'] = df['数学'].apply(lambda x: '优秀' if x >= 90 else '良好' if x >= 80 else '加油')
```

### 2.9 分组统计（groupby）

```python
# 按班级分组，计算各科平均分
df.groupby('班级')[['数学', '英语']].mean()

# 多种统计方式
df.groupby('班级')['数学'].agg(['count', 'mean', 'max', 'min'])
```

### 2.10 数据清洗

#### 处理缺失值
```python
df.isna().sum()         # 每列缺失值数量
df.dropna()             # 删除有空值的行
df.fillna(0)            # 空值填充为0
df['列名'].fillna(df['列名'].mean())  # 用平均值填充
```

#### 去重
```python
df.drop_duplicates()    # 删除完全重复的行
```

#### 改列名
```python
df.rename(columns={'旧列名': '新列名'}, inplace=True)
```

### 2.11 合并数据

```python
# 纵向拼接（两个表结构一样，上下拼）
df_total = pd.concat([df1, df2], ignore_index=True)

# 横向合并（按某列关联，类似 Excel 的 VLOOKUP）
df_merged = pd.merge(df1, df2, on='学号', how='left')
```

---

## 3. Matplotlib：数据可视化

### 3.1 Matplotlib 是什么？

Matplotlib 是 Python 最基础、最常用的**绘图库**，可以画折线图、柱状图、散点图、饼图等各种统计图表，是数据分析报告必备工具。

**安装**：
```bash
pip install matplotlib
```

**导入与配置**：
```python
import matplotlib.pyplot as plt

# 解决中文显示问题（必加！）
plt.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei']
plt.rcParams['axes.unicode_minus'] = False  # 解决负号显示问题
```

### 3.2 一张图的基本组成

```
        标题 (title)
    ┌───────────────────┐
    │     ┌─────────┐   │
    │  y  │  绘图区  │   │
    │  轴 │         │   │
    │     └─────────┘   │
    │        x 轴       │
    └───────────────────┘
         图例 (legend)
```

### 3.3 折线图（plot）

**适用场景**：展示数据随时间/顺序的变化趋势

```python
# 数据
x = [1, 2, 3, 4, 5]
y1 = [20, 25, 18, 30, 28]
y2 = [15, 22, 26, 24, 30]

# 画图
plt.figure(figsize=(8, 5))  # 设置画布大小
plt.plot(x, y1, marker='o', color='blue', label='产品A')
plt.plot(x, y2, marker='s', color='red', linestyle='--', label='产品B')

# 装饰
plt.title('月度销量对比', fontsize=14)
plt.xlabel('月份')
plt.ylabel('销量（件）')
plt.legend()  # 显示图例
plt.grid(True, alpha=0.3)  # 网格线

# 显示/保存
plt.tight_layout()  # 自动调整布局
plt.savefig('折线图.png', dpi=150)  # 保存图片
plt.show()
```

**常用参数**：
| 参数 | 说明 | 可选值 |
|------|------|--------|
| `color` | 线条颜色 | 'red', 'blue', '#FF6B6B' |
| `linestyle` | 线型 | '-'实线, '--'虚线, ':'点线 |
| `marker` | 数据点标记 | 'o'圆点, 's'方块, '^'三角 |
| `linewidth` | 线宽 | 数字，如 2 |

### 3.4 柱状图（bar）

**适用场景**：对比不同类别数据的大小

```python
categories = ['一班', '二班', '三班', '四班']
scores = [82.5, 88.3, 79.6, 85.1]

plt.figure(figsize=(8, 5))
bars = plt.bar(categories, scores, color=['#4ECDC4', '#45B7D1', '#96CEB4', '#FFEAA7'])

# 在柱子上方显示数值
for bar in bars:
    height = bar.get_height()
    plt.text(bar.get_x() + bar.get_width()/2, height + 0.5,
             f'{height:.1f}', ha='center', fontsize=10)

plt.title('各班数学平均分对比', fontsize=14)
plt.ylabel('平均分')
plt.ylim(70, 95)  # 设置y轴范围
plt.tight_layout()
plt.show()
```

**横向柱状图**：
```python
plt.barh(categories, scores)  # barh 横向
```

### 3.5 散点图（scatter）

**适用场景**：观察两个变量之间的相关性

```python
# 学习时长 vs 考试成绩
study_hours = [2, 3, 4, 5, 6, 7, 8, 9, 10]
scores = [55, 60, 65, 70, 75, 80, 85, 90, 95]

plt.figure(figsize=(8, 5))
plt.scatter(study_hours, scores, s=80, c='red', alpha=0.7)

plt.title('学习时长与成绩关系', fontsize=14)
plt.xlabel('学习时长（小时）')
plt.ylabel('考试成绩')
plt.grid(True, alpha=0.3)
plt.show()
```

### 3.6 饼图（pie）

**适用场景**：展示各部分占整体的比例

```python
labels = ['优秀', '良好', '及格', '不及格']
sizes = [15, 35, 40, 10]
colors = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4']
explode = (0.05, 0, 0, 0)  # 突出显示第一块

plt.figure(figsize=(7, 7))
plt.pie(sizes, 
        labels=labels,
        colors=colors,
        autopct='%1.1f%%',  # 显示百分比
        explode=explode,
        startangle=90)

plt.title('班级成绩分布', fontsize=14)
plt.axis('equal')  # 保证是正圆形
plt.show()
```

### 3.7 直方图（hist）

**适用场景**：查看数据的分布情况

```python
# 生成1000个正态分布的成绩数据
scores = np.random.normal(75, 10, 1000)

plt.figure(figsize=(8, 5))
plt.hist(scores, bins=20, color='#45B7D1', edgecolor='white', alpha=0.8)

plt.title('学生成绩分布直方图', fontsize=14)
plt.xlabel('分数')
plt.ylabel('人数')
plt.grid(axis='y', alpha=0.3)
plt.show()
```

### 3.8 子图（subplot）

在一张画布上画多个图：

```python
plt.figure(figsize=(12, 8))

# 子图1：折线图（2行2列，第1个位置）
plt.subplot(2, 2, 1)
plt.plot([1,2,3], [4,5,6])
plt.title('折线图')

# 子图2：柱状图
plt.subplot(2, 2, 2)
plt.bar(['A','B','C'], [3,5,2])
plt.title('柱状图')

# 子图3：散点图
plt.subplot(2, 2, 3)
plt.scatter([1,2,3], [4,5,6])
plt.title('散点图')

# 子图4：饼图
plt.subplot(2, 2, 4)
plt.pie([3,5,2], labels=['A','B','C'])
plt.title('饼图')

plt.tight_layout()
plt.show()
```

### 3.9 Pandas 直接画图（更简单）

Pandas 封装了 Matplotlib，DataFrame 可以直接调用绘图方法，代码更简洁：

```python
# 折线图
df.plot(x='日期', y='销售额', kind='line')

# 柱状图
df.plot(x='班级', y='平均分', kind='bar')

# 直方图
df['成绩'].plot(kind='hist', bins=20)

plt.show()
```

---

## 4. 三库联动实战案例

### 案例：分析班级成绩并可视化

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 中文配置
plt.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei']
plt.rcParams['axes.unicode_minus'] = False

# ========== 1. 生成模拟数据（NumPy） ==========
np.random.seed(42)  # 固定随机种子，结果可复现
n_students = 50

data = {
    '学号': range(1, n_students + 1),
    '班级': np.random.choice(['一班', '二班', '三班'], n_students),
    '数学': np.random.normal(75, 12, n_students).round(1),
    '英语': np.random.normal(78, 10, n_students).round(1),
    '语文': np.random.normal(80, 8, n_students).round(1)
}

# ========== 2. 数据处理（Pandas） ==========
df = pd.DataFrame(data)

# 计算总分和平均分
df['总分'] = df['数学'] + df['英语'] + df['语文']
df['平均分'] = (df['总分'] / 3).round(1)

# 按班级统计
class_stats = df.groupby('班级')[['数学', '英语', '语文', '平均分']].mean().round(1)
print("各班平均分：")
print(class_stats)

# 筛选前十名
top10 = df.sort_values('总分', ascending=False).head(10)

# ========== 3. 可视化（Matplotlib） ==========
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# 图1：各班平均分对比柱状图
class_stats['平均分'].plot(kind='bar', ax=axes[0], color=['#4ECDC4', '#45B7D1', '#96CEB4'])
axes[0].set_title('各班平均成绩对比', fontsize=14)
axes[0].set_ylabel('平均分')
axes[0].set_ylim(60, 90)
axes[0].tick_params(axis='x', rotation=0)

# 图2：成绩分布直方图
df['总分'].plot(kind='hist', bins=15, ax=axes[1], color='#FF6B6B', edgecolor='white', alpha=0.8)
axes[1].set_title('总分分布直方图', fontsize=14)
axes[1].set_xlabel('总分')
axes[1].set_ylabel('人数')

plt.tight_layout()
plt.savefig('班级成绩分析.png', dpi=150)
plt.show()

# 保存结果
df.to_excel('班级成绩表.xlsx', index=False)
top10.to_excel('成绩前十名.xlsx', index=False)
print("\n分析完成，文件已保存！")
```

---

## 5. 速查表附录

### 5.1 NumPy 速查

```python
import numpy as np

# 创建
np.array([1,2,3])          # 从列表创建
np.arange(0, 10)           # 连续整数
np.linspace(0, 1, 5)       # 等差数列
np.zeros((3,3))            # 全0
np.ones((2,3))             # 全1
np.random.rand(3,3)        # 随机数

# 属性
arr.shape                  # 形状
arr.size                   # 元素个数
arr.dtype                  # 数据类型

# 运算
arr + 1                    # 每个元素+1
arr * 2                    # 每个元素×2
a + b                      # 对应位置相加

# 统计
arr.sum()                  # 求和
arr.mean()                 # 平均值
arr.max() / arr.min()      # 最大/最小
arr.std()                  # 标准差

# 变形
arr.reshape(2, 3)          # 改变形状
arr.flatten()              # 展平
arr.T                      # 转置
```

### 5.2 Pandas 速查

```python
import pandas as pd

# 读写
pd.read_csv('file.csv')               # 读CSV
pd.read_excel('file.xlsx')            # 读Excel
df.to_csv('out.csv', index=False)     # 存CSV

# 查看
df.head()              # 前5行
df.shape               # 形状
df.info()              # 详细信息
df.describe()          # 统计摘要

# 选择
df['列名']             # 选一列
df[['列1','列2']]      # 选多列
df[df['列'] > 10]      # 条件筛选

# 处理
df.sort_values('列')   # 排序
df.groupby('列').mean() # 分组统计
df.dropna()            # 删除空值
df.fillna(0)           # 填充空值
df['新列'] = ...       # 新增列
```

### 5.3 Matplotlib 速查

```python
import matplotlib.pyplot as plt

plt.rcParams['font.sans-serif'] = ['SimHei']  # 中文
plt.rcParams['axes.unicode_minus'] = False    # 负号

plt.figure(figsize=(8, 5))    # 创建画布
plt.plot(x, y)                # 折线图
plt.bar(x, y)                 # 柱状图
plt.scatter(x, y)             # 散点图
plt.pie(sizes, labels=...)    # 饼图
plt.hist(data, bins=20)       # 直方图

plt.title('标题')             # 标题
plt.xlabel('x轴')             # x轴标签
plt.ylabel('y轴')             # y轴标签
plt.legend()                  # 图例
plt.grid(True)                # 网格

plt.tight_layout()            # 自动布局
plt.savefig('图.png', dpi=150) # 保存
plt.show()                    # 显示
```

---

## 学习建议

1. **Pandas 优先掌握**：日常学习、作业中用得最多，处理 Excel、CSV 数据必备
2. **NumPy 打好基础**：理解数组运算原理，为后续机器学习、科学计算打基础
3. **Matplotlib 边用边查**：不用死记所有参数，需要画图时查模板即可
4. **多做练习**：找一份真实数据（成绩表、销售数据），从读取到分析到画图完整走一遍

> 💡 三库是数据分析的基础工具，掌握了这三个，Python 数据分析的大门就打开了！
