---
tags: []
parent: ""
collections:
    - 智慧社区
version: 0
libraryID: 1
itemKey: 3T8W4JWS

---
# 2021-AAAI-Traffic Flow Forecasting with Spatial-Temporal Graph Diffusion Network

> ⭐ 交通流量预测 多尺度 图扩散模型

## 问题定义

将城市划分成I\*J个块，从历史流量数据中预测未来每个区域的入流和出流

## 主要挑战【技术挑战】

*   已有的工作在关注区域之间的联系时忽略了全局的上下文信息
*   已有的工作没能很好地编码交通模式中的时序依赖以及多尺度信息

## 场景描述

## 关键理念

## 关键技术

*   **时序层次信息建模（Temporal Hierarchy Modeling）**：将数据在时间轴上按Hour\Day\Week进行整理，形成在时序上不同尺度的数据表示，在每个尺度上分别使用Self-Attention机制学习数据特征。

    ![\<img alt="" data-attachment-key="NT2HH4AC" width="442" height="359" src="attachments/NT2HH4AC.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/NT2HH4AC.png)

*   **全局上下文交通依赖学习（Traffic Dependency Learning with Global Context）**:使用多头注意力机制来学习不同区域间的消息传递机制，其中$h \in \{1, \dots, H\}$，$Y^P$ 是从Self-Attention学习得到的每个区域的表征，$W^P$ 为学习参数

    ![\<img alt="" data-attachment-key="KKCCM4VI" width="476" height="120" src="attachments/KKCCM4VI.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/KKCCM4VI.png)

    其中$w_{(i, j);(i’,j’)}^h$  的定义方式如下,其中$\tilde{y^p_{i, j}}= y^p_{i, j} \cdot W^p$

    ![\<img alt="" data-attachment-key="4XRGU83A" width="544" height="89" src="attachments/4XRGU83A.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/4XRGU83A.png)

    之后再通过下式将其聚合为区域的特征信息：

    ![\<img alt="" data-attachment-key="BZL9E5W6" width="374" height="93" src="attachments/BZL9E5W6.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/BZL9E5W6.png)

    最后通过下式进行高阶信息传递：

    ![\<img alt="" data-attachment-key="CA666HKF" width="552" height="79" src="attachments/CA666HKF.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/CA666HKF.png)

    每个区域的最终全局表征为：

    ![\<img alt="" data-attachment-key="V7DLPKEQ" width="265" height="39" src="attachments/V7DLPKEQ.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/V7DLPKEQ.png)

*   ### **<span style="background-color: rgb(255, 255, 255)">Region-wise Relation Learning with Graph Diffusion Paradigm</span>**

    这部分利用图扩散网络将图的空间关系加入到模型当中，其中k表示扩散阶数，A 根据顶点距离计算出的加权邻接矩阵，其中A是根据顶点间距离计算出的加权邻接矩阵，$D_o = A \cdot I$表示出度对角矩阵

    ![\<img alt="" data-attachment-key="74QRLTDS" width="452" height="76" src="attachments/74QRLTDS.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/74QRLTDS.png)

*   最后便是将多个尺度的交通模式进行融合：

    ![\<img alt="" data-attachment-key="IWG4CDEV" width="376" height="40" src="attachments/IWG4CDEV.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/IWG4CDEV.png)

## 模型介绍

模型比较复杂，但是模块间的作用写的比较分明，第一步是先将数据在多个时序尺度下进行表示并学习每个区域的交通模式在不同尺度下的特征，第二部就通过多头注意力机制学习全局及局部的交通模式，第三步考虑区域间的空间关系，使用图扩散网络将空间关系建模到网络中，最后就是多个尺度的信息融合，最后在经过一层和external features的结合，学习后得到最终的输出。

![\<img alt="" data-attachment-key="CMBYPE9P" width="1170" height="566" src="attachments/CMBYPE9P.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/CMBYPE9P.png)

## 工作评价

我不知道如何评价这篇工作，它太花哨了。对应其提出的insight，在学习时序多尺度信息上的方法并无什么创新，在学习全局交通模式的信息时似乎“全局”信息的学习并不够，因为在第二个模块中使用的也都是有关联的区域信息来进行信息聚合的，第三个模块使用扩散模型但说是“全局”也有些牵强。
