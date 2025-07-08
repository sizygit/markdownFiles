# 多智能体

```matlab
[V,D,W] = eig(L);
zeroIdx = find(abs(diag(D)) < 1e-10);
WL1= W(:,zeroIdx)/(sum(W(:,zeroIdx))
```

# 论文阅读

线性系统多智能体的研究比较丰富且成熟，

研究切换拓扑/网络攻击有随机扰动、正状态约束、时变时滞等的多智能体系统一致性问题。

## 分布式控制

### 1.多智能体系统的时间触发一致性控制

**带有随机扰动的多智能体系统的一致性问题**：

- 采用Ito型随机微分方程进行建模，提出了新的静态事件触发控制协议。
- 通过强制给出执行时间的正下界，避免无穷快执行。
- 提出了新的随机收敛分析方法，实现了几乎必然一致性和均方一致性，并展示了控制器更新频次的减少。

**带有状态约束和切换拓扑的多智能体系统事件触发一致性问题**：

- 利用平均驻留时间切换方法，确保有限时间内拓扑切换次数有限。
- 设计了静态事件触发控制协议，结合正系统理论和Lyapunov方法分析闭环性能，保证状态在非负象限内保持一致。

**带有状态约束与多时滞的多智能体系统的事件触发保成本一致性问题**：

- 通过平均驻留时间方法构建拓扑切换序列，设计了静态事件触发一致性协议。
- 利用Lyapunov-Krasovskii函数处理时变时滞，得出闭环系统正一致性条件，提出了基于二次成本函数的保成本控制器。

**带有多信道DoS攻击的多智能体系统的保成本安全一致性问题**：

- 根据攻击信号分类，设计了基于切换思想的事件触发安全一致性协议，并提出保成本安全一致性准则。
- 通过反馈增益调整和控制成本分析，优化系统在抵御攻击时的性能和通信资源使用。

**带有多重网络攻击的多智能体系统动态事件触发有限时间一致性问题**：

- 将系统模型转化为网络化切换系统，设计动态事件触发机制，提出有限时间安全一致性协议。
- 通过自适应调整触发阈值，减轻计算和通信负担，并确保在有限时间内实现一致性。

这些研究成果展示了事件触发控制在多智能体系统中的应用潜力，尤其在应对随机扰动、时滞、状态约束、网络攻击等复杂因素时，提出了有效的分析和控制方法。

# Formation Control(E-Books)

## Global Stabilization

​		对于基于距离的分布式编队控制，通常使用关于每个agent可以感知到的距离误差的函数作为势函数，则通过势函数的梯度来设计控制器，通常对于特定的图可以设计全局收敛的梯度控制律，但是对于推广到n个智能体的一般刚性拓扑结构，无法实现任意初始条件下的全局收敛控制律，因此我们先研究针对特定形态的全局收敛梯度控制律。

### Gradient Control Laws

$$
\begin{aligned}
\text{u}_i& =-\nabla_{p_i}\phi(z)  \\
&=-\left[\frac{\partial\phi\left(z\right)}{\partial p_i}\right]^T \\
&=-\sum_{j\in\mathcal{N}_{i}}\frac12(\|z_{ij}\|^{2}-\bar{d}_{ij}^{*})z_{ij}=\sum_{j\in\mathcal{N}_{i}}\frac12(\|z_{ij}\|^{2}-\bar{d}_{ij}^{*})z_{ji}
\end{aligned}
$$

### Global Convergence of Three-Agent Formations

​		定义一个2维空间的n个智能体$\dot{p_i}=u_i$（全局坐标系$^g\sum$下），感知拓扑为一个无向图$\mathcal{G}=(\mathcal{V},\mathcal{E}^{s})$，满足$p_{ji}^{i}\triangleq(p_{j}-p_{i})^{i}=p_{j}^{i}$（注意$p^i_j$指从$i$的局部坐标系下测量到$j$的距离与方向）。定义期望的编队可表述为：
$$
E_{p^*}\triangleq\{p\in\mathbb{R}^{2n}:\|p_i-p_j\|=\|p_i^*-p_j^*\|,i,j\in\mathcal{V}\}
$$
​		针对控制器的设计有两种方法，一种是针对每个智能体设计其各自控制输入$u_i$，另一种间接方法是以边$(i,j)^e$为对象，使用智能体间的相对距离$\bar{d}_{ij}\stackrel{\Delta}{=}\|p_{i}-p_{j}\|^{2}$作为虚拟控制输入。
$$
\begin{aligned}
\dot{\bar{d}}_{ij}& =u_{ij}  =\frac d{dt}(\|p_i-p_j\|^2) \\
&=\frac{\partial(\|p_i-p_j\|^2)}{\partial p_i}\frac{\partial p_i}{\partial t}+\frac{\partial(\|p_i-p_j\|^2)}{\partial p_j}\frac{\partial p_j}{\partial t} \\
&=2(p_i-p_j)^T(u_i-u_j)
\end{aligned}
$$
我们可以针对$(i,j) ^e$设计反馈控制器：
$$
\dot{\bar{d}}_{ij}=-k_{ij}(\bar{d}_{ij} - \bar{d}_{ij}^*)
$$
理论上总会存在$\bar{e}_{ij}\rightarrow0$，但是这种方法存在两个技术问题：一是拓扑构型的几何可行性，二是$u_{ij}$到$u_i,u_j$的分解。	

