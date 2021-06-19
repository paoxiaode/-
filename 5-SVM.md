## 5-SVM

SVM是一种监督学习的分类器，通过计算超平面来实现二分类。

### 问题定义

寻找到一个超平面，使得数据离超平面尽可能的远。

<img src="imag/图片25.png" alt="图片25" style="zoom: 67%;" />

### 问题转化：关键点

SVM关键在于将对 w,b​  的优化转化为了只对 w的优化

Margin 宽度 $r$ 与 $||w||$ 成反比，<font color='red'>即： $r\propto \frac{1}{||w||}$</font>

#### 推导

<img src="imag/图片26.png" alt="图片25" style="zoom: 67%;" />

<img src="imag/图片28.png" alt="图片25" style="zoom: 67%;" />

### <font color='red'>新的问题定义（要会推导）</font>

<img src="imag/图片29.png" alt="图片25" style="zoom: 67%;" />

对于有约束的优化问题，采用拉格朗日乘子

**<font color='red'>KKT条件（局部极值，可能不是最优）</font>**

<img src="imag/图片30.png" alt="图片25" style="zoom: 67%;" />

经推导后可得对偶问题：

<img src="imag/图片31.png" alt="图片25" style="zoom: 50%;" />



**<font color='red'>画图</font>**

考法：给几个坐标，然后画出超平面和支持向量。



### 线性不可分

#### 升维

<img src="imag/图片32.png" alt="图片25" style="zoom: 67%;" />

##### 非线性SVM

* 分类效果可能更好
* 分类的时间复杂度比线性SVM高

**核技巧**

对于输入空间中的非线性分类问题，可以通过非线性变换将它转化为某个维特征空间中的线性分类问题，在高维特征空间中学习线性支持向量机。由于在线性支持向量机学习的对偶问题里，目标函数和分类决策函数都**只涉及实例和实例之间的内积，所以不需要显式地指定非线性变换**，**而是用核函数替换当中的内积**。

<font color='red'>不需要知道升维后的向量形式，只需要知道kernel</font>

具体地， ![[公式]](https://www.zhihu.com/equation?tex=+K%5Cleft%28+x%2Cz+%5Cright%29+) 是一个函数，或正定核，意味着存在一个从输入空间到特征空间的映射 ![[公式]](https://www.zhihu.com/equation?tex=+%5Cphi+%5Cleft%28+x+%5Cright%29+) ，对任意输入空间中的 ![[公式]](https://www.zhihu.com/equation?tex=+x%2Cz+) ，有

![[公式]](https://www.zhihu.com/equation?tex=K%5Cleft%28+x%2Cz+%5Cright%29+%3D%5Cphi+%5Cleft%28+x+%5Cright%29+%5Ccdot+%5Cphi+%5Cleft%28+z+%5Cright%29)

<font color='red'>$K$是核函数当且仅当对任意数据  ，核矩阵(kernal matrix,gram matrix)总是半正定的</font>。

在线性支持向量机学习的对偶问题中，用核函数 ![[公式]](https://www.zhihu.com/equation?tex=+K%5Cleft%28+x%2Cz+%5Cright%29+) 替代内积，求解得到的就是非线性支持向量机
$$
Because\ W=\sum\limits_i\lambda_id_i\phi(x_i)\\
\begin{aligned}
y &=W^T\phi(x)+b\\
&= \sum\limits_i\lambda_id_i\phi(x_i)\phi(x)+b\\
&=\sum\limits_i\lambda_id_iK(x_i,x)+b
\end{aligned}
$$
即对于非线性SVM来说，当有新数据时需要新数据与所有原有数据算核函数，而线性SVM的$W$有解析式，只需要$W^Tx+b$ ，<font color='red'>时间复杂度从$O(1)$变为$O(n)$。</font>



解决方案：

* 并行化，多个核函数同时计算。
* 哈希，提取记录类似输入值的结果
* 采样，只算比较重要的核函数



#### 引入松弛变量

引入松弛变量$\xi_i$，问题转化为
$$
\ d_i\left( \boldsymbol{w}\cdot \boldsymbol{x}_{\boldsymbol{i}}+b \right) \ge 1-\xi _i
$$
优化问题为
$$
\underset{\boldsymbol{w,}b,\xi _i}{\min}\ \frac{1}{2}\lVert \boldsymbol{w} \rVert ^2+C\sum_{i=1}^m{\xi _i}\\
s.t.\ \ d_i\left( \boldsymbol{w}\cdot \boldsymbol{x}_{\boldsymbol{i}}+b \right) \ge 1-\xi _i\\
\xi _i\ge 0\ ,\ i=1,2,...,N
$$

对偶问题为
$$
\underset{\boldsymbol{\lambda }}{\min}\ \frac{1}{2}\sum_{i=1}^N{\sum_{j=1}^N{\lambda _i\lambda _jd_id_j\left( \boldsymbol{x}_{\boldsymbol{i}}\cdot \boldsymbol{x}_{\boldsymbol{j}} \right)}}-\sum_{i=1}^N{\lambda _i}\\
s.t.\ \ \ \ \sum_{i=1}^N{\lambda _id_i}=0\\
0\le \lambda _i\le C,\ i=1,2,...,N
$$



<font color='red'>对偶问题的变化</font>：$\lambda_i$多出了一个上界$C$

* 当$C$无穷大时，回归到无松弛的SVM。

* $C$大惩罚大，$\lambda_i$可选范围大，分类效果好。
* $C$过小，$\lambda_i$可选范围小，可能优化不足。



### 多分类场景

假设共有C个类，如何将二分类SVM用在此

* 一对多

  <font color='red'>训练时依次把某个类别的样本归为一类,其他剩余的样本归为另一类，这样就构造出了C个SVM。分类时将未知样本分类为具有最大分类函数值的那类。</font>

  假如我有四类要划分（也就是4个Label），他们是A、B、C、D。于是我在抽取训练集的时候，分别抽取

  1. A所对应的向量作为正集，B，C，D所对应的向量作为负集；
  2. B所对应的向量作为正集，A，C，D所对应的向量作为负集；
  3. C所对应的向量作为正集，A，B，D所对应的向量作为负集；
  4. D所对应的向量作为正集，A，B，C所对应的向量作为负集；

  最后每个测试都有一个结果f1(x),f2(x),f3(x),f4(x),以这四个值中最大的一个作为分类结果。

* 一对一

  <font color='red'>在任意两类样本之间设计一个SVM，因此C个类别的样本就需要设计 C(C-1)/2 个SVM，当对一个未知样本进行分类时，采用投票法，每个SVM投一票，最后得票最多的类别即为该未知样本的类别。</font>

  假设有四类A,B,C,D四类。在训练的时候我选择A,B; A,C; A,D; B,C; B,D;C,D所对应的向量作为训练集，然后得到六个训练结果，在测试的时候，把对应的向量分别对六个结果进行测试，然后采取投票形式，最后得到一组结果。

  投票是这样的：
  A=B=C=D=0;
  (A,B)-classifier 如果是A win,则A=A+1;otherwise,B=B+1;
  (A,C)-classifier 如果是A win,则A=A+1;otherwise, C=C+1;
  ...
  (C,D)-classifier 如果是A win,则C=C+1;otherwise,D=D+1;
  The decision is the Max(A,B,C,D)