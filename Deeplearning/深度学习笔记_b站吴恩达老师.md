# 深度学习笔记_b站吴恩达老师

## 神经网络基础知识

### 符号定义集

m：训练样本的个数。（如：（x^(1)^, y^(1)^）、（x^2^, y^2^））

y：输出标签（output, label）。

x：输入向量（一维向量）。

n = n~x~：输入特征向量的维度，如输入的是图像数据，其特征向量维度：C×W×H（通道数×高×宽）。

$\alpha$：学习率（learning rate）。

（x，y）：表示单个样本，其中x$\in$$\mathbb{R^{n_{x}}}$。y$\in${0，1}（二分类）。

X=$\begin{bmatrix}
 &.  &.  &  &  & &.\\ 
 &.  &.  &  &  & &.\\ 
 &x^{(1)}  &x^{(2))}  &.  &.  &. &x^{(m)}\\ 
 &.  &.  &  &  & &.\\ 
 &.  &.  &  &  & &.
\end{bmatrix} $ ，X$\in\mathbb{R^{n_{x} \times m}}$

Y=$\begin{bmatrix}y^{(1)}&y^{(2)}&.&.&y^{(m)}\end{bmatrix}$ ，Y$\in\mathbb{R^{1 \times m}}$

$Z^{[i](j)\{t\}}_{k}$：

- 上标[i]：第 i 层神经网络。
- 上标(j)：第 j 个实例样本。
- 上标{t}：min-batch中的第t批次。
- 下标k：第 i 层神经网络的第 k 个神经元节点。

### sigmoid函数

图像：

<img src="F:\笔记\Deeplearning\assets\1681212804511.png" style="zoom:50%">

表达式：
$$
\sigma (z) = \frac{1} {1 + e^{-z}}
$$

### 损失函数loss function

$$
L(\hat{y},y)=？
$$

其结果值越小，误差越小，效果越好。

本章中所构造的损失函数cost function：
$$
J(w,b) = \frac{1}{m} \sum_{i=1}^{m}L(\hat{y},y)
=-\frac{1}{m}\sum_{i=1}^{m}[y^{(i)}ln^{\hat{y}^{i}}+(1-y^{(i)})ln(1-\hat{y}^{(i)})]
$$

### 逻辑回归方程

$$
Z = W^{T}X+b\\
\hat{y} = a = \sigma(z)\\
L(a,y)=-[ylog^{a} + (1-y)log^{(1-a)}]
$$

注：在本文中，若log无下底，则 $log=log_{e}=ln$

### 逻辑回归梯度下降

流程图：

<img src="F:\笔记\Deeplearning\assets\1681281885801.png" style="zoom:60%">

需要更新的参数：
$$
w_{1}、 w_{2}、 b
$$
计算图求导（梯度）：
$$
dw_{1} = x_{1} \cdot dz\\
dw_{2} = x_{2} \cdot dz\\
db = dz
$$
更新规则（更新计算式）：
$$
w_{1} = w_{1} - \alpha \cdot dw_{1}\\
w_{2} = w_{2} - \alpha \cdot dw_{2}\\
b = b - \alpha \cdot db
$$
在神经网络的训练迭代中，由于数据量的庞大，在进行梯度更新是就不可避免的使用for循环（for loop），但是大量的for loop会大大增加网络迭代所需要的时间，所以在实际模型中，避免使用for loop。同时在数据的输入中，巧妙地使用数据向量化可以加快模型的运算速度。

向量化code：即将数据以numpy的数据格式存储并进行运算。

在神经网络的构建中，要少使用rank=1的数据，即shape为(5, )或(n, )的数据。

### Formulas for computing derivatives