​		我们回顾上式，$u_{ij}=2(p_i-p_j)^T(u_i-u_j)=-k_{ij}\bar{e}_{ij}$，可以解得$\bar{d}_{ij}(t)=e^{-k_{ij}t}\bar{d}_{ij}(t_0)+(1-e^{-k_{ij}t})\bar{d}_{ij}^*$。即使初始与期望构型均可实现，但不清楚在出实体按键后；对于上述的控制量$u_{ij}$进行如下分解：
$$
\begin{aligned}(p_i-p_j)^Tu_i&=-\frac14k_{ij}\bar{e}_{ij}\\(p_j-p_i)^Tu_j&=-\frac14k_{ij}\bar{e}_{ij}\end{aligned}
$$
对于三角形构型而言，每一个智能体$i$都与两条边相连（两个约束），按照上式的分解可将$u_i$写成如下形式：
$$
\begin{aligned}
\underbrace{\left[\begin{array}{c}
\left(p_{i}-p_{j}\right)^{T} \\
\left(p_{i}-p_{k}\right)^{T}
\end{array}\right]}_{\triangleq A_{i(2\times2)}} u_{i}&=-\frac{k_{i j}}{4} \underbrace{\left[\begin{array}{c}
\bar{e}_{i j} \\
\bar{e}_{i k}
\end{array}\right]}_{\triangleq b_{i}}
\\

u_i& =-\frac{k_{ij}}{4}A_{i}^{-1}b_{i}  \\
&\left.=-\frac{k_{ij}}{4\mathrm{det}(A_i)}\left[\begin{matrix}y_i-y_k&-(y_i-y_j)\\-(x_i-x_k)&x_i-x_j\end{matrix}\right.\right]b_i
\end{aligned}
$$
注意到若三角形排列接近共线时，$det(A_i)\approx0$，则控制器会发散，可对控制律进行如下修改:
$$
\begin{aligned}
u_{i} & =-\frac{k_{i j}}{4}\left|\operatorname{det}\left(A_{i}\right)\right| A_{i}^{-1} b_{i} \\
& =-\frac{k_{i j}}{4}\left[\begin{array}{cc}
y_{i}-y_{k} & -\left(y_{i}-y_{j}\right) \\
-\left(x_{i}-x_{k}\right) & x_{i}-x_{j}
\end{array}\right]\left[\begin{array}{c}
\bar{e}_{i j} \\
\bar{e}_{i k}
\end{array}\right]
\end{aligned}
$$
​		上面得到的控制律是通过将边的控制力均匀分配给两端得到的表达式，实际上我们还可以进行不均匀的分配。同时上式控制律并不能保证三角形的编队中心静止，也不能保证三角形的区域面积是恒定的($det(A_i)$)。

### Global Convergence of Polygon Formations

​		当智能体的个数超过四个时，基于相对距离的分析方法便会变得异常困难，传统的梯度控制不能完整的解决这类问题。

​		本节我们假设网络拓扑只有一个有向环，我们定义有向相对距离为
$$
z_{(i+1)i}=p_{i+1}-p_i
$$
期望的相对距离的平方为$\bar{d}_{i(i+1)}^*$。为了确保给定的等价框架是可实现的，我们需要给出多边形编队在二维空间实现的充要条件：
$$
2\max\{d_{1(2)}^*,d_{2(3)}^*,\ldots,d_{n-1(n)}^*\}\leq\sum_{i=1}^nd_{i(i+1)}^*
$$
​		我们定义相对距离平方误差的标量为$\bar e_{i(i+1)}=||z_{(i+1)i}||^2-\bar d_{i(i+1)}^*$。我们设计如下势函数：
$$
\phi(\bar{e})=\frac14\sum_{i=1}^n\bar{e}_{i(i+1)}^2
$$
按照梯度控制律可的$u_i$表达式：
$$
\begin{aligned}
u_i& =-\left[\frac{\partial\phi\left(\bar{e}\right)}{\partial p_i}\right]^T  \\
&=(\|z_{(i+1)i}\|^2-\bar{d}_{i(i+1)}^*)(p_{i+1}-p_i)-(\|z_{(i)i-1}\|^2-\bar{d}_{i-1(i)}^*)(p_i-p_{i-1}) \\
&=\bar{e}_{i(i+1)}z_{(i+1)i}-\bar{e}_{i-1(i)}z_{(i)i-1}\\
 &\because \text{第二项无法测量得到} \\
