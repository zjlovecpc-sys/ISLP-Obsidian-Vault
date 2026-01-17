---
tags:
  - Python
  - statsmodels
  - GLM
  - 代码实战
aliases:
  - sm.families
  - GLM参数配置
  - 链接函数修改
---

# Statsmodels GLM 全能配置指南

在 `statsmodels` 中，所有的广义线性模型都通过 `sm.GLM` 类来实现。
核心语法如下：

```python
import statsmodels.api as sm

model = sm.GLM(y, X, family=sm.families.FamilyName(link=...))
results = model.fit()
```

本指南将教你如何配置 `family` 和 `link` 以实现不同的回归模型。

## 1. 泊松回归 (Poisson Regression)

**场景**：计数数据（均值 $\approx$ 方差）。

```python
# 默认配置 (Canonical Link: Log)
# 不需要手动写 link，默认就是 Log
model_poisson = sm.GLM(y, X, family=sm.families.Poisson())

# 训练
results = model_poisson.fit()
print(results.summary())
```

- **注意**：如果数据中有非整数（如平均计数），会抛出警告，但通常能跑通（QMLE 估计）。

## 2. 负二项回归 (Negative Binomial)

**场景**：过离散的计数数据（方差 $\gg$ 均值）。

⚠️ 巨坑预警：

statsmodels 有两种方式实现负二项回归，千万别混淆：

### 方法 A：固定 $\alpha$ (使用 sm.GLM)

如果你知道离散参数 $\alpha$（即 `statsmodels` 里的 `alpha`，对应理论公式的 $1/\theta$），或者想尝试几个固定值：


```python
# alpha 是辅助参数，必须指定（默认是 1.0）
# 链接函数默认是 Log
model_nb = sm.GLM(y, X, family=sm.families.NegativeBinomial(alpha=1.0))
results = model_nb.fit()
```

### 方法 B：自动估计 $\alpha$ (使用 sm.NegativeBinomial)

如果你不知道 $\alpha$ 是多少，想让模型自己算（推荐）：

```python
# 注意：这里不调用 sm.GLM，而是直接调用 sm.NegativeBinomial
# loglike_method='nb2' 是标准负二项分布
model_nb_auto = sm.NegativeBinomial(y, X, loglike_method='nb2')
results = model_nb_auto.fit()
print(results.summary()) # 结果里会有一个 'alpha' 的估计值
```

## 3. Gamma 回归

**场景**：右偏的连续正值（金额、降雨量）。

### 默认配置 (Inverse Link)

理论上的标准配置，但实战中经常报错（因为倒数可能导致分母极小）。

```python
# 默认 link 是 1/mu
model_gamma = sm.GLM(y, X, family=sm.families.Gamma())
```

### 🔥 推荐配置 (Log Link)

这是美赛最常用的技巧！ 强制让 Gamma 分布使用 Log 链接函数。

这结合了 Gamma 的“异方差适应性”和 Log 的“物理非负约束”，效果通常最好。

Python

```python
# 手动指定 link 参数
model_gamma_log = sm.GLM(y, X, 
                         family=sm.families.Gamma(link=sm.families.links.log()))
results = model_gamma_log.fit()
```

- **注意**：`link` 参数必须传入 `sm.families.links` 下的**小写**对象实例。

## 4. 逆高斯回归 (Inverse Gaussian)

**场景**：极度右偏的长尾数据（寿命、反应时间）。


```python
# 默认 link 是 1/mu^2
model_ig = sm.GLM(y, X, family=sm.families.InverseGaussian())

# 如果想换成 Log Link 也可以：
model_ig_log = sm.GLM(y, X, 
                      family=sm.families.InverseGaussian(link=sm.families.links.log()))
```

## 5. 完整的“菜单” (Cheat Sheet)

| **模型名称** | **代码配置 (family=...)** | **默认链接 (link)** | **常用替代链接** |
| --- | --- | --- | --- |
| **高斯 (线性)** | `sm.families.Gaussian()` | `identity` ($y$) | 无 |
| **二项 (逻辑)** | `sm.families.Binomial()` | `logit` | `probit` |
| **泊松** | `sm.families.Poisson()` | `log` | `identity` |
| **负二项** | `sm.families.NegativeBinomial(alpha=1)` | `log` | 无 |
| **Gamma** | `sm.families.Gamma()` | `inverse_power` ($1/\mu$) | **`log`** (推荐) |
| **逆高斯** | `sm.families.InverseGaussian()` | `inverse_squared` ($1/\mu^2$) | `log` |

## 6. 如何从结果中提取关键指标？

Python

```python
print("AIC:", results.aic)
print("Deviance:", results.deviance)       # 残差偏差
print("Null Deviance:", results.null_deviance) # 空偏差
print("P-values:", results.pvalues)
```