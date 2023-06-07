# 机翼参数化实验

## NACA系列机翼

​		NACA翼型是美国国家航空咨询委员会（NACA）开发的一系列翼型。每个翼型的代号由“NACA”这四个字母与一串数字组成，将这串数字所描述的几何参数代入特定方程中即可得到翼型的精确形状。NACA翼型是由厚度包络线与弧度或平均线相结合而成的。描述这个过程的方程式是$ x_u=x-y_t(x)\sin\theta \\  y_u=y_c(x)+y_t(x)\cos\theta $和$\begin{array}{ll}&x_l=x+y_t(x)\sin\theta\\ &y_l=y_c(x)-y_t(x)\cos\theta\end{array}$，$y_t(x)$为厚度函数，$y_c$为弯度线函数,注意此公式说明厚度是在垂直于弯度线/中弧线方向计算的。且$\theta=\tan^{-1}\left(\dfrac{dy_c}{dx}\right)$为弯度线斜率。

----

​		通常有4-digit，5-digit，6-series，6A-series 等，我们首先介绍NACA 4-Digit Airfoil:：NACA **MPXX**

​		几何参数如下：首位数字M代表**最大弯度**占弦长百分比，第二位数字P代表**最大弯度**距机翼**前缘的距离**占弦长的十分之几，XX代表机翼**最大厚度**占弦长的**百分比**。（例如，NACA 2412翼型的最大弯度为弦长的2%，位于距前缘0.4弦长处，而其最大厚度为弦长的12%。四位数翼型默认最大厚度位于距前缘0.3弦长处）。

​		对于后两位数字"xx"，其决定着最大厚度占弦长的百分比，形状方程如下，： 

​	$y_{t}=5 t c\left[0.2969 \sqrt{\frac{x}{c}}+(-0.1260)\left(\frac{x}{c}\right)+(-0.3516)\left(\frac{x}{c}\right)^{2}+0.2843\left(\frac{x}{c}\right)^{3}+(-0.1015)\left(\frac{x}{c}\right)^{4}\right]$

其中，$c$表示弦长，$x$表示沿弦长的距离（从0到*c*），$y_t$表示$x$处厚度的一半，**$t$表示最大厚度占弦长的百分比**。

​		弧线方程如下：

​	$y_c=\begin{cases}M\dfrac{x}{P^2}\left(2P-\dfrac{x}{c}\right),&0\le x\le Pc\\ \\ M\dfrac{c-x}{(1-P)^2}\left(1+\dfrac{x}{c}-2P\right),&Pc\le x\le c\end{cases}$

