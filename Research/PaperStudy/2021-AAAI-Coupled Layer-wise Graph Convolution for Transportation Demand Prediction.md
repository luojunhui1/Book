---
tags: []
parent: ""
collections:
    - 智慧社区
version: 0
libraryID: 1
itemKey: QBZXSL6I

---
# 2021-AAAI-Coupled Layer-wise Graph Convolution for Transportation Demand Prediction

> ⭐⭐ 多步预测 出行需求预测 GRU Seq2Seq SVD

该工作最大的亮点在于它的insight：

不同区域间的关联是多层次的，现有的工作缺少了针对不同层次区域联系对时空预测的影响的研究。

针对此作者提出了一个图到图之间的学习多层次网络特征的模型，并使用注意力机制将不同层次的输出结果聚合，之后再使用Seq2Seq结构数据学习时间维度上的关联。

![\<img alt="" data-attachment-key="AB22STHP" width="400" height="212" src="attachments/AB22STHP.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/AB22STHP.png)

*   问题定义：从P个历史数据中预测未来Q个时段内的出行需求

*   场景描述：

*   主要挑战：

    *   大多数图卷积网络中的邻接矩阵是启发式构造的（距离、连通性），不能反映真实的空间关系
    *   已有的预测方法忽略了出行需求的层次性
    *   由于学习过程中输入信号的衰退，难以堆叠图卷积层以学习多层特征
    *   不同层次特征的权重应当动态变化

*   关键理念：

*   关键技术：

    *   数据驱动的邻接矩阵初始化构造方法【1】
    *   原创的图卷积结构CGC【2，3】
    *   基于层次的耦合机制【4】

*   模型说明：

    ![\<img alt="" data-attachment-key="4NU9SBEI" width="400" height="213" src="attachments/4NU9SBEI.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/4NU9SBEI.png)
