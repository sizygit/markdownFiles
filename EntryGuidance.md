# 高超声速再入制导

## 相关内容

再入轨迹一般分为两段，初始下降段与滑翔段。前者不能满足平衡滑翔条件且不具备侧向机动能力，后者大气密度较大以有机动能力。之间的过渡点为交班点。

归一化能量$\tilde{E}=\frac{E-E_{0}}{E_{f}-E_{0}}$

纵向规划器的功能$D_{r e f}(\tilde{E})=k D_{\max }(\tilde{E})+(1-k) D_{\min }(\tilde{E})$

$D_{r e f}=\left\{\begin{array}{c}
D_{c}+\frac{D_{1}-D_{c}}{E_{1}-E_{c}}\left(E-E_{c}\right), \tilde{E}_{c} \leq \tilde{E}<\tilde{E}_{c}+0.1 \\
k D_{\max }+(1-k) D_{\min }, \quad \tilde{E}_{c}+0.1 \leq \tilde{E}<0.9 \\
D_{f}+\frac{D_{2}-D_{f}}{E_{2}-E_{f}}\left(E-E_{f}\right), 0.9 \leq \tilde{E}<1
\end{array}\right.$

标称轨迹制导法：在线轨迹规划算法、轨迹跟踪法

Legendre 多项式

 Riccati 代数方程

轨迹优化与配点法：[直接配点法](https://blog.csdn.net/qq_35007540/article/details/105672547)是通过离散化将最优控制问题转化为非线性规划问题,是一种数值方法来求解优化问题

拉格朗日插值，变分法，伪谱法： [最优控制问题的Legendre伪谱法求解及其应用_徐少兵.pdf](..\个人文件\大创\大创资料\大创资料\最优控制问题的Legendre伪谱法求解及其应用_徐少兵.pdf)  

bang-bang控制系统：简陋的低精度控制系统

## 文献总结

### 1.滑翔式飞行器再入制导与控制方法研究

背景：

- 滑翔式飞行器拥有良好的机动能力，制导控制系统可以通过改变升力的方向来控制其飞行轨迹。
- 为降低设计难度，在再入制导设计中**攻角剖面**一般由轨迹分析**事先给定**，而**倾侧角是主要的调节指令**。攻角剖面的确定需要综合考虑飞行器的航程能力，热防护以及飞行控制等方面。在再入飞行的初期阶段热流密度较高，飞行器应采用大攻角飞行以保证飞行器受到足够大的阻力加速度来使其较快减速。之后将攻角逐渐减小至对应于最高升阻比的攻角值。

核心

- **在线参考轨迹规划器**：纵向子规划器在**阻力加速度-能量平面**内，再入走廊上下界插值得到参考轨迹（攻角确定，走廊上下界确定，自行设计走廊）；侧向子规划器对**航路点约束**采用两次倾侧角翻转策略，针对禁飞区约束采用**动态方向角误差走廊策略**。
- **基于自抗扰控制的再入轨迹跟踪制导律**：并进行适应性测试与蒙特卡洛数值仿真
- **基于滚动时域控制和间接Legendre伪谱法的轨迹跟踪制导律**：制导控制系统设计工作的一般做法是将系统分为高频率的 6-DOF 控制内回路和低频率的 3-DOF 制导外回路分别设计，两个回路的设计工作是可以分开进行的；建立飞行器的状态方程并考虑不确定性， ,在滚动时域内进行线性化转换为线性时变系统，设计性能泛函（类似LQR），进行求解，并鲁棒性分析；利用间接伪谱法进行LGL点的离散从而减小积分运算量。
- **大范围系统不确定性和外界扰动的有限时域姿态控制**:**[反馈线性化](https://www.fenice.website/archives/120#11)技术**消除运动方程的非线性，建立姿态控制器的基本模型，提出**两种结合扰动观测器的时变滑膜控制**方法。
- **主动容错控制策略**：飞行故障检测方法和再入姿态控制器重构。

### 2.基于三维剖面的再入制导方法研究

背景：

临近空间内无动力再入飞行器进行再入制导。

- 以能量为自变量的三自由度运动模型：

- 三维再入走廊建模及机动分析：

- 基于D-E剖面的轨迹快速生成方法：利用纵程与横程利用几何关系求取经纬度；纵程与横程利用瞬时曲率半径$R$ 瞬时转弯角$\Psi$及补偿项计算出轨迹与航程。

	$\left\{\begin{array}{l}
	d x=R_{2} \sin (\psi+d \psi)-R_{1} \sin \psi+C_{x} \\
	d y=R_{1} \cos \psi-R_{2} \cos (\psi+d \psi)+C_{y}
	\end{array}\right.$

简化代换后

$\left\{\begin{array}{l}
d x=2 R_{0} \sin (\beta / 2) \cos \left(\psi+\frac{d \psi}{2}\right) \\
d y=2 R_{0} \sin (\beta / 2) \sin \left(\psi+\frac{d \psi}{2}\right)
\end{array}\right.$

航程角$\beta$可以通过侧向剖面预测得到，只需要求出$\psi\quad d\psi$即可求出轨迹。

不考虑地球自转，有$m \frac{(V \cos \theta)^{2}}{R}=C_{L} q S_{r} \sin v \rightarrow R=\frac{2 m}{\rho C_{L} S_{r} \sin v}$,因此倾侧角的大小与符号决定了当前时刻的瞬时曲率半径，而其由阻力加速度剖面直接决定。

​		我们主要讨论利用D-E剖面求解**倾侧角**$v$（速度系与地面/发射系，即为速度滚动角），根据参考 D-E 剖面，结合阻力加速度的定义可以计算出对应于某个能量E下的参考地心距$r_0$和速度$v_0$。以$D=E_0+k(E-E_0)$表示的D-E剖面，近似有$v=\cos ^{-1}\left[\frac{1}{D} \frac{1}{(L / D)}\left(g-\frac{V^{2}}{r}\right)\right]$，即可求解出积分得到$\psi=\int_{E_{0}}^{E}\left(-\frac{\sqrt{L^{2}-\left(g-\frac{V^{2}}{r}\right)^{2}}}{m D V^{2}}\right) d E$.根据定义，有初始时刻的转弯角。则利用式(2.35)和(2.37)即可求出沿设计的 D-E 剖面飞行至任意能量处对应的转弯半径和转弯角。

​        纵向轨迹规划的最终目的是求取倾侧角的大小，根据当前迭代的轨迹长度S解算$D_{ref}$，即解算k值；侧向轨迹规划的任务是求解倾侧角方向，确定合适的反转时机来使飞行器飞过航路点、避开禁飞区最终到达目标点。 侧倾角符号改变不影响升力的大小，仅改变其方向。

​		在已知纬度和航向角剖面的情况下，可以计算实际飞行弹道的纵程R ，通过引入R来达到修正待飞航程S的目的。(选择$S^{(0)}=R_d$,得到初始D-E剖面;调整飞行轨迹长度$S^{i+1}=S^{i}+(S^{0}-R^{(i)})$，继续求解纵向轨迹规划获得修正后的D-E剖面。

## 


```matlab

function [x,w,P]=lglnodes(N)
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%
% lglnodes.m
%
% Computes the Legendre-Gauss-Lobatto nodes, weights and the LGL Vandermonde 
% matrix. The LGL nodes are the zeros of (1-x^2)*P'_N(x). Useful for numerical
% integration and spectral methods. 
%
% Reference on LGL nodes and weights: 
%   C. Canuto, M. Y. Hussaini, A. Quarteroni, T. A. Tang, "Spectral Methods
%   in Fluid Dynamics," Section 2.3. Springer-Verlag 1987
%
% Written by Greg von Winckel - 04/17/2004
% Contact: gregvw@chtm.unm.edu
%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Truncation + 1
N1=N+1;
% Use the Chebyshev-Gauss-Lobatto nodes as the first guess
x=cos(pi*(0:N)/N)';
% The Legendre Vandermonde Matrix
P=zeros(N1,N1);
% Compute P_(N) using the recursion relation
% Compute its first and second derivatives and 
% update x using the Newton-Raphson method.
xold=2;
while max(abs(x-xold))>eps
    xold=x;
        
    P(:,1)=1;    P(:,2)=x;
    
    for k=2:N
        P(:,k+1)=( (2*k-1)*x.*P(:,k)-(k-1)*P(:,k-1) )/k;
    end
     
    x=xold-( x.*P(:,N1)-P(:,N) )./( N1*P(:,N1) );
             
end
w=2./(N*N1*P(:,N1).^2);

```

### 3.基于外环迭代的再入轨迹规划

流程如下：

1. 首先进行初始下降段的轨迹规划，该过程采用名义攻角方案，清册角选用常值倾侧角方案。倾侧角大小通过不断选值试出来。
2. 滑翔段轨迹规划采用如下流程：首先以大圆弧长度$R_d$作为航程初值，利用$S=-\int \frac{1}{D_{r e f}(\tilde{E})} d E=\left(E_{0}-E_{f}\right) \int \frac{1}{D_{r e f}(\tilde{E})} d \tilde{E}$ 求解出$D(\tilde{E})$剖面$\rightarrow$纵向规划器中利用$\nu=\arccos\bigg[\dfrac{1}{b}\dfrac{D}{L}\big(\ddot{D}-a\big)\bigg]$ 求出倾侧角$\nu$大小$\rightarrow$侧向规划器中利用降阶侧向模型求出反转点时机，得到航向角$\sigma$与经纬度$\lambda.\phi$,对比与目标的纵程误差$\rightarrow$修正
