---
tags:
  - Python
  - 库对比
  - 避坑指南
aliases:
  - Scikit-Learn vs Statsmodels
  - 机器学习库选择
---

# Statsmodels vs Scikit-Learn：美赛该用谁？

在 ISLP 的 Python 实验中，你会发现我们经常在这两个库之间反复横跳。它们代表了两种不同的文化。

## 1. 核心定位差异
| 特性       | **Statsmodels**                                  | **Scikit-Learn (sklearn)**                            |
| :------- | :----------------------------------------------- | :---------------------------------------------------- |
| **出身**   | 统计学 (Statistics)                                 | 机器学习 (Machine Learning)                               |
| **核心目标** | **推断 (Inference)**<br>解释 $\beta$ 系数，看 P 值，做假设检验。 | **预测 (Prediction)**<br>追求最高的准确率 (Accuracy)，不在乎系数显不显著。 |
| **长项**   | 线性回归、逻辑回归、GLM、时间序列。                              | KNN、决策树、SVM、随机森林、神经网络。                                |
| **输出**   | 详尽的 `summary()` 统计报表。                            | 仅输出预测结果，几乎没有统计报表。                                     |

## 2. 代码习惯的巨大裂痕 (必背！)

这是新手最容易报错的地方：

### (1) 参数顺序 (最大的坑)
- **Statsmodels**: `model = sm.OLS(y, X)`
    - 遵循统计学传统：先因变量 ($Y$)，后自变量 ($X$)。
    - **口诀：先果后因**。
- **Sklearn**: `model.fit(X, y)`
    - 遵循编程习惯：先输入 ($X$)，后标签 ($y$)。
    - **口诀：先因后果**。

### (2) 截距项 (Intercept)
- **Statsmodels**: **默认不加截距**。
    - 必须手动调用 `sm.add_constant(X)` 或使用 `ISLP.models.ModelSpec`。
- **Sklearn**: **默认自动加截距**。
    - `LinearRegression(fit_intercept=True)` 是默认开启的，不需要手动处理 $X$。

### (3) 数据格式
- **Statsmodels**: 对 Pandas DataFrame 支持极好，保留列名。
- **Sklearn**: 历史上更喜欢 NumPy 数组 (虽然现在对 DataFrame 支持变好了，但输出结果往往会丢失列名)。

## 3. ISLP / 美赛 最佳实践
- **什么时候用 Statsmodels?**
    - 需要写论文分析**“哪个变量更重要”**时。
    - 做线性回归、逻辑回归、GLM 时。
    - 需要 AIC, BIC, P-value 时。
- **什么时候用 Sklearn?**
    - 需要做 **[[K最近邻法|KNN]]** 时 (Statsmodels 的 KNN 很难用)。
    - 需要做 **交叉验证 (Cross-Validation)** 时。
    - 需要画 **[[混淆矩阵与ROC|ROC 曲线]]** 时 (Sklearn 的 `metrics` 模块非常强大)。

## 总结
在第 4 章 Lab 中：
- **逻辑回归 / LDA / QDA**：我们依然首选 `statsmodels` (为了看系数)。
- **KNN**：我们将切换到 `sklearn`。