&\therefore u_i \approx \bar{e}_{i(i+1)}z_{(i+1)i}
\end{aligned}
$$
​		我们对势函数进行求导得
$$
\dot{\phi} = -\frac12\sum_{i=1}^n\|\bar{e}_{i(i+1)}z_{(i+1)i}-\bar{e}_{i+1(i+2)}z_{(i+2)i+1}\|^2
$$
可知若误差不为零时$\dot{\phi}=0$，此时误差便不能收敛(例如某些共线情况)。

### Global Convergence of K(3) +1 Edge Formations

​		在三角形编队中新加一个单边节点，该附加节点只有一个约束，被称为**K(3)+1**边图。假设智能体的邻域集合为
$$
\mathcal{N}_1=\{2,3\},\mathcal{N}_2=\{1,3\},\mathcal{N}_3=\{1,2,4\},\mathcal{N}_4=\{3\}
$$
​		同前文定义如下势函数
$$
\phi(\bar{e})=\frac{1}{4}\sum_{(i,j)^{e}\in\mathcal{E}}\bar{e}_{ij}^{2}
$$
控制律为
$$
\begin{aligned}
u&=-\nabla_{p}\phi(\bar{e})=-\left[\frac{\partial\phi(\bar{e})}{\partial p}\right]^{T}=-\mathbb{R}_{\mathcal{G}}^{T}\bar{e}=-(\mathbb{E}_{\mathcal{G}}\otimes\mathbb{I}_{2})p\\
&=\begin{bmatrix}-\bar{e}_{12}z_{12}-\bar{e}_{13}z_{13}\\\bar{e}_{12}z_{12}-\bar{e}_{23}z_{23}\\\bar{e}_{23}z_{23}+\bar{e}_{13}z_{13}-\bar{e}_{34}z_{34}\\\bar{e}_{34}z_{34}\end{bmatrix}\\
\text{式中}&\left.\mathbb{R}_{\mathcal{G}}=\left[\begin{array}{cccc}p_1^T-p_2^T&p_2^T-p_1^T&0&0\\p_1^T-p_3^T&0&p_3^T-p_1^T&0\\0&p_2^T-p_3^T&p_3^T-p_2^T&0\\0&0&p_3^T-p_4^T&p_4^T-p_3^T\end{array}\right.\right]\\
&
\left.\mathbb{E}_{\mathcal{G}}=\left[\begin{array}{ccc}\bar{e}_{12}+\bar{e}_{13}&-\bar{e}_{12}&-\bar{e}_{13}&0\\-\bar{e}_{12}&\bar{e}_{12}+\bar{e}_{23}&-\bar{e}_{23}&0\\-\bar{e}_{13}&-\bar{e}_{23}&\bar{e}_{13}+\bar{e}_{23}+\bar{e}_{34}&-\bar{e}_{34}\\0&0&-\bar{e}_{34}&\bar{e}_{34}\end{array}\right.\right]
\end{aligned}
$$
式中的图误差矩阵$\mathbb{E}_{\mathcal{G}}$形式类似拉普拉斯矩阵，$\mathbb{R}_{\mathcal{G}}$称为rigidity matrix。

分量形式为（即边误差和在i系下的观测相对位置向量数量积）
$$
u_{i}=-\sum_{j\in\mathcal{N}_{i}}\bar{e}_{ij}(p_{i}-p_{j})=\sum_{j\in\mathcal{N}_{i}}\bar{e}_{ij}z_{ji}
$$

### Global Convergence of K(4) − 1 Edge Formations

​		K(4) − 1 边图即删除一条边后的K(4)图，我们定义相邻集如下：
$$
\mathcal{N}_{1}=\{2,3\},\mathcal{N}_{2}=\{1,3,4\},\mathcal{N}_{3}=\{1,2,4\},\mathcal{N}_{4}=\{3\}
$$
易得图中有5条边，即误差$\bar{e}=(\bar{e}_{12},\bar{e}_{13},\bar{e}_{23},\bar{e}_{24},\bar{e}_{34})^T$，则对应的rigidity matrix与error graph matrix如下：
$$
\begin{aligned}
&\mathbb{R}_{\mathcal{G}} \left.=\left[\begin{array}{cccc}p_1^T-p_2^T&p_2^T-p_1^T&0&0\\p_1^T-p_3^T&0&p_3^T-p_1^T&0\\0&p_2^T-p_3^T&p_3^T-p_2^T&0\\0&p_2^T-p_4^T&0&p_4^T-p_2^T\\0&0&p_3^T-p_4^T&p_4^T-p_3^T\end{array}\right.\right]  \\
\mathbb{E}_{\mathcal{G}}&\left.=\left[\begin{matrix}\bar e_{12}+\bar e_{13}&-\bar e_{12}&-\bar e_{13}&0\\-\bar e_{12}&\bar e_{12}+\bar e_{23}+\bar e_{24}&-\bar e_{23}&-\bar e_{24}\\-\bar e_{13}&-\bar e_{23}&\bar e_{13}+\bar e_{23}+\bar e_{34}&-\bar e_{34}\\0&-\bar e_{24}&-\bar e_{34}&\bar e_{24}+\bar e_{34}\\\end{matrix}\right.\right] 
\end{aligned}
$$
展开表达式为
$$
\left.u=\left[\begin{array}{c}-\bar{e}_{12}z_{12}-\bar{e}_{13}z_{13}\\\bar{e}_{12}z_{12}-\bar{e}_{23}z_{23}-\bar{e}_{24}z_{24}\\\bar{e}_{23}z_{23}+\bar{e}_{13}z_{13}-\bar{e}_{34}z_{34}\\\bar{e}_{24}z_{24}+\bar{e}_{34}z_{34}\end{array}\right.\right]
$$
​		这种方法同样存在一个问题，就是当$\frac{\partial\phi}{\partial p^*}=0$时，$p^*$并不是理想的稳定平衡点（$u=0$时误差未均收敛至0），我们可以利用$\phi(\bar{e})$的Hessian矩阵分析，参考书籍p86页。

