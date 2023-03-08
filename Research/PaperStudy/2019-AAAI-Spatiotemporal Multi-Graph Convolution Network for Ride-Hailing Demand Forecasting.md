---
tags: []
parent: ""
collections:
    - 智慧社区
version: 0
libraryID: 1
itemKey: 3X2SZXMR

---
# 2019-IJCAI-Spatiotemporal Multi-Graph Convolution Network for Ride-Hailing Demand Forecasting

> ⭐⭐ 打车需求预测 多步预测 多视图 多图网络卷积 RNN

此工作从三个角度构建不同区域间的联系，分别是通过网格化建设的相邻性关系、通过POI特征构建的功能相似性关系，通过交通网构建的连通性关系。在学习打车的时空特征的方法选择上。使用类似于CV中的POOL方法将区域维度压缩至一维来学习区域中不同特征的权重，使用创新的多层图卷积来学习区域在不同联系视角下的特征并通过特征聚合方法（取和、平均、取最大值等）将其组合至一起得到当前时刻每个区域的一维特征表示，随后通过两层神经网络，以及每个区域共享的RNN层得到最后的预测输出。

这篇工作中目前看来在多层图卷积结果的组合方法上略显粗糙，仅是简单的相加了。两个池化（特征聚合）操作能否通过神经网络的降维方法实现以提高表现？

![image-20230306184304159](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/image-20230306184304159.png)
