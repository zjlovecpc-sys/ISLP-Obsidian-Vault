---
tags:
  - Python
  - 算法实现
  - Bootstrap
  - 统计推断
  - 参数估计
aliases:
  - Bootstrap Implementation
  - 自助法实战
---

# Bootstrap 实战 (The Bootstrap Implementation)

## 0. 核心思想

Bootstrap 不用于训练模型，而是用于**给统计量算命**（估计标准误 SE）。
核心流程：`重抽样 (Resample)` $\to$ `算指标 (Calculate)` $\to$ `统计波动 (Analyze)`.

## 1. 基础案例：投资组合优化 ($\alpha$ 估计)

目标：计算两个资产 X 和 Y 的最优投资比例 $\alpha$，使得总风险最小。

### 定义统计量函数

```python
def alpha_func(D, idx):
    # D: 数据集, idx: 重抽样的索引列表
    # .loc[idx] 支持重复提取 (Key!)
    cov_ = np.cov(D[['X','Y']].loc[idx], rowvar=False)
    return ((cov_[1,1] - cov_[0,1]) / 
            (cov_[0,0]+cov_[1,1]-2*cov_[0,1]))
```

- **关键点**：`D.loc[idx]` 是 Bootstrap 的引擎。如果 `idx=[0, 0, 1]`，它会生成一个包含两行 0 号样本的新表。

### 执行 Bootstrap

```python
# 生成 1000 个平行宇宙
rng = np.random.default_rng(0)
B = 1000
idx = rng.choice(100, 100, replace=True) # 有放回抽样
alpha_star = alpha_func(Portfolio, idx)
```

---

## 2. 通用 Bootstrap 标准误计算器

为了避免重复写循环，我们封装一个通用的 `boot_SE` 函数。

```python
def boot_SE(func, D, n=None, B=1000, seed=0):
    rng = np.random.default_rng(seed)
    first_, second_ = 0, 0 # 累加器 (流式计算方差)
    n = n or D.shape[0]
    
    for _ in range(B):
        idx = rng.choice(D.index, n, replace=True) # 使用 .index 防止位置索引报错
        value = func(D, idx)
        first_ += value
        second_ += value**2
        
    return np.sqrt(second_ / B - (first_ / B)**2)
```

- **Pro Tip**: 使用 `first_` (和) 和 `second_` (平方和) 可以在不存储 1000 个数值的情况下直接计算方差，极大节省内存。

---

## 3. 进阶案例：线性回归系数的波动

我们想知道：线性回归算出的“截距”和“斜率”到底有多准？

### 构造适配函数 (`partial` 的妙用)

由于 `boot_SE` 只接受 `(D, idx)` 两个参数，而回归函数需要更多参数，我们使用 `functools.partial` 进行“参数冷冻”。

```python
from functools import partial

# 定义回归系数提取逻辑
def boot_OLS(model_matrix, response, D, idx):
    D_ = D.loc[idx]
    # 必须在重抽样数据上重新 fit_transform (如重新算截距)
    X_ = clone(model_matrix).fit_transform(D_)
    Y_ = D_[response]
    return sm.OLS(Y_, X_).fit().params

# 冷冻前两个参数，打包成 hp_func
hp_func = partial(boot_OLS, MS(['horsepower']), 'mpg')

# 一键计算 SE
se_estimates = boot_SE(hp_func, Auto, B=1000)
```

---

## 4. 巅峰对决：Bootstrap SE vs. 公式 SE

这是本章最深刻的实验结果，揭示了模型假设的影响。

### 实验 1：线性模型 (Linear Model)

- **公式 SE**: Intercept $\approx 0.717$
- **Bootstrap SE**: Intercept $\approx 0.85$
- **结论**：**差异巨大**。
- **原因**：线性模型**欠拟合** (Underfitting)，且数据存在**异方差性** (Heteroscedasticity)。公式法的假设（同方差、线性）破裂，导致其低估了误差。**此时 Bootstrap 更可信。**

### 实验 2：二次方模型 (Quadratic Model)

- **公式 SE**: Intercept $\approx 2.09$
- **Bootstrap SE**: Intercept $\approx 2.06$
- **结论**：**几乎一致**。
- **原因**：二次模型完美拟合了数据的弯曲趋势，残差接近随机噪声，满足了 OLS 公式的假设。

### 建模启示

当 Bootstrap SE 与 公式 SE 结果高度一致时，通常意味着你的**模型结构（Model Specification）是正确的**。这是一个极佳的模型诊断信号。

---
## 理论回溯
- **核心理论**：[[自助法]]
- **关联模型**：[[简单线性回归]]、[[回归扩展模型]]