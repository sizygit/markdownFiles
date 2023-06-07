# 轨迹仿真

脚本文件常用函数：

*ndgrid*:生成二维网格
*griddedInterpolant*：网格数据插值
*scatteredInterpolant*:插入二维或三维散点数据
*interp2*:二维查表  `例如：Cx = interp2(alpha_Cx,Ma_Cx,Cx_table,8,1.4,'spline');`
*atmoscoesa*：1976 COESA 大气模型 `例如 [T, a, P, Rho] = atmoscoesa(height, action)`

$\bar{p}$


# XFLR5与matlab实现固定翼飞行器联合控制仿真

*[S-Function使用](https://zhuanlan.zhihu.com/p/511253062)*：需要编译

*MATLAB-Function*：可直接编写并使用

*Lookup Tables*:查找表进行数据插值；由break point与table data组成，前者为输入定义域，后者为输出值域。

| lookup table type                                            | description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [1-D Lookup Table](https://ww2.mathworks.cn/help/simulink/slref/1dlookuptable.html) | 逼近一维函数                                                 |
| [2-D Lookup Table](https://ww2.mathworks.cn/help/simulink/slref/2dlookuptable.html) | 逼近二维函数                                                 |
| [Direct Lookup Table (n-D)](https://ww2.mathworks.cn/help/simulink/slref/directlookuptablend.html) | 为 N 维表进行索引，以检索元素、向量或二维矩阵                |
| [Interpolation Using Prelookup](https://ww2.mathworks.cn/help/simulink/slref/interpolationusingprelookup.html) | 使用预先计算的索引和区间比值快速逼近 N 维函数。              |
| [Prelookup](https://ww2.mathworks.cn/help/simulink/slref/prelookup.html) | 计算 Interpolation Using Prelookup 模块的索引和区间比，与上行同时使用，效率更高 |
| [Lookup Table Dynamic](https://ww2.mathworks.cn/help/simulink/slref/lookuptabledynamic.html) | 使用动态表逼近一维函数                                       |
| [Sine, Cosine](https://ww2.mathworks.cn/help/simulink/slref/sine.html) | 通过利用象限波对称性的查找表方法实现定点正弦或余弦波         |
| [n-D Lookup Table](https://ww2.mathworks.cn/help/simulink/slref/ndlookuptable.html) | 逼近 N 维函数                                                |

*Dynamic Pressure*:动压模块，也可自己编写

*Product*：乘积模块

*COESA Atmosphere Model*：大气模型，注意设置单位

*3DOF (Wind Axes)*:输入为升力阻力与俯仰力矩，输出为俯仰角/速度/加速度，距离，速度，加速度。注意Wind Axes的坐标轴正方向与升力阻力方向相反。模块中设置初始环境时需注意使用弧度值

9DOF类似

*Degrees to Radians*：角度转换为弧度

*Dot Product*：点乘配合sqrt来计算模值

*Selector*：选择向量中的某一维度，在Index里面选取需要选出来的维度

*Divide*：乘除操作

Linearization Manager：**线性分析工具箱**的使用

## 拉丁方验证

