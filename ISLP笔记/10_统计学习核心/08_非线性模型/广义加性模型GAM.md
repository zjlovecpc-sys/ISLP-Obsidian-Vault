---
tags:
  - 统计学习
  - 非线性模型
  - 模型集成
aliases:
  - Generalized Additive Models
  - GAM
  - Backfitting
  - 后向拟合
---

# 广义加性模型 (GAM)

## 1. 模型定义
GAM 将线性模型简单地扩展为非线性函数的线性组合：
$$y_i = \beta_0 + \sum_{j=1}^p f_j(x_{ij}) + \epsilon_i$$
$$y_i = \beta_0 + f_1(x_{i1}) + f_2(x_{i2}) + \dots + f_p(x_{ip}) + \epsilon_i$$
* 每个 $f_j$ 可以是前面学到的任何非线性积木：[[回归样条]]、[[平滑样条]] 或 [[局部回归]]。

## 2. 核心优势：可加性与可解释性
* **控制变量法**：由于模型是可加的，我们可以说：“在保持 $X_2, \dots, X_p$ 不变的情况下，$X_1$ 的变化会导致 $Y$ 按照 $f_1(X_1)$ 的曲线变化。”
* **可视化**：我们可以单独画出 $f_1(X_1)$，$f_2(X_2)$ 等曲线，直观地展示每个变量对响应变量的非线性影响。这在美赛论文中是极佳的图表素材。

## 3. 局限性
* **无交互作用**：默认假设变量之间独立叠加，忽略了 $X_1 \times X_2$ 的交互效应。
    * *补救*：可以手动添加形式为 $f_{jk}(X_j, X_k)$ 的二维平滑项（如 Thin Plate Splines）。

## 4. 拟合算法：后向拟合 (Backfitting)
既然有多个未知函数 $f_1, \dots, f_p$，如何同时求出它们？
GAM 使用一种迭代算法，类似于坐标下降法：
1.  固定 $f_2, \dots, f_p$，对残差 $y - \sum_{k \neq 1} f_k(x_k)$ 拟合 $f_1$。
2.  固定 $f_1, f_3, \dots, f_p$，对新的残差拟合 $f_2$。
3.  重复循环，直到所有 $f_j$ 不再变化。

## 5. Python 实现库
`sklearn` 对 GAM 的支持较弱（仅 `SplineTransformer`）。美赛推荐使用专门的库：
* **pygam**：功能最全，支持 LinearGAM, LogisticGAM 等。
* **InterpretML (EBM)**：基于 GAM 的高可解释性模型（Explainable Boosting Machine）。

## 关联笔记
* ⬅️ 组件来源：[[平滑样条]], [[回归样条]]
* 🔗 扩展阅读：[[逻辑回归]] (GAM 同样适用于分类问题，即 Logistic GAM)