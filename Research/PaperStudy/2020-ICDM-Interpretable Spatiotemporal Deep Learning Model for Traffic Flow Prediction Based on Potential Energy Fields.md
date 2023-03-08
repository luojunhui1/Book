---
tags: [交通流量预测, GRN, GAT]
parent: ""
collections:
    - 未分类
version: 5471
libraryID: 1
itemKey: BM4XJCLE
---
# 2020-ICDM-Interpretable Spatiotemporal Deep Learning Model for Traffic Flow Prediction Based on Potential Energy Fields

> ⭐⭐ GAT GRU 交通流量预测

## 问题定义

给定空间网格以及每个网格间流量的历史数据，预测未来相邻网格间的流量值。

![\<img alt="" data-attachment-key="S3AMGP77" width="459" height="108" src="attachments/S3AMGP77.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/S3AMGP77.png)

## 主要挑战

文章面对的不是场景挑战，而是认为已有的方法要么使用统计方法效果不好，要么使用黑箱模型不可解释，而关于区域间流量的物理意义上的学习却比较少

## 关键理念

*   将区域间的交通流量类比于重力场下的水流，但是水流间没有循环的情况，但是人群流量却可能出现循环的情况，这种循环不符合势能场的模型
*   将区域进一步划分成更小的社区单位，最大化社区间的人群流动而尽量减小社区间的流量，在语义上使得划分出来的社区单位更加具有内在的完整性，这样使得模型的建模更加势能场的模型（用不同节点间的势能场来建模社区和社区之间的人群流动要比区域和区域间的人群流动在直观上更为合理）

## 关键技术

*   流量图分解，对应关键理念【1】，将流量图分解为四个正交的多重树，在保证建模对真实场景表达的完整性的同时，保持了对关键理念“势能场模型类比”的遵循
*   时空建模（GRU+GAT）

## 模型说明

![\<img alt="" data-attachment-key="WFSK86GF" width="722" height="433" src="attachments/WFSK86GF.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/WFSK86GF.png)

## 工作评价

在创造性上，使用了物理上的一个理论类比交通流量预测场景来进行建模，在效果上其实没有特别出彩的地方，实验比较的模型也都比较老。
