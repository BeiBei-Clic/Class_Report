---
date: "2026-04-22"
paper_id: "ICML-2025-SoftShape"
title: "Learning Soft Sparse Shapes for Efficient Time-Series Classification"
authors: "Zhen Liu, Yicheng Luo, Boyuan Li, Emadeldeen Eldele, Min Wu, Qianli Ma"
domain: "模式识别"
tags:
  - 论文笔记
  - 时间序列分类
  - Shapelet
  - 混合专家模型
  - 软稀疏化
  - 可解释性
  - ICML-2025
quality_score: "8.2/10"
created: "2026-04-22"
updated: "2026-04-22"
status: analyzed
---

# Learning Soft Sparse Shapes for Efficient Time-Series Classification

## 核心信息
- **论文ID**：ICML 2025
- **作者**：Zhen Liu, Yicheng Luo, Boyuan Li, Emadeldeen Eldele, Min Wu, Qianli Ma
- **机构**：华南理工大学 (SCUT) & 新加坡科技研究局 (A*STAR)
- **发布时间**：2025
- **会议**：ICML 2025 (42nd International Conference on Machine Learning, Vancouver, Canada, PMLR 267)
- **代码**：[GitHub](https://github.com/qianlima-lab/SoftShape)
- **通讯作者**：Qianli Ma (qianlima@scut.edu.cn), Min Wu (wumin@i2r.a-star.edu.sg)

## 摘要翻译

### 英文摘要
Shapelets are discriminative subsequences (or shapes) with high interpretability in time series classification. Due to the time-intensive nature of shapelet discovery, existing shapelet-based methods mainly focus on selecting discriminative shapes while discarding others to achieve candidate subsequence sparsification. However, this approach may exclude beneficial shapes and overlook the varying contributions of shapelets to classification performance. To this end, we propose a Soft sparse Shapes (SoftShape) model for efficient time series classification. Our approach mainly introduces soft shape sparsification and soft shape learning blocks. The former transforms shapes into soft representations based on classification contribution scores, merging lower-scored ones into a single shape to retain and differentiate all subsequence information. The latter facilitates intra- and inter-shape temporal pattern learning, improving model efficiency by using sparsified soft shapes as inputs. Specifically, we employ a learnable router to activate a subset of class-specific expert networks for intra-shape pattern learning. Meanwhile, a shared expert network learns inter-shape patterns by converting sparsified shapes into sequences. Extensive experiments show that SoftShape outperforms state-of-the-art methods and produces interpretable results.

### 中文翻译
Shapelet（形状子序列）是时间序列分类中具有高可解释性的判别性子序列。由于 shapelet 发现过程计算量巨大，现有基于 shapelet 的方法主要关注通过选择判别性形状并丢弃其他形状来实现候选子序列稀疏化。然而，这种方法可能排除有益的形状，并忽略 shapelet 对分类性能的不同贡献。为此，我们提出了 Soft sparse Shapes (SoftShape) 模型，用于高效的时间序列分类。我们的方法主要引入了软形状稀疏化和软形状学习模块。前者基于分类贡献分数将形状转换为软表示，将低分形状合并为单个形状，从而保留和区分所有子序列信息。后者促进形状内部和形状之间的时间模式学习，通过使用稀疏化的软形状作为输入来提高模型效率。具体来说，我们采用可学习的路由器来激活类特定专家网络的子集，用于形状内部模式学习。同时，共享专家网络通过将稀疏化的形状转换为序列来学习形状之间的模式。大量实验表明，SoftShape 超越了最先进的方法，并产生了可解释的结果。

### 核心要点提炼
- **研究背景**：传统 shapelet 方法采用硬筛选策略丢弃子序列，导致信息损失且忽略各 shapelet 的差异化贡献
- **研究动机**：硬筛选方式可能排除有益形状，同时未能建模不同 shapelet 对分类的差异化贡献
- **核心方法**：软形状稀疏化（基于注意力分数加权融合）+ MoE 驱动的双模式学习（类内/类间模式）
- **主要结果**：在 128 个 UCR 数据集上平均准确率 0.9334，平均排名 2.72，在 53 个数据集上取得最佳
- **研究意义**：将硬 shapelet 选择范式转变为软稀疏化范式，在保持可解释性的同时显著提升分类性能

## 研究背景与动机

### 领域现状
时间序列分类（TSC）在人类活动识别、医疗诊断、智能交通等领域有广泛应用。深度学习方法在 TSC 上取得了显著成功，但其黑盒特性限制了在关键领域（如医疗）的采用。Shapelet 作为具有判别性的子序列，为可解释的 TSC 提供了有前景的方向。

### 现有方法的局限性
1. **硬筛选信息损失**：现有方法（Grabocka et al., 2014; Li et al., 2020）通过 shapelet 变换策略直接丢弃大量子序列，排除了可能有益于分类的形状
2. **忽略差异化贡献**：未能考虑不同 shapelet 对分类性能的差异化贡献
3. **计算效率低**：暴力搜索所有子序列的计算成本随样本数 N 和序列长度 T 急剧增长

### 研究动机
如图1所示，传统的硬 shapelet 选择（图1b）在第三、四个子序列上因类间差异极小而未能很好地捕获类别模式，同时遗漏了大量信息区域。而 SoftShape 的软判别区域（图1d）基于分类贡献分数为形状分配权重，有效保留和区分所有子序列信息。

![[extracted_img-000.png|400]]

> 图1a：Walking on Carpet 类别的时间序列样本（黄色线）

![[extracted_img-003.png|400]]

> 图1c：Walking on Cement 类别的时间序列样本（蓝色线）

![[extracted_img-005.jpg|400]]

> 图1b：硬 shapelet 选择（红色子序列为选中的 shapelet）

![[extracted_img-004.png|400]]

> 图1d：SoftShape 的软判别区域（红色区域，颜色越深判别力越强）

## 研究问题

### 核心研究问题
如何在不丢失有益子序列信息的前提下，高效地学习具有判别力的 shapelet 表示用于时间序列分类？

具体子问题：
1. 如何避免硬筛选导致的信息损失？
2. 如何建模不同 shapelet 对分类的差异化贡献？
3. 如何同时学习形状内部的局部模式和形状之间的全局依赖关系？

## 方法概述

### 核心思想
SoftShape 将传统的"硬 shapelet 选择"范式替换为"软形状稀疏化"：基于注意力分数对子序列进行加权，高分保留、低分加权融合为单个形状，既减少计算量又不丢失信息。然后通过 MoE 驱动的双模式学习（类内专家 + 共享专家）增强形状嵌入的判别力。

### 方法框架

#### 整体架构

![[extracted_img-018.jpg|800]]

> 图2：SoftShape 模型的整体架构。主要包括：(a) 软形状稀疏化模块，将形状转换为软表示并融合低分形状；(b) 软形状学习模块，使用 MoE 路由器激活类特定专家学习形状内模式，并利用共享专家学习形状间模式。

#### 模块1：形状嵌入层（Shape Embedding Layer）
- **功能**：将输入时间序列转换为多个等长子序列的嵌入表示
- **输入**：原始时间序列 $X_n = \{x_1, x_2, \ldots, x_T\}$
- **输出**：$M = \frac{T-m}{q} + 1$ 个重叠子序列嵌入 $\hat{S}_n^m$
- **处理流程**：
  1. 使用一维卷积神经网络（CNN）提取子序列嵌入
  2. 卷积核大小为 $m$，步长为 $q$
  3. 加入可学习的位置嵌入以捕获时间依赖关系
- **数学公式**：

$$\hat{S}_{n,p}^m = W_i S_{n,p}^m \mid p = 0, q, 2q, \ldots, T-m$$

其中 $W_i$ 是第 $i$ 个卷积核的权重，$\hat{S}_{n,p}^m \in \mathbb{R}^d$。

#### 模块2：软形状稀疏化（Soft Shape Sparsification）
- **功能**：基于分类贡献分数对形状嵌入进行加权，高分保留、低分融合
- **输入**：形状嵌入 $\hat{S}_n^m$
- **输出**：稀疏化的软形状嵌入 $S_e^n^m$
- **处理流程**：
  1. 使用门控注意力机制计算每个形状的分类贡献分数
  2. 按分数排名，前 $\eta$ 比例的形状保留并按分数缩放
  3. 低于前 $\eta$ 比例的形状加权融合为单个嵌入
- **关键公式**：

注意力分数计算：

$$\alpha(\hat{S}_{n,p}^m) = \sigma(W_2 \tanh(W_1 \hat{S}_{n,p}^m + b_1) + b_2)$$

高分形状的软表示：

$$S_{e,n,p}^m = \alpha(\hat{S}_{n,p}^m) \hat{S}_{n,p}^m$$

低分形状的加权融合：

$$S_{e,n,\text{fused}}^m = \sum_{p \in E} \alpha(\hat{S}_{n,p}^m) \hat{S}_{n,p}^m$$

其中 $E$ 为分数低于前 $\eta$ 比例的索引集合。

#### 模块3：软形状学习块（Soft Shape Learning Block）
- **功能**：学习形状内部（intra-shape）和形状之间（inter-shape）的时间模式
- **输入**：稀疏化的软形状嵌入 $S_e^n^m$
- **输出**：增强判别力的形状嵌入

**子模块3a：形状内学习（Intra-Shape Learning）**
- 采用 MoE 路由器激活类特定专家
- 每个专家使用轻量级 MLP 学习类特定模式
- 路由器函数：

$$G(S_{e,n,p}^m) = \text{TOP}_k(\text{softmax}(W_t S_{e,n,p}^m))$$

- 专家函数：

$$h_e(\theta, S_{e,n,p}^m) = \hat{G}_e(S_{e,n,p}^m) \text{GeLU}(W_e S_{e,n,p}^m + b_e)$$

- 负载均衡损失：

$$L_{\text{load}}(S_{e,n,p}^m) = W_{\text{load}} \text{CV}(\text{Load}(S_{e,n,p}^m))^2$$

$$L_{\text{imp}}(S_{e,n,p}^m) = W_{\text{imp}} \text{CV}\left(\sum_{S_{e,n,p}^m \in S_e^n} G(S_{e,n,p}^m)\right)^2$$

**子模块3b：形状间学习（Inter-Shape Learning）**
- 使用共享专家（基于 Inception 模块的 CNN）学习全局时间模式
- 将稀疏化的形状视为序列，每个形状作为一个序列单元
- 变换定义：

$$Q_n^m = (S_e^n)^T, \quad Q_n^m \in \mathbb{R}^{B \times d \times \text{Num}}$$

- Inception 模块包含三个不同核大小的一维卷积层，沿第三维度滑动以学习多分辨率时间模式

#### 训练目标
总损失函数：

$$L_{\text{total}} = L_{\text{ce}} + \lambda(L_{\text{imp}} + L_{\text{load}})$$

其中交叉熵损失：

$$L_{\text{ce}}(X, Y) = -\frac{1}{B} \sum_{i=1}^B \sum_{j=1}^C \mathbb{1}\{y_i = j\} \log(\hat{Y}_i^j)$$

分类预测使用合取池化（conjunctive pooling）：

$$\hat{Y}_n = \frac{1}{\text{Num}} \sum_{i=0}^{\text{Num}} \alpha(O_n^m) \phi(O_{n,i}^m)$$

## 实验结果

### 实验目标
验证 SoftShape 在时间序列分类任务上的性能、效率和可解释性。

### 数据集
- **基准**：UCR 时间序列档案 128 个数据集
- **划分策略**：合并原始训练集和测试集，按 60%-20%-20% 划分为训练-验证-测试集
- **领域覆盖**：人类活动识别、医疗诊断、智能交通等

### 基线方法
共 19 种基线方法：

| 类别 | 方法 |
|------|------|
| DL-CNN | FCN, T-Loss, SelfTime, TS-TCC, TS2Vec, TimesNet, InceptionTime, ShapeConv, ModernTCN, TSLANet |
| DL-Trans | TST, PatchTST, Medformer |
| DL-FM | GPT4TS, UniTS |
| Non-DL | RDST, MR-H |

### 主要结果

#### 主实验结果

| 方法 | 平均准确率 | 平均排名 | Win数 | P-value |
|------|-----------|----------|-------|---------|
| TSLANet | 0.9205 | 3.68 | 31 | 1.06E-03 |
| InceptionTime | 0.9181 | 4.05 | 29 | 7.39E-06 |
| TS2Vec | 0.8691 | 8.43 | 9 | 1.69E-15 |
| MR-H | 0.8972 | 5.51 | 29 | 3.80E-07 |
| RDST | 0.8897 | 6.41 | 23 | 7.54E-10 |
| GPT4TS | 0.8593 | 9.34 | 6 | 7.89E-16 |
| PatchTST | 0.8265 | 9.56 | 12 | 1.27E-15 |
| **SoftShape (Ours)** | **0.9334** | **2.72** | **53** | **-** |

> 注：所有基线方法的 P-value 均 < 0.05，表明 SoftShape 的优势具有统计显著性。Wilcoxon 符号秩检验。

#### 结果分析
- SoftShape 在平均准确率（0.9334）和平均排名（2.72）上均为最优
- 在 53/128 个数据集上取得最佳准确率，远超第二名 TSLANet 的 31 个
- 与次优方法 TSLANet 相比，准确率提升 1.29 个百分点
- 所有对比的 P-value 均远小于 0.05，统计显著性极强

### 消融实验

| 变体 | 平均准确率 | 平均排名 | Win数 | P-value |
|------|-----------|----------|-------|---------|
| w/o Soft Sparse | 0.9123 | 3.04 | 29 | 4.69E-06 |
| w/o Intra | 0.9245 | 2.75 | 31 | 5.39E-04 |
| w/o Inter | 0.9022 | 3.74 | 19 | 1.81E-09 |
| w/o Intra & Inter | 0.8696 | 5.02 | 11 | 4.35E-16 |
| with Linear Shape | 0.9164 | 3.23 | 22 | 1.80E-09 |
| **SoftShape (Full)** | **0.9334** | **2.04** | **60** | **-** |

**消融分析**：
- **w/o Inter 影响最大**：去除形状间学习导致性能大幅下降（0.9022 vs 0.9334），说明全局时间模式对分类至关重要
- **w/o Soft Sparse 也有显著影响**：说明软稀疏化能有效保留有益形状信息
- **w/o Intra & Inter 最差**：证实双模式学习的必要性
- **with Linear Shape**：1D CNN 嵌入优于线性层，验证了 CNN 架构对形状嵌入学习的优势

### 稀疏比分析

| 稀疏比 (1-η) | 平均准确率 | 平均排名 | Win数 | P-value |
|--------------|-----------|----------|-------|---------|
| 0% | 0.9461 | 2.44 | 4 | 2.97E-01 |
| 10% | 0.9469 | 2.39 | 7 | 2.66E-01 |
| 30% | 0.9448 | 2.78 | 5 | 3.46E-01 |
| 50% | 0.9453 | 2.61 | 6 | 9.37E-03 |
| 70% | 0.9323 | 3.89 | 5 | 4.02E-04 |
| 90% | 0.9261 | 4.50 | 2 | -- |

- 稀疏比 ≤ 50% 时性能无显著下降（P > 0.05）
- 稀疏比 > 50% 时性能显著下降，过度稀疏会损失有用的形状间全局时间模式
- 主实验采用 η = 50% 以平衡计算效率和性能

### 效率分析

![[extracted_img-019.png|800]]

> 图3：运行时间分析。SoftShape 在长序列场景下的训练速度显著优于所有深度学习基线，且与 MR-H（非深度学习方法）的差距较小。

### 可视化分析

![[extracted_img-020.png|600]]

> 图4：Trace 数据集上的 MIL 可视化。红色越深表示对分类越有利，蓝色表示不太相关。SoftShape 能有效识别最具判别力的子序列区域。

![[extracted_img-026.png|800]]

> 图5：CBF 数据集上的 t-SNE 可视化。(a) 输入形状嵌入，(b) 形状内嵌入（类内聚类），(c) 形状间嵌入（类间分离），(d) 最终输出（结合两种模式的平衡表示）。

## 深度分析

### 研究价值评估

#### 理论贡献
- **贡献1**：提出了从"硬 shapelet 选择"到"软形状稀疏化"的范式转换
  - 创新点：通过注意力分数加权融合替代硬筛选，保留所有子序列信息
  - 学术价值：为 shapelet-based TSC 方法提供了新的理论框架
  - 影响范围：时间序列分类、可解释机器学习

- **贡献2**：MoE 驱动的双模式形状学习
  - 创新点：将类特定专家用于形状内局部模式，共享专家用于形状间全局模式
  - 学术价值：首次将 MoE 路由机制与 shapelet 学习有机结合
  - 影响范围：时间序列表示学习、混合专家模型应用

#### 实际应用价值
- **应用场景1**：医疗诊断中的时间序列分类
  - 适用性：模型的可解释性有助于医生理解决策依据
  - 优势：注意力分数可视化直接展示判别性区域

- **应用场景2**：工业设备异常检测
  - 适用性：软形状稀疏化对长序列高效
  - 优势：计算效率优于 Transformer-based 方法

### 方法优势详解

#### 优势1：信息保留完整性
- **描述**：软稀疏化通过加权融合保留所有子序列信息，避免硬筛选的信息损失
- **技术基础**：门控注意力机制为每个形状分配贡献分数
- **实验验证**：消融实验中 w/o Soft Sparse 导致准确率从 0.9334 降至 0.9123

#### 优势2：计算效率
- **描述**：50% 稀疏比下性能无损，显著减少计算量
- **技术基础**：低分形状融合为单个嵌入，减少输入序列长度
- **实验验证**：长序列场景下训练时间优于所有深度学习基线

#### 优势3：可解释性
- **描述**：注意力分数直接反映子序列的判别贡献
- **技术基础**：MIL 可视化展示形状的判别区域
- **实验验证**：t-SNE 可视化验证了形状嵌入的类判别能力

### 局限性分析

#### 局限1：仅支持单变量时间序列
- **描述**：论文明确指出未考虑多变量之间的关系建模
- **影响**：限制了在多变量 TSC 任务上的直接应用
- **可能的解决方案**：扩展为多通道输入，引入变量间注意力机制

#### 局限2：短序列/小样本场景效率有限
- **描述**：在短序列和小样本场景中，稀疏化带来的效率提升有限
- **影响**：某些短序列任务上运行时间略高于 TSLANet
- **原因**：稀疏化后形状数量减少不显著

#### 局限3：Shape 长度超参数敏感性
- **描述**：需要设置 shape 长度 m，不同数据集可能需要不同的 m
- **影响**：增加超参数搜索成本
- **可能的解决方案**：引入自适应 shape 长度或多尺度并行策略

### 适用性与场景分析

#### 适用场景
- **场景1**：长序列时间序列分类
  - 适用原因：软稀疏化减少计算量，效率优势显著
  - 注意事项：稀疏比不宜超过 50%

- **场景2**：需要可解释性的关键应用
  - 适用原因：注意力分数可视化提供直观解释
  - 预期效果：可清晰展示判别性子序列

#### 不适用场景
- **场景1**：多变量时间序列分类
  - 不适用原因：模型仅支持单变量
  - 替代方案：使用 Medformer 或其他多变量方法

## 与相关论文对比

### 对比论文选择依据
选择同为 shapelet-based 和 patch-based 的时间序列分类方法进行对比。

### Shapeformer (Le et al., 2024) - Shapelet Transformer
- **核心方法**：使用 Transformer 学习 shapelet 表示
- **对比**：

| 对比维度 | Shapeformer | SoftShape |
|----------|-------------|-----------|
| Shapelet 选择 | 硬筛选 | 软稀疏化 |
| 学习架构 | Transformer | MoE + CNN |
| 计算复杂度 | 自注意力 $O(n^2)$ | 门控注意力 $O(n)$ |
| 信息保留 | 丢弃大量子序列 | 保留所有信息 |

### TSLANet (Eldele et al., 2024) - 轻量级时间序列注意力
- **核心方法**：基于 CNN 的 patch tokenization + 轻量级注意力
- **对比**：

| 对比维度 | TSLANet | SoftShape |
|----------|---------|-----------|
| 输入处理 | 使用全部 patch | 软稀疏化 |
| 可解释性 | 较弱 | shapelet 注意力分数 |
| 平均准确率 | 0.9205 | 0.9334 |
| 平均排名 | 3.68 | 2.72 |

### InceptionTime (Ismail Fawaz et al., 2020) - 经典深度 TSC
- **核心方法**：Inception 模块堆叠的深度 CNN
- **对比**：SoftShape 借鉴了 Inception 模块用于共享专家，但引入了软稀疏化和 MoE 机制，准确率从 0.9181 提升至 0.9334

## 技术路线定位

### 所属技术路线
本文属于**基于 Shapelet 的时间序列分类**技术路线，核心特点：
- 利用判别性子序列作为分类依据
- 追求可解释性与性能的平衡
- 从硬选择到软稀疏化的范式演进

### 技术路线发展历程
```
Ye & Keogh 2009     Grabocka et al. 2014    Le et al. 2024        本文 2025
(Shapelet提出)  →  (Learning Shapelets)  →  (Shapeformer)  →  (SoftShape)
   ↓                    ↓                       ↓                    ↓
暴力搜索          梯度学习shapelet        Transformer学习       软稀疏化+MoE
```

### 本文在技术路线中的位置
- **承上**：继承了 shapelet 变换策略的基本框架，借鉴了 InceptionTime 的 CNN 架构和 PatchTST 的 patch 思想
- **启下**：为多变量扩展、自适应 shape 长度等方向提供了基础
- **关键节点**：首次将硬 shapelet 选择转变为软稀疏化范式

## 未来工作建议

### 作者建议的未来工作
1. **多变量时间序列分类**：扩展到多变量 TSC，建模变量间关系
   - 可行性：中等，需要重新设计稀疏化和学习机制
   - 价值：大幅扩展应用范围

### 基于分析的未来方向
1. **自适应 Shape 长度**：当前需要预设 shape 长度 m，可引入自适应或可学习长度机制
2. **多尺度融合**：借鉴 Multi-Seq 实验结果，自动选择最优 shape 长度组合
3. **跨域迁移学习**：探索 shapelet 表示在不同时间序列域之间的可迁移性

## 我的综合评价

### 价值评分

#### 总体评分
**8.2/10** - 在 shapelet-based TSC 方向做出了扎实的贡献，范式转换有新意，实验全面充分

#### 分项评分

| 评分维度 | 分数 | 评分理由 |
|----------|------|----------|
| 创新性 | 8/10 | 软稀疏化范式有新意，MoE 与 shapelet 的结合较为巧妙 |
| 技术质量 | 8/10 | 方法论严谨，数学推导完整，设计合理 |
| 实验充分性 | 9/10 | 128 个 UCR 数据集 + 19 种基线 + 详细消融 + 可视化分析 |
| 写作质量 | 8/10 | 结构清晰，图表丰富，逻辑顺畅 |
| 实用性 | 8/10 | 代码开源，长序列场景高效，可解释性强 |

### 重点关注

#### 值得关注的技术点
- 门控注意力机制的线性复杂度设计（独立假设下的注意力）
- 低分形状加权融合为单个嵌入的稀疏化策略
- MoE 路由器用于类特定专家激活的思路
- 负载均衡和重要性损失的联合优化

#### 需要深入理解的部分
- 软稀疏化与硬筛选在不同数据特性下的差异
- MoE 专家数量 k 的选择对性能的影响机制

## 我的笔记

%% 用户可以在这里添加个人阅读笔记 %%

## 相关论文

### 直接相关
- [[Shapeformer]] - Shapelet Transformer，同属 shapelet-based 方法，SoftShape 的直接对比对象
- [[InceptionTime]] - SoftShape 的共享专家使用了 Inception 模块

### 背景相关
- [[PatchTST]] - patch tokenization 思想的来源
- [[TSLANet]] - 近期基于 CNN 的 patch-based TSC 方法

### 后续工作
- 多变量时间序列版本的 SoftShape（未来工作方向）

## 外部资源
- 代码仓库：https://github.com/qianlima-lab/SoftShape
- UCR 时间序列分类档案：https://www.cs.ucr.edu/~eamonn/time_series_data_2018/

> [!tip] 关键启示
> 软稀疏化（加权融合）替代硬筛选（丢弃）是一种通用的信息保留策略，可推广到其他需要子序列选择的场景。

> [!warning] 注意事项
> - 模型仅支持单变量时间序列
> - 短序列场景下效率提升有限
> - Shape 长度 m 需要作为超参数设置

> [!success] 推荐指数
> ⭐⭐⭐⭐ 推荐阅读。SoftShape 在 shapelet-based TSC 方向做出了实质性贡献，软稀疏化思想有启发性，实验非常充分。作为华南理工大学的工作，对本组研究方向有参考价值。
