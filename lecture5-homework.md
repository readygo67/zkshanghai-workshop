# 第5课 课后作业

## 第1题 假的KZG承诺
<img width="769" alt="image" src="https://github.com/readygo67/zkshanghai-workshop/assets/78890754/8cc21608-824f-423f-b806-2fd6be2371f3">


## 第2题 KZG向量承诺方案

从 KZG 多项式承诺方案构造一个**向量承诺方案**。 （提示：对于向量$m=[m_1,m_2,...,m_q]$  是否存在一个“插值多项式$I(x)$ 使得$I(x_i)=y_i$）

为对所有$x_i$的计算 $f(x_i)=y_i$  们知道这样一个多项式存在，并且可以通过拉格朗日插值来计算它：

$f(x) = \sum\limits^n\limits_{i=1} y_i*L_i(x)$

$$
L_i(x) = \prod\limits^{n-1}\limits_{j=0,j!=i}(x-j)/(i-j)
$$