（引理3.8）针对**K(4)−1**的编队拓扑结构，可证得在梯度控制律下图误差矩阵$\mathbb{E}_{\mathcal{G}}$在任何不正确的平衡点$p ^{*^c}$不是半正定的。

​		我们对于不正确的平衡集可以由用四个约束限制（分别对应着不同的共线情况）：
$$
\begin{array}{l}\bar{e}_{12}z_{12}+\bar{e}_{13}z_{13}=0\\\bar{e}_{12}z_{12}-\bar{e}_{23}z_{23}-\bar{e}_{24}z_{24}=0\\\bar{e}_{23}z_{23}+\bar{e}_{13}z_{13}-\bar{e}_{34}z_{34}=0\\\bar{e}_{24}z_{24}+\bar{e}_{34}z_{34}=0\end{array}
$$
（定理3.8）针对**K(4)−1**的编队拓扑结构，按照前面规定的梯度控制律，若初始条件系统不处于错误平衡集$\mathcal{U}_{eq}^{I}$，那么轨迹会收敛到正确平衡集$\mathcal{U}_{eq}^{C}$。

###  Global Convergence in 3-Dimensional Space

​		若考虑到三维空间的$K(4)$编队，需要考虑六个节点间距的可行性。稳定性的分析比较复杂，我们直接给出几个证明出的结论：

（定理3.9）$K(4)$图在三维空间的编队系统在所有不正确的平衡点$p^{*^{c}}$都是不稳定的。

（定理3.10）如果智能体初始位置处于一个平面内，则在梯度控制律下将始终停留在一个平面。

（定理3.11）如果初始智能体不在一个平面内，则通过梯度控制律可实现三维空间下的期望构型。

### Summary

​		本章利用梯度控制率，当感知变量为相对位置，控制变量为边的距离，针对2D中的三角形、K(3)+1、K(4)-1、有向多边形（环形）以及3D中的K(4)编队几乎都能实现全局收敛。当给定一个特定的图形，它已经收敛到一个期望的配置时，任何同构的图形都会收敛到期望的那一个。因此我们给不同的节点重新编号时，也不会影响期望构型的实现。

## Local Stabilization

### Inter-agent Dynamics

​		讨论将前文对于三角形构型的控制律推广到一般情况，无向拓扑下每个智能体的度为便是其收到的约束数，如下：
$$
\left.\underbrace{\left[\begin{array}{c}\vdots\\(p_i-p_j)^T\\\vdots\end{array}\right]}_{\triangleq A_i}u_i=-\frac{k_{ij}}{4}\underbrace{\left[\begin{array}{c}\vdots\\\bar{e}_{ij}\\\vdots\end{array}\right]}_{\triangleq b_i},j\in\mathcal{N}_i,i\in\mathcal{V}\right.
$$
​		当约束数过多$A_i$超定，此时方程无解，只能寻求等价的**近似最小二乘**解：
$$
\begin{aligned}
u_i&=\underset{u_i\in\mathbb{R}^2}{\operatorname*{argmin}}\left\|A_iu_i+\frac{k_{ij}}4b_i\right\|^2,i\in\mathcal{V}\\
&=-\frac{k_{ij}}4(A_i^TA_i)^{-1}A_i^Tb_i
\end{aligned}
$$
只有当$A_i^TA_i$的逆存在时才会有唯一解，可证得若图的实现是无限小刚性时$A_i^TA_i$是正定的（无限小刚性意味着节点不存在共线情况）。

（定理4.1）当期望框架$(\mathcal{G},p^{*})$是无限小刚性时，上式的最小二乘梯度控制律可以确保期望集合的局部渐进稳定性，最终会收敛到一点。

### Exponential Convergence

