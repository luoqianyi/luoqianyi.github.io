# LaTex 语法使用指南

💭 [LaTex 中文介绍.pdf（全）](https://wugenqiang.gitee.io/file-storage/pdf.js/web/viewer.html?file=../../LaTex/LaTex使用指南（文档）.pdf)

## 1 公式显示位置

### 1.1 行内公式

在公式两边各自加上一个美元符号即是行内公式：

```latex
这是文本 $a^2+b^2=c^2$ 这是文本
```

效果：

这是文本 $a^2+b^2=c^2$ 这是文本

### 1.2 行间公式

在公式两边各自加上两个美元符号即是行间公式：

```latex
这是文本 $$a^2+b^2=c^2$$ 这是文本
```

效果：

这是文本 $$a^2+b^2=c^2$$ 这是文本

## 2 数学符号

### 2.1 上下标

在 LaTex 中用 `^` 和 `_` 表明上下标。

注意：如果上下标只对其后面的一个字符起作用，如果上下标的内容超过一个字符，则需要用花括号 `{}` 包裹，否则上下标只对后面的一个符号起作用

```latex
$$p^3_{ij}\qquad\sum_{K=1}^3ka^x+y\qquad\neq a^{x+y}$$
```

效果：

$$p^3_{ij}\qquad\sum_{K=1}^3ka^x+y\qquad\neq a^{x+y}$$

### 2.2 导数符号

导数符号 `'` 是一个特殊的上标，可以适当连用表示多阶导数，也可以在其后连用上标：

```latex
$f(x)=x^2 \quad f'(x)=2x \quad f''^{2}=4$
```

效果：

$f(x)=x^2 \quad f'(x)=2x \quad f''^{2}=4$

#### 2.2.1 偏导数

偏导符号 ∂

```latex
$\frac{\partial}{\partial θ_j}J(θ_0,θ_1)$
```

效果：

$\frac{\partial}{\partial θ_j}J(θ_0,θ_1)$

#### 2.2.2 条件偏导

偏导符号 ∂

```latex
$\left.\frac{\partial f(x,y)}{\partial x}\right|_{x=0}$
```

效果：

$\left.\frac{\partial f(x,y)}{\partial x}\right|_{x=0}$

### 2.3 分式

如果是简单的根式，可以写成`斜分式`的形式,如 `3/8`。

在 latex 中分式一般使用 `\frac{分子}{分母}` 来书写，不过这种分式的大小在行间公式是正常显示大小，而在行内被极度压缩了。

amsmath 提供了方便的命令 `\dfrac{分子}{分母}` ，令用户能够在行内使用正常大小的行内公式。

```latex
斜分式: $3/8$

压缩分式：$\frac{3}{8}$

显示正常大小：$\dfrac{3}{8}$
```

效果：

斜分式: $3/8$

压缩分式：$\frac{3}{8}$

显示正常大小：$\dfrac{3}{8}$

### 2.4 根式

一般的根式使用 `\sqrt{…}` 表示，表示 n 次方根式写成 `\sqrt[n]{…}`：

```latex
$\sqrt{x} \Leftrightarrow x^{1/2}
\quad \sqrt[3]{2}
\quad \sqrt{x^{2}+\sqrt{y}}
$
```

效果：

$\sqrt{x} \Leftrightarrow x^{1/2}
\quad \sqrt[3]{2}
\quad \sqrt{x^{2}+\sqrt{y}}
$

特殊的分式形式，如二项式结构，由 amsmath 宏包的 \binom 命令生成：

```latex
Pascal's rule is
$$
\binom{n}{k}=\binom{n-1}{k}+\binom{n-1}{k-1}
$$
```

效果：

Pascal's rule is
$$
\binom{n}{k}=\binom{n-1}{k}+\binom{n-1}{k-1}
$$

### 2.5 无穷大符号

```latex
$ \infty $
```

这个是个缩写，原单词（infinity）

效果：

$\infty$

### 2.6 省略号

| 名称       | 符号   | 效果 |
| :--------- | :----- | :--- |
| 水平省略号 | \dots  | …    |
| 水平省略号 | \cdots | ⋯    |
| 竖直省略号 | \vdots | ⋮    |
| 斜排省略号 | \ddots | ⋱    |

```latex
$a_1,a_2,\dots,a_n$

$a_1,a_2,\cdots,a_n$
```

效果：

$a_1,a_2,\dots,a_n$

$a_1,a_2,\cdots,a_n$

\cdots 和 \dots 是完全等效的，它们既能用在公式中，也用在文本里作为**省略号**。

除此之外，在矩阵中可能会用到竖排的 ⋮ (\vdots) 和斜排的 ⋱ (\ddots)。

### 2.7 关系符

LaTex 常见的关系符号除了可以直接输入的 =, >, <，其他符号用命令输入，常用的有不等于: ≠ (\ne)，大于等于号:  ≥  (\ge) 和小于等于号: ≤ (\le)，约等于号: ≈  (\approx)，等价 ≡ (\equiv)，正比:  ∝ (\propto)，相似:  ∼ (\sim) 等等。

LaTex 还提供了自定义二元关系符的命令 `\stackrel`，用于将一个符号叠加在原有的二元关系符之上：

```latex
$$
f_n(x) \stackrel{*}{\approx} 1
$$
```

效果：
$$
f_n(x) \stackrel{*}{\approx} 1
$$

### 2.8 巨算子

积分号，求和号等符号称为**[巨算子](http://texdoc.net/texmf-dist/doc/latex/lshort-chinese/lshort-zh-cn.pdf#54)**。

| 名称   | 符号 | 效果 |
| :----- | :--- | :--- |
| 积分号 | \int | ∫    |
| 求和号 | \sum | ∑    |

```latex
$$
\sum_{i=1}^n \quad
\int_0^{\frac{\pi}{2}} \quad
\oint_0^{\frac{\pi}{2}} \quad
\prod_\epsilon 
$$
```

效果：
$$
\sum_{i=1}^n \quad
\int_0^{\frac{\pi}{2}} \quad
\oint_0^{\frac{\pi}{2}} \quad
\prod_\epsilon
$$
巨算符的上下标用作其**上下限**。行间公式中，积分号默认将上下限放在右上角和右下角，求和号默认在上下方；行内公式一律默认在右上角和右下角。**可以在巨算符后使用 \limits 手动令上下限显示在上下方**，\nolimits 则相反，即不显示在上下方，也就是显示在右上角，右下角。

```latex
$$
\sum\nolimits_{i=1}^n \quad
\int\limits_0^{\frac{\pi}{2}} \quad
\prod\nolimits_\epsilon 
$$
```

效果：
$$
\sum\nolimits_{i=1}^n \quad
\int\limits_0^{\frac{\pi}{2}} \quad
\prod\nolimits_\epsilon
$$

### 2.9 极限符号

#### 2.9.1 行间公式极限

行间公式下标默认放在符号的下方，所以直接在极限符号 `\lim` 下方写上符号极限范围即可：

```latex
$${\lim_{x \to +\infty}}$$
```

效果：

$${\lim_{x \to +\infty}}$$

```latex
$${\lim_{x \to -\infty}}$$
```

效果：

$${\lim_{x \to -\infty}}$$

```latex
$${\lim_{x \to 0}}$$
```

效果：

$${\lim_{x \to 0}}$$

```latex
$${\lim_{x \to 0^+}}$$
```

效果：

$${\lim_{x \to 0^+}}$$

```latex
$${ \lim_{x \to 0} \frac{3x^2 +7x^3}{x^2 +5x^4} = 3}$$
```

效果：

$${ \lim_{x \to 0} \frac{3x^2 +7x^3}{x^2 +5x^4} = 3}$$



#### 2.9.2 行内极限

行内的下标默认放在右下角，可以使用 `\limits_{下标}` 把下标放到符号下方即可。

```latex
${\lim \limits_{x \to -\infty}}$
```

效果：

${\lim \limits_{x \to -\infty}}$

## 3 集合相关符号

### 3.1 元素与集合的关系

| 名称   | 符号   | 效果 |
| :----- | :----- | :--- |
| 属于   | \in    | ∈    |
| 不属于 | \notin | ∉    |

### 3.2 集合与集合相关的关系

| 名称   | 符号            | 效果   |
| :----- | :-------------- | :----- |
| 空集   | \emptyset       | ∅      |
| 子集   | \subset         | ⊂      |
| 真子集 | \subseteq       | ⊆      |
| 交集   | \bigcap 和 \cap | ⋂ 和 ∩ |
| 并集   | \bigcup 和 \cup | ⋃ 和 ∪ |

## 4 数学重音和上下括号

数学符号可以像文字一样**加重音**，比如对时间求导的符号 r˙(\dot{r})、ddotr(ddot{r})、表示向量的箭头 vecr(vec{r})、表示欧式空间单位向量的 e^(\hat{\mathbf{e}}) 等，详见表[4.9](http://texdoc.net/texmf-dist/doc/latex/lshort-chinese/lshort-zh-cn.pdf#e1)。使用时要注意重音符号的作用区域，**一般应当对某个符号而不是符号加下标使用重音：**

```latex
$\bar{x_0} \quad \bar{x}_0$

$\vec{x_0} \quad \vec{x}_0$

$\hat{\mathbf{e}_x} \quad
\hat{\mathbf{e}}_x$
```

效果：

$\bar{x_0} \quad \bar{x}_0$

$\vec{x_0} \quad \vec{x}_0$

$\hat{\mathbf{e}_x} \quad
\hat{\mathbf{e}}_x$

LATEX 也能为多个字符加重音，包括直接画线的 \overline 和 \underline 命令（可叠加使用）、宽重音符号 \widehat、表示向量的箭头 \overrightarrow 等。

```latex
$0.\overline{3} =
\underline{\underline{1/3}}$

$\hat{XY} \qquad \widehat{XY}$

$\vec{AB} \qquad
\overrightarrow{AB}$
```

效果：

$0.\overline{3} =
\underline{\underline{1/3}}$

$\hat{XY} \qquad \widehat{XY}$

$\vec{AB} \qquad
\overrightarrow{AB}$

\overbrace 和 \underbrace 命令用来生成上/下括号，各自可带一个上/下标公式。

```latex
$\underbrace{\overbrace{a+b+c}^6
\cdot \overbrace{d+e+f}^7}
_\text{meaning of life} = 42$
```

效果：

$\underbrace{\overbrace{a+b+c}^6
\cdot \overbrace{d+e+f}^7}
_\text{meaning of life} = 42$



## 5 多行公式

### 5.1 长公式折行

通常来讲应当避免写出超过一行而需要折行的长公式。如果一定要折行的话，优先在等号之前折行，其次在加号、减号之前，再次在乘号、除号之前。其它位置应当避免折行。

amsmath 宏包的 multline 环境提供了书写折行长公式的方便环境。它允许用 `\\\\` ( markdown 中显示：\ \ )折行，**将公式编号放在最后一行**。多行公式的**首行左对齐**，**末行右对齐**，**其余行居中**。
因为 markdown 中 `\` 是转义符，所以 `\\` 才表示一个 \ ，所以这里要写四个 `\`(`\\\\` )

```latex
$$
a + b + c + d + e + f+ g + h + i \\
= j + k + l + m + n\\
= o + p + q + r + s\\= t + u + v + x + z
$$
```

效果：
$$
a + b + c + d + e + f+ g + h + i \\
= j + k + l + m + n\\
= o + p + q + r + s\\= t + u + v + x + z
$$
与表格不同的是，公式的最后一行不写 `\\`，如果写了，反倒会产生一个多余的空行。类似 equation，multline 环境排版不带编号的折行长公式。

### 5.2 多行公式

更多的情况是，我们需要罗列一系列公式，并令其按照等号对齐。读者可能阅读过其它手册或者资料，知道 LATEX 提供了 eqnarray 环境。它按照等号左边——等号——等号右边呈三列对齐，但等号周围的空隙过大，加上公式编号等一些 bug，目前已不推荐使用。

**目前最常用的是 align 环境，它将公式用 & 隔为两部分并对齐。分隔符通常放在等号左边：**

```latex
$$
\begin{align}
a & = b + c \\
& = d + e
\end{align}
$$
```

效果：

![image-20200611115020866](https://gitee.com/wugenqiang/PictureBed/raw/master/NoteBook/20200611115022.png)

### 5.3 公式中的间距

前文提到过，绝大部分时候，数学公式中各元素的间距是根据符号类型自动生成的，需要我们手动调整的情况极少。我们已经认识了两个生成间距的命令 `\quad` 和 `\qquad`。在公式中我们还可能用到的间距包括 `\,`、`\:`、`\;` 以及负间距 `\!`，其中 `\quad`、`\qquad` 和 `\,` 在文本和数学环境中可用，后三个命令只用于数学环境。文本中的 `\␣` 也能使用在数学公式中。

![](https://gitee.com/wugenqiang/PictureBed/raw/master/NoteBook/20200611120855.png)

一个常见的用途是修正积分的被积函数 f(x) 和微元 dx 之间的距离。

注意微元里的 d 用的是直立体：

```latex
$$
\int_a^b f(x)\mathrm{d}x
\qquad
\int_a^b f(x)\,\mathrm{d}x
$$
```

效果：
$$
\int_a^b f(x)\mathrm{d}x
\qquad
\int_a^b f(x)\,\mathrm{d}x
$$
另一个用途是生成多重积分号。如果我们直接连写两个 \int，之间的间距将会过宽，此时可以使用负间距\!修正之。不过 amsmath 提供了更方便的多重积分号，如**二重积分 `\iint`**、**三重积分 `\iiint`** 等。

## 6 数组与矩阵

为了排版二维数组，LATEX 提供了 array 环境，用法与 tabular 环境极为类似，也需要定义列格式，并用\换行。数组可作为一个公式块，在外套用 \left、\right 等定界符：

```latex
$$
\mathbf{X} = \left(
\begin{array}{cccc}
x_{11} & x_{12} & \ldots & x_{1n}\\
x_{21} & x_{22} & \ldots & x_{2n}\\
\vdots & \vdots & \ddots & \vdots\\
x_{n1} & x_{n2} & \ldots & x_{nn}\\
\end{array}
\right) 
$$
```

效果：
$$
\mathbf{X} = \left(
\begin{array}{cccc}
x_{11} & x_{12} & \ldots & x_{1n}\\
x_{21} & x_{22} & \ldots & x_{2n}\\
\vdots & \vdots & \ddots & \vdots\\
x_{n1} & x_{n2} & \ldots & x_{nn}\\
\end{array}
\right)
$$
值得注意的是，上一节末尾介绍的 aligned 等环境也可以用定界符包裹。我们还可以利用空的定界符排版出这样的效果：

```latex
$$
|x| = \left\{
\begin{array}{rl}
-x & \text{if } x < 0,\\
0 & \text{if } x = 0,\\
x & \text{if } x > 0.
\end{array} \right. 
$$
```

效果：
$$
|x| = \left\{
\begin{array}{rl}
-x & \text{if } x < 0,\\
0 & \text{if } x = 0,\\
x & \text{if } x > 0.
\end{array} \right.
$$

## 7 分段函数

不过上述例子可以用 amsmath 提供的 cases 环境更轻松地完成：

```latex
$$ |x| =
\begin{cases}
-x & \text{if } x < 0,\\
0 & \text{if } x = 0,\\
x & \text{if } x > 0.
\end{cases} $$
```

效果：

$$ |x| =
\begin{cases}
-x & \text{if } x < 0,\\
0 & \text{if } x = 0,\\
x & \text{if } x > 0.
\end{cases} $$

我们当然也可以用 array 环境排版各种矩阵。amsmath 宏包还直接提供了多种排版矩阵的环境，包括不带定界符的 matrix，以及带各种定界符的矩阵 pmatrix（(）、bmatrix（[）、Bmatrix（{）、vmatrix（）、Vmatrix（）。使用这些环境时，无需给定列格式：

```latex
$$
\begin{matrix}
1 & 2 \\\\ 3 & 4
\end{matrix} \qquad
\begin{bmatrix}
x_{11} & x_{12} & \ldots & x_{1n}\\
x_{21} & x_{22} & \ldots & x_{2n}\\
\vdots & \vdots & \ddots & \vdots\\
x_{n1} & x_{n2} & \ldots & x_{nn}\\
\end{bmatrix}
$$
```

效果：
$$
\begin{matrix}
1 & 2 \\\\ 3 & 4
\end{matrix} \qquad
\begin{bmatrix}
x_{11} & x_{12} & \ldots & x_{1n}\\
x_{21} & x_{22} & \ldots & x_{2n}\\
\vdots & \vdots & \ddots & \vdots\\
x_{n1} & x_{n2} & \ldots & x_{nn}\\
\end{bmatrix}
$$
在矩阵中的元素里排版分式时，一来要用到 \dfrac 等命令，二来行与行之间有可能紧贴着，这时要调节间距：

```latex
$$
\mathbf{H}=
\begin{bmatrix}
\dfrac{\partial^2 f}{\partial x^2} &
\dfrac{\partial^2 f}
{\partial x \partial y} \\
\dfrac{\partial^2 f}
{\partial x \partial y} &
\dfrac{\partial^2 f}{\partial y^2}
\end{bmatrix}
$$
```

效果：
$$
\mathbf{H}=
\begin{bmatrix}
\dfrac{\partial^2 f}{\partial x^2} &
\dfrac{\partial^2 f}
{\partial x \partial y} \\
\dfrac{\partial^2 f}
{\partial x \partial y} &
\dfrac{\partial^2 f}{\partial y^2}
\end{bmatrix}
$$

## 8 数学符号的字体控制

### 8.1 数学字母字体

LATEX 允许一部分数学符号切换字体，主要是拉丁字母、数字等等。某一些命令需要字体宏包的支持。

```latex
$\mathcal{R} \quad \mathfrak{R}
\quad \mathbb{R}$

$$\mathcal{L}
= -\frac{1}{4}F_{\mu\nu}F^{\mu\nu}$$

$\mathfrak{su}(2)$ and
$\mathfrak{so}(3)$ Lie algebr
```

效果：

$\mathcal{R} \quad \mathfrak{R}
\quad \mathbb{R}$

$$\mathcal{L}
= -\frac{1}{4}F_{\mu\nu}F^{\mu\nu}$$

$\mathfrak{su}(2)$ and
$\mathfrak{so}(3)$ Lie algebr

### 8.2 数学符号的尺寸

数学符号按照符号排版的位置规定尺寸，从大到小包括行间公式尺寸、行内公式尺寸、上下标尺寸、次级上下标尺寸。除了字号有别之外，行间和行内公式尺寸下的巨算符也使用不一样的大小。

例如行间公式的分式内，分子分母使用行内公式尺寸，巨算符采用行内尺寸的形式。对比一下分子分母使用 \displaystyle 命令与否的区别：

```latex
$$
P = \frac
{\sum_{i=1}^n (x_i- x)(y_i- y)}
{\displaystyle \left[
\sum_{i=1}^n (x_i-x)^2
\sum_{i=1}^n (y_i-y)^2
\right]^{1/2} }
$$
```

效果：
$$
P = \frac
{\sum_{i=1}^n (x_i- x)(y_i- y)}
{\displaystyle \left[
\sum_{i=1}^n (x_i-x)^2
\sum_{i=1}^n (y_i-y)^2
\right]^{1/2} }
$$


## 9 符号表

### 9.1 文本/数学模式通用符号

| 文本/数学模式通用符号 | 命令       |
| :-------------------- | :--------- |
| `${$`                 | {          |
| }                     | }          |
| `$$$`                 | `$`        |
| %                     | \%         |
| \dag                  | \dag       |
| §                     | \S         |
| \copyright            | \copyright |
| …                     | \dots      |
| \ddag                 | \ddag      |
| \P                    | \P         |
| \pounds               | \pounds    |

### 9.2 二元关系符

有的二元关系符都可以加 \not 前缀得到相反意义的关系符，例如 \not= 就得到不等号（同 \ne ）。

| 效果 | 命令        | 效果 | 命令         | 效果 | 命令        |
| ---: | :---------- | ---: | :----------- | ---: | :---------- |
|    < | <           |    > | >            |    = | =           |
|    ≤ | \leq or \le |    ≥ | \geq or \ge  |    ≡ | \equiv      |
|    ≪ | \ll         |    ≫ | \gg          |    ≐ | \doteq      |
|    ≺ | \prec       |    ≻ | \succ        |    ∼ | \sim        |
|    ⪯ | \preceq     |    ⪰ | \succeq      |    ≃ | \simeq      |
|    ⊂ | \subset     |    ⊃ | \supset      |    ≈ | \approx     |
|    ⊆ | \subseteq   |    ⊇ | \supseteq    |    ≅ | \cong       |
|    ⊏ | \sqsubset   |    ⊐ | \sqsupset    |    ⋈ | \Join       |
|    ⊑ | \sqsubseteq |    ⊒ | \sqsupseteq  |    ⋈ | \bowtie     |
|    ∈ | \in         |    ∋ | \ni or \owns |    ∝ | \propto     |
|    ⊢ | \vdash      |    ⊣ | \dashv       |    ⊨ | \models     |
|    ∣ | \mid        |    ∥ | \parallel    |    ⊥ | \perp       |
|    ⌣ | \smile      |    ⌢ | \frown       |    ≍ | \asymp      |
|    : | :           |    ∉ | \notin       |    ≠ | \neq or \ne |

### 9.3 二元运算符

![](https://gitee.com/wugenqiang/PictureBed/raw/master/NoteBook/20200611122013.png)

### 9.4 希腊字母

latex 希腊字母符号代码就是 `斜杠+其英文名称`，如 αα:αα,ββ:ββ… 等。
大写的 latex 希腊字母就是`斜杠+首字母大写的英文名称`，如 ΓΓ:ΓΓ,ΔΔ:ΔΔ…。
\Alpha，\Beta 等希腊字母符号不存在，因为它们和拉丁字母 A，B 等一模一样；小写字母里也不存在 \omicron，可以直接用字母 `o` 代替，省的打那么长的代码。

| 序号 | 大写 | 小写 | 英文    |
| :--- | :--- | :--- | :------ |
| 1    | A    | α    | alpha   |
| 2    | B    | β    | beta    |
| 3    | Γ    | γ    | gamma   |
| 4    | Δ    | δ    | delta   |
| 5    | E    | ϵ    | epsilon |
| 6    | Z    | ζ    | zeta    |
| 7    | H    | η    | eta     |
| 8    | Θ    | θ    | theta   |
| 9    | I    | ι    | iota    |
| 10   | K    | κ    | kappa   |
| 11   | Λ    | λ    | lambda  |
| 12   | M    | μ    | mu      |
| 13   | N    | ν    | nu      |
| 14   | Ξ    | ξ    | xi      |
| 15   | O    | ο    | omicron |
| 16   | Π    | π    | pi      |
| 17   | P    | ρ    | rho     |
| 18   | Σ    | σ    | sigma   |
| 19   | T    | τ    | tau     |
| 20   | Υ    | υ    | upsilon |
| 21   | Φ    | ϕ    | phi     |
| 22   | X    | χ    | chi     |
| 23   | Ψ    | ψ    | psi     |
| 24   | Ω    | ω    | omega   |

### 9.5 巨算符

![](https://gitee.com/wugenqiang/PictureBed/raw/master/NoteBook/20200611122613.png)

### 9.6 箭头

除了作为上下标之外，箭头还用于表示过程。amsmath 的  `\xleftarrow`和`\xrightarrow ` 命令可以为箭头增加上下标：

```latex
$$ a\xleftarrow{x+y+z} b $$

$$c\xrightarrow[x<y]{a*b*c}d $$
```

效果：

$$ a\xleftarrow{x+y+z} b $$

$$c\xrightarrow[x<y]{a*b*c}d $$

**箭头详细表：**

![](https://gitee.com/wugenqiang/PictureBed/raw/master/NoteBook/20200611122801.png)

## 10 括号与定界符

### 10.1 作为重音的箭头符号

![](https://gitee.com/wugenqiang/PictureBed/raw/master/NoteBook/20200611121029.png)

### 10.2 定界符

![](https://gitee.com/wugenqiang/PictureBed/raw/master/NoteBook/20200611121055.png)



## 11 参考资料

* [💭 01. 一份（不太）简短的 LATEX 2ε 介绍 - lshort-zh-cn](http://texdoc.net/texmf-dist/doc/latex/lshort-chinese/lshort-zh-cn.pdf)

