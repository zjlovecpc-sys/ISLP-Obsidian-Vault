---
tags:
  - Python
  - statsmodels
  - ISLP库
  - 线性回归
  - API详解
aliases:
  - OLS Regression
  - ModelSpec
  - anova_lm
  - get_prediction
---

# 线性回归 (Statsmodels 实现详解)

## 1. 核心库架构

ISLP 的 Python 版使用了一套独特的“组合拳”：
1. **Statsmodels**: 负责核心统计计算（回归、假设检验、P值）。
2. **ISLP.models.ModelSpec (MS)**: 负责数据转换（构建设计矩阵 $X$，自动处理截距、哑变量、交互项）。

```python
import statsmodels.api as sm
from ISLP.models import ModelSpec as MS, summarize, poly
from ISLP import load_data
```

## 2. 构建设计矩阵 (Design Matrix)

这是建模的第一步。我们需要把原始数据 (`DataFrame`) 转换成数学模型能吃的矩阵 (`NumPy Array`)。

### `MS(terms).fit_transform(df)`

Python

```python
# 1. 定义转换规则 (处方)
design = MS(['lstat', 'age', ('lstat', 'age')]) 

# 2. 学习并转换数据 (抓药)
X = design.fit_transform(Boston)
```

- **参数详解**：
    - `terms`: 一个列表，包含列名字符串。
        - `'var'`: 简单线性项。
        - `('var1', 'var2')`: **交互项** (Interaction)，自动计算 $X_1 \times X_2$。
        - `poly('var', degree=2)`: **多项式项**，自动生成正交多项式基底。
- **返回值**：
    - 返回一个包含截距列 (`intercept`) 的 DataFrame/Array，可以直接喂给 `sm.OLS`。
- **自动处理机制**：
    - **截距**：自动添加全为 1 的 `intercept` 列（无需手动 `sm.add_constant`）。
    - **定性变量**：如果列是字符串/类别型，自动进行 One-Hot 编码（生成哑变量）。

### 关键坑点：训练集 vs 测试集

在处理新数据（测试集）时，**千万不要**再次调用 `fit_transform`！

Python

```python
# 错误做法 (Data Leakage / 维度不匹配)
# new_X = design.fit_transform(new_df) 

# 正确做法 (复用之前的规则)
new_X = design.transform(new_df)
```

- **原理**：比如 `poly` 变换需要用到训练集的均值 and 范围；哑变量需要知道训练集有哪些类别。如果对测试集重新 fit，标准就不统一了。

## 3. 拟合模型 (Model Fitting)

### `sm.OLS(endog, exog)`

Python

```python
model = sm.OLS(y, X)
results = model.fit()
```

- **参数详解**：
    - `endog` (Endogenous): **因变量 Y**。注意 Statsmodels 把 Y 放在第一个参数！
    - `exog` (Exogenous): **自变量 X** (设计矩阵)。
    - **顺序记忆**：“先果后因”（与 Scikit-Learn 的 `fit(X, y)` 相反）。
- **返回值**：
    - `results`: 一个 `RegressionResultsWrapper` 对象，包含了模型的所有秘密。

## 4. 结果解读 (Inference)

### `results.summary()`

打印像论文附录一样的完整统计报表（系数、Std.Err、t值、P值、R²、F统计量）。

- **技巧**：`summarize(results)` 是 ISLP 提供的简化版函数，输出更干净的 DataFrame 格式。

### `results.conf_int(alpha=0.05)`

获取回归系数 $\beta$ 的置信区间。

## 5. 预测 (Prediction)

这是美赛中最常用的功能：给定新的 X，预测未来的 Y。

### `results.get_prediction(new_X)`

Python

```python
# 1. 准备新数据
new_df = pd.DataFrame({'lstat': [5, 10, 15], 'age': [50, 60, 70]})
new_X = design.transform(new_df) # 记得用 transform!

# 2. 获取预测对象
pred = results.get_prediction(new_X)

# 3. 提取结果
y_hat = pred.predicted_mean  # 点估计 (预测值)
ci_table = pred.summary_frame(alpha=0.05) # 获取详细区间表
```

### 置信区间 vs 预测区间

`pred.summary_frame()` 返回的表中包含两组核心区间，美赛解题时务必分清：

1. **`mean_ci_lower` / `mean_ci_upper`**:
    - **置信区间 (Confidence Interval)**。
    - 含义：平均而言，Y 会落在哪里？（区间较窄）。
2. **`obs_ci_lower` / `obs_ci_upper`**:
    - **预测区间 (Prediction Interval)**。
    - 含义：**某一个具体的新数据**，Y 会落在哪里？（区间较宽，包含了 $\epsilon$ 的波动）。
    - **应用**：如果题目问“预测明年销量的波动范围”，选这个！

## 6. 模型对比与诊断

### `anova_lm(model1, model2)`

用于比较两个模型（通常是一个简单模型 vs 一个复杂模型）。

Python

```python
# H0: 两个模型效果一样 (复杂模型增加的变量没用)
# Ha: 复杂模型显著更好
print(anova_lm(results_simple, results_complex))
```

- **看点**：如果 `Pr(>F)` (P值) < 0.05，说明复杂模型值得用。

### 残差图绘制

Python

```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(8, 6))
# 横轴：拟合值 (Fitted Values)
# 纵轴：残差 (Residuals)
ax.scatter(results.fittedvalues, results.resid)
ax.axhline(0, color='black', linestyle='--')
ax.set_xlabel('Fitted Values')
ax.set_ylabel('Residuals')
```

- **诊断**：如果图中出现 **U型** 趋势，说明需要加非线性项（如 `poly`）。如果是 **漏斗型**，说明存在异方差性。

## 关联笔记

- 理论基础见 [[简单线性回归]] 和 [[多元线性回归]]。
- 如果需要手动处理数组（不经过 MS），参考 [[NumPy数组操作详解]]。
- 诊断图的意义见 [[回归诊断与潜在问题]]。