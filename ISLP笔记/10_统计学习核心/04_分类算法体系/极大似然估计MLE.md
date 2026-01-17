---
tags:
  - 统计学习
  - 参数估计
  - 数学原理
aliases:
  - Maximum Likelihood Estimation
  - MLE
  - 似然函数
---

# 极大似然估计 (MLE)

## 核心思想
在[[简单线性回归]]中，我们用[[最小二乘法]] (RSS) 来估计参数。但在[[逻辑回归]]中，我们使用 **极大似然估计 (Maximum Likelihood Estimation)**。

**直觉**：我们要寻找一组参数 $\hat{\beta}_0, \hat{\beta}_1$，使得在这个模型下，**观察到当前这组数据的概率（即似然性）最大**。

## 似然函数 (Likelihood Function)
假设我们有观测数据，对于 $y_i=1$ 的样本，我们要预测概率大；对于 $y_i=0$ 的样本，我们要预测概率小。
数学上，我们要最大化以下乘积：

$$L(\beta_0, \beta_1) = \prod_{i:y_i=1} p(x_i) \prod_{i':y_{i'}=0} (1 - p(x_{i'}))$$

通常会对两边取对数（Log-Likelihood），将乘积转化为求和，方便计算。

## 美赛应用
- **不需要手算**：Python 的 `statsmodels` 或 `sklearn` 会自动帮你求解。
- **理解差异**：如果你在论文中提到“Using Least Squares for Logistic Regression”，评委就知道你外行了。必须写“Estimated via Maximum Likelihood”。