---
title: matlab笔记
date: 2023-02-07 11:03:30
tags:categories:
- Work
tags: 
- Matlab
---

# Matlab符号计算

## 1.符号对象简介

### 符号对象

符号对象属于MATLAB语言中数据类型之一。符号对象是符号的字符串表示。符号对象主要用于表示：

符号常量，符号变量，符号函数，各种符号表达式

###  符号运算和数值运算的差别

- 符号运算的结果用分数，幂指数等形式表示。

- 表达式中有一个为符号数据，则整个表达式的计算结果是一个符号数据。

- 数值型表达式计算的结果为数值型数据（包括整型、浮点型）。

可以看出：当采用符号运算时并不直接给出表达式的数值结果，而是给出符号表达。如果要查看符号b所代表的数值，可以使用double()函数。

符号运算的结果用分数，幂指数等形式表示。

### 符号变量和符号表达式的生成

符号变量是在MATLAB的符号计算中内容可变的符号对象。符号变量与数值变量名称的命名规则相同。可以用命令`sym`或者`syms`来建立符号变量，分别用于单个符号变量和多个符号变量的定义.

### “查找”和 “替换”字符变量函数用法

1. `symvar`函数用于确定符号表达式中符号变量

```matlab
C = symvar(expr) %搜索表达式 expr，查找除 i、j、pi、inf、nan、eps 和公共函数之外的标识符。这些标识符是表达式中变量的名称。symvar 返回字符向量元胞数组 C 中的标识符。如果 symvar 找不到标识符，则 C 是一个空的元胞数组。
```

2. `subs`函数用于将符号表达式中的符号变量替换为其他符号变量或数值

```matlab
subs(s,old,new)%将符号表达式s中所有字符变量old用new替换后将结果返回s
subs(s,new)    %subs(s,new) 将符号表达式s中所有默认的字符变量（一般为一个）用new替换后将结果返回s，默认变量可以通过symvar进行查询。
```

### 符号和数值之间的转换

1. 数值型转换为符号型 `sym(x)` 或者`sym (x,’flag’)`

Flag可以是’r’-有理数，’d’-十进制数，’e’-估计误差，’f’-浮点数 

2.  字符型转换为数值型`double()`

### 符号函数

### 任意精度的计算

符号计算的优点：不会产生舍入误差，从而可以得到任意精度的数值解
符号计算的缺点：需要更多的计算时间和存储空间
MATLAB工具箱中3种不同类型的算术运算
（1）数值型：浮点数
（2）有理数类型：Maple精确符号运算
（3）Vpa型：Maple任意精度算术运算

## 2.符号表达式的替换与化简

### 化简

`expand`函数：expand(s)函数能对表达式s进行**因式展开**,常用于多项式、三角函数、指数函数和对数函数。 

`factor`函数：**因式分解**，factor(s)，s可以是正整数、符号整数、符号表达式或符号矩阵。当s为正整数时，因式分解的结果返回的是s的质数分解式。当s为符号表达式时，结果返回乘积形式。

`collect`函数：进行符号表达式**同类项合并**。

`simplify`函数：根据一定规则对符号表达式进行**化简**，它可以完成对指数、对数、三角函数等各种数学表达式的化简。

`horner`函数：将多项式转换为**嵌套格式**，该格式在多项式求值中可以降低计算时间复杂度。

### 替换

`subexpr`函数：调用格式如下：

```matlab
[r,sigma] = subexpr(expr)
%使用重复出现的字符串改写表达式expr，重复的字符串用sigma代替，改写后表达式通过r输出
[r,var] = subexpr(expr,'var')
[r,var] = subexpr(expr,var)
%使用工作空间中已有的字符变量 var或者未有的变量’var’改写表达式expr，重复的字符串用var表示，改写后的表达式通过r输出。
```

`subs`函数：

```matlab
R= subs(s,new)
%使用新的符号变量new代替s表达式中所有默认的变量，即由symvar函数返回变量。
 subs(s,old,new)
%使用新的符号变量new代替s表达式中所有变量old。
```

## 3.符号函数图像绘制

### 3.1符号函数曲线绘制

MATLAB中`ezplot`函数和`ezplot3`函数分别实现符号函数二维和三维曲线的绘制。(在matlab中推荐使用`fplot`函数，此函数输入参数指定为**命名或匿名函数的函数句柄**。)

