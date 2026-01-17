---
tags:
  - Python
  - NumPy
  - 数据结构
  - API详解
aliases:
  - NumPy Arrays
  - ndarray
  - default_rng
---

# NumPy 数组操作详解

## 1. 核心概念
**NumPy (Numerical Python)** 是 Python 科学计算的基石。
- **核心对象**：`ndarray` (N-dimensional array)，一种固定类型、多维度的容器。
- **为什么要用它？**
    1. **速度快**：底层由 C 语言编写，支持向量化运算（Vectorization），比 Python 原生 `List` 快得多。
    2. **数学基础**：ISLP 中所有的统计模型（如 [[线性回归]]）底层都在对 NumPy 矩阵进行线性代数运算。
    3. **通用接口**：Pandas 的底层就是 NumPy，Scikit-Learn 的输入输出通常也是 NumPy 数组。

## 2. 基础属性 (Attributes)
了解数据的“长相”是建模的第一步。假设 `x` 是一个 NumPy 数组：

| 属性 | 代码 | 解释 | 示例 |
| :--- | :--- | :--- | :--- |
| **维度** | `x.ndim` | 数组是几维的？(0=标量, 1=向量, 2=矩阵) | `2` |
| **形状** | `x.shape` | 具体的行数和列数元组 | `(2, 3)` 表示2行3列 |
| **类型** | `x.dtype` | 数组内元素的数据类型 | `int64` 或 `float64` |
| **元素总数** | `x.size` | 数组中一共有多少个数字 | `6` |

```python
import numpy as np
x = np.array([[1, 2, 3], [4, 5, 6]])
print(f"维度: {x.ndim}")  # 2
print(f"形状: {x.shape}") # (2, 3)
```

## 3. 数组创建 (Creation)

### `np.array()`

最常用的创建方法。

```python
x = np.array(object, dtype=None)
```

- **参数详解**：
    - `object` (必填): 输入数据。通常是列表 `[1, 2]` 或嵌套列表 `[[1, 2], [3, 4]]`。
    - `dtype` (选填): **强制指定数据类型**。
        - 常用：`float` (浮点数，用于科学计算), `int` (整数)。
        - **⚠️ 避坑指南**：NumPy 数组要求**类型统一**。如果你给了一个 `[1, "a"]`，NumPy 会自动把 `1` 也变成字符串 `'1'`，导致后续无法进行数学运算。

## 4. 形状变换 (Reshape)

### `x.reshape()`

改变数据的排列方式，但不改变数据内容。

```python
new_x = x.reshape(shape)
```

- **参数详解**：
    - `shape`: 目标形状元组，如 `(2, 3)`。
    - **自动推导 (`-1`)**：你可以把其中一个维度设为 `-1`，NumPy 会自动计算该维度的大小。
        - 例：`x.reshape(-1, 1)` 把一维数组变成 **列向量** (n行1列)。
- **⚠️ 内存机制 (View vs Copy)**：
    - `reshape` 返回的是 **视图 (View)**，不是副本！它和原数组共享同一块内存。
    - **后果**：`new_x[0,0] = 999`，你会发现原数组 `x` 的对应位置也变成了 999。
    - **解决**：如果需要独立副本，请使用 `x.reshape(...).copy()`。

## 5. 随机数生成 (ISLP 标准)

在 ISLP 第2版（Python版）中，不再推荐使用旧的 `np.random.seed()`，而是推荐使用更先进的 **Generator** 机制。

### `np.random.default_rng()`

```python
rng = np.random.default_rng(seed)
```

- **参数**：`seed` (整数)，随机种子。
- **作用**：创建一个独立的随机数生成器。只要种子相同（如 `1303`），你的实验结果（如模型系数、训练集切分）就是**可复现**的。

### `rng.normal()`

生成正态分布（高斯分布）数据。

```python
data = rng.normal(loc=0.0, scale=1.0, size=None)
```

- **参数详解**：
    - `loc`: 均值 ($\mu$)，默认为 0。
    - `scale`: 标准差 ($\sigma$)，默认为 1。**注意是标准差不是方差！**
    - `size`: 输出形状。
        - 传入 `100` $\rightarrow$ 生成 100 个数的一维数组。
        - 传入 `(100, 2)` $\rightarrow$ 生成 $100 \times 2$ 的矩阵。

## 6. 聚合运算 (Aggregation)

在处理数据时，我们经常需要求和或平均。

### `np.sum()` / `np.mean()`

```python
total = np.sum(x, axis=None)
```

- **参数详解**：
    - `x`: 输入数组。
    - `axis`: **(最容易晕的地方)**
        - `None` (默认): 计算所有元素的总和。
        - `0`: 沿着 **行 (index)** 的方向操作 $\rightarrow$ **压缩行，保留列**（计算每一列的和）。
        - `1`: 沿着 **列 (columns)** 的方向操作 $\rightarrow$ **压缩列，保留行**（计算每一行的和）。

## 关联笔记

- 数组常用于绘图输入，见 [[Matplotlib 绘图框架详解]]
- 在回归分析中，设计矩阵 $X$ 本质上就是一个二维 NumPy 数组，见 [[线性回归_statsmodels实现]]。