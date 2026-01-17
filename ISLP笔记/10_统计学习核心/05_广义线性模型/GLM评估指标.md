---
tags:
  - 统计学习
  - GLM
  - 模型评估
aliases:
  - Deviance
  - AIC
  - Null Deviance
  - Residual Deviance
---

# GLM 评估指标

在 [[简单线性回归|线性回归]] 中，我们使用 [[简单线性回归#参数估计：最小二乘法 (Least Squares)|RSS]] 和 [[回归模型评估指标#2. $R^2$ 统计量 (R-squared)|R²]] 来衡量模型好坏。
但在 [[广义线性模型GLM]]（如逻辑回归、泊松回归）中，我们使用基于 **似然函数 (Likelihood)** 的指标。

## 1. 偏差 (Deviance)
偏差类似于线性回归中的 RSS（残差平方和）。**偏差越小，模型拟合越好。**

### 两个关键指标
在 `statsmodels` 的输出报告中，你会看到：
1.  **空偏差 (Null Deviance)**：
    - 仅包含截距项（没有任何特征 $X$）的模型的偏差。
    - 相当于线性回归中的 **TSS** (总平方和)。
    - **基准线**：这是瞎猜的模型表现。
2.  **残差偏差 (Residual Deviance)**：
    - 包含所有特征 $X$ 的当前模型的偏差。
    - 相当于线性回归中的 **RSS**。

### 判定标准
- 如果 **Residual Deviance** 远小于 **Null Deviance**，说明你的特征 $X$ 起到了显著的预测作用。

## 2. 信息准则 (AIC / BIC)
由于 GLM 使用极大似然估计，我们无法直接比较 RSS。为了对比不同模型（例如：泊松 vs 负二项），我们使用 **AIC (赤池信息量准则)**。

$$AIC = -2 \log(L) + 2 \cdot p$$
* $L$：似然函数值（越大越好）。
* $p$：特征数量（惩罚项）。

**美赛判据**：
- **AIC 越小越好**。
- 当你在 [[泊松回归]] 和 [[负二项回归]] 之间犹豫时，看一眼 AIC。通常负二项回归的 AIC 更低，证明其对过离散数据的拟合更优。

## 3. 伪 $R^2$ (Pseudo R-squared)
GLM 没有标准的 $R^2$。常用的近似指标是 **McFadden's $R^2$**：
$$R^2_{McFadden} = 1 - \frac{\text{Residual Deviance}}{\text{Null Deviance}}$$
- **注意**：它的值通常比线性回归的 $R^2$ 小得多。0.2-0.4 有时就已经是很不错的表现了。