> 对于`ezplot`,其输入参数f的选取，
>
> 在使用**函数句柄传递**时，必须使用数组幂、数组乘法和数组除法运算符 (`.^, .*, ./`)，因为在有字符向量或字符串输入的情况下 `ezplot` 不改变语法。而在**字符向量与字符串形式传递**函数时，`x^2` 解释为 `x.^2`。

#### 显函数绘制

```matlab
ezplot(f) %绘制函数f在区间[-2pi, 2pi]内的图像；
ezplot(f, [min, max])  %绘制函数f在指定区间[min, max] 内的图像；
ezplot(f, [min, max], fign) %在指定的窗口fign中，绘制函数f在指定区间[min, max] 内的图像。
%% fplot不支持符号表达式作为输入参数，只可以使用函数句柄传递。在绘制显函数时类似
```

#### 隐函数绘制

符号变量隐函数使用时可以使用`ezplot`,但是不能使用`fplot`，但是可以使用传入函数句柄的`fimplic`隐函数绘制函数。

```matlab
ezplot(f)  %绘制函数f(x,y)=0在区间x和y在[-2pi, 2pi]内的图像；
ezplot(f, [xmin, xmax, ymin, ymax]) %绘制函数在指定区间[xmin, xmax] 、[ymin, ymax]内的图像；
```

#### 参数方程绘制

`ezplot`与`fplot`均可以绘制参数方程

```matlab
ezplot(x, y) %绘制参数方程x=x(t), y=y(t)在区间[0, 2pi]内的曲线；
ezplot(x, y，[tmin, tmax]) %绘制参数方程x=x(t), y=y(t)在区间[tmin, tmax]内的曲线；
fplot(funx,funy,tinterval) %将在指定区间绘图。将区间指定为 [tmin tmax] 形式的二元素向量。
```

#### 三维曲线绘制

使用`ezplot`与`fplot`均可绘制三维曲线，区别也只是后者只能传入命名函数或者匿名函数句柄

```matlab
ezplot3(x, y, z), %绘制参数方程x=x(t), y=y(t)，z=z(t)在区间[0, 2pi]内的图像；
ezplot3(x, y，z, [tmin, tmax])  %绘制参数方程x=x(t), y=y(t), z=z(t) 在区间[tmin, tmax]内的曲线；
ezplot3(…, ‘animate’) %生成空间曲线的动态轨迹。
```

### 3.2符号函数曲面绘制

MATLAB中`ezmesh`、`ezmeshc`、`ezsurf`、`ezsurfc`函数实现三维网格图，曲面图的绘制，其中后两者会绘**制等值线**图。

有相应的`fmesh`，`fsurf`函数

```matlab
ezmesh(f), %绘制函数f(x,y)的图像
ezmesh(f，domain), %指定区域绘制函数f(x,y)的图像
ezmesh(x, y, z), %在默认区域绘制三维参数方程图像
ezmesh(x,y,z，[smin, smax, tmin, tmax])
ezmesh(x,y, z, [min, max]) %在指定区域绘制三维参数方程的图像。

ezmeshc(f), 绘制函数f(x,y)在默认区域[-2pi, 2pi] 的图像
ezmeshc(f，domain), 指定区域绘制函数f(x,y)的图像，domain为4×1或2×1数组
ezmeshc(x, y, z), 在默认区域绘制三维参数方程图像
ezmeshc(x, y, z， [smin, smax, tmin, tmax])或ezmeshc(x,y, z, [min, max] %在指定区域绘制三维参数方程的图像。
ezmeshc(…, n)，%指定绘图的网格数，默认为60
ezmeshc(…, ‘circ’)%在以指定区域为中心的原盘上绘制图形
```

### 3.3 等值线绘制

MATLAB中`ezcontour`和`ezcontourf`这两个函数分别用于绘制等值线和带有**区域填充**的等值线。以`ezcontour`函数为例进行介绍。同时也有相应的`fcounter`函数（调整Fill参数即可实现区域填充）。

```matlab
ezcontour(f), %绘制符号二元函数f(x,y)在默认区域内的等值线图像
ezcontour(f，domain), %绘制符号二元函数f(x,y)在指定区域内的等值线图像
ezcontour(…, n), %绘制等值线，并指定等值线数目。

fcontour(@(x,y) erf((y+2).^3) - exp(-0.65*((x-2).^2+(y-2).^2)),'Fill','on');
```

