---
tags: []
parent: ""
collections:
    - 智慧社区
version: 5489
libraryID: 1
itemKey: 8FIUYV3D

---
# 2020-Learning from Multiple Cities: A Meta-Learning Approach for Spatial-Temporal Prediction

> ⭐⭐ 聚类 流量预测 迁移学习 元学习 CNN LSTM

该工作属于迁移学习领域，解决的是目标城市数据稀少的问题，从少量的数据中难以提取出时空模式，但是可以通过学习其他城市中的时空模式并将其迁移到目标城市中减缓该问题。

在抽取业务数据的时空模式上，使用了传统的CNN+LSTM的方法。但在迁移模式上有两点主要亮点：

*   使用ST-Mem来存储不同的可训练的时空模式，将时空数据特征线性映射到模式空间中，得到该时空数据与各项时空模式间的相似度，该相似向量与原始的时空数据concat在一起后进行最后的预测工作；
*   其中为了更新不同的时空模式，首先将各个时空数据进行聚类，并通过最小化时空数据映射到模式空间中的结果与聚类结果之间的偏差，这样就使得时空模式可更新并且赋予了其时空模式的语义；

![\<img alt="" data-attachment-key="CSIYEFZP" width="1189" height="443" src="attachments/CSIYEFZP.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/CSIYEFZP.png)

![\<img alt="" data-attachment-key="6UGKL2B3" width="706" height="575" src="attachments/6UGKL2B3.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/6UGKL2B3.png)
