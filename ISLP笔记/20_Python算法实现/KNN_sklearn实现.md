---
tags:
  - Python
  - KNN
  - 数据预处理
  - sklearn
aliases:
  - KNeighborsClassifier
  - StandardScaler
---

# KNN 算法实现 (sklearn)

[[K最近邻法|KNN]] 是基于距离的算法，因此对数据的尺度极其敏感。**在做 KNN 之前，必须进行标准化！**

## 1. 数据标准化 (Standardization)

**核心逻辑**：KNN 是基于距离的。如果“工资”是 10000 级，“年龄”是 10 级，工资的波动会淹没年龄的差异。

### 核心代码流

```python
from sklearn.preprocessing import StandardScaler

# 1. 初始化 (准备尺子)
# with_mean=True: 中心化 (减均值)
# with_std=True: 缩放 (除标准差)
scaler = StandardScaler(with_mean=True, with_std=True)

# 2. 拟合 (量尺寸)
# 这一步计算了 feature_df 的均值和方差，存入 scaler 内部
scaler.fit(feature_df)

# 3. 转换 (做衣服)
# 应用公式：(x - mean) / std
X_std = scaler.transform(feature_df)
```

- **比喻**：
    - `fit`: 裁缝量你的身材（计算 $\mu, \sigma$）。
    - `transform`: 按照尺寸裁剪衣服（对数据进行变换）。
    - **切记**：做预测时，新数据（测试集）也要用**同一把尺子**（同一个 `scaler`）去 transform，千万不能重新 fit！

### 还原 DataFrame

`transform` 后返回的是纯 NumPy 数组（丢失了列名）。为了方便查看，可以重建 DataFrame：

```python
feature_std = pd.DataFrame(X_std, columns=feature_df.columns)
# 检查：标准化后，每列的标准差应该都接近 1
print(feature_std.std()) 
```

## 2. 训练与测试拆分

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X_std,          # 特征
    y,              # 标签
    test_size=0.2,  # 20% 做测试集
    random_state=0  # 锁定随机种子(复现关键)
)
```

## 3. KNN 模型训练

KNN 没有显式的“训练”过程（它是懒惰学习），`fit` 只是在存储数据。

```python
from sklearn.neighbors import KNeighborsClassifier

knn = KNeighborsClassifier(n_neighbors=1) # K=1, 最灵活，最易过拟合
knn.fit(X_train, y_train)
pred = knn.predict(X_test)
```

## 4. 选择最佳的 K (调参)

通过循环遍历不同的 K 值，寻找准确率最高的超参数。

```python
for K in range(1, 10):
    knn = KNeighborsClassifier(n_neighbors=K)
    knn.fit(X_train, y_train)
    pred = knn.predict(X_test)
    
    # 计算准确率
    accuracy = np.mean(pred == y_test)
    print(f"K={K}, Accuracy={accuracy:.2%}")
```

- **趋势**：
    - K 很小 (1) $\to$ 过拟合 (Overfitting)，决策边界破碎。
    - K 很大 (100) $\to$ 欠拟合 (Underfitting)，决策边界过于平滑，接近于“猜平均”。