## 4.符号微积分

---

#### 4.1符号表达式求极限

函数`limit`用于求符号表达式的极限，该函数的调用格式如下：

```matlab
Limit(F, x, a)，%当x趋近于a时表达式F的极限。
Limit(F, a)，%当F中的自变量趋近于a时表达式F的极限，自变量由symvar函数确定。
Limit(F)，%当F中的自变量趋近于0时表达式F的极限，自变量由symvar函数确定。
Limit(F, x, a, ‘right’)，%当x从右侧趋近于a时F的极限。
Limit(F, x, a, ‘left’)，%当x从左侧趋近于a时F的极限。
```

#### 4.2符号微分

函数`diff`用于函数求导和求微分，包括一元函数和多元函数。该函数的调用格式如下：

```matlab
diff(S)，%求表达式S的导数，自变量由symvar确定。
diff(S, ‘v’)，%求表达式S对指定变量v的导数，或者diff(S, sym(‘v’))。
diff(S, n)，%求S的n阶导数
diff(S, ‘v’,n) %求S对v的n阶导数，或者diff(S,n,’v’)
```

MATLAB中`Jacobian`用于计算jocobian矩阵，该函数调用格式如下：

```matlab
R=jacobian(f,v)
%如果f是函数向量，v为自变量向量，则计算f的jacobian矩阵，如果f是标量，则计算f的梯度，如果v也是标量，则结果与diff函数相同。
```

#### 4.3符号积分

函数`int`用于求表达式的积分，该函数的调用格式如下：

```matlab
R=int(S)，%求表达式S的不定积分，自变量通过symvar确定。
R=int(S,v)，%求表达式S对自变量v的不定积分。
R=int(S, a, b)，%求表达式S在区间[a,b]内的定积分。
R=int(S, v，a, b)，%求表达式S在区间[a,b]内的定积分，自变量为v。
```

#### 4.4级数求和

函数`symsum`用于级数求和，该函数的调用格式如下：

```matlab
r=symsum(s, v, a, b)，%计算变量v从a到b之间s的和。
r=symsum(s)，%自变量通过symvar确定，设其为k，则计算s从0到k-1的和。
r=symsum(s,v)，%计算表达式s从0到v-1的和。
r=symsum(s, a, b)，%计算自变量从a到b之间s的和。

symsum(x^k/factorial(k), k, 0, inf)
```

#### 4.5泰勒级数

函数`taylor`用于实现taylor级数的计算。该函数的调用格式如下：

```matlab
r=taylor(f)，%计算表达式f的泰勒级数，自变量由symvar确定，计算f在0的不超过5阶泰勒级数。
r=taylor(f, x, a)，%计算表达式f在自变量x=a处泰勒级数。
r=taylor(f, x, ‘order’, n)，%计算表达式f在自变量x处n阶泰勒级数。
```

### 5.符号方程求解

#### 5.1代数方程及方程组组求解

代数方程包括线性方程、非线性方程和超越方程等。在 MATLAB 中函数 `solve` 用于求解代数方程和方程组，其调用格式如下：

```matlab
g = solve(eq)，%求解方程 eq 的解，对默认自变量求解，输入的参数 eq 可以是符号表达式或字符串；
g = solve(eq,var)，%求解方程 eq 的解，对指定自变量求解。
g = solve(eq1,eq2,...,eqn)，%求由方程 eq1、eq2、…、eqn 等组成的系统，自变量为默认自变量；
g = solve(eq1,eq2,...,eqn,var1,var2,...,varn)，%求由方程eq1、eq2、…、eqn 等组成的系统，自变量为指定的自变量：var1、var2、…、varn。
```

#### 5.2微分方程及方程组的求解

微分方程的求解通过函数 `dsolve` 进行，该函数用于求解常微分方程。

```matlab
 r = dsolve(‘eq1,eq2,...’, ‘cond1,cond2,...’, ‘v’)
 r = dsolve ('eq1','eq2',...,'cond1','cond2',...,'v')
```

其中eq1, eq2等表示待求解的方程，默认自变量t。方程中**用D表示微分**，**D后面的数字表示高阶导数**。Cond1, cond2等表示初始值，通常表示为y(a)=b或者Dy(a)=b的形式。注意每一个方程应该带上**单引号**，结果放在结构体里。

#### 5.3复合方程求解