​		本节我们基于梯度控制律进行改进，实现刚性图的指数收敛。首先需要将一个图分为最小无穷小刚度部分与其余部分，设计梯度控制律实现相对距离误差的指数收敛。

​		定义最小无穷小刚度部分子图为$\underline{\mathcal{G}}=(\mathcal{V},\underline{\mathcal{E}})$，其余部分为$\tilde{\mathcal{G}}=(\mathcal{V},\tilde{\mathcal{E}})$，基于上下标的规定，定义误差向量为$\bar{e}=(\underline{e}^T,\tilde{e}^T)^T$，其分量为相对距离的平方与期望距离的平方差。定义势函数如下：
$$
V(p)=\frac14\bar{e}^T\bar{e}
$$
梯度控制律为
$$
\begin{aligned}
\dot{p}&=u=-\left[\frac{\partial V}{\partial p}\right]^T=-\mathbb{R}_G^T\bar{e} \\
&\mathbb{R}_{\mathcal{G}}=\frac12[\partial\bar{e}/\partial p]=[\underline{\mathbb{R}_{\mathcal{G}}}^T \text{ }\tilde{\mathbb{R}}_{\mathcal{G}}^T]^T
\end{aligned}
$$
考虑指数收敛的控制律为
$$
\dot{p}=u=-k\underline{\mathbb{R}}_{\mathcal{G}}^{T}\underline{e}-\tilde{\mathbb{R}}_{\mathcal{G}}^{T}\tilde{e},k>0
$$
此时误差动态方程为
$$
\dot{\bar{e}}=\frac{\partial\bar{{e}}}{\partial p}\dot{{p}}=-2\begin{bmatrix}k\underline{\mathbb{R}}_{\mathcal{G}}\underline{\mathbb{R}}_{\mathcal{G}}^T\underline{e}+\underline{\mathbb{R}}_{\mathcal{G}}\tilde{\mathbb{R}}_{\mathcal{G}}^T\tilde{e}\\k\tilde{\mathbb{R}}_{\mathcal{G}}\underline{\mathbb{R}}_{\mathcal{G}}^T\underline{e}+\tilde{\mathbb{R}}_{\mathcal{G}}\tilde{\mathbb{R}}_{\mathcal{G}}^T\tilde{e}\end{bmatrix}
$$
​		我们在此回顾下rigidity matrix的形式，其列对应这不同的节点，列对应这不同的边，因此矩阵的形式还与对应边的序号有关，若一个拓扑图如下所示:

![image-20231214111940526](multiAgent.assets/image-20231214111940526.png)

给定好边的序号后得到rigidity matrix如下：
$$
\left.\mathbb{R}_G=\left[\begin{array}{cccccc}p_1^T-p_2^T&p_2^T-p_1^T&0&0&0\\0&p_2^T-p_3^T&p_3^T-p_2^T&0&0\\0&0&p_3^T-p_4^T&p_4^T-p_3^T&0\\0&0&0&p_4^T-p_5^T&p_5^T-p_4^T\\p_1^T-p_5^T&0&0&0&p_5^T-p_1^T\\p_1^T-p_3^T&0&p_3^T-p_1^T&0&0\\p_1^T-p_4^T&0&0&p_4^T-p_1^T&0\\0&p_2^T-p_4^T&0&p_4^T-p_2^T&0\\0&0&p_3^T-p_5^T&0&p_5^T-p_3^T\end{array}\right.\right]
$$
​		相应证明可见书籍相关章节。

### Local Asymptotic Stability in d-Dimensional Space

​		本节讨论梯度控制律下的编队系统的局部稳定性，定义一个一般形式局部势函数：
$$
\phi(p)\triangleq\frac{k_p}2\sum_{(i,j)^e\in\mathcal{E}_+}\gamma(\|z_{ij}\|^2-\|p_j^*-p_i^*\|^2)=\frac{k_p}2\sum_{(i,j)^e\in\mathcal{E}_+}\gamma(\bar{e}_{ij})
$$
此处$k_p>0$，$\gamma:\mathbb{R}\rightarrow \mathbb{R}_+$为一个满足如下条件的函数：

- $\gamma(x)\geq0$且当且仅当$x=0$时等号成立
- $\gamma(x)$在$0$的邻域是解析的。

得到控制输入为
$$
\begin{aligned}
u_i& =-\nabla_{p_{i}}\phi_{i}  \\
&=-\left[\frac{\partial\boldsymbol{\phi}_i}{\partial p_i}\right]^T \\
&=-\left[\frac{k_p}2\sum_{j\in\mathcal{N}_i}\frac{\partial\gamma\left(\bar{e}_{ij}\right)}{\partial\bar{e}_{ij}}\frac{\partial\bar{e}_{ij}}{\partial p_i}\right]^T \\
&=k_{p}\sum_{j\in\mathcal{N}_{i}}\frac{\partial\gamma(\bar{e}_{ij})}{\partial\bar{e}_{ij}}(p_{j}-p_{i})
\end{aligned}
$$
​		若取$\gamma=\sum\bar{e}_{ij}^2$，便可得到传统的梯度控制律的表达式$u_{i}=\sum_{j\in\mathcal{N}_{i}}\bar{e}_{ij}(p_{j}-p_{i})$。

