## HMM

记录关于 HMM 的计算和推导

### 独立性假设

输出仅仅与当前状态有关

### 计算似然度

$P\left(\mathbf{o}_{1}^{T} | \mathbf{q}_{1}^{T}\right)=\prod_{t=1}^{T} b_{i}\left(\mathbf{o}_{t}\right)=\prod_{t=1}^{T} \sum_{m=1}^{M} \frac{c_{i, m}}{(2 \pi)^{D / 2}\left|\boldsymbol{\Sigma}_{i, m}\right|^{1 / 2}} \exp \left[-\frac{1}{2}\left(\mathbf{o}_{t}-\boldsymbol{\mu}_{i, m}\right)^{\mathrm{T}} \boldsymbol{\Sigma}_{i, m}^{-1}\left(\mathbf{o}_{t}-\boldsymbol{\mu}_{i, m}\right)\right]$

其中， $o_{1}^{T}$ 表示观测序列 $o_1,o_2,...,t_T$ ,  $q_1^T$ 同理

### 状态序列概率

$P\left(\mathbf{q}_{1}^{T}\right)=\pi_{q_{1}} \prod_{t=1}^{T-1} a_{q_{t} q_{t+1}}$

## 计算联合概率$P\left(\mathbf{o}_{1}^{T}, \mathbf{q}_{1}^{T}\right)$

通过条件概率公式:

$P\left(\mathbf{o}_{1}^{T}, \mathbf{q}_{1}^{T}\right)=P\left(\mathbf{o}_{1}^{T} | \mathbf{q}_{1}^{T}\right) P\left(\mathbf{q}_{1}^{T}\right)$

### 计算$P(O_1^T)$

原则上可以通过

​	$P\left(\mathbf{o}_{1}^{T}\right)=\sum_{\mathbf{q}_{1}^{T}} P\left(\mathbf{o}_{1}^{T}, \mathbf{q}_{1}^{T}\right)$

来计算，即遍历所有可能的状态序列。但是这在计算复杂度上不可行。所以引入前向后向算法来计算该概率。

$\begin{aligned}
P\left(q_{t}\right.&\left.=i, \mathbf{o}_{1}^{T}\right)=P\left(q_{t}=i, \mathbf{o}_{1}^{t}, \mathbf{o}_{t+1}^{T}\right) \\
&=P\left(q_{t}=i, \mathbf{o}_{1}^{t}\right) P\left(\mathbf{o}_{t+1}^{T} | \mathbf{o}_{1}^{t}, q_{t}=i\right) \\
&=P\left(q_{t}=i, \mathbf{o}_{1}^{t}\right) P\left(\mathbf{o}_{t+1}^{T} | q_{t}=i\right) \\
&=\alpha_{t}(i) \beta_{t}(i)
\end{aligned}$



### 前向后向算法

- 定义每个状态 i 下的前向概率:

  ​	$\alpha_{t}(i)=P\left(q_{t}=i, \mathbf{o}_{1}^{t}\right), \quad t=1, \ldots, T$

- 定义每个状态 i 下的后向概率：

  ​	$\beta_{t}(i)=P\left(\mathbf{o}_{t+1}^{T} | q_{t}=i\right), \quad t=1, \ldots, T-1$

- 前向算法的递归计算：

  ​	$\alpha_{t}(j)=\sum_{i=1}^{N} \alpha_{t-1}(i) a_{i j} b_{j}\left(\mathbf{o}_{t}\right), \quad t=2,3, \ldots, T ; \quad j=1,2, \ldots, N$

  ​	$\alpha_{1}(i)=P\left(q_{1}=i, \mathbf{0}_{1}\right)=P\left(q_{1}=i\right) P\left(\mathbf{o}_{1} | q_{1}\right)=\pi_{i} b_{i}\left(\mathbf{0}_{1}\right)$

- 后向算法的递归计算:

  ​	$\beta_{t}(i)=\sum_{j=1}^{N} \beta_{t+1}(j) a_{i j} b_{j}\left(\mathbf{o}_{t+1}\right), \quad t=T-1, T-2, \ldots, 1 ; \quad i=1,2, \ldots, N$

  ​	$\beta_{T}(i)=1, \quad i=1,2, \dots, N$ (人为定义的，为了正确计算T-1项)

