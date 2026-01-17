---
tags:
  - Python实战
  - 数据可视化
  - Matplotlib
  - 工具手册
  - 论文作图
aliases:
  - Matplotlib Guide
  - 绘图框架
  - fig与ax对象
  - Python绘图模板
  - Plotting Cheat Sheet
---
# Matplotlib 绘图框架详解 (ISLP/美赛版)

## 1. 核心理念：面向对象 (Object-Oriented)

Matplotlib 有两套画法：

1. **状态机模式 (Pyplot API)**：像 Matlab 一样，使用 `plt.plot()`。它自动找“当前”的图去画。**（美赛不推荐，多图时容易乱）**
2. **面向对象模式 (OO API)**：先创建“画布”和“坐标轴”对象，然后指定在哪个对象上画。**（ISLP 和专业代码标准）**

### 核心对象层级

- **Figure (画布)**：整张图的最底层容器（可以理解为一张白纸）。它包含一个或多个 Axes。
- **Axes (坐标系)**：实际画图的区域（白纸上的一个个格子）。包含 X 轴、Y 轴、标题、线条等。**我们 90% 的操作都是针对 Axes 进行的。**

## 2. 画布创建 (Setup)

### `plt.subplots()`

这是创建 Figure 和 Axes 的标准起手式。

Python

```python
import matplotlib.pyplot as plt

# 创建一个 2行 x 2列 的画布
fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(12, 10), dpi=100)
```

- **参数详解**：
    - `nrows`, `ncols`: 子图的行数和列数。
    - `figsize`: 元组 `(宽, 高)`，单位是英寸。美赛论文建议：单图 `(8, 6)`，双图并排 `(12, 5)`，四图拼盘 `(12, 10)`。
    - `dpi`: 分辨率。屏幕显示 100 即可，**保存图片时建议 300**。
    - `sharex`, `sharey`: `True` 表示所有子图共用坐标轴刻度（方便对比趋势）。
- **返回值解包 (的关键)**：
    - `fig`: 画布对象（主要用于保存文件 `fig.savefig`）。
    - `axes`: 坐标轴对象容器。
        - **单图** (`nrows=1, ncols=1`)：`axes` 就是一个普通的 Axes 对象。
        - **多图**：`axes` 是一个 **NumPy 数组**。
            - 访问方式：`axes[行, 列]`，例如 `ax=axes[0, 1]` (第1行第2列)。
            - **扁平化技巧**：使用 `ax_list = axes.flatten()` 将矩阵拍扁成一维数组，方便循环操作。

## 3. Pandas 与 Matplotlib 的混合双打 (Pandas Integration)

这是 ISLP 中最常用的模式：**用 Matplotlib 建房子（控制框架），用 Pandas 搞装修（快速填入数据）。**

### 核心语法

Python

```python
# 必须显式指定 ax 参数，否则 Pandas 会自己弹出一个新窗口
df.plot(x='Col_A', y='Col_B', kind='line', ax=axes[0,0], title='Title')
```

### 关键参数 (`kind`)

- `'line'`: 折线图（默认）。适合看趋势。
- `'scatter'`: 散点图。适合看相关性（**注意坑点，见下文**）。
- `'bar'` / `'barh'`: 柱状图。适合看对比。
- `'hist'`: 直方图。适合看分布。
- `'box'`: 箱线图。适合看异常值。
- `'kde'`: 密度图。平滑版的直方图。

### 避坑指南：Scatter Plot 与 `reset_index()`

Pandas 的散点图 (`kind='scatter'`) **必须**同时指定 `x` 和 `y` 的列名。

- **问题**：如果想用“行号”（索引）作为 X 轴，直接画会报错，因为索引在 Pandas 里不是一列数据。
- **解决**：使用 `reset_index()` 给行号一个“名分”。

Python

```python
# 错误写法：Scatter requires an x and y column
# df.plot(y='Volume', kind='scatter') 

# 正确写法：先把索引变成名为 'index' 的列
df.reset_index().plot(x='index', y='Volume', kind='scatter', ax=ax)
```

## 4. 基础绘图方法 (Native Matplotlib)

当 Pandas 的自动绘图无法满足需求（如需要复杂的自定义样式）时，直接调用 `ax` 的原生方法。

### `ax.plot()` (折线图/点图)

最通用的方法。

Python

```python
ax.plot(x, y, color='blue', linestyle='--', marker='o', label='Data 1', alpha=0.5)
```

- `alpha`: **透明度** (0~1)。数据点密集时（如几千个点），设置 `alpha=0.5` 可防止重叠成一团黑。

### `ax.scatter()` (高级散点图)

比 Pandas 的 scatter 更强大，支持**每个点**有不同的大小和颜色。

Python

```python
ax.scatter(x, y, s=50, c=z, cmap='viridis')
```

- **美赛应用**：绘制[[回归诊断与潜在问题|残差图]]时，可以用颜色深浅代表残差大小。

## 5. 高维绘图 (ISLP Lab 2 特色)

用于展示 $f(x, y)$，如分类边界或损失函数地形。

- **`ax.contour()` / `ax.contourf()`**: 绘制等高线（`contourf` 带填充颜色）。
- **`ax.imshow()`**: 绘制热图（Heatmap）。
    - **注意**: 记得设置 `origin='lower'`，否则原点默认在左上角（图片习惯），而数学函数原点在左下角。

## 6. 图表修饰 (Decoration)

无论用 Pandas 还是原生方法画图，最后都用 `ax.set_` 方法统一修饰。

| **功能** | **代码** | **备注** |
| :--- | :--- | :--- |
| **标题** | `ax.set_title("Title")` | 支持 LaTeX，如 `r"$\beta_1$ Value"` |
| **轴标签** | `ax.set_xlabel("Time")` | Pandas 会自动加，但也可用此覆盖 |
| **轴范围** | `ax.set_xlim(0, 10)` | 手动固定视野范围 |
| **网格** | `ax.grid(True, linestyle='--')` | 增加网格线辅助读数 |
| **图例** | `ax.legend()` | 必须先在 plot 中设置 `label` 参数 |

## 7. 布局整理与保存 (Final Output)

Python

```python
# 1. 自动调整子图间距（美赛排版神器！）
# 防止子图标题盖住上一张图的坐标轴
plt.tight_layout() 

# 2. 保存图片
# bbox_inches='tight' 防止标签被裁掉
fig.savefig("my_analysis.png", dpi=300, bbox_inches='tight')

# 3. 显示（必须放在 savefig 之后）
plt.show()
```

### 进阶技巧：Flatten (拍扁数组)
当 `nrows > 1` 且 `ncols > 1` 时，`axes` 是一个二维矩阵（如 `axes[0, 1]`）。这在循环画图时很不方便。
可以使用 `.flatten()` 将其变成一维数组：

```python
fig, axes = plt.subplots(2, 2, figsize=(10, 10))
ax_flat = axes.flatten() # 变成 [ax1, ax2, ax3, ax4]

# 现在可以无脑循环了
features = ['Lag1', 'Lag2', 'Lag3', 'Volume']
for i, col in enumerate(features):
    Smarket[col].plot(kind='hist', ax=ax_flat[i], title=col)

plt.tight_layout() # 防止标题重叠的神器
plt.show()
```
## 关联笔记

- 数据源通常来自 NumPy 或 Pandas，见 [[NumPy数组操作详解]]。
- 在回归分析中绘制残差图，见 [[线性回归_statsmodels实现]]。
- ISLP 第4章 Lab 代码实战：[[逻辑回归与判别分析实现]]。