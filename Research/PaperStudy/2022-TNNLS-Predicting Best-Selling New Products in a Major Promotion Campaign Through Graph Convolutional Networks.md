---
tags: []
parent: ""
collections:
    - 智慧社区
version: 0
libraryID: 1
itemKey: BW86D5QM

---
# 2022-TNNLS-Predicting Best-Selling New Products in a Major Promotion Campaign Through Graph Convolutional Networks

> ⭐⭐ 购买量预测 热销预测 多任务学习

## 问题定义

给定在购物节前上架的新产品及其历史数据，预测其在购物节的销量

![\<img alt="" data-attachment-key="27AGFHT3" width="456" height="193" src="attachments/27AGFHT3.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/27AGFHT3.png)

## 主要挑战【场景挑战】

*   新上架的产品缺少足量的历史数据，容易产生数据规律学习不准确或过拟合的现象导致预测不准

## 场景说明

*   购物节的销量预测，购物节的销量会表现为暴涨并且难以预测新产品的销量或者那些产品会热销

## 关键理念

*   通过5种产品之间的关系（购买习惯关联、搜索关联、商店品牌关联、描述关联、子分类关联）将新旧产品相联系，从而使得新产品能够利用旧产品的知识进行销量预测

## 关键技术

*   **Multirelation Graph Convolutional**

    为了提高计算效率减小计算复杂度，文中提出了另一种图卷积的方法，其中$A_s$为商品邻接矩阵，$S_l$为商品特征，$\phi^K$ 指的是卷积操作，其中K是超参，$H_{l, p}$其中$l$指第$l$层卷积，$p$指的是不同的卷积核将多维特征融合为了一维。

    ![\<img alt="" data-attachment-key="U5ULBLKH" width="273" height="81" src="attachments/U5ULBLKH.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/U5ULBLKH.png)

*   **Multiobjective Optimization Problem**

    由于文中的预测任务实际可以分为两个子任务，销量预测以及热销预测，但是两个子任务如何一起进行学习成为一个问题，简单地将两个子任务的损失函数拼接在一起，如下式，可能会引起这两个不同尺度的损失函数其中一个被困在局部最优影响模型整体效果。

    ![\<img alt="" data-attachment-key="IRCQGTGD" width="199" height="32" src="attachments/IRCQGTGD.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/IRCQGTGD.png)

    所以作者将损失函数定义为了下式并进行迭代优化模型效果

    ![\<img alt="" data-attachment-key="UWX44N5V" width="290" height="46" src="attachments/UWX44N5V.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/UWX44N5V.png)

## 模型说明

![\<img alt="" data-attachment-key="2CKDAVLW" width="894" height="389" src="attachments/2CKDAVLW.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/2CKDAVLW.png)

## 工作评价

作者来自淘宝并且称本文的工作已经在双十一上应用过了，工作具体而言非常工程，大量的篇幅用于介绍数据介绍及其构造原理，在方法上抠的点比较细，一个是GCN的复杂度简化，另一个就是多目标优化问题的迭代。
