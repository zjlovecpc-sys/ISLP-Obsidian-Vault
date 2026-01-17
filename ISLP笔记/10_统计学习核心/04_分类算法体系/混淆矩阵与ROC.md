---
tags:
  - 统计学习
  - 模型评估
  - ROC
  - 混淆矩阵
aliases:
  - Confusion Matrix
  - AUC
  - Sensitivity
  - Specificity
---

# 混淆矩阵与 ROC 曲线

## 1. 为什么准确率 (Accuracy) 不够？
在[[逻辑回归进阶#2. 罕见事件与病例对照抽样 (Case-Control Sampling)|不平衡数据]]中（如 99% 正常，1% 欺诈），如果你盲猜“全为正常”，准确率高达 99%，但模型毫无用处。我们需要更细致的指标。

## 2. 混淆矩阵 (Confusion Matrix)
这是一个 $2 \times 2$ 的表格，用于展示预测结果与真实情况的对比。

| | 预测：No (0) | 预测：Yes (1) |
| :--- | :--- | :--- |
| **真实：No (0)** | **TN** (True Neg) | **FP** (False Pos, 误报) |
| **真实：Yes (1)** | **FN** (False Neg, 漏报) | **TP** (True Pos) |

### 核心指标
- **敏感度 (Sensitivity) / 召回率 (Recall)**：$\frac{TP}{TP+FN}$
    - 含义：所有**真病人**中，你找出了几个？（看重“不漏诊”）
- **特异度 (Specificity)**：$\frac{TN}{TN+FP}$
    - 含义：所有**真健康人**中，你排除了几个？（看重“不误诊”）
- **精确率 (Precision)**：$\frac{TP}{TP+FP}$
    - 含义：在你预测为阳性的人中，有几个是真阳性？

## 3. ROC 曲线与 AUC
分类器的输出通常是一个概率 $p(X)$。我们需要设定一个**阈值 (Threshold)**（默认 0.5）来决定归类。
- **权衡**：降低阈值（如 0.1） $\rightarrow$ 敏感度 $\uparrow$，特异度 $\downarrow$（宁可错杀一千，不可放过一个）。

### ROC 曲线
- **横轴**：False Positive Rate ($1 - \text{Specificity}$)
- **纵轴**：True Positive Rate (Sensitivity)
- **含义**：展示在**所有可能的阈值**下，模型表现的轨迹。

### AUC (Area Under Curve)
- **定义**：ROC 曲线下的面积。
- **判据**：
    - AUC = 0.5：瞎猜（随机）。
    - AUC = 1.0：完美分类。
    - **美赛标准**：AUC > 0.8 通常被认为模型表现良好。