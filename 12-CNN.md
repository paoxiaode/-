## CNN

### 卷积

#### 卷积层中输入与输出的关系

<img src="imag/图片62.png" alt="图片54" style="zoom: 33%;" />

<img src="imag/图片60.png" alt="图片54" style="zoom: 33%;" />

<font color='red'>Formula for output size: </font>
$$
output\ size = \frac{N-F}{stride}+1
$$
例：

<img src="imag/图片61.png" alt="图片54" style="zoom: 33%;" />



#### 可学习的参数

可学习：

* 卷积层conv可学习/maxpooling不可/
* BN（Batch Normalization)可/
* FC（全连接）可

不可学习：

* maxpooling 池化层不可



**imagnet模型**+**新的图片**

把最后一层删掉，重新调整最后一层的输出，输入新的数据，更新模型（前面层步长小，后面大） 