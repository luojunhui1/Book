---
tags: []
parent: ""
collections:
    - 智慧社区
version: 0
libraryID: 1
itemKey: GH6NL2PB

---
# 2021-KDD-Talent Demand Forecasting with Attentive Neural Sequential Model

> ⭐ 岗位需求预测 Attention 注意力机制 MF 矩阵分解

## 问题定义

从市场中公司的的历史招聘信息中，预测未来每个公司对特定岗位的招聘需求趋势

## 主要挑战

*   数据稀疏，一个公司对于单个岗位的需求比较少
*   时空依赖复杂

## 场景描述

## 关键理念

## 关键技术

*   数据增强&数据处理【1】：

    *   将原始数据按不同的视角（岗位视角、公司视角）及层次（公司-岗位层次、公司聚合数据层次、岗位聚合数据层次、整体层次）进行处理，得到多个单变量的序列
    *   为了捕获趋势变化，使用一阶差分处理这些单变量的序列，并使用SAX算法将其从数值序列转换成趋势类别序列（less->more）
    *   原有的单变量序列不利于神经网络学习特征，将每个点之前若干个数据加入到当前节点中，提高每个数据元的维度和内涵信息量
    *   使用Embeddding从公司的属性信息中捕获不同公司的特征和关联，使用MF用于捕获在公司-岗位矩阵中蕴含的公司及岗位的内涵特征同时解决数据稀疏的问题

*   网络模型【2】：

    *   使用了MIR（Mixed Input Attention）用于捕获不同层次的关联信息，其中Attention的权重也由多个层次的信息+统计信息（从公司信息映射得到的Embeddding|从MF得到的公司Feature|岗位）学习得到
    *   使用了RTA（Relation Temporal Attention）来捕获时序上公司及岗位之间的关联

## 模型说明

![\<img alt="" data-attachment-key="PQBZX4LS" width="556" height="481" src="attachments/PQBZX4LS.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/PQBZX4LS.png)

![\<img alt="" data-attachment-key="G6XDTITG" width="592" height="708" src="attachments/G6XDTITG.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/G6XDTITG.png)

![\<img alt="" data-attachment-key="XQUUFXAQ" width="920" height="590" src="attachments/XQUUFXAQ.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/XQUUFXAQ.png)

## 工作评价

这篇工作偏向工程方向，在数据处理方面花费了很大的篇幅，包括：

1.  通过将前T个时序的特征加入到当前时序的特征内，完成特征维度增广
2.  使用SAX将数据特征离散化，将预测回归问题转化为分类问题
3.  使用MF方法解决矩阵稀疏的问题

在模型的设计和实验上也做得非常扎实。

但在具体的模型效果上而言MIR于RTA两者相加对于模型整体性能的提升其实是并不明显的，那一点微弱的提升甚至可能是单纯的模型的表达能力提升所带来的。

问题：QKV的语义以及为什么是QV与J\_emb结合？