​		需要注意的是，此方程中(*x*/*c*)=1处（后缘）的厚度并不完全为零。如果需要零后缘厚度的话，可以改动其中一个系数使得所有系数之和为零。改动最后一个系数对于翼型的整体形状影响最小。翼型前缘则近似一个半径为$r=1.1019t^2$的圆柱。

​		然后我们可以计算出上下表面坐标：

$\left\{\begin{matrix}&x_U=x-y_t\sin\theta,\quad&y_U=y_c+y_t\cos\theta,\\ &x_L=x+y_t\sin{\theta},\quad&y_L=y_c-y_t\cos{\theta},\end{matrix}\right.$

​	$\theta=\arctan\bigg(\dfrac{dy_c}{dx}\bigg),$$\dfrac{dy_c}{dx}=\begin{cases}\dfrac{2M}{P^2}\left(P-\dfrac{x}{c}\right),&0\leq x\leq P c\\ \\ \dfrac{2M}{(1-P)^2}\left(P-{\dfrac{x}{c}}\right),&Pc\leq x\leq c\end{cases}$

---

NACA 5-Digit Airfoil: NACA  **LPQXX**

​		几何参数如下：XX是**最大厚度**的弦长百分比；L×0.15表示理想攻角下设计的**最佳升力系数**；P表示**最大弯度位置**，5×P表示最大弯度距前缘位置占弦长百分比。Q表示标准翼型(1)或者负弯度型(0)。

​		厚度公式如4-digit型：

​    $y_{t}=5 t c\left[0.2969 \sqrt{\frac{x}{c}}+(-0.1260)\left(\frac{x}{c}\right)+(-0.3516)\left(\frac{x}{c}\right)^{2}+0.2843\left(\frac{x}{c}\right)^{3}+(-0.1015)\left(\frac{x}{c}\right)^{4}\right]$

​		弧线方程如下：

​	$\left\{\begin{array}{rl}
\frac{y_{c}}{c} & =\frac{K_{1}}{6}\left[(x / c)^{3}-3 m(x / c)^{2}+m^{2}(3-m)(x / c)\right] \\
\frac{d y_{c}}{d x} & =\frac{K_{1}}{6}\left[3(x / c)^{2}-6 m(x / c)+m^{2}(3-m)\right]
\end{array}\right. \quad , 0 \leq(x / c) \leq m$

​	$\left\{\begin{array}{rl}
\frac{y_{c}}{c} & =\frac{K_{1}}{6} m^{3}[1-(x / c)] \\
\frac{d y_{c}}{d x} & =-\frac{K_{1}}{6} m^{3}
\end{array}\right. \quad ,m<(x / c) \leq 1$

此处m并不为最大弯度的位置，而是与最大弯度相关：$x_f=m\left(1-\sqrt{\dfrac{m}{3}}\right)$，$m$是由给定$x_f$的简单不动点迭代得到的。定义$K_1$是为了避免一个规定的$C_{li}$和$m$的前缘奇点：

​	$K_1=\dfrac{6C_{l_i}}{Q}$，

其中$Q=\dfrac{3m-7m^2+8m^3-4m^4}{\sqrt{m(1-m)}}-\dfrac32(1-2m)\Biggl[\dfrac\pi2-\sin^{-1}(1-2m)\Biggr]$

---



## 参数化方法

### 形函数线性扰动法(H-H)

翼型利用基准翼型、形函数及其系数定义。一般用Hicks-Henne 形函数。

### 特征参数描述法(PARSEC)

特征参数描述法是通过一系列特征参数确定的解析函数获得翼型坐标的方法。

### 正交基函数法(OBF)

​		在数学中，正交函数是指在一定的条件下，函数之间的内积为零的一组函数。具体来说，对于定义在区间$[a,b]$上的两个函数$f(x)$和$g(x)$，它们的内积定义为：$\langle f,g\rangle=\int_a^b f(x)g(x)dx$。

​		一组正交函数通常具有如下特点：

1. 任意两个正交函数的内积为0。
2. 正交函数组成的集合通常是线性无关的。
3. 一组正交函数可以用于函数的逼近和拟合，可以将待拟合函数表示为正交函数的线性组合。

### 类别形状函数变换法(CST)

Bernstein多项式的定义如下：

$B_i^n(x) )=\binom{n}{i}x^{i}(1-x)^{n-i},\quad i=0,1,\ldots,n.$

其中，$n$为多项式的次数，$t$为自变量，$i$为多项式中$t$的次数，$\binom{n}{i}$为组合数，表示从$n$个元素中选取$i$个元素的组合数。

Bernstein多项式的性质如下：

1. $B_{i,n}(t) \geq 0$，且$\sum_{i=0}^nB_{i,n}(t)=1$；
2. $B_{i,n}(t)$在$[0,1]$内是单调不降的；
3. $B_{i,n}(t)$在$[0,1]$内的任意导数都是非负的；
4. 当$t=0$或$t=1$时，只有一个$B_{i,n}(t)$不为零，其余的都为零。

由于Bernstein多项式具有良好的性质，可以用来构造Bézier曲线和曲面，以及在计算机图形学中进行其他操作。

$\begin{gathered}
\left\{\begin{array}{l}{{y_{u}=C_{N2}^{N1}(x)S_{u}(x)+x y_{teu}}}\\ {{y_{l}=C_{N2}^{N1}(x)S_{l}(x)+x y_{tel}}}\\ {{C_{N2}^{N}(x)=x^{N l}(1-x)^{N2}}}\\ {{S_{u}(x)=\sum_{0}^{n_u}A_{u}(i)\dfrac{n_u!}{j!(n_u-j)!}x^{i}(1-x)^{n_u-i}}} \\
S_l(x)=\sum_0^{n_l}A_l(j)\dfrac{n_l!}{j!(n_l-j)!}x^i(1-x)^{n_l-j}  \end{array}\right.
\end{gathered}$

参数规律[(1)](https://kns.cnki.net/KXReader/Detail?invoice=cNuK1MctXtCIB%2FM23V9t4Gcg6cVPuNuRyuSUxEA7SOjhJ6mRjzPN70hjCQoo5E7F21%2Bg%2B01tg8eyam0gyVxIDQskrCuC3RgBpLCWkmBwIa1tWgmJAGMlXJo13SYQhxTI7f1xgr82NEZZFe%2F%2Ba2I7lSmfuyjcectIfk8gZxN%2B9FA%3D&DBCODE=CJFD&FileName=HKJJ201804004&TABLEName=cjfdlast2018&nonce=BFE4ED7AA0B54927AA96BFB5C554844A&uid=&TIMESTAMP=1681972429749)：

- $N_1 =0 $ 可得到一个圆鼻翼型，$N_2 =1 $ 可得到尖的翼型后缘，
- $x y_{teu}=0$时，后端封闭
- 应用中发现:对于一般的超临界翼型, 如RAE2822, 使用6阶、8阶或10阶CST拟合时, 误差范围都足以满足风洞模型加工精度要求, 而且CST阶数越高拟合误差越小;但对于前缘较钝的超临界翼型, 如RAE5214, CST参数化方法的拟合误差一直到12阶都无法完全满足风洞模型加工精度要求
- 

### 贝塞尔曲线法

[贝塞尔曲线](https://juejin.cn/post/6844903666361565191) [B样条曲线](https://juejin.cn/post/6844904170256859143)NURBS 曲线-非均匀有理B样条曲线

- 三次均匀B样条曲线，若给定9个控制点，绘制6段三次B样条曲线及其连接点。各段曲线实现自然连接。第一次计算使用$P_0P_1P_2P_3$这4个控制点生成第一段B样条曲线，然后向后移动一个控制点，使用$P_1P_2P_3P_4$这4个控制点生成第二段B样条曲线，两段B样条曲线会自然形成平滑连接。B样条曲线的其余部分依此类推，直至使用$P_5P_6P_7P_8$这4个控制点生成第6段B样条曲线。因此改变一个控制点，会影响到周围四段曲线的形状；四个控制点共线可形成一条直线；三控制点重合会使曲线通过控制点。

​		B样条曲线不能精确表示抛物面以外的二次曲线弧，因此有了非均匀有理B样条曲线。NURBS统一了B样条方法与Bezier方法，引入了权因子。

​		$\boldsymbol{C}(u)=\dfrac{\sum_{i=0}^n N_{i,p}(u)\boldsymbol{P}_iw_i}{\sum_{i=0}^{n}N_{i,p}(u)w_i}$

​		其中，$n+1$ 是控制点的数量，$p$ 是阶数，$\boldsymbol{P}_i$ 是第 $i$ 个控制点的坐标，$w_i$ 是第 $i$ 个控制点的权重，$N_{i,p}(u)$ 是第 $i$ 个 B-Spline 基函数在参数 $u$ 处的取值。

​		在优化过程中，节点向量$\boldsymbol{U}=\{0,0,0,0,u_1,\dots,u_i,1,1,1,1\}$,因此只需要设计控制点位置与权重。

​		可以利用python的geomdl库来帮助实现。