### Summary

​		本章节讨论了最小二乘意义下的基于距离的收敛，进行了数学分析；利用梯度控制律实现了局部的指数收敛，但是受限于需重新进行网络分配的条件；还提出了一个一般形式的势函数，在相关文献中对双积分器及领航-跟随模式有着更多的讲解。

​		对于梯度控制律，我们要明确它是通过将控制效果均匀分配给所有节点，因此所有节点会通过旋转或者平移来实现期望的队形配置。但是我们要明确梯度控制律无法避免收敛到局部最优的非期望平衡点，因此初始配置是梯度收敛律能否收敛的一个关键点。在这个意义上，梯度控制法通常无法将编队稳定到同构程度上。若要实现全局收敛，还需加上一些附加条件，也有相关文献进行研究与分析。

## Persistent Formations

​		前两章节内容讨论了无向图的全局及局部收敛，有向情况只讨论了多边形编队控制。对于多边形（环）图，即使相邻节点间的距离满足约束，但也可能不会得到唯一的实现配置。为了解决有向感知与执行拓扑结构下的编队控制问题，我们谈论增加依赖条件实现有向图的唯一形成。

### Acyclic Minimally Persistent Formations in 2-Dimensional Space

​		对于含有多个环的有向图如何实现特定唯一编队有较大困难，因此大多数文献聚焦在无环有向图的编队控制。本节重点研究**cycle-free (acyclic) persistence**，对于持久性而言显然图需要满足一个必要条件：底层拓扑结构（不考虑方向）是刚性的；同时，需要给出另一个必要条件，即适当地指定边的方向，使得方向约束在代理之间是可实现且一致的。因此，持久性概念是底层拓扑结构的刚性和方向分配的一致性的结合。

​		本节研究最简单的持久编队问题—三角形无环最小持久编队，我们假设leader节点1是静止的，图结构如下所示：

![](multiAgent.assets/2023-12-16-14h18m.png)

取势函数为
$$
\phi_{2}=\frac{1}{4}\bar{e}_{12}^{2},\phi_{3}=\frac{1}{4}\bar{e}_{13}^{2}+\bar{e}_{23}^{2}
$$
对传统的梯度控制律是选择最速下降方向，我们将其修改得到一个新的控制律：
$$
\begin{aligned}
\dot{p}_{2}&=u_{2}=-\left[\frac{\partial\phi_{2}}{\partial p_{2}}\right]^{T}=\bar{e}_{12}z_{12}\\
\dot{p}_{3}&=u_{3}=-Q\left[\frac{\partial\phi_{3}}{\partial p_{3}}\right]^{T}=Q(\bar{e}_{13}z_{13}+\bar{e}_{23}z_{23})\\

\end{aligned}
$$
式中$\text{}&\left.Q=\left[\begin{array}{cc}\cos\theta&-\sin\theta\\\sin\theta&\cos\theta\end{array}\right.\right]$，决定势函数下降方向。易得上式和Polygon Formations部分的控制律形式类似，因此无环持久性编队也可用那节的控制律稳定。但是，为了对不期望或退化的平衡点进行更严谨的分析，在本章中我们采用基于线性化的方法。利用以上的设置，我们可以证明在一些初始条件下代理会收敛到期望的配置。

​		我们分解平衡集为$\mathcal{E}=\mathcal{D}\cup\mathcal{U}$，其中$\mathcal{D}\triangleq\{{p}:e=0\}$，$\mathcal{U}=\mathcal{U}_{1}\cup\mathcal{U}_{2}\cup\mathcal{U}_{3}$分别对应三种不期望的平衡集：
$$
\begin{aligned}
&\mathcal U_{1} \triangleq\{p:z=0\}  \\
&\mathcal U_{2} \triangleq\{p:z_{12}=0,\bar{e}_{13}=\bar{e}_{23}=0\}  \\
&\mathcal U_3 \triangleq\{p:\bar{e}_{12}=0,\bar{e}_{13}z_{13}+\bar{e}_{23}z_{23}=0,\bar{e}_{13}\neq0\mathrm{~or~}\bar{e}_{23}\neq0\} 
\end{aligned}
$$

> 在书籍`p120`相关证明可知以上三个平衡集都是不稳定的
>
> 若$p(t_0)\notin\mathcal{C}_1\cup\mathcal{U}_3=\{p:||z_{12}||=0\}\cup\mathcal U_3$，误差均指数收敛至零。

​		当将无环最小持久编队推广至n个智能体时，前三个智能体以本节结构排列，后续编号的各自有两个出度，则可以证得$1,2,3$全局指数收敛，而节点$3$以后之间边的误差有界。