$\begin{aligned}
P\left(q_{t}\right.&\left.=i, \mathbf{o}_{1}^{T}\right)=P\left(q_{t}=i, \mathbf{o}_{1}^{t}, \mathbf{o}_{t+1}^{T}\right) \\
&=P\left(q_{t}=i, \mathbf{o}_{1}^{t}\right) P\left(\mathbf{o}_{t+1}^{T} | \mathbf{o}_{1}^{t}, q_{t}=i\right) \\
&=P\left(q_{t}=i, \mathbf{o}_{1}^{t}\right) P\left(\mathbf{o}_{t+1}^{T} | q_{t}=i\right) \\
&=\alpha_{t}(i) \beta_{t}(i)
\end{aligned}$

### 占有概率

通过前向后向概率，不仅可以计算观测序列概率，还可以计算占有概率，即给定观测序列，在时刻 t 状态为 $i$ 的概率, 可以由下式计算

$P(q_t=i|o_t^T) = \frac{P(q_t = i, o_1^T)}{P(o_1^T)}$

### 前向后向算法递归式的证明

- 对于前向递归式有：

  ​	$\begin{aligned}
  \alpha_{t}(j) &=P\left(q_{t}=j, \mathbf{o}_{1}^{t}\right) \\
  &=\sum_{i=1}^{N} P\left(q_{t-1}=i, q_{t}=j, \mathbf{o}_{1}^{t-1}, \mathbf{o}_{t}\right) \\
  &=\sum_{i=1}^{N} P\left(q_{t}=j, \mathbf{o}_{t} | q_{t-1}=i, \mathbf{o}_{1}^{t-1}\right) P\left(q_{t-1}=i, \mathbf{o}_{1}^{t-1}\right) \\
  &=\sum_{i=1}^{N} P\left(q_{t}=j, \mathbf{o}_{t} | q_{t-1}=i\right) \alpha_{t-1}(i) \\
  &=\sum_{i=1}^{N} P\left(\mathbf{o}_{t} | q_{t}=j, q_{t-1}=i\right) P\left(q_{t}=j | q_{t-1}=i\right) \alpha_{t-1}(i) \\
  &=\sum_{i=1}^{N} b_{j}\left(\mathbf{o}_{t}\right) a_{i j} \alpha_{t-1}(i)
  \end{aligned}$

- 对于后向递归式有

  ​		$\begin{aligned}
  \beta_{t}(i) &=P\left(\mathbf{o}_{t+1}^{T} | q_{t}=i\right) \\
  &=\frac{P\left(\mathbf{o}_{t+1}^{T}, q_{t}=i\right)}{P\left(q_{t}=i\right)}
  \end{aligned}$

  $\begin{aligned}
  &=\frac{\sum_{j=1}^{N} P\left(\mathbf{o}_{t+1}^{T}, q_{t}=i, q_{t+1}=j\right)}{P\left(q_{t}=i\right)}\\
  &=\frac{\sum_{j=1}^{N} P\left(\mathbf{o}_{t+1}^{T} | q_{t}=i, q_{t+1}=j\right) P\left(q_{t}=i, q_{t+1}=j\right)}{P\left(q_{t}=i\right)}\\
  &=\sum_{j=1}^{N} P\left(\mathbf{o}_{t+1}^{T} | q_{t+1}=j\right) \frac{P\left(q_{t}=i, q_{t+1}=j\right)}{P\left(q_{t}=i\right)}\\
  &=\sum_{j=1}^{N} P\left(\mathbf{o}_{t+2}^{T}, \mathbf{o}_{t+1} | q_{t+1}=j\right) a_{i j}\\
  &=\sum_{j=1}^{N} P\left(\mathbf{o}_{t+2}^{T} | q_{t+1}=j\right) P\left(\mathbf{o}_{t+1} | q_{t+1}=j\right) a_{i j}\\
  &=\sum_{j=1}^{N} \beta_{t+1}(j) b_{j}\left(\mathbf{o}_{t+1}\right) a_{i j}
  \end{aligned}$

  其中 $P\left(\mathbf{o}_{t+1}^{T} | q_{t}=i, q_{t+1}=j\right) = P\left(\mathbf{o}_{t+1}^{T} | q_{t+1}=j\right)$ 的原因是: 已经给定 $q_{t+1}$ ，$q_{t+1}$ 是确定，那么$q_{t}$ 就不对$o_{t+1}^T$ 再产生影响了。 