Forward propagation:
$$
Z^{[1]} = W^{[1]}X + b^{[1]} \\
A^{[1]} = g^{[1]}(z^{[1]}) \\
Z^{[2]} = W^{[2]}A^{[1]} + b^{[2]} \\
A^{[2]} = g^{[2]}(Z^{[2]}) = \sigma (Z^{[2]}) \\
$$
Backward propagation:
$$
dZ^{[2]} = A^{[2]} - Y \\
dW^{[2]} = \frac{1}{m} dZ^{[2]}A^{[1]T} \\
db^{[2]} = \frac{1}{m} np.sum(dZ^{[2]}, axis=1, keepdim=True) \\
dZ^{[1]} = W^{[2]}dZ^{[1]} \times g^{[1]}{'}(Z^{[1]})\qquad element-wise \space product \\
dW^{[1]} = \frac{1}{m} dZ^{[1]} X^{T} \\
db^{[1]} = \frac{1}{m} np.sum(dZ^{[1]}, axis=1, keepdim=True)
$$

### 数据预处理的一般步骤

解决输入数据的维度问题

reshape数据的维度，如处理成一维数据（向量化）

标准化数据、

### L层神经网络的forward

$$
Z^{[L]} = W^{[L]} A^{[L-1]} + b^{[L]} \\
A^{[L]} = g(Z^{[L]}) \\
其中： \\
W^{[L]}:(n^{[L]}, n^{[L-1]}) \\
b^{[L]}:(n^{[L]}, 1) \\
m个样本：
Z^{[L]}:(n^{[L]}, m) \\
A^{[L]}:(n^{[L]}, m)
$$

## 神经网络优化

### 偏差，方差（bias,variance）

高偏差：欠拟合，在train set，test set中表现都不好。

高方差：过拟合，在train set中表现好，但在test set中表现差。

### Regularization（正则化）

正则化可以在训练集数据有限的情况下，有效的防止过拟合。

### Dropout

Dropout可以使神经网络节点随机失活，可以有效的防止过拟合。

### Normalizing（归一化）

Normalizing可以将数据缩小统一到一定的范围内，可以加快梯度下降速度。

### 梯度检查

### 小批量（min-batches）

min-batches：小批量梯度下降，在所有数据集中取min-batch size个数据出来进行神经网络的梯度下降计算。

### 指数加权平均

$$
V_{t} = \beta V_{t-1} + (1 - \beta) \theta _{t} \qquad （0<\beta < 1）\\
其中,V_{t}为第t天的预测气温，V_{t-1}为第t-1天的预测气温，\theta 为第t天的实际气温 \\
\beta 越接近1，预测曲线越平滑。
$$

### 动量梯度下降（momentum）

momentum：在更新参数w，b时，在原始基础上乘以$\beta$，加快参数下降速度。

### RMSprop（均方根传递）

Root Mean Square prop：减小垂直方向的震荡，加快水平方向的梯度下降，可以有效的减少噪音。

### Adam（自适应矩估计）

Adam（Adaptive Moment Estimation）：
$$
V_{dw} = \beta _{1} V_{dw} + (1 - \beta _{1})dw,\quad V_{db} = \beta _{1} V_{db} + (1 - \beta _{1})db \\
S_{dw} = \beta _{2} S_{dw} + (1 - \beta _{2})dw^{2},\quad S_{db} = \beta _{2} S_{db} + (1 - \beta _{2})db^{2} \\
V^{corrected}_{dw} =  \frac{V_{dw}}{(1 - \beta _{1}^{t})}, \quad V^{corrected}_{db} =  \frac{V_{db}}{(1 - \beta _{1}^{t})} \\
S^{corrected}_{dw} =  \frac{S_{dw}}{(1 - \beta _{2}^{t})}, \quad S^{corrected}_{db} =  \frac{S_{db}}{(1 - \beta _{2}^{t})} \\
W = W - \alpha \frac{V^{corrected}_{dw}}{\sqrt{S^{corrected}_{dw}}+\varepsilon}, \quad b = b - \alpha \frac{V^{corrected}_{db}}{\sqrt{S^{corrected}_{db}} + \varepsilon} \\
\alpha :需要被确定 \\
\beta _{1}:0.9 \\
\beta _{2}:0.999 \\
\varepsilon :10^{-8}
$$

### Learning rate decay

学习衰减率，可以设置动态学习率。

$$ \alpha = \frac{1}{1 + decayRate \times \lfloor\frac{epochNum}{timeInterval}\rfloor} \alpha_{0} $$

### Batch Normalization

批量归一化（Batch Normalization）：BN层可以有效的限制其输出固定在同一均值方差下，因此可以减少因数据集的变化而带来的影响，加快网络学习效率。
$$
\mu = \frac{1}{m} \sum_{i}Z^{(i)} \\
\sigma^{2} = \frac{1}{m} \sum_{i} (Z^{(i)} - \mu)^{2} \\
Z^{(i)}_{norm} = \frac{Z^{(i)} - \mu}{\sqrt{\sigma^{2} + \varepsilon}} \\
\widetilde{Z}^{(i)} = \gamma Z^{(i)}_{norm} + \beta
$$

### Softmax regression

Softmax回归：分类器，在网络的最后一层输出每种预测种类的概率值。
$$
a^{[L]} = \frac{e^{Z^{[L]}}}{\sum^{4}_{i=1}t_{i}} \\
4分类器
$$

## 构建机器学习项目

### 单一数评价指标

| Precision（精确率）                        | Recall（召回率）                 | F1 Score         |
| ------------------------------------------ | -------------------------------- | ---------------- |
| 模型判为正的所有样本中有多少是真正的正样本 | 所有正样本有多少被模型判为正样本 | P和R的加权平均值 |

### 满足指标和优化指标

当在进行模型评估时，有多个想要关心的指标，可以将指标分为满足指标和优化指标。

满足指标：模型必须满足的指标。

优化指标：模型需要不断优化的指标。

### Train Dev Test set distribution

在大数据集下，只要保证Dev和Test set能够进行模型评估即可。例如：现有100,000张图片，可将数据集划分为90% ： 5% ：5%。

### Human level performance

人类水平表现：在一分类任务中，Human error 的值即为 Human level performance，不同群体会有不一样的结果，可以视任务需要选择Human level performance。一般情况下，最佳的Human error 接近Bayesian error（贝叶斯误差）。

|   Human error   | 0.5% |                    |
| :-------------: | :--: | :----------------: |
|   train error   |  3%  |  avoid bias 2.5%   |
| train-dev error | 3.5% |   variance 0.5%    |
|    dev error    |  7%  | mismatch data 3.5% |

|   Human error   | 0.5% |                  |
| :-------------: | :--: | :--------------: |
|   train error   |  3%  | avoid bias 2.5%  |
| train-dev error |  6%  |   variance 3%    |
|    dev error    |  7%  | mismatch data 1% |

### transfer learning

迁移学习的适用情况：transfer from A to B

- Task A and B have the same input X

- You have a lot more data for Task A than Task B

### multi-task learning

多任务学习适用情况：

- Training on a set of tasks that could benefit from having shared lower-level features（对一组任务进行培训，这些任务可以从共享较低级别的特性中受益）
- Usually: Amount of data you have for each task is quite similar（通常:每个任务的数据量非常相似）
- Can train a big enough neural network to do well on all the tasks（可以训练一个足够大的神经网络来完成所有的任务）

### end to end deeplearning

端对端的深度学习：与传统机器学习相比，没有中间过程，只有输入X和映射Y。

适用情况：拥有大量数据来训练模型，使得输入X和输出Y之间能更好的映射。

## 卷积神经网络（Convolutional Neural Networks）

### 卷积

$$
input\ size(n, n) * fitle\ size(f, f) \rightarrow output\ size(n-f+1, n-f+1)\\
(6, 6) * (3,3) \rightarrow (4, 4)\\
fitle\ size = kernel\ size\\
padding = 0\\
stride = 1
$$

### Padding（填充）

$$
input\ size(n, n) * fitle\ size(f, f) \rightarrow output\ size(n+2p-f+1, n + 2p-f+1)\\
(6, 6) * (3,3) \rightarrow (6, 6) \quad(p=1)\\
stride = 1
$$

Padding有两种：

Valid：即$p=0$。

Same：即$p = \frac{f-1}{2}$

### Stride（步长）

$$
(n, n) * (f, f) \rightarrow (\lfloor \frac{n+2p-f}{s}\rfloor +1, \lfloor \frac{n + 2p-f}{s}\rfloor+1)\\
(6, 6) * (3,3) \rightarrow (6, 6) \quad(p=1)\\
stride = s\\
$$

### 三维卷积

$$
(n ,n, c) * (f, f, c) \rightarrow (n-f+1, n-f+1, c')\\
C:channel\\
彩色图有RGB三通道，即C=3 \\
c' = fitle的个数
$$

### Pooling layer

Max pooling：被池化层覆盖的区域取最大值。

Average pooling：被池化层覆盖的区域取平均值。

### Data augmentation

常用的数据增强方法：垂直镜像，随机裁剪，随机翻转，图像变形，局部弯曲等。

色彩变化：对R,G,B三通道做加减算法，如PCA color augmentation。

### 应用领域

图像分类，目标静态或动态检测，人脸识别

## 循环神经网络（Recurrent Neural Networks）

### 符号注释

x：表示单个输入序列信号。

y：表示单个输出标签。

$x^{<t>}$：上标\<t>表示在序列信号x中的第t个位置（与输入是语音序列或者文本序列无关）。

$y^{<t>}$：上标\<t>表示在序列信号y中的第t个位置（与输入是语音序列或者文本序列无关）。

$T_{x}$：输入序列信号的长度。

$T_{y}$：输出序列信号的长度。

### Forward propagation

$$
a^{<t>} = g(w_{aa}a^{<t-1>} + w_{ax}x^{<t>} + b_{a}) \\
y^{<t>} = g(w_{ya}a^{<t>} + b_{y})
$$

角标说明：下标左侧的y代表要计算出类似y的值，右侧的a代表乘以类似a的值。
$$
a^{<t>} = g(w_{a}[a^{<t-1>}, x^{<t>}] + b_{a}) \\
y^{<t>} = g(w_{y}a^{<t>} + b_{y}) \\
其中：w_{a} =\begin{bmatrix} w_{aa} & w_{ax} \end{bmatrix}\\
[a^{<t-1>}, x^{<t>}] = \begin{bmatrix} a^{<t-1>} \\ x^{<t>} \end{bmatrix}
$$

### RNN的类型

one to one：RNN = CNN

one to many：例如产生音乐。

many to one：例如电影的影评评级。

many to many：例如文本中名字识别，机器翻译。

### GRU（Gated Recurrent Unit）

门循环单元：c：memory cell
$$
c^{<t>} = a^{<t>} \\
\Gamma_{r} = \sigma(W_{r}[c^{<t-1>},\ x^{<t>}] + b_{r}) \\
\tilde{c}^{<t>} = tanh(W_{c}[\Gamma_{r} * c^{<t-1>},\ x^{<t>}] + b_{c}) \\
\Gamma_{u} = \sigma(W_{u}[c^{<t-1>},\ x^{<t>}] + b_{u}) \\
c^{<t>} = \Gamma_{u}*\tilde{c}^{<t>} + (1 - \Gamma_{u}) * c^{<t-1>}
$$

### LSTM Unit（Long Short Term Memory）

长短期记忆：核心公式
$$
\tilde{c}^{<t>} = tanh(W_{c}[a^{<t-1>}, \ x^{<t>}] + b_{c}) \\
(updata)\quad \Gamma_{u} = \sigma(W_{u}[a^{<t-1>}, \ x^{<t>} + b_{u}) \\
(forget)\quad \Gamma_{f} = \sigma(W_{f}[a^{<t-1>}, \ x^{<t>} + b_{f}) \\
(output)\quad \Gamma_{o} = \sigma(W_{o}[a^{<t-1>}, \ x^{<t>} + b_{o}) \\
c^{<t>} = \Gamma_{u} * \tilde{c}^{<t>} + \Gamma_{f} * c^{<t-1>} \\
a^{<t>} = \Gamma_{o} * tanh(c^{<t>})
$$

### BRNN（Bidirectional）

<img src="F:\笔记\Deeplearning\assets\1689902357502.png" style="zoom:40%">

### Beam Search

<img src="F:\笔记\Deeplearning\assets\1690180431458.png" style="zoom:40%">

## Numpy基础

### **numpy中常用函数**

|                         **函数名**                          |                        **函数功能**                        |                           **参数**                           |
| :---------------------------------------------------------: | :--------------------------------------------------------: | :----------------------------------------------------------: |
|          **np.sum(x, axis = 0, keepdims = True)**           |                        **矩阵求和**                        | **x：待求和矩阵<br>axis = 0：垂直方向求和。<br>axis = 1：水平方向求和。<br>keepdims：是否保持矩阵的二维性** |
|                    **np.reshape(m, n)**                     |                      **重构矩阵维度**                      |                     **新矩阵维度m行n列**                     |
|                       **np.shape()**                        |                      **求矩阵的维度**                      |                                                              |
|                      **np.dot(a, b)**                       |         **矩阵a和矩阵b相乘（线代中的乘法或内积）**         |                       **输入两个矩阵**                       |
|                        **np.exp(m)**                        |                    **自然对数的底数e**                     |                         **e的m次方**                         |
| **np.linalg.norm(x,ord = None, axis = 1, keepdims = True)** |                      **求向量的范数**                      | **x：待求范数的向量或矩阵<br>ord：选择计算范数的公式<br>axis：按行（1）或列（1）求范数<br>keepdims：是否保持矩阵的二维性** |
|                     **np.outer(x1,x2)**                     | **求向量的外积，结果是一个x1.shape$\times$x2.shape的矩阵** |                        **向量x1，x2**                        |
|                   **np.multiply(x1,x2)**                    |                   **矩阵元素点对点相乘**                   |                        **矩阵x1,x2**                         |
|                                                             |                                                            |                                                              |

```markdown
A trick when you want to flatten a matrix X of shape (a,b,c,d) to a matrix X_flatten of shape (b∗c∗d, a) is to use:

X_flatten = X.reshape(X.shape[0], -1).T      # X.T is the transpose of X
```



### **广播机制（Broadcast）**

**例1：**
$$
\begin{bmatrix}1\\2\\3\\4\end{bmatrix} + 100 = \begin{bmatrix}1\\2\\3\\4\end{bmatrix} + \begin{bmatrix}100\\100\\100\\100\end{bmatrix} = \begin{bmatrix}101\\102\\103\\104\end{bmatrix}
$$
**例2：**
$$
\begin{bmatrix}1&2&3\\4&5&6\end{bmatrix} + \begin{bmatrix}100&200&300\end{bmatrix} = \begin{bmatrix}1&2&3\\4&5&6\end{bmatrix} + \begin{bmatrix}100&200&300\\100&200&300\end{bmatrix} = \begin{bmatrix}101&202&303\\104&205&306\end{bmatrix}
$$
**例3：**
$$
\begin{bmatrix}1&2&3\\4&5&6\end{bmatrix} + \begin{bmatrix}100\\200\end{bmatrix} = 
\begin{bmatrix}1&2&3\\4&5&6\end{bmatrix} + \begin{bmatrix}100&100&100\\200&200&200\end{bmatrix}
= \begin{bmatrix}101&102&103\\204&205&206\end{bmatrix}
$$
