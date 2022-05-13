RESEARCH DAILY

**build for recording research**

## 2021

## 2021-12

### **2021年12月 2日  1:50:40**

关于SVM的一些知识，具体是关于使用拉格朗日乘子和KKT条件来寻找在有限制条件的情况下的最优化问题问题。除此之外，实现了使用KNN分类Iris数据集，预计在明天完成SVM的原理梳理以及代码实现。


---

### **2021年12月 3日  1:07:18**

今天再次学习了以下SVM方面的知识，对拉格朗日乘子，以及使用KKT条件来求解带有不等式约束的最优化问题有了更深的了解，但是对于SMO方法的实现部分还未来得及全部了解，但对SVM的整体轮廓有了大致的了解。

从二位分类问题入手SVM，对于二维分类问题需要在样本空间中划分超平面将样本分为两类，其中超平面的数学表示就为：$f(x) = W^Tx + b$。这个划分的**目标在于找到使间隔最大的支持向量和分割平面**。以下是样本x到超平面的“间隔”数学表示：

![[公式]](https://www.zhihu.com/equation?tex=%5Cgamma%3D%5Cfrac%7B%7C%5Cboldsymbol+w%5ET%5Cboldsymbol+x%2Bb%7C%7D%7B%7C%7C%5Cboldsymbol+w%7C%7C%7D)

而支持向量指的是距离分割线最近的样本。下图中更形象地说明了这两个概念：

![](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture/Windows/20211204014935.png)

为了方便计算，对于支持向量到超平面的间隔我们通过缩放$w、b$参数使得$|W^Tx + b| = 1$。如此，根据支持向量的定义，对于样本空间中所有的样本而言，有下式成立：

![[公式]](https://www.zhihu.com/equation?tex=%5Cfrac%7B%7C%5Cboldsymbol+w%5ET%5Cboldsymbol+x_i%2Bb%7C%7D%7B%7C%7C%5Cboldsymbol+w%7C%7C%7D+%5Cgeq+%5Cfrac%7B1%7D%7B%7C%7C%5Cboldsymbol+w%7C%7C%7D%2C+i%3D1%2C2%2C...m)

将等式两边都乘上$\Vert w\Vert$,对于样本中的两类，我们又有如下式子成立：

![[公式]](https://www.zhihu.com/equation?tex=+%5Cbegin%7Bcases%7D++%5Cboldsymbol+w%5ET+%5Cboldsymbol+x_i+%2B+b+%5Cgeq%2B1%2C+y%3D%2B1+%5C%5C+%5Cboldsymbol+w%5ET+%5Cboldsymbol+x_i+%2B+b+%5Cleq+-1%2C+y%3D-1++%5Cend%7Bcases%7D+)

将以上两个式子合并，得到：

 ![[公式]](https://www.zhihu.com/equation?tex=y_if%28x_i%29%3Dy_i%28%5Cboldsymbol+w%5ET+%5Cboldsymbol+x_i+%2B+b%29+%5Cgeq+1)

会议我们最初的目标：“找到使间隔最大的支持向量和分割平面”，即求解下式：

![[公式]](https://www.zhihu.com/equation?tex=%5Cmax_%7B%5Cboldsymbol+w%2Cb%7D+%5Cfrac%7B2%7D%7B%7C%7C%5Cboldsymbol+w%7C%7C%7D+)

为方便计算，将其形式转换为我们最终的目标优化函数：

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Baligned%7D++%26+%5Cmin_%7B%5Cboldsymbol+w%2Cb%7D+%5Cfrac%7B1%7D%7B2%7D%7C%7C%5Cboldsymbol+w%7C%7C%5E2+%5C%5C%5B2ex%5D++%26+%5Ctext%7Bs.t.+%7D+y_i%28%5Cboldsymbol+w%5ET+%5Cboldsymbol+x_i+%2B+b%29+%5Cgeq+1%2C+i%3D1%2C2%2C...m+%5Cend%7Baligned%7D+)

这就是一个有不等式约束的最优化问题，需要使用到拉格朗日乘子及KKT条件。

对于拉格朗日乘子的作用和原理我已经了解；为了方便求解引入了对偶函数，为了对偶函数起到作用又引入了KKT条件，对于对偶函数的左右以及原理我也已经了解，但是对于KKT条件是如何发挥作用的还不是特别清楚。

参考：

[简易解说拉格朗日对偶（Lagrange duality） - 90Zeng - 博客园 (cnblogs.com)](https://www.cnblogs.com/90zeng/p/Lagrange_duality.html#!comments)

[(36条消息) Python3《机器学习实战》学习笔记（八）：支持向量机原理篇之手撕线性SVM_Jack-Cui-CSDN博客](https://blog.csdn.net/c406495762/article/details/78072313#2-smo算法)

[(5 封私信 / 80 条消息) SVM代码实现 - 搜索结果 - 知乎 (zhihu.com)](https://www.zhihu.com/search?type=content&q=SVM代码实现)

为什么支持向量机要用拉格朗日对偶算法来解最大化间隔问题？ - 马同学的回答 - 知乎 https://www.zhihu.com/question/36694952/answer/1616415387

[马同学 (matongxue.com)](https://www.matongxue.com/madocs/987/)




---

### 2021年12月 4日  2:12:14

今天继续学习了关于SVM、软间隔、对偶问题、KKT条件方面的知识。SVM本身的目标函数虽然也是凸函数，是比较方便求解最值的，不过由于涉及到不等式约束的原因，因此需要利用拉格朗日函数及对偶算法来讲问题进一步转化为无约束的凸函数最值问题。为什么拉格朗日函数的对偶函数必然是凸函数呢？这是由于对偶函数先求解了关于x的函数的最小值，使得这部分成为一个定值，而在后面以w,b为参数求解最大值的时候，各个凸函数相加也必然得到一个凸函数。而符合了slater条件原问题，可以确定对偶问题与原问题的最值是等价的，而由强对偶关系推导出来的必要条件就是KKT条件。但是在实际使用中，我们就是通过KKT条件来求解SVM中的各参数的，虽然他是一个必要条件而非充分条件，但是类似于求解函数最值时使用导数等于0的条件一样，这也是一个必要条件，但并不妨碍在实际使用中它的便捷性。

