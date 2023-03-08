---
tags: []
parent: ""
collections:
    - 智慧社区
version: 5473
libraryID: 1
itemKey: I9UBXA8N

---
# 2019-AAAI-STG2Seq: Spatial-Temporal Graph to Sequence Model for Multi-step Passenger Demand Forecasting

> ⭐⭐ GCN 多步预测

论文的贡献主要有两点，第一点是在学习数据时序特征的方法选择上，没有选择传统的基于RNN及其变种的方法，作者认为这样的方法主要有两点缺陷

*   存在大量的循环操作，造成了信息的冗余浪费，实际是使用冗余的数据会造成资源浪费和性能损失；
*   将RNN用于多步预测的任务中，RNN的预测偏差会在后续步的预测结果中累计增加；

所以，作者使用多层次GGCM堆叠的学习方法来学习时序特征，不失为一种创新；

第二点贡献有些牵强，只是在输出前加了两层隐藏层就认为这两层隐藏层完成了注意力机制，这实际是一种降低维度的方法，比较常见，不能算作贡献。

![image-20230306184601841](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/image-20230306184601841.png)

![image-20230306184610159](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/image-20230306184610159.png)

![image-20230306184622919](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/image-20230306184622919.png)
