---
title: Latex宏包pgfplots做矩形函数图像
date: 2024-08-14 19:47:58
tags: [Latex, 图像处理]
categories: 
	- 软件
	- 工具类软件
---
持续更新遇到的问题，完结或不再使用该包将删除这句话

## pgfplots

Pgfplots是一种可视化工具，可简化在文档中包含绘图的过程。基本思想是，用户提供输入数据/公式，然后pgfplots 宏包会帮助用户绘制响应的图像。

<!--more-->

### 实例一、通过函数方程绘制函数图像

```latex
%Ex1:f(x)=exp(x)
\begin{tikzpicture}
\begin{axis}
\addplot[color=red]{exp(x)};
\end{axis}
\end{tikzpicture}
```

![test-1-crop-1](https://s2.loli.net/2024/05/07/iPmhCOxRgzZGYVy.png)

其中绘制图像的语法如下：

```latex
\addplot[option] {expression of 2D function};
```

在方括号内可以传递一些选项，比如我们可以像Ex1一样，将函数图像的颜色设置为红色。我方括号是必需的，如果没有传递任何选项，则方括号之间留有空白即可。在花括号内，我们要填写的2D 函数的表达式，比如在Ex1中，我们所写的表达式是 exp⁡(𝑥) 。最后最重要的是该命令必须以分号 **;** 结尾。

### 实例二、坐标系的绘制以及3d函数图像

```latex
\documentclass{ctexart}
\pagestyle{empty}
\usepackage{pgfplots}
\begin{document}
%Ex2: put the 2D plot and the 3D plot together
\begin{tikzpicture}
	\begin{axis}
		\addplot[color=red]{exp(x)};
	\end{axis}
\end{tikzpicture}
%Here ends the furst 2D plot

\hskip 10pt

%Here begins the 3d plot
\begin{tikzpicture}
	\begin{axis}
		\addplot3[
		surf,
		]
		{exp(-x^2-y^2)*x};
	\end{axis}
\end{tikzpicture}
\end{document}
```

![test-1-crop-1](https://s2.loli.net/2024/05/07/d7OUNVRI3LWthxK.png)

```latex
\addplot3[
    surf,
]
{exp(-x^2-y^2)*x};
```

注意到这里只是将2d图像的命令后面加了一个3，读者可以多修改一下option中的参数，这里的surf指surface，声明这是一个曲面图，并表面着色。

可以看到对多个图像只需要设定多个tikzpicture作用域即可

#### 坐标系图像中的常用命令、参数与特性

- xlabel、ylabel：设定x,y坐标轴上的标志。
- 多个addplot可在同一个图中多次作图
- $$\tt{domian = a:b}$$设置 𝑥 的范围为 [𝑎,𝑏] 。即只绘制函数在 𝑥∈[𝑎,𝑏] 之间的图像；
- $$\tt{axis\,\, lines = left}$$这个命令会仅在图的左侧和底部设置轴，即我们的平面直角坐标系，而不是像我们在图片3中看到的那种默认框；
- \addlegendentry{$function（x)$​} 添加函数标签 
- \legend 按顺序批量谭家函数标签
- title在axis中为图像添加标题

### 实例三、数据图

```latex
%Ex6:plot from data
\begin{tikzpicture}
	\begin{axis}[
		title={Temperature dependence of CuSO$_4\cdot$5H$_2$O solubility},
		xlabel={Temperature [\textcelsius]},
		ylabel={Solubility [g per 100 g water]},
		xmin=0, xmax=100,
		ymin=0, ymax=120,
		xtick={0,20,40,60,80,100},
		ytick={0,20,40,60,80,100,120},
		legend pos=north west,
		ymajorgrids=true,
		grid style=dashed,
		]
		
		\addplot[
		color=blue,
		mark=square,
		]
		coordinates {
			(0,23.1)
			(10,27.5)
			(20,32)
			(30,37.8)
			(40,44.6)
			(60,61.8)
			(80,83.8)
			(100,114)
		};
		\legend{CuSO$_4\cdot$5H$_2$O}
		
	\end{axis}
\end{tikzpicture}
```



#### 数据图像中的常用命令、参数与特性![test-1-crop-1](https://s2.loli.net/2024/05/08/NzwV7TsPHLWjbJB.png)

- mark：设定图像点的形状；如square、triangle、x等
- $$\tt{ymajorgrids=true/false\,,xmajorgrids=true/false}$$​启用/禁用 𝑦,𝑥 轴上**刻度线位置上**的网格线；
- $$\tt{xmin=a, xmax=b, ymin=c, ymax=d}$$​设置 𝑥 的最小值为 𝑎 ，最大值为 𝑏 ；设置 𝑦 的最小值为 𝑐 ，最大值为 𝑑 ；
- $$\tt{coordinates \{\}}$$​设定坐标点画折线图
- \addplot[option] table {file_with_the_data.dat},使用该命令可直接通过dat数据画图

### 其他图像

pgfplots还支持散点图，直方图，3d图等图像。由于笔者暂时没用到，对这些图不甚了解，就不做阐述了。值得注意的是，在这些图像中使用dat数据格式的数据较多。

### 导言区的一些设定

```latex
\pgfplotsset{width=10cm}
```

这里指定了每一张图的宽度为10cm

```tex
\usepgfplotslibrary{external}
\tikzexternalize 
```

由于$$LATEX$$ 诞生初期并未考虑使其具备绘图功能，因此当文档中有多个pgfplot图形或它们非常复杂时，渲染它们将花费大量时间。