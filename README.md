# ISLP-Obsidian-Notes: 统计学习导论 (Python版) 知识库

> 📚 基于 *An Introduction to Statistical Learning with Applications in Python (ISLP)* 构建的 Obsidian 个人知识库。

## 简介 (Introduction)

这是一个开源的统计学习笔记仓库，内容基于 **《统计学习导论》 (ISLP)** 一书。

与传统的线性笔记不同，本项目利用 **Obsidian** 的双向链接功能，将统计学概念、Python 代码实现以及数学原理构建成了一个网状的知识图谱。旨在帮助学习者不仅理解单个概念，更能看清知识点之间的逻辑联系。

## 🌟 项目特色 (Features)

* **原子化笔记**：将复杂的章节拆解为独立的知识点（如 `Lasso回归`、`偏差-方差权衡`），便于快速检索与复习。

![关系谱图展示](image/关系谱图展示.png#width=300px)

- **强大的名词索引**：我创建了一个非常详细的名词索引表格，用于快速查阅统计学相关名词

![名词索引表展示](image/名词索引表展示.png#width=300px)

* **知识关联**：通过双向链接（Wiki Links）串联起跨章节的概念。例如，在学习“正则化”时，可以一键跳转复习“线性回归”的基础假设。

* **Python 实现**：笔记中包含了基于 `pandas`, `scikit-learn`, `statsmodels` 的代码实践（持续更新中）。

* **数模实战导向**：包含数学建模竞赛（MCM/ICM）相关的核心词汇与应用场景整理。

## 📅 当前进度 (Current Progress)

目前已覆盖 ISLP 书籍的前半部分核心内容（第 1-6 章），正在持续更新后续章节。

- [x] **Chapter 2**: 统计学习概论 (涵盖 MSE, 偏差-方差权衡, KNN等)
- [x] **Chapter 3**: 线性回归 (简单/多元线性回归, 潜在问题, F统计量)
- [x] **Chapter 4**: 分类 (逻辑回归, LDA, QDA, 朴素贝叶斯)
- [x] **Chapter 5**: 重抽样方法 (交叉验证, 自助法)
- [x] **Chapter 6**: 线性模型选择与正则化 (子集选择, Lasso, Ridge, 降维方法)
- [ ] **Chapter 7+**: (非线性模型、树模型等待更新...)

## 📂 目录结构 (Structure)

本仓库是一个标准的 Obsidian Vault，建议使用 Obsidian 打开以获得最佳体验。

```text
ISLP-Obsidian-Vault
├── ISLP笔记/
│   ├── 02_统计学习概论/          # 核心概念与基础框架
│   ├── 03_线性回归/              # 回归模型细节与诊断
│   ├── 04_分类/                  # 各类分类器推导与对比
│   ├── 05_重抽样方法/            # CV与Bootstrap详解
│   ├── 06_线性模型选择与正则化/   # 维度规约与正则化方法
│   ├── 10_统计学习基础概念/       # 通用的统计学术语表
│   ├── 12_MCM核心词汇/           # 数学建模竞赛专用词汇
│   └── 99_Attachments/           # 图片与附件资源
├── 统计学导论/                   # 补充的基础统计学知识
└── PythonProject/               # 配套的 Python 代码脚本

该目录仅为示例目录，请以实际为准
````

## 🚀 如何使用 (Usage)

### 方式 1：直接在 GitHub 阅读

你可以直接点击文件夹浏览 `.md` 文件，GitHub 支持渲染 Markdown 公式与代码。

### 方式 2：作为 Obsidian 库打开（推荐）

为了体验完整的知识图谱效果：

1. **Clone** 本仓库到本地：

2. 下载并安装 [Obsidian](https://obsidian.md/)。

3. 在 Obsidian 中选择 "打开本地仓库"，选中克隆下来的文件夹。


## 🤝 贡献与反馈

本人是一名南京工业的大一新生，这是一个本人在备战美赛的路上创立的个人的学习项目，后期会视情况持续更新。立个flag，至少要把全部章节更完，同时也欢迎大家指正错误或提交 PR 补充笔记！如果你觉得这个仓库对你有帮助，欢迎点亮右上角的 ⭐️ **Star**。

## 📜 参考资料

- Gareth James, Daniela Witten, Trevor Hastie, Robert Tibshirani. _An Introduction to Statistical Learning with Applications in Python_.

---

_Last Updated: 2026-01_