### Acyclic Minimally Persistent Formations in 3-Dimensional Space

​		对于三维空间的无环最小持久编队，借鉴[[Viet Hoang Pham文献](https://ieeexplore.ieee.org/author/37086249904)](https://ieeexplore.ieee.org/abstract/document/8105430)内容，leader和前两个follower同上一节的拓扑结构，而其余的ordinary follower均有三个出度。

​		假设期望$d^*$为定值，定义局部势函数
$$
\begin{equation*} V_{i}(\mathbf{p}_{i}^{i},\ \ldots,\ \mathbf{p}_{j}^{i},\ \ldots)=\frac{1}{2}\sum_{j\in \mathcal{N}_{i}}\phi(e_{ij},{d}^*_{ij})  \end{equation*}
$$
梯度控制律为
$$
\begin{equation*} \dot{\mathbf{p}}_{i}=\mathbf{u}_{i}=-\nabla_{\mathbf{p}_{i}}V_{i}=-\sum_{j\in \mathcal{N}_{i}}g_{ij}\mathbf{z}_{ij} \tag{4} \end{equation*}
$$
将ordinary follower与前三个agent的控制律可分别写为
$$
\begin{align*} &\dot{\mathbf{p}}_{1}=0 \\ &\dot{\mathbf{p}}_{2}=g_{21}\mathbf{z}_{12} \\ &\dot{\mathbf{p}}_{3}=g_{31}\mathbf{z}_{13}+g_{32}\mathbf{z}_{23} \\ &\dot{\mathbf{p}}_{{i}}=g_{ii_{1}}\mathbf{z}_{i_{1}i}+g_{ii_{2}}\mathbf{z}_{i_{2}i}+g_{ii_{3}}\mathbf{z}_{i_{3}i}, i\ge 4 \end{align*}
$$
***Theorem 5.1***:在上式控制律下，1、2、3智能体在初始条件不共线的情况下将指数收敛至$g_{21}=g_{31}=g_{32}=0 $。

​		接下来是对编队控制的平衡集收敛，不期望平衡集的可斥性，期望平衡集的稳定性，非循环最小持久编队的稳定性分析。

​		根据特征值可判断出不期望平衡集是不稳定的，同时有$\dot{V}_{i}=\sum_{j\in \mathcal{N}_{i}}\frac{d\phi_{ij}}{de_{\mathrm{i}j}}\frac{de_{ij}}{dt}=-\Vert\nabla_{\mathbf{p}_{i}}V_{i}\Vert^{2}-\sum_{j\in \mathcal{N}_{i}}g_{ij}\mathbf{z}_{ij}^T\dot{\mathbf{p}}_{j} =$，推理$V_i$有界，则$\dot{V}_{i}$与$\sum_{j\in \mathcal{N}_{i}}g_{ij}\mathbf{z}_{ij}^T\dot{\mathbf{p}}_{j}$均收敛至0，显然$\mathbf{P}_i(t)$会收敛至如下平衡集：
$$
\mathcal{U}_{i,eq}=\left\{\mathbf{p}_{i}\in \mathbb{R}^{d}:\nabla_{\mathbf{p}_{i}}V_{i}=\sum_{j\in \mathcal{N}_{i}}g_{ij}\mathbf{z}_{ji}=0\right\}.
$$
若$\mathcal{U}_{i,eq} = \mathcal{U}_{i,eq}^C \cup \mathcal{U}_{i,eq}^I$，其中不期望平衡集显然意味着四个点共面的情况。文献中对此进行了证明与分析，我们直接给出得到的结论。

***Theorem 5.4***:若某一时刻$p_{i_j}=p_{i_j}^*$且$\dot p_{i_j}=0(j=1,2,3)$，智能体$i$并不与其他三个智能体共面，那$\mathbf{p}_i$不会收敛至不期望平衡集。

***Theorem 5.5***:对于N个智能体，其交互图为非循环最小持久性，且对于所有$N_i={1,2,3}$，对任何$i\ge0$如果在初始条件下代理 1、2、3 不共线且代理$i$ 与其余三个不共面，那么所期望编队在梯度控制律下几乎是全局渐近稳定的。

> Barbalat's lemma:标量函数$V(\mathbf x，t)$有下界，且$\dot V(\mathbf x，t)$半负定且对时间一致连续，则$t\rightarrow \infty$时，$\dot V(\mathbf x，t) \rightarrow0$。

### Acyclic Persistent Formations in 2-Dimensional Space

​		对于前两节讨论的二三维空间下无环最小持久编队在梯度控制律下的可实现性（相对距离与期望值一致），但这不能确保编队构型满足配置，因为二维空间每个智能体需要三个约束（三维需要四个）才能实现稳定性。本节我们将在二维空间下利用上一节的梯度控制律来实现，即每个follower leader均有三个约束。

​		为了解决维度问题，需要引入一个z轴的虚轴分量。我们假设三维空间存在一个虚节点$i^v$，其与二维空间下的$i$为映射关系，如下图所示：

![image-20231219103506373](multiAgent.assets/image-20231219103506373.png)

$$
\left.\mathbf{p}_{i^\nu}=\left[\begin{array}{cc}1&0\\0&1\\0&0\end{array}\right.\right]\mathbf{p}_i+\left[\begin{array}{c}0\\0\\1\end{array}\right]h_i
$$
式中的虚拟位置$\mathbf{p}_{i}$仅用于计算，虚拟智能体间也存在一个期望间距$\bar{d}_{i^{\nu}j}^{*}=\bar{d}_{ij}^{*}+(h_{i}^{*})^2$，通常选择虚拟轴初始长度为$h_i(t_0)>0$。基于上节的符号，仍使用三维空间下无环持久编队的梯度控制律：
$$
\begin{align*} &\dot{\mathbf{p}}_{1}=0 \\ &\dot{\mathbf{p}}_{2}=g_{21}\mathbf{z}_{12} \\ &\dot{\mathbf{p}}_{3}=g_{31}\mathbf{z}_{13}+g_{32}\mathbf{z}_{23} \\ 
&\mathbf{\dot{p}}_i=\begin{bmatrix}1&0&0\\0&1&0\end{bmatrix}\mathbf{\dot{p}}_{i^\nu},i\geq4
\\
&\dot{\mathbf{p}}_{{i}^v}=g_{i^vi_{1}}\mathbf{z}_{i_{1}i^v}+g_{i^vi_{2}}\mathbf{z}_{i_{2}i^v}+g_{i^vi_{3}}\mathbf{z}_{i_{3}i^v}, i\ge 4 \end{align*}
$$

​		基于此思想，当约束多于三个以上时也可以适用，只需要增加多的虚轴即可。

### Finite-Time Convergence of Formations

​		由于全局渐进收敛不能保证时间上的快速性，研究有限时间下的收敛从而实现收敛时间的预测。结合上一节基编队构型唯一稳定性的原理，于[Pham](https://ieeexplore.ieee.org/document/8431032)研究进行拓展(李雅普诺夫意义下的有限时间稳定定理)，将一般梯度控制律的$g_{ij}$进行修改：
$$
\begin{aligned}
g_{ij}^{f_{1}}& =\frac{g_{ij}}{\|\sum_{j\in\mathcal{N}_i}g_{ij}z_{ij}\|}  \\
\text{or  }g_{ij}^{f_{2}}& =\left|g_{ij}\right|^{\alpha}\mathrm{sign}(g_{ij})\triangleq\mathrm{sig}(g_{ij})^{\alpha},\alpha\in(0,1) 
\end{aligned}
$$
式中$sig(x)=\frac{1}{1+e^{-x}}$为sigmoid函数，当$\alpha\rightarrow0$时，sig函数变退化为符号函数sign。从数学角度分析，当$|g_{ij}|\gg1\rightarrow |g_{ij}|>sig(g_{ij})^\alpha$；而$|g_{ij}|\ll1\rightarrow |g_{ij}|<sig(g_{ij})^\alpha$。因此当误差太大时，有限时间梯度率可以减小过大的控制量，同时在$|g_{ij}|<1$时可以逐步减小$\alpha$来实现加速收敛。

​		同样我们可以证明该种控制量的有限时间收敛到期望平衡集，以及不期望平衡集的可斥性。

### Summary

​		全局稳定性要求图不能含有环，因此底层拓扑应当是无环持久性的。当一个节点在二维中有超过 2 个约束，在三维中有超过 3 个约束时，传统的梯度控制法无法稳定形态。因此，我们严格要求每个节点在二维中具有 2 个约束，在三维中具有 3 个约束。为了实现编队的唯一性，我们在无环最小持久编队的基础上为每个节点增加了一个约束，对每个节点进行一种虚拟节点的计算，因此会增加计算负担。同时还提出了几种不同的势函数，对比得出梯度控制律在收敛速度上的优势。

## Formation Control via Orientation Alignment

​		基于梯度控制律，我们已经讨论过针对特定构型保证了全局稳定性，以及对一般图形进行了局部稳定性的保证。但传统的梯度控制律或修改后的梯度控制律只依赖于在局部坐标框架中测量的误差信号。因此，它完全基于传感信息，而没有与相邻代理进行任何通信。然而，正不使用任何通信的梯度控制律计算轻便，但可能无法确保对于一般图形的全局收敛性。在本章中，我们假设相邻代理之间可以进行通信。代理可以相对地感知彼此；然后，感知变量和/或执行变量可以在相邻代理之间进行交换。例通过通信的帮助，可以证明在更广泛的初始条件下可以实现（准）全局收敛。在这里，我们希望解决的关键问题是对齐代理的方向。

### Formation Control via Orientation Estimation

​		方位对齐主要包括方位的预测与方位控制，对齐后编队问题便会变为一致性的问题。