复合方程通过函数 `compose` 进行：

```matlab
compose(f,g)，%返回函数 f(g(y))，其中 f = f(x)，g = g(y)，x 是 f 的默认自变量，y 是 g 的默认自变量；
compose(f,g,z)，%返回函数 f(g(z))，自变量为 z；
compose(f,g,x,z)，%返回函数 f(g(z))，指定 f 的自变量为 x；
compose(f,g,x,y,z)，%返回函数 f(g(z))，f 和 g 的自变量分别指定为 x 和 y。
```

#### 5.4符号反函数求解

反方程通过函数 `finverse` 求得：

```matlab
g = finverse(f)，%在函数 f 的反函数存在的情况下，返回函数 f 的反函数，自变量为默认自变量；
g = finverse(f,v)，%在函数 f 的反函数存在的情况下，返回函数 f 的反函数，自变量为 v。
```

### 6.符号积分变换

---

#### 6.1符号拉普拉斯变换

1. 傅立叶变换

	```matlab
	F = fourier(f)，%实现函数f的傅立叶变换，如果 f 的默认自变量为 x，则返回 f 的傅立叶变换结果，默认自变量为w；如果 f 的默认自变量为w，则返回结果的默认自变量为 t。
	F = fourier(f, v)，%返回结果为v的函数。
	F = fourier(f, u, v)，%f的自变量为u，返回结果为v的函数。
	```

2. 傅立叶逆变换

	```matlab
	f = ifourier(F)，%实现函数F的傅立叶逆变换，如果F的默认自变量为w，则返回结果f的默认自变量为x，如果F的自变量为x，则返回结果f的自变量为t。
	f = ifourier(F,u)，%实现函数F的傅立叶逆变换，返回结果f为u的函数； 
	f = ifourier(F,v,u)，%实现函数F的傅立叶逆变换，F的自变量为v，返回结果f为u的函数。
	```

#### 6.2符号拉普拉斯变换

   1. 拉普拉斯变换

	```matlab
	laplace(F)，%实现函数F的拉普拉斯变换，如果F的默认自变量为t，返回结果的默认自变量为s如果F的默认自变量为s，则返回结果为t的函数。
	laplace(F, t)，%返回函数的自变量为t。
	laplace(F, w, z)，%指定F的自变量为w，返回结果为z的函数拉普拉斯逆变换
	```

   2. 拉普拉斯逆变换

```matlab
F = ilaplace(L)，%实现函数L的拉普拉斯逆变换，如果L的自变量为s，则返回结果为t的函数；如果L的自变量为t，则返回结果为x的函数。
 F = ilaplace(L, y)，%返回结果为y的函数。
 F = ilaplace(L, y, x)，%指定L的自变量为y，返回结果为x的函数。
```

#### 6.3符号Z变换

1. Z变换

```matlab
F = ztrans(f)，%如果f的默认自变量为n，则返回结果为z的函数，如果f  为函数z的函数，则返回结果为w的函数。
 F = ztrans(f, w)，%返回结果为w的函数。
 F = ztrans(f, k, w)，%f的自变量为k，返回结果为w的函数。
```

2. Z 逆变换

```matlab
 f = iztrans(F)，%若F的默认自变量为z，则返回结果为n 的函数；如果F是n 的函数，则返回结果为k的函数。
 f = iztrans(F, k)，%指定返回结果为k的函数。
 f = iztrans(F, w, k)，%指定F的自变量为w，返回结果为k的函数。
```

### 7.符号函数计算器

#### 7.1单变量符号函数计算器 

在命令窗口中执行`funtool`即可调出单变量符号函数计算器。单变量符号函数计算器用于对单变量函数进行操作，可以对符号函数进行化简、求导、绘制图形等。

控制按钮:d

f/dx=: 求函数f 的导数。
int f:  求函数f的积分。
simplify: 对函数f化简
num f: 函数f的分子
den f: 函数f的分母
第2行为函数与常数a之间的操作
第3行为函数f与函数g之间的操作
第4行功能按钮作用如下：
Insert 将函数f加入到函数列表中
Cycle 将函数列表中下一个函数代替f值
Delete 将f从函数列表中删去
Reset 重置计算器                     Help 在线显示帮助
Demo 演示                              Close 关闭

#### 7.2Taylor函数逼近器

输入`taylortool`即可进入taylor函数逼近器

# 控制原理

margin
