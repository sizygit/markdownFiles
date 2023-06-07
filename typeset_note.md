# LATEX 

[文字提取](https://web.baimiaoapp.com/) [公式识别1](https://www.latexlive.com/home) [公式识别2](https://www.simpletex.cn/ai/latex_ocr)

## 常用语法

```latex
\newcommand{\⟨name⟩} [⟨num⟩] {⟨definition⟩}
```

以定义你自己的命令：在命令的定义中，标记 #1 代表指定的参数。如果想使用多个参数，可以依次使用#2、……、#9 等标记。

```latex
\fontsize{⟨size⟩} {⟨base line-skip⟩}
```



### 图片引用

```latex
\begin{figure}[H]
	\centering
	\caption{a figure C} \label{ fig:ex1}
	\includegraphics[width = 7cm]{figures/可行域.png}
\end{figure}

here,the figure \ref{ fig:ex1} can understand this
```

另外 `graphicx` 宏包还提供了 \graphicspath 命令，用于声明一个或多个图片文件存放的目录，使用这些目录里的图片时可不用写路径；\includegraphics 命令的可选参数 *⟨**options**⟩* 支持 *⟨**key**⟩*=*⟨**value**⟩* 形式赋值，常用的参数如：

>width=*⟨**width**⟩*   将图片缩放到宽度为 *⟨**width**⟩*
>
>height=*⟨**height**⟩*  将图片缩放到高度为 *⟨**height**⟩*
>
>scale=*⟨**scale**⟩*      将图片相对于原尺寸缩放 *⟨**scale**⟩* 倍     这里没有单位
>
>angle=*⟨**angle**⟩*   令图片逆时针旋转 *⟨**angle**⟩* 度

多个图片排版 需要使用`\sufigure`和`minipage`环境,需要加入**subfigure**宏包

```latex
\begin{figure}[H]
	\caption{a figure C} \label{fig:xxx}
	\subfigure{
		\begin{minipage}[b]{.3\linewidth}
			\centering
			\includegraphics[scale=0.25]{figures/example-image-c.pdf}
		\end{minipage}
	} \quad \quad \quad  \quad  \qquad  
	\subfigure{
		\begin{minipage}[b]{.3\linewidth}
			\centering
			\includegraphics[scale=0.25]{figures/example_figure.jpg}
    \end{minipage}
	}
\end{figure}
as figure \ref{fig:xxx} we can know
```



### 交叉引用

交叉引用是 LATEX 强大的自动排版功能的体现之一。在能够被交叉引用的地方，如章节、公

式、图表、定理等位置使用 **\label** 命令：

\label{*⟨**label-name**⟩*}

之后可以在别处使用 **\ref** 或 **\pageref** 命令，分别生成交叉引用的编号和页码：

\ref{*⟨**label-name**⟩*} \pageref{*⟨**label-name**⟩*}

```latex
A reference to this subsection

\label{sec:this} looks like:

``see section~\ref{sec:this} on

page~\pageref{sec:this}.''

A reference to this subsection looks like:

“see section 3.3 on page 22.”
```

**为了生成正确的交叉引用，一般也需要多次编译源代码。**

\label 命令可用于记录各种类型的交叉引用，使用位置分别为：

**章节标题** 在章节标题命令 \section 等之后紧接着使用。

**行间公式** 单行公式在公式内任意位置使用；多行公式在每一行公式的任意位置使用。

**有序列表** 在 enumerate 环境的每个 \item 命令之后、下一个 \item 命令之前任意位置使用。

**图表标题** 在图表标题命令 \caption 之后紧接着使用。

**定理环境** 在定理环境内部任意位置使用。

在使用不记编号的命令形式（\section*、\caption*、带可选参数的 \item 命令等）时不

要使用 \label 命令，否则生成的引用编号不正确。

### 公式

#### 行内公式

The Pythagorean theorem is `$a^2 + b^2 = c^2$`.

*\mathbf{R}* 实现加粗

#### 行间公式

- **equation** 环境为公式自动生成一个编号，这个编号可以用 **\label** 和 **\ref** 生成交叉引用，amsmath 的 **\eqref** 命令甚至为引用自动加上圆括号；还可以用 \tag 命令手动修改公式的编号，或者用 \notag 命令取消为公式编号（与之基本等效的命令是 \nonumber或这直接使用**equation***环境）。不允许换行 9 9 7 9 16 6 6 2 7 13 8 7 = 99

```latex
\begin{equation}
	\label{equ1}
	\begin{matrix}
		1 & 2 \\ 3 & 4
		\end{matrix}   \qquad
		\begin{pmatrix}
		x_{11} & x_{12} & \ldots & x_{1n}\\
		x_{21} & x_{22} & \ldots & x_{2n}\\
		\vdots & \vdots & \ddots & \vdots\\
		x_{n1} & x_{n2} & \ldots & x_{nn}\\
		\end{pmatrix} \times  1  = ?
\end{equation}
here,the eqref \eqref{equ1} can understand this \ref{equ1}
\left(  \right)
```

- **align** 环境：使用`\\`换行，它将公式用 `&` 隔为两部分并对齐。分隔符通常放在**等号左边**，align 环境会给每行公式都编号。我们仍然可以用 \notag 去掉某行的编号。

- **gather**环境：使用`\\`换行，无对齐的多行公式环境，默认给每行都编号。

另一个常见的需求是将多个公式组在一起公用一个编号，编号位于公式的居中位置。为此，amsmath 宏包提供了诸如 **aligned**、**gathered** 等环境，*与 equation 环境套用*。以 -ed 结尾的环境用法与前一节不以 -ed 结尾的环境用法一一对应。我们仅以 aligned 举例：

```latex
\begin{equation}\left\{
\begin{aligned}
    X^{\prime}&=f\frac{X}{Z} \\
    Y^{\prime}&=f\frac{Y}{Z} 
\end{aligned}\right.
\end{equation}注意大括号不能在aligned环境内
```

split 环境和 aligned 环境用法类似，也用于和 equation 环境套用，区别是 split 只能将每行的一个公式分两栏，aligned 允许每行多个公式多栏

- **cases**环境：用于分段函数，**&**后跟条件，自动会补上左侧的分界符。 使用**aligned**与定界符***\left\{***也可以起到同样的效果。

  ```latex
  \begin{equation}
  	f = \left\{
  	\begin{aligned} 
  		&0 ,  &x  \neq  0, \\
   	&1,   &x =  0.  
  	\end{aligned}\right.
  \end{equation} 
  
  \begin{equation}
  	f = 
  	\begin{cases}
  		0 & x \neq  0,\\
  		1 & x = 0.
  	\end{cases}
  \end{equation}
  ```

  amsmath 提供了一个 **\boldsymbol** 命令（由调用的 amsbsy 宏包提供），用于打破 **\boldmath**的限制，在公式内部将一部分符号切换为粗体
  
  Havesine公式
  
  $
  d=2 \operatorname{Rarcsin}\left(\sqrt{\sin ^{2}\left(\frac{\varphi_{2}-\varphi_{1}}{2}\right)+\cos \left(\varphi_{1}\right) \cos \left(\varphi_{2}\right) \sin ^{2}\left(\frac{\lambda_{2}-\lambda_{1}}{2}\right)}\right)
  $
  
  $x^{(0)} = (x^{(0)}(1),x^{(0)}(2),\ldots, x^{(0)}(n))$
  
  $x^{(1)} (k) = \sum\limits_{i=1}^k x^{(0)} (i), \qquad k=1,2,\ldots,n.$
  
  $\frac{\text{d}x^{(1)}}{\text{d}y} + ax^{(1)} = b$
  
  $z^{(1)}(k)=\alpha x^{(1)}(k)+(1-\alpha) x^{(1)}(k-1) \quad k=2,3, \ldots, n$
  
  $\hat{x}^{(1)}(k)=\left[x^{(0)}(1)-\frac{b}{a}\right] e^{-\alpha(k-1)}+\frac{b}{a} \quad k=2,3, \cdots, n$
  
  $\hat{x}^{(0)}(k)=\hat{x}^{(1)}(k)-\hat{x}^{(1)}(k-1) \quad k=2,3,4, \cdots, n$
  
  $\hat{x}^{(0)}(k)=\left[x^{(0)}(1)-\frac{b}{a}\right] e^{-a(k-1)}\left(1-e^{a}\right) \quad k=2,3,4, \cdots, n$
  
  $\lambda(k)=\frac{x^{(0)}(k-1)}{x^{(0)}(k)} \quad, k=2,3, \ldots, n$ 残差
  
  $X=\left(e^{\frac{-2}{n+1}}, e^{\frac{2}{n+1}}\right)$ 区间
  
  $y^{(0)}(k)=x(0)(k)+c \quad, k=1,2, \ldots, n$ 平移变换
  
  
  
  
  
  $C_{t}=f_{t} * C_{t-1}+\left(1-f_{t}\right) * \tilde{C}_{t}$
  
  $f_t = \sigma (U_f\times X_t + W_f \times h_{t-1} + b_f)$
  
  $i_{t}=\sigma\left(U_{i} \times X_{t}+W_{i} \times h_{t-1}+b_{i}\right)$
  $\tilde{c}_{t}=\tanh \left(U_{\mathrm{c}} \times X_{t}+W_{c} \times h_{t-1}+b_{c}\right)$
  $c_{t}=f_{t} c_{t-1}+i_{t} \tilde{c}_{t}$
  
  $o_{t}=\sigma\left(U_{o} \times X_{t}+W_{o} \times h_{t-1}+b_{o}\right)$
  $h_{t}=o_{t} \times \tanh \left(C_{t}\right)$
  
  $Z^\ast = Z_{GM} +\zeta (Z_{GM} - Z_{LSTM})$
  
  

### 代码环境

`\usepackage{listings}`

```latex
\begin{lstlisting}[language = Matlab]

 dx = [ -28 -21 -14 -7 0 7 14 21 28 ];

 dy = [-8 -4 0 4 8];

\end{*lstlisting}
```



### 列表

基本的有序和无序列表环境 **enumerate** 和 **itemize**，两者的用法很类似，都用 **\item** 标明每个列表项。enumerate 环境会自动对列表项编号。

```latex
\begin{enumerate}

\item …

\end{enumerate}
```

关键字环境 **description** 的用法与以上两者类似，不同的是 \item 后的可选参数用来写关键字，以粗体显示，一般是必填的：

```latex
\begin{Theorem} \label{thm:latex}
  dsadasda
\end{Theorem}
\begin{Lemma} \label{thm:tex}
\TeX .
\end{Lemma}
\begin{proof}
The proof of theorem.
\end{proof}
\begin{description}
  \item[define1]
  \item[define2]  
\end{description}
```



### 表格

三线表示例

```latex
\begin{center}
	\begin{tabular}{cc}
	\toprule[1.5pt]
	\makebox[0.3\textwidth][c]{符号}	&  \makebox[0.4\textwidth][c]{意义} \\
	\midrule[1pt]
	$ W $	    	& 某一小时内该路段运行总收益-总成本   \\ 
	$ W_0 $	    & 区分高峰和低峰的一个临界值  \\ 
	$ P $	    	& 线路在一小时内所有站的总上车人数 \\ 
	$ x $	    	& 线路在一小时内的车辆数 \\  
	$ T_t $	    & 长期趋势项 \\ 
	$ M_t $	    & 简单移动平均项 \\ 
	\bottomrule[1.5pt]
	\end{tabular}
\end{center}
```

排版表格最基本的 **tabular** 环境用法为：

```latex
\begin{tabular}[⟨align⟩]{⟨column-spec⟩} ⟨item1⟩ & ⟨item2⟩ & … \\
\hline
⟨item1⟩ & ⟨item2⟩ & … \\
\end{tabular}
```

#### 列格式

> l/c/r                单元格内容左对齐/居中/右对齐，不折行
>
> p{*⟨**width**⟩*} 	单元格宽度固定为 *⟨**width**⟩*，可自动折行
>
> |                       绘制竖线
>
> @{*⟨**string**⟩*}       自定义内容 *⟨**string**⟩*

**@** 格式可在单元格前后插入任意的文本，但同时它也消除了单元格前后额外添加的间距。**@**格式可以适当使用以充当“竖线”。特别地，**@{}** 可直接用来消除单元格前后的间距：

#### 列宽

在控制列宽方面，LATEX 表格有着明显的不足：l/c/r 格式的列宽是由文字内容的自然宽度决定的，而 p 格式给定了列宽却不好控制对齐（可用 array 宏包的辅助格式）。可使用tabularx包。

这里还推荐表格整体缩放的**\resizebox**命令 其在**\usepackage{*graphicx*}**宏包中调用

```latex
\resizebox*{\linewidth}{!}{

\begin{tabular}{ ......}
......
}

```



#### 横线

**\hline** 。另外 **\cline{<i>-<j>}** 用来绘制跨越部分单元格的横线.

在科技论文排版中广泛应用的表格形式是三线表，形式干净简明。三线表由 booktabs 宏包支持，它提供了 **\toprule**、**\midrule** 和 **\bottomrule** 命令用以排版三线表的三条线，以及和\cline 对应的 **\cmidrule**。除此之外，最好不要用其它横线以及竖线

#### 嵌套表格与合并单元格

* 横向合并单元格较为容易，由 **\multicolumn** 命令实现：
  `\multicolumn{⟨n⟩}{⟨column-spec⟩}{⟨item⟩}`
  其中 ⟨n⟩ 为要合并的列数，⟨column-spec⟩ 为合并单元格后的列格式，只允许出现一个 l/c/r 或p 格式。如果合并前的单元格前后带表格线 |，合并后的列格式也要带 | 以使得表格的竖线一致。形如 **\multicolumn{1}{⟨column-spec⟩}{⟨item⟩}** 的命令可以用来修改某一个单元格的列格式。重复划线可以加粗。

* 纵向合并单元格需要用到 **multirow** 宏包提供的 **\multirow** 命令：
  `\multirow{⟨n⟩}{⟨width⟩}{⟨item⟩}`
  ⟨width⟩ 为合并后单元格的宽度，可以填 * 以使用自然宽度。

#### 行距控制

- LATEX 生成的表格看起来通常比较紧凑。修改参数 \arraystretch 可以得到行距更加宽松的表格`\renewcommand\arraystretch{1.8}`

- 另一种增加间距的办法是给换行命令 `\\` 添加可选参数，

  ```latex
  Head & lines \\ [6pt]
  ```

  在这一行下面加额外的间距，适合用于在行间不加横线的表格：但是这种换行方式的存在导致了一个缺陷——**表格的首个单元格不能直接使用中括号** **[]**，否则 \\ 往往会将下一行的中括号当作自己的可选参数，因而出错。如果要使用中括号，应当放在花括号 {} 里面。或者也可以选择将换行命令写成 \\[0pt]

#### 表格斜分

宏包：\usepackage{diagbox}
环境：tabular
适用方法：\diagbox[斜线方形]{分区1内容}{分区2内容}{分区3内容}...
说明：斜线的防线有4种，有几条斜线分几个区，由后面的{}个数确定。

```latex
\begin{tabular}{|l|c|c|c|}
\hline
\diagbox{学科}{成绩}{姓名} & ABC & BBC & CED \\
\hline
数学 & 99 & 89 & \diagbox{ce1}{ce2} \\
\hline
化学 & \diagbox[dir=SW]{ce3}{ce4} & 67 & 82 \\   % 注意方向参数是[]
\hline
\end{tabular}
```



## 论文组成部分

### 题目

### 摘要                           

综述：定性问题，阐述使用的总模型或基本方法，阐释创新点

具体问题的解答：针对各问进行阐述，这是一个什么问题基于**理论，建立\*\*模型，结合\*\*数据，采用\*\*方法进行求解，得到了\*\*结果

对模型进行简要评价：对优缺点进行分析

### 关键词

3~5个

### Literature Review

### Our Work

### 问题重述                    

问题背景与问题重述，反映自己对整个问题的理解，最多半页

### 问题分析	     

只是写一写大致思路，加上一个流程图表达自己的思维过程，不需要把模型进行过于透彻的分析      

### **Assumptions**                

Through the full analysis of the problem, in order to simplify our model, we make the following reasonable assumptions.

对实际问题进行**必要的合理的简化**，一般8~10条，不要太多，也不要假设明确给出的条件

###  **Notations**                 

只说明大部分模型会使用的符号，解释也不要太长

### 模型建立

基于问题1 的求解，可用流程图+描述说明

### 模型求解

### 结果分析，做出结论

根据实际问题分析结果是否合理，对现实做出评价，提出建议

### 模型评价                      优缺点

写写模型的优点和缺点，可以网上搜索

### 模型改进          

根据之前的缺点部分稍微提下改进方案             

### 引用文献

5~10篇为宜，尽量写些英文的

```latex
\addcontentsline{toc}{section}{References}
```

将参考文献加到目录

## 文献资料

全球免费数据库：

DOAJ:   http://www.doaj.org/

Open J-Gate:  http://www.openj-gate.com/Search/QuickSearch.aspx

Oaister:   http://www.oclc.org/oaister/

arXiv:   http://cn.arxiv.org

 https://www.scihub.net.cn/

https://cnki.net/

https://www.wanfangdata.com.cn/index.html?index=true

常用网站

华中科技大学外文数据库  http://202.114.9.3/dzzy/dzzy2005.nsf/waiwen?OpenPage

香港中文大学的数学中英对照http://www.cmi.hku.hk/Ref/Glossary/Mat/i.htm

美国ＭＣＭ的主页http://www.comap.com/

美国普查局 http://2010.census.gov/2010census/language/chinese-simplified.php 
美国交通统计局 http://www.bts.gov/

美国劳工统计局 http://stats.bls.gov/
美国国家农业统计署 http://www.usda.gov/wps/portal/usda/usdahome

美国国家统计局http://www.fedstats.gov/

[***\*http://citeseerx.ist.psu.edu/\****](http://citeseerx.ist.psu.edu/)

[***\*http://www.docstoc.com/\****](http://www.docstoc.com/)







[1]王晓蕾, et al."基于LSTM与GARCH族组合模型的苹果价格预测分析." *山东农业大学学报(自然科学版)* *52*.06(2021):1055-1062. doi:

Markowitz H．Foundation of ponfolio theory．Journal of finance．1991，V01．46，
No．2．469—477．

## 模型

### 1.整数规划

  决策变量 $x_i$  

  已知变量$b_j$   $c_{ij}$ 

  目标函数  $\sum_1^i x_i c_{ij} > b_j $

  约束条件



### 2.元胞自动机

#### 构成

- 邻居：某一元胞状态更新时要搜索的空间域

  冯诺依曼型（4） Moor型    扩展Moor型...

- 元胞

- 边界条件：给边界上的元胞构造出虚拟的邻居

  固定型  周期型边界 绝热型边界 映射型边界 

- 规则

#### 特征

- 离散性（时间 空间 状态）
- 同质性 （服从相同的规律分布方式相同）
- 并行性  （状态更新规则变化时是同步）
- 高维度

#### 初等元胞自动机

 一维元胞自动机的元胞半径为1 且元胞有且只有两种状态{1,0}

 256种规则 $2^8$ 

#### 实例

森林火灾模型

```matlab
%% fire
clc
clear
n = 300;                           % 定义表示森林的矩阵大小
Plight = 5e-6; Pgrowth = 1e-2;     % 定义闪电和生长的概率  
UL = [n,1:n-1]; DR = [2:n,1];      % 定义上左，下右邻居
veg=zeros(n,n);                    % 初始化表示森林的矩阵
imh = image(cat(3,veg,veg,veg));   % 可视化表示森林的矩阵
% veg = 空地为0 着火为1 树木为2
for i=1:3000 
    sum =(veg(UL,:)==1) + (veg(:,UL)==1)+(veg(:,DR)==1) +  (veg(DR,:)==1);  % 计算出所有格子有几个邻居是着火的
% 根据规则更新森林矩阵：是否树=是否树-是否着火的树+是否新生的树（0-1运算）
    veg = 2*(veg==2) - ( (veg==2) & (sum>0 |(rand(n,n)<Plight)) ) +2*((veg==0) & rand(n,n)<Pgrowth) ;  
    set(imh, 'cdata', cat(3,(veg==1),(veg==2),zeros(n)) ) %更新
    drawnow                         % 可视化表示森林的矩阵
end
```

### 3.LSTM

$C_t=f_t*C_{t-1}+i_t*C_t$

遗忘门 更新门 输出门

### 4.决策树分类



## 行文

1.总结过去参加数模的经验，每一次都有提高，不要一味地模仿上一次；
2.模仿优秀论文的行文结构；
3.图表的绘制要美观，得体；
4.问题分析问题不要大段论述，加上思维导图之类的分析；
5.美赛论文不仅有文字还有公式，图片，表格；
6.学习优秀论文，不能光看，要练习，摘抄，推广优秀图表，句子，结构(箭头例子）；
7.数据收集使用三线表表示出来，左边使用网站名称，右边写出网址；
8.目录不要单调，避免问题一，问题二等的小标题雷同，标题有新意；
9.不要无意义的多而杂公式的堆砌，公式要有文字的解释，对题目要有结合；
10.画图尽量用matlab,Python等，少用Excel，比较难看；
11.使用伪代码或者算法流程图描述算法。(

## 提交

![image-20220217152815303](image-20220217152815303.png)

## 美赛思路

$\text{SharpeRatio}=\frac{E(R_p)-R_f}{\sigma _p} $

**资本资产定价模型**

夏普(Sharpe，

w．E)【4J、林特纳(Lintner,J．)【5】和毛新(Mossin，J．)16]研究了在竞争均衡市场中，

金融资产的价格形成，提出了著名的资本资产定价理论(CapitalAsset Pricing Model，

CAPM)。他们用投资组合(或更一般的资本资产)的价格变化与“市场投资组合”

(即按每种证券的市值与市场中证券总市值之比确定权重)的价格变化之间的回

归系数来衡量证券交易的风险。他们认为“市场投资组合”是刻画证券市场总体变化

的量，理论上可由马科维茨的分析得到，实际计算时可由证券指数得到．他们证明

了在均衡市场中，市场投资组合是有效投资组合，每种组合资产的预期收益率和

它们与市场投资组合的协方差之间有线性关系，这就是资本资产定价模型．CAPM

存证券估价、投资组合的绩效的测定、资本预算和投资风险分析中得到广泛应用。

马科维茨，夏普，米勒三人因其在会融学中的巨大贡献(投资组台理论、CAPM、

M—M定理)而获1990年经济学诺贝尔奖。

**Markowitz组合投资模型**

- 均值-方差模型：用均值(即数学期望)来表示收益的好坏，用方差来度量风险的大小。
- 均值一绝对离差模型：
- 最大最小化平均绝对离差模型
- 均值．半方差模型[26]
- 均值-方差-偏度模型：
- 投资者除了希望投资组合具有最大的期望收益，最小的方差外，如果还希望具有最大的偏度可

安全一首要模型有三种形式：

- Roy一形式 ：求投资组合P的收益露低于给定生存水平R的概率达到最小
- Kataka一形式：投资组合P的收益‘低于给定生存水平R的概率不超过指定的小概率口时，要求它的生存水平达到最大
- Telser’S-形式：投资组合P的收益低于给定生存水平R的概率不超过指定的小概率a时，投资组合的期望收益达到最大

风险价值模型

VaR方法:     $\operatorname{Pr}\left(\Delta P_{\Delta t} \leq-V a R\right)=\alpha$



在一个不允许卖空的市场中，采用亏损概率的风险测量形式  p14

**基于可信性分布的投资组合问题**

# 美赛工具

## 相关软件

绘制流程图：飞书、[draw.io](https://github.com/jgraph/drawio-desktop)



三次样条差值的公式

给定数据点 $(x_i,y_i)$，其中 $i=0,1,\ldots,n$，并且 $x_i < x_{i+1}$，定义区间 $[x_i,x_{i+1}]$ 的三次样条为一个三次函数 $S_i(x)$，满足以下条件：

1. 在每个子区间 $[x_i,x_{i+1}]$ 内，$S_i(x)$ 是一个三次多项式；

2. 在 $n-1$ 个内节点处，满足 $S_i(x_{i+1})=S_{i+1}(x_{i+1})$ 和 $S_i'(x_{i+1})=S_{i+1}'(x_{i+1})$；

3. 在两个端点处，满足 $S''(x_0)=S''(x_n)=0$。

则三次样条插值函数为

$$
S(x)=
\begin{cases}
S_0(x),& x \in [x_0,x_1], \\
S_1(x),& x \in [x_1,x_2], \\
\vdots \\
S_{n-1}(x),& x \in [x_{n-1},x_n].
\end{cases}
$$

其中

$$
S_i(x)=\frac{y_{i+1}-y_i}{h_i}-\frac{h_i}{3}(2C_i+C_{i+1})+\frac{(x-x_i)^3}{6h_i}(C_{i+1}-C_i)+\frac{(x-x_i)h_i}{6}(C_i-\frac{(y_{i+1}-y_i)}{h_i}).
$$

其中 $C_i=S_i''(x_i)$，$h_i=x_{i+1}-x_i$。
