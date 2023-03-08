---
tags: []
parent: ""
collections:
    - 智慧社区
version: 0
libraryID: 1
itemKey: 7NHRW8P9

---
# 2021-AAAI-Community-Aware Multi-Task Transportation Demand Prediction

> ⭐⭐ 多步预测 出行需求预测 GNN GRU 多任务学习

# 群体×区域多任务多步出行需求预测

该工作不仅考虑了常见的时空关联来进行需求预测，还将群体作为需求预测的一个单元，考虑了群体与区域间的关联，这是本篇工作的一大亮点。为了捕获这种关联，首先在数据表示上采用了区域×群体×时间这种表达方式，而不是常见的区域×区域×时间的构造方式。随后在群体、区域两个不同的视角下使用GNN捕获区域和群体间的潜在联系，区域和群体各自的边分别通过连通性和皮尔逊系数计算得到的相似度来构造，联系的学习方式是将两个两个节点的特征值concat在一起后与参数相乘在乘以对应的相似度，其中这个相似度通过注意力机制来归一化度量;在GNN模块后紧跟着的就是GRU模块用于学习时间上的联系。

![\<img alt="" data-attachment-key="GPAHD8T6" width="400" height="377" src="attachments/GPAHD8T6.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/GPAHD8T6.png)

该工作中将每个群体的需求预测作为一个单独的任务，同时认为不同区域中的群体也可能具有相当的关联性，这种关联性是区域、群体四种关系的交叉作用，所以作者将区域、群体间的特征两两聚合在一起后进行学习，最后的输出结果来自多个任务输出的算术平均，其中有个Mask Vector的机制来度量当前输入与任务簇之间关联度的机制尚不明确。

![\<img alt="" data-attachment-key="SUXUS2Y2" width="400" height="240" src="attachments/SUXUS2Y2.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/SUXUS2Y2.png)

## 问题定义

从T个历史数据中预测未来t个时间段内的区域中每种群体的流入流出量。

![\<img alt="" data-attachment-key="559SXWCQ" width="333" height="36" src="attachments/559SXWCQ.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/559SXWCQ.png)

## 主要挑战

*   如何有效建模出行需求与时空条件间的复杂依赖【区域×区域|区域×时间】
*   如何捕获不同类群体人群间多样化的出行需求以及它们之间的联系【群体人群×群体人群】
*   如何构造模型以高效学习区域与群体人群之间的关系【区域×群体人群】

## 关键理念

*   将群体定义为具有某类相同属性的人群集合，如根据性别属性、年龄属性、职业属性等划分不同的群体【基础概念】
*   基于GNN与RNN的时空网络模型【1, 2】
*   引入软聚类多任务学习模块，将每种群体人群在不同区域的出行预测视作单独的任务【2, 3】

### 场景描述

*   两张图：区域图|群体图，每个时段内都会计算图内各个节点间的相似度，因此会分别在区域、群体两个视图上形成两个图序列

    ![\<img alt="" data-attachment-key="SAWBGT5K" width="1165" height="351" src="attachments/SAWBGT5K.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/SAWBGT5K.png)

### 关键技术

*   GNN+Attention

    ![\<img alt="" data-attachment-key="LUR5AATF" width="336" height="74" src="attachments/LUR5AATF.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/LUR5AATF.png)

    ![\<img alt="" data-attachment-key="TZ63687A" width="448" height="88" src="attachments/TZ63687A.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/TZ63687A.png)

*   GRU

    ![\<img alt="" data-attachment-key="NSCVYP9U" width="242" height="51" src="attachments/NSCVYP9U.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/NSCVYP9U.png)

*   Adaptively Clustered Multi-Task Learning（自适应软聚类多任务学习）

    *   ### **<span style="background-color: rgb(255, 255, 255)">软聚类多任务学习</span>**

        ![\<img alt="" data-attachment-key="WPNCT9TV" width="553" height="50" src="attachments/WPNCT9TV.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/WPNCT9TV.png)

    *   ### **<span style="background-color: rgb(255, 255, 255)">Mutually supervised adaptive task grouping（相互监督的自适应任务分组）</span>**

        ![\<img alt="" data-attachment-key="SAXT4KNL" width="400" height="374" src="attachments/SAXT4KNL.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/SAXT4KNL.png)

### 模型说明

![\<img alt="" data-attachment-key="6BLKTL7G" width="1031" height="458" src="attachments/6BLKTL7G.png" ztype="zimage">](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture//Windows/6BLKTL7G.png)
