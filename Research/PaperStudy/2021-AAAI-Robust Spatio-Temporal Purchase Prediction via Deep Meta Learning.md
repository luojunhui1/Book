---
tags: []
parent: ""
collections:
    - 智慧社区
version: 0
libraryID: 1
itemKey: 3WSX524B

---
2021-AAAI-Robust Spatio-Temporal Purchase Prediction via Deep Meta Learning

# 基于深度元学习的时空购买预测

*   问题定义：从历史的购物数据、购物车数据以及其他时空数据，预测未来每个品类的购买量。

*   场景描述：

    ![\<img alt="" data-attachment-key="NMACCVTD" width="652" height="387" src="attachments/NMACCVTD.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/NMACCVTD.png)

*   主要挑战：

    *   单个城市的数据稀疏 => 如何从不同的城市和日期类型中学习通用的知识
    *   如和捕获用户购买模式的完整时空特征，既能预测一般情况，又能预测特别情况

*   关键理念：

    *   将购买预测任务细化，根据地区和日期类别分为不同的子任务【2】
    *   使用共享的隐藏统计结构【1】

*   关键技术：

    *   将购买预测任务细化，根据地区和日期类别分为不同的子任务，使用元学习学习任务参数分布，捕获购买预测任务的统计结构信息【1，2】
    *   时序网络建模（LSTM、AR、Skip-LSTM）【2】

*   模型说明：

    ![\<img alt="" data-attachment-key="HVRSIFDV" width="671" height="519" src="attachments/HVRSIFDV.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/HVRSIFDV.png)
