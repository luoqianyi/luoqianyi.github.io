# 	Go基础学习

### 1.初识Go

**“Go是一种开源的程序设计语言，它意在使得人们能够方便地构建简单、可靠、高效率的软件”**

（来自go官网golang.org）

​				我们程序员在开发程序，开发软件时都会选择一门编程语言，那么我们应该怎样进行选择呢？

​				可能有同学会说，我们要选择一门简单的，容易学习的，而且开发效率高的，能够在很短的时间内开发完成一个软件，这样老板会非常的满意，能够升职加薪，Python语言或者Ruby语言就非常适合这种快速开发。

​				但是问题是，用这种语言开发的软件，当用户量多了，运行的速度会非常慢，给人的感觉就是非常卡，大家想一下这种软件还有人愿意使用吗？那可能又有同学说了，我们要学习运行速度快的编程语言，例如C或者是C++,但是这类编程语言学习难度是非常大的。

​				那么有没有一种编程语言，**<u>学习非常简单，开发速度非常快，开发出的软件电脑运行速度非常快</u>**呢？有，就是我们今天开始学习的GO语言。GO 语言借鉴了Python等其它编程语言简单，易学，生产效率高，同时GO语言专门针对多处理器（多核CPU，在这里可以给学生看一下windows下的多核CPU，）系统的程序进行了优化（让每核CPU都能够执行GO语言开发的程序），这样使用GO语言开发的程序运行的速度可以媲美C或C++开发程序的速度。

#### 1.优势

- 运行速度快，简单易学
- 薪水待遇高
- 适合区块链开发
- 跨平台
- 丰富的标准库（指令），Go目前已经内置了大量的库，特别是网络库非常强大(开发飞秋软件)
- Go里面也可以直接包含C代码，利用现有的丰富的C库
- 语言层面支持并发，这个就是Go最大的特色，天生的支持并发。所谓并发，所有正在运行的程序轮流使用CPU，每个程序允许占用CPU的时间非常短（比如10毫秒），这样用户根本感觉不出来CPU是在轮流为多个程序服务，就好象所有的程序都在不间断地运行一样。并发好处就是可以运行多个程序，并不卡顿。

#### 2.Go能干什么？

- 网络编程，这一块目前应用最广，例如网站开发。
- 服务器编程，例如：处理日志，处理文件。（在网站开发的过程中都会涉及到）
- 区块链开发

#### 3.Go相关文档

Go语言官网(需要翻墙)：https://golang.org/

go中文社区：https://studygolang.com

go中文在线文档：https://studygolang.com/pkgdoc

### 2.变量

#### 1.变量声明

所谓声明变量就是创建一个变量，并且指定该变量存储什么类型的数据。

Go语言引入了关键字var，而类型信息放在变量名之后，示例如下：

```go
package main
import "fmt"

func main(){
    fmt.Println("测试变量定义")
    //1.声明格式 var 变量名 类型，变量声明了，必须要使用
    //2.声明整型变量，该变量默认值为0
    //3.同一个{}里，声明的变量名是唯一的
    var a int
    fmt.Println("a=",a)
    //4.可以同时声明多个变量，中间用逗号隔开
    //var a,b int
}
```

> 注意:这里的输出是先将变量a的值取出来，然后在打印在屏幕上。所以不能给a这个变量加上引号。

#### 2.变量初始化

现在我们虽然已经完成变量的定义了，但是该变量中存储的值，并不是我们想要的，我们希望变量中存储的是我们想要的值，应该怎么办？

我们可以在定义变量时，就给变量赋值，这种方式就是变量的初始化。示例如下：

```go
var b int = 10
fmt.Println(b) 
```

注意：在这里我们将”=”符号，读作“赋值号”，不能读作“等号”。

#### 3.变量赋值

除了在定义变量时，完成初始化以外，我们也可以在变量定义完后，再给变量赋值，也就是先声明后赋值，示例如下：

```go
var b,c int
b=20
c=20
fmt.Println(b,c)
```



#### 4.自动推导类型

在给变量赋值时，我们感觉非常麻烦，有没有更简单的给变量赋值的方式，我们可以使用自动推导类型，具体示例如下：

```go
num := 40
fmt.Println(num)
```

所谓自动推导类型，就是不用通过var 声明变量，不用指定类型，直接在变量名后面跟”:”号，同时完成赋值。那么GO会根据所赋的值自动推导出变量的类型。如果给num变量赋值为小数，那么该变量的类型为小数类型（浮点）。

通过比较，我们发现这种方式比前面的赋值方式要简单方便。这种方式，也是我们以后开发过程中最常用的方式。

#### 5.多重赋值匿名变量

（1）多重赋值

在上面的讲解中，我们给变量num赋值，采用了自动推导的方式，如果想一次使用自动推导的方式，给多个变量赋值，应该怎样实现呢？具体如下：

```go
func main(){
    a := 1
    b := 10
    c := 20
    fmt.Println(a,b,c)
}
```

 

但是这种方式写起来非常的复杂，可以用如下的方式进行简化：

```go
a,b,c := 1,10,30
fmt.Println(a,b,c) 
```

将1的值赋值给a,将10的值赋值给b,将30的值赋值给c。

（2）匿名变量

_匿名变量，丢弃数据不进行处理, _匿名变量配合函数返回值使用才有价值。

匿名变量的语法：

```go
_,i,_,j=1,2,3,4
```



#### 6.数据置换

通过多重赋值的方式交换两个变量的值，比通过第三个变量来进行变量交换更简单，代码也少。

```go
a,b := 10,20
a,b = b,a
fmt.Println(a,b)
```

#### 7.接收输入

如何通过接收键盘输入呢？

```go
func main(){
    var age int
    fmt.Println("请输入你的年龄：")
    fmt.Scanf("%d",&age)
    fmt.Println(age)
}
```

在GO中我们用到了“fmt”这个包中的Scanf()函数来接收用户键盘输入的数据。当程序执行到Scanf()函数后，会停止往下执行，等待用户的输入，输入完成后程序继续往下执行。在这里重点要注意的是Scanf()函数的书写格式，首先也要用“%d”,来表示输入的是一个整数，输入完整数后存储到变量age中，注意这里age变量前面**一定要加上“&”符号**，表示**获取内存单元的地址**（前面我们说的内存存储区域的编号），然后才能够存储。

还有另外一种获取用户输入数据的方式，如下：

```go
func main(){
    var age int
    fmt.Println("请输入你的年龄：")
    fmt.Scan(&age)
}
```

通过Scan函数接收用户输入，这时可以省略掉%d,这种写法更简单。

#### 8.变量命名规范

1. 变量命名规范要求

   ​		名字必须以一个字母（Unicode字母）或下划线开头，后面可以跟任意数量的字母、数字或下划线。大写字母和小写字母是不同的：heapSort和Heapsort是两个不同的名字。

   ​		除了上面提到的规范要求以外，GO语言自己特有的，具有一定含义的一些字符，也不能作为变量名称。例如，前面我们接触到的func, fmt,print等等，这些都是GO自带的，具有特殊含义的字符，我们称为关键字。

   ![image-20220115161719326](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115161719.png)

   除了上面这些外，还有30多个预定义的名字：

   ![image-20220115161859487](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115161859.png)

2. 见名知意

3. 驼峰命名法

### 3.基本数据类型

| **类型**  | **名称** | **长度** | **零值** | **说明**                                      |
| --------- | -------- | -------- | -------- | --------------------------------------------- |
| bool      | 布尔类型 | 1        | false    | 其值不为真即为假，不可以用数字代表true或false |
| byte      | 字节型   | 1        | 0        | uint8别名                                     |
| int, uint | 整型     | -        | 0        | 有符号32位或无符号64位                        |
| int8      | 整型     | 1        | 0        | -128 ~ 127,                                   |
| uint8     | 整型     | 1        | 0        | 0 ~ 255                                       |
| int16     | 整型     | 2        | 0        | -32768 ~ 32767,                               |
| uint16    | 整型     | 2        | 0        | 0 ~ 65535                                     |
| int32     | 整型     | 4        | 0        | -2147483648 到 2147483647                     |
| uint32    | 整型     | 4        | 0        | 0 到 4294967295(42亿)                         |
| int64     | 整型     | 8        | 0        | -9223372036854775808到 92233720368547758070   |
| uint64    | 整型     | 8        | 0        | 到 18446744073709551615(1844京)               |
| float32   | 浮点型   | 4        | 0.0      | 小数位精确到7位                               |
| float64   | 浮点型   | 8        | 0.0      | 小数位精确到15位                              |
| string    | 字符串   |          | ""       | utf-8字符串                                   |

> **字符声明**用  byte类型来声明，如：
>
> ```go
> var ch byte
> ch = 97
> fmt.Printf("ch=%c",ch) //输出字符'a'
> fmt.Printf("ch=%d",ch) //输出整数97
> 
> ch = 'a'
> fmt.Println("ch=",ch) 
> ```

> **字符串长度**通过len(str)获取

> **fmt输出输入**
>
> | **格式** | **含义**                                                     |
> | -------- | ------------------------------------------------------------ |
> | %%       | 一个%字面量                                                  |
> | %b       | 一个二进制整数值(基数为2)，或者是一个(高级的)用科学计数法表示的指数为2的浮点数 |
> | %c       | 字符型。可以把输入的数字按照ASCII码相应转换为对应的字符      |
> | %d       | 一个十进制数值(基数为10)                                     |
> | %f       | 以标准记数法表示的浮点数或者复数值                           |
> | %o       | 一个以八进制表示的数字(基数为8)                              |
> | %p       | 以十六进制(基数为16)表示的一个值的地址，前缀为0x,字母使用小写的a-f表示 |
> | %q       | 使用Go语法以及必须时使用转义，以双引号括起来的字符串或者字节切片[]byte，或者是以单引号括起来的数字 |
> | %s       | 字符串。输出字符串中的字符直至字符串中的空字符（字符串以'\0‘结尾，这个'\0'即空字符） |
> | %t       | 以true或者false输出的布尔值                                  |
> | %T       | 使用Go语法输出的值的类型                                     |
> | %x       | 以十六进制表示的整型值(基数为十六)，数字a-f使用小写表示      |
> | %X       | 以十六进制表示的整型值(基数为十六)，数字A-F使用小写表示      |

#### 1.强制类型转换总结

强制类型转换的语法格式：

` 数据类型名(待转换的值)`

谨记:<u>**int 转float强制转换,多小数；float转int强制转换,丢精度.**</u>

### 4.常量

#### 1.常量定义使用

```go
func main(){
    //变量：程序运行期间，可以改变的量，变量声明需要var
    //常量：程序运行期间，不可以改变的量，常量声明需要const
    const a int = 20
    // a = 20 //err 常量不允许修改
    fmt.Println("a=",a)
    
    //自动推导类型
    const b = 11.2 //没有使用 :=
    fmt.Println("b=",b)    
}
```

#### 2.字面常量

所谓字面常量（literal），是指程序中硬编码的常量，如：

​	![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115164705.png)

#### 3.iota枚举

常量声明可以使用iota常量生成器初始化，它用于生成一组以相似规则初始化的常量，但是不用每行都写一遍初始化表达式。

注意：在一个const声明语句中，在第一个声明的常量所在的行，iota将会被置为0，然后在每一个有常量声明的行加一。

具体使用方式如下：

```go
package main
import "fmt"
func main(){
	//1. iota常量自动生成器每个一行，自动累加1
	//2. iota给常量赋值使用
	const(
		a = iota //0
		b = iota //1
		c = iota //2
	)
	fmt.Println("a =",a,",b =",b,",c =",c)
	//3. iota遇到const,重置为0
	const d = iota // 0
	fmt.Println("d =",d)
	//4. 可以只写一个iota
	const (
		a1 = iota // 0
		b1 // 1
		c1 // 2
	)
	fmt.Println("a1 =",a1,",b1 =",b1,",c1 =",c1)
	//5. 如果是在同一行，值都一样
	const (
		i = iota // 0
		j1,j2,j3 = iota,iota,iota // 1,1,1
		k = iota //2
	)
	fmt.Println("i =",i,",j1 =",j1,",j2 =",j2,",j3 =",j3,",k =",k)
}

```

### 5.运算符

#### 1.算术运算符

| **运算符** | **术语**           | **示例** | **结果** |
| ---------- | ------------------ | -------- | -------- |
| +          | 加                 | 10 + 5   | 15       |
| -          | 减                 | 10 - 5   | 5        |
| *          | 乘                 | 10 * 5   | 50       |
| /          | 除                 | 10 / 5   | 2        |
| %          | 取模(取余)         | 10 % 3   | 1        |
| ++         | 后自增，没有前自增 | a=0; a++ | a=1      |
| --         | 后自减，没有前自减 | a=2; a-- | a=1      |

>  注意：GO语言中没有前自增，这是与其它编程语言不同的地方。

#### 2.赋值运算符

关于赋值运算符前面我们已经使用过多次，赋值运算符	=  ，

varint num=9;num=num+1; 这里的=号是赋值运算符,不是数学义意上的相等.

常见的赋值运算符如下，前面我们使用的=是普通赋值，+=，-=等我们称为“复合赋值运算符”

| **运算符** | **说明**     | **示例**                              |
| ---------- | ------------ | ------------------------------------- |
| =          | 普通赋值     | c = a + b 将 a + b 表达式结果赋值给 c |
| +=         | 相加后再赋值 | c += a 等价于 c = c + a               |
| -=         | 相减后再赋值 | c -= a 等价于 c = c - a               |
| *=         | 相乘后再赋值 | c *= a 等价于 c = c * a               |
| /=         | 相除后再赋值 | c /= a 等价于 c = c / a               |
| %=         | 求余后再赋值 | c %= a 等价于 c = c % a               |

#### 3.关系运算符

| **运算符** | **术语** | **示例** | **结果** |
| ---------- | -------- | -------- | -------- |
| ==         | 相等于   | 4 == 3   | false    |
| !=         | 不等于   | 4 != 3   | true     |
| <          | 小于     | 4 < 3    | false    |
| >          | 大于     | 4 > 3    | true     |
| <=         | 小于等于 | 4 <= 3   | false    |
| >=         | 大于等于 | 4 >= 1   | true     |

#### 4.逻辑运算符

| **运算符** | **术语** | **示例** | **结果**                                                 |
| ---------- | -------- | -------- | -------------------------------------------------------- |
| !          | 非       | !a       | 如果a为假，则!a为真；如果a为真，则!a为假。               |
| &&         | 与       | a && b   | 如果a和b都为真，则结果为真，否则为假。                   |
| \|\|       | 或       | a \|\| b | 如果a和b有一个为真，则结果为真，二者都为假时，结果为假。 |

#### 5.其它运算符

| **运算符** | **术语**     | **示例** | **说明**                |
| ---------- | ------------ | -------- | ----------------------- |
| &          | 取地址运算符 | &a       | 变量a的地址             |
| *          | 取值运算符   | *a       | 指针变量a所指向内存的值 |

### 6.流程控制

#### 1.选择结构

- 顺序结构：程序按顺序执行，不发生跳转。
- 选择结构：依据是否满足条件，有选择的执行相应功能。
- 循环结构：依据条件是否满足，循环多次执行某段代码。

> 注意：Go的条件不需要加括号

#### 2.循环结构

在GO语言中，我们有专门实现这种循环的结构就是for结构（GO语言中只有for循环结构，没有while,do-while结构），基本语法结构如下：

```go
for 表达式1;表达式2;表达式3{
	循环体
}
```

表达式1:定义一个循环的变量，记录循环的次数

表达式2：一般为循环条件，循环多少次

表达式3：一般为改变循环条件的代码，使循环条件终有一天不再成立

循环体：重复要做的事情。

### 7.函数

通过func关键字来定义函数，函数名后面必须加括号。下面是一个完整的函数定义：

```go
func 函数名(形参 类型,形参 类型)（返回值名 类型,返回值名 类型）{
	函数体
}
```

- **不定参数列表**

举例说明：上一小节我们写过一个求两个整数之和的函数，但是在实际的开发中，也会经常遇到这样的情况，就是项目经理（对整个项目的进度进行把控，对程序员进行管理的人员，称为项目经理），要求你写一个函数，实现整数的和。在这个要求中，项目经理并没有说清楚到底是有几个整数，那么我们应该怎样确定该函数的参数呢？就用接下来给大家讲解的“不定参数列表”来解决这个问题

那么我们可以通过如下的方式来定义函数:

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115183110.jpg) 

Test()函数的参数名字叫args（参数的名字可以随便起）,类型是整型的。但是，大家一定要注意，在args后面跟了三个点，就是表示该参数可以接收0或多个整数值。所以，args这个参数我们可以想象成是一个集合（类似数学中集合），可以存放多个值。

所以，在Test()函数内，我们通过以前学习的一个函数叫len(),来计算出args这个集合中存储了多少个数（如果args这个集合中存储了5个数，那么len()函数的值就是5），通过for循环将该集合中的数全部输出，在输出时我们通过下标的方式将args集合中的值输出的。所谓的下标，我们可以理解成就是一个编号，对存储在args这个集合中每个数字都加上了编号。在这里要注意的是：下标是从0开始计算的。如下图所示：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115183115.jpg) 

args集合中存储了5,6,7三个数，对应的下标（编号），分别是0,1,2. 如果该集合中存储了4个数，那么第4个数的编号就是3.

现在取出第一个数就是args[0]值为5，第二个数args[1]值为6，以此类推。

在main()函数中，我们分别调用了三次Test()函数，在第一次调用时，我们只传递了一个参数1，那么形参args中也就只有1，所以只循环了一次就将该值输出。第二次调用时，传递了两个参数，循环两次输出，第三次调用，传递了三个参数，循环了三次输出。

在Test()函数中，我们除了使用len()函数，计算出集合中存储的数据的个数，然后再输出以外，还有另外一种输出的方式就是使用range关键字。如下所示：

```go
for i,data := range args{
    fmt.Println("编号为:",i)
    fmt.Println("值为:",data)
}
```

range会从集合中返回两个数，第一个是对应的坐标，赋值给了变量i，第二个就是对应的值，赋值了变量data。

> 使用不定参数时，需注意下面几个问题：
>
> 1. **一定（只能）放在形参中的最后一个参数**
> 2. **在对函数进行调用时，固定参数必须传值，不定参数可以根据需要来决定是否要传值**。

- 函数返回值的几种写法
  1. ![image-20220115183817812](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115183817.png)
  2. ![image-20220115183835130](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115183835.png)
  3. ![image-20220115183849260](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115183849.png)

以上几种写法，都可以。可以根据自己的习惯进行选择。

- **函数类型**

  在GO语言中还有另外一种定义使用函数的方式,就是函数类型，所谓的函数类型就是**将函数作为一种类型可以用来定义变量**，这种用法类似于前面我们讲过的int ,float64,string等类型，这些类型都是可以用来定义变量。基本语法如下：

  ```go
  package main
  
  import "fmt"
  
  func Test(a int,b int)(sum int){
  	sum = a+b
  	return
  }
  type FuncType func(a int,b int) int  //定义函数类型
  func main(){
  	var s int
  	var result FuncType //定义函数类型的变量
  	result = Test //Test这种类型的函数
  	s = result(4,6)
  	fmt.Println(s)
  }
  ```

  type关键字后面跟着类型的名字(FunType)，FunType就是一个类型.那么FunType是一个什么类型呢？

  ​			是一个函数类型，因为FunType后面跟着func(用来定义函数的)，但是这里注意的是**没有函数名字**。那么FunType是怎样的一个函数类型呢？是一个需要传递两个整型参数，有一个整型返回值的函数类型。

- **函数作用域**

  1. 在函数外边定义的变量叫做全局变量。
  2. 全局变量能够在所有的函数中进行访问
  3. 如果全局变量的名字和局部变量的名字相同，那么使用的是局部变量的，小技巧：**强龙不压地头蛇**

- **匿名函数**

  如果我们想**在一个函数中再定义一个函数**，那么可以使用匿名函数。

  定义匿名函数：

  ```go
  func main(){
      var num int = 9
      f := func(){
          num++;
          fmt.Println("匿名函数：",num)
      }
      f()
      fmt.Println("main函数：",num)
  }
  
  ```

  > ​						注意：在这里，有一件非常有意思的事情，就是在匿名函数中可以直接访问main( )函数中定义的局部变量，并且在匿名函数中对变量的值进行了修改，最终会影响到整个main( )函数中定义的变量的值。所以上面两行输入都是10.
  >
  > 关于这一点，一定与函数作用域进行区别！	

  定义匿名函数时直接调用：

  ![image-20220115190238530](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115190238.png)

  该方式，需要在匿名函数的末尾加上小括号，表示调用。同时也不需要将定义好的匿名函数赋值给某个变量。

  匿名函数如果有返回值，怎样进行处理呢？

  ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115190332.jpg) 

  以上案例中定义了一个匿名函数，该匿名函数需要两个整型参数，同时指定了该函数返回值的名字是变量max与min。

  当执行到return时，让变量manx与min返回，赋值了变量x,y。x中存储的是max变量的值，y中存储的是min变量的值。

  **匿名函数最重要的功能就是实现了闭包**。



### 8.工程管理

为了更好的管理项目中的文件，要求将文件都要放在相应的文件夹中。GO语言规定如下的文件夹如下：

1. src目录：用于以代码包的形式组织并保存Go源码文件。（比如：.go .c .h .s等）
2. pkg目录：用于存放经由go install命令构建安装后的代码包（包含Go库源码文件）的“.a”归档文件。
3. bin目录：与pkg目录类似，在通过go install命令完成安装后，保存由Go命令源码文件生成的可执行文件。

以上目录称为工作区，工作区其实就是一个对应于特定工程的目录。

目录src用于包含所有的源代码，是Go命令行工具一个强制的规则，而pkg和bin则无需手动创建，如果必要Go命令行工具在构建过程中会自动创建这些目录。

#### 1.同级目录直接调用函数

**注意：同一个目录下不能定义不同的package**

#### 2.不同目录下包管理

在main( )函数中要使用相应的函数，必须进行导包，然后根据包名去调用相应的函数。

通过上面的代码，我们也能够体会出“包”的优势，就是可以在userinfo包中定义名叫Add( )方法，在product包中也可以定义Add( )方法，但是在main( )函数中进行调用时，通过包名进行调用，就可以很清楚Add( )方法来自哪个包，不会造成混乱，和名称的冲突。并且相关的功能代码，放在一个包中，可以很好的被复用。例如：可以在userinfo包中使用product,如下图所示：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115191054.jpg) 

 

但是我们创建的的自定义包最好放在GOPATH的src目录下，在Go语言中，代码包中的源码文件名可以是任意的。但是，这些任意名称的源码文件都必须以包声明语句作为文件中的第一行，每个包都对应一个独立的名字空间。

包中成员以名称⾸字母⼤⼩写决定访问权限：

- public: ⾸字母⼤写，可被包外访问

- private: ⾸字母⼩写，仅包内成员可以访问

 

注意：同一个目录下不能定义不同的package。

（3.5）导包的问题

在上面的案例中，要使用包，必须要进行导入，可以通过关键字进行import进行导入，它会告诉编译器你想引用该包内的代码。

如果导入的是标准库中的包（GO语言自带，例如:”fmt”包）会在安装 Go 的位置找到。 Go 开发者创建的包会在 GOPATH 环境变量指定的目录里查找。所以，import关键字的作用就是查找包所在的位置。

如果编译器查遍 GOPATH 也没有找到要导入的包，那么在试图对程序执行 run 或者 build

的时候就会出错。

***\*注意：\****如果导入包之后，未调用其中的函数或者类型将会报出编译错误。

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115191109.jpg) 

 

我们常规的导包方式是用import关键子一个个导入。

例如：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115191114.jpg) 

表示导入三个包，有GO语言自带的包，也有我们自定义的包。但是，这种写法可能比较麻烦，所以为了简化也可以采用如下的方式进行导包：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/20220115191117.jpg) 

这种方式，使用的频率是非常高的。

### 9.数据格式

#### 1.数组

1. 数组定义

   ```go
   var a [10]int
   length := len(a)
   ```

   数组定义也是通过var 关键字，后面是数组的名字a，长度是10,类型是整型。表示：数组a能够存储10个整型数字。也就是说，数组a的长度是10。**Go中数组的长度只能是常量**。

2. 数组赋值

   - 下标赋值
   - 没有赋值的话：
     1. 整型数组，结果全是0
     2. 浮点数组，结果全是0
     3. 字符串数组，结果全是空字符
     4. 布尔数组，结果全是false

3. 数组初始化

   ```go
   var a [3]int = [3]int{0, 1, 2}                         // a = [0 1 2]
   var b [3]int = [3]int{}                                // b = [0 0 0]
   var c [3]int
   c = [3]int{}
   c = [3]int{0,0,0}                                      // c = [0 0 0]
   d := [3]int{}                                          // d = [0 0 0]
   fmt.Printf("%T\t%#v\t%d\t%d\n", d, d, len(d), cap(d))  
   // [3]int    [3]int{0, 0, 0}    3    3
   ```

   可以使用`...`自动计算数组的长度：

   ```go
   var a = [...]int{0, 1, 2}
   
   // 多维数组只能自动计算最外围数组长度
   x := [...][3]int{{0, 1, 2}, {3, 4, 5}}
   y := [...][2][2]int{{{0,1},{2,3}},{{4,5},{6,7}}}
   
   // 通过下标访问数组元素
   println(y[1][1][0])                                    // 6
   ```

   初始化指定索引的数组元素，未指定初始化的元素保持默认零值：

   ```go
   var a = [3]int{2:3}
   var b = [...]string{2:"c", 3:"d"}
   ```

#### 2.切片

1. 数组存在的问题？

   - 数组定义完，长度是固定的。
   - 使用数组作为函数参数进行传递时，如果实参为5个元素的整型数组，那么形参也必须5个元素的整型数组，否则出错。

   针对上面两个问题，可以使用切片来解决。

   **切片**：

   - 切片与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大，所以可以将切片理解成“**动态数组**”，**但是，它不是数组**。

2. 切片与数组的区别

   - `a:=[5]int{ }`:数组中[ ]是一个固定的数字，表示长度。定义完后，长度是固定，最多存储5个数字。

   - `s:=[ ]int{ }`:切片中的[ ]是空的，或者是“...”.切片的长度和容量可以不固定。

     - 如何向切片中追加数据？

       ```go
       //初始化切片
       s := []int{1,2,3}
       //通过append函数向切片中追加数据
       s = append(s,4,5,6) //第一个参数表示向哪个切片追加数据，后面表示具体追加的数据。
       ```

     - 其它定义方式？

       `var s1 []int` 声明切片和声明数组一样，只是少了长度，此为空(nil)切片。

       `s := make([]int, 5, 10)`借助make函数, 格式 `make(切片类型, 长度, 容量)`

       > 什么是切片的容量和长度？
       >
       > - 长度是已经初始化的空间(以上切片s初始空间默认值都是0)。容量是已经开辟的空间，包括已经初始化的空间和空闲的空间。
       >
       >   ![image-20220117160009256](https://s2.loli.net/2022/01/17/NWDgxQfVmzREPub.png)
       >
       > - 切片长度一定小于等于容量
       >
       > - make函数的容量可以省略，默认与长度相等
       >
       >   - 计算切片长度：`len(s)`
       >   - 计算切片容量：`cap(s)`

3. 切片截取

   ```go
   s := []int{10,20,30,0,0}
   
   //从切片s中截取数据
   slice := s[0:3:5]
   fmt.Println(slice) //输出[10 20 30]
   ```

   

我们可以`使用s[low:high:max]`来表示切片截取：

第一个数（low）表示下标的起点（从该位置开始截取），如果low取值为0表示从第一个元素开始截取，也就是对应的切片s中的10

第二个数(high)表示取到哪结束，也就是下标的终点（不包含该位置），3表示取出下标是0,1,2的数据（10,20,30），不包括下标为3的数据，那么也就是说取出的数据长度是3. 可以根据公式：3-0 计算(len=high-low)，也就是第二个数减去第一个数，差就是数据长度。在这里可以将长度理解成取出的数据的个数。

第三个数用来计算容量，所谓容量:是指切片目前可容纳的最多元素个数。通过公式5-0计算(cap=max-low)，也就是第三个数据减去第一个数。该案例中容量为5。

| **操作**        | **含义**                                                     |
| --------------- | ------------------------------------------------------------ |
| s[n]            | 切片s中索引位置为n的项                                       |
| s[:]            | 从切片s的索引位置0到len(s)-1处所获得的切片                   |
| s[low:]         | 从切片s的索引位置low到len(s)-1处所获得的切片                 |
| s[:high]        | 从切片s的索引位置0到high处所获得的切片，len=high             |
| s[low:high]     | 从切片s的索引位置low到high处所获得的切片，len=high-low       |
| s[low:high:max] | 从切片s的索引位置low到high处所获得的切片，len=high-low，cap=max-low |
| len(s)          | 切片s的长度，总是<=cap(s)                                    |
| cap(s)          | 切片s的容量，总是>=len(s)                                    |

> 切片容量不够时以2倍的容量进行扩容。

`copy(切片1，切片2)`：将第二个切片里面的元素，拷贝到第一个切片中。

**在GO语言中，数组作为参数进行传递是值传递，而切片作为参数进行传递是引用传递。**

> 1. **值传递**
>
>    ​		方法调用时，实参数把它的值传递给对应的形式参数，方法执行中形式参数值的改变不影响实际参数的值。
>
> 2. **引用传递**
>
>    ​			也称为传地址。函数调用时，实际参数的引用(地址，而不是参数的值)被传递给函数中相对应的形式参数（实参与形参指向了同一块存储区域），在函数执行中，对形式参数的操作实际上就是对实际参数的操作，方法执行中形式参数值的改变将会影响实际参数的值。

#### 3.字符串

常用字符串处理函数，借助于包"strings"：

1. Contains

   ```go
   func Contains(s,substr string)bool
   功能：字符串s中是否包含substr，返回bool值
   ```

2. Join

   ```go
   func Join(a[]string,sep string)string
   功能：字符串链接，把切片a通过sep链接起来
   ```

3. Index

   ```go
   func Index(s,sep string)int
   功能：在字符串s中查找sep所在的位置，返回位置值，找不到返回-1
   ```

4. Repeat

   ```go
   func Repeat(s string,count int)string
   功能：重复s字符串count次，最后返回重复的字符串
   ```

5. Replace

   ```go
   func Replace(s,old,new string,n int)string
   功能：在s字符串中，把old字符串替换为new字符串，n表示替换的次数，小于0表示全部替换
   ```

6. Split

   ```go
   func Split(s,sep string)[]string
   功能：把s字符串按照sep分割，返回slice
   ```

7. Trim

   ```go
   func Trim(s string,cutset string)string
   功能：在s字符串的头部和尾部去除cutset指定的字符串
   ```

8. Fields

   ```go
   func Fields(s string)[]string
   功能：去除s字符串的空格符，并且按照空格分割返回slice
   ```

#### 4.字符串转换

GO语言也提供了字符串与其它类型之间相互转换的函数。相应的字符串转换函数都在”strconv”包。

1. Format

   Format 系列函数把其他类型的转换为字符串。

   ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/1BpH4m7QrMgdwyl.jpg) 

   将整型转换成字符串：

   ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/Du3yzGYS2jUegwA.jpg) 

   ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsFF8A.tmp.jpg)

2. Parse

Parse 系列函数把字符串转换为其他类型

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsFF8B.tmp.jpg) 

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsFF8C.tmp.jpg)

1. Append

Append 系列函数将整数等转换为字符串后，添加到现有的字节数组中。

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsFF8D.tmp.jpg) 

对应的结果是：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsFF8E.tmp.jpg) 

#### 5.Map

字典结构定义：

`map[keyType]valueType`

定义字典结构使用map关键字，[ ]中指定的是键(key)的类型,后面紧跟着的是值的类型。

键的类型，必须是支持==和!=操作符的类型，切片、函数以及包含切片的结构类型不能作为字典的键，使用这些类型会造成编译错误。

注意：**字典中不能使用cap函数，只能使用len( )函数。len( )函数返回map拥有的键值对的数量**

当然也可以使用make( )函数来定义，如下所示：

```go
var m = make(map[keyType]valueType)
```

map可以自动扩容。map是无序的，我们无法决定它的返回顺序，所以，每次打印结果的顺利有可能不同。

删除map中的某个元素使用`delete(m map[keyType]valueType,key keyType)`函数。

map 作为函数参数是引用传递。

#### 6.结构体

在GO语言中，我们可以通过结构体来存储多个数据，结构体的定义如下：

```go
//定义一个结构体
type Student struct{
	id int
	name string
	sex byte //字符类型
	age int
	addr string
}
```

type 后面跟着的是结构体的名字Student, struct表示定义的是一个结构体。

大括号中是结构体的成员，注意在定义结构体成员时，不要加var。通过结构体来定义复杂的数据结构，非常清晰。

结构体定义完成后，可以进行初始化。

```go
//顺序初始化，每个程序必须初始化
	var s1 = Student{1,"m",'0',18,"bj"}
	fmt.Println("s1 =",s1)

	//指定成员初始化，没有初始化的成员，自动赋值为0
	s2 := Student{name: "mike",addr: "hn"}
	fmt.Println("s2 =",s2)
```

两个结构体可以使用 == 或 != 运算符进行比较，但不支持 > 或 <。

定义一个结构体数组：

```go
students := []Student{
		{id: 1},{id: 2},{id: 3,name: "hhh"},
	}
```

结构体也可以作为函数参数进行传递：

- 值传递

  ```go
  func test01(stu Student){
  	stu.id = 666
  	fmt.Println("test01",stu)
  }
  func main(){
      //1.指针变量有合法指向后，才操作成员
  	//先定义一个普通结构体变量
  	var S Student
  	//再定义一个指针变量，保存s的地址
  	var p *Student
  	p = &S
  	//通过指针操作成员 p.id和*p.id完全等价，只能使用.运算符
  	p.id = 18
  	(*p).name = "luoqianyi"
  	p.sex = 'm'
  	p.age = 20
  	p.addr = "河南省"
      test01(S) //值传递，形参无法改实参
  	fmt.Println("main",S)
  }
  ```

  

- 引用传递

  ```go
  func test02(stu *Student) {
  	stu.id = 999
  }
  func main(){
      test02(&S) //地址传递（引用传递），形参改实参
  	fmt.Println("test02->main",S)
  }
  ```

  

#### 7.指针

##### 1.变量内存与地址

前面我们讲过存储数据的方式，可以通过变量，或者复合类型中的数组，切片，Map,结构体。

我们不管使用变量存储数据，还是使用符合类型存储数据，都有两层的含义：

存储的数据（内存），对应的地址。

接下来，通过变量来说明以上两个含义。例如，定义如下变量：

![img](C:/Users/Lenovo/AppData/Local/Temp/ksohtml/wps76E0.tmp.jpg) 

第一个Printf( )函数的输出，大家都很熟悉，输出变量i的值，这个实际上就是输出内存中存储的数据。在前面的课程中，已经讲解过，定义一个变量，就是在内存中开辟一个空间，用来存储数据，当给变量i赋值为100，其实就是将100存储在改空间内。

第二个Printf( )函数的输出，输出的是变量i在内存中的地址。通过如下图来给大家解释：

 

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wps76E1.tmp.jpg) 

这张图，大家也应该非常熟悉，是在讲解变量时，画的一张图，0x100010假设是变量i的内存地址（通过第二个输出可以获取实际的地址），内存地址的作用：在输出变量中存储的数据时，是通过地址来找到该变量内存空间的。

这个内存地址和实际生活中的地址也很相似，例如：大家可以将内存空间想象成，我们上课的教室，教室中存放有学生，那么现在要找一个学生，必须要知道具体的地址以及教室门牌号。

以上程序输出的结果是：

![img](https://gitee.com/luoqianyi/static-image/raw/master/图床/wps76E2.tmp.jpg) 

##### 2.指针变量

现在已经知道怎样获取变量在内存中的地址，但是如果想将获取的地址进行保存，应该怎样做呢？

可以通过指针变量来存储，所谓的指针变量：就是用来存储任何一个值的内存地址。

指针变量的定义如下：

```go
var i int = 100
var p *int //定义一个指针变量
p = &i //把变量i的地址赋值给指针变量p
fmt.Printf("i=%d,p=%v",i,p)
```



指针变量p的定义是通过 *这个符号来定义，指针变量p的类型为*int, 表示存储的是一个整型变量的地址。

如果指针变量p存储的是一个字符串类型变量的地址，那么指针变量p的类型为*string

p=&i :该行代码的意思是，将变量i的地址取出来，并且赋值给指针变量p.也就是指针变量p指向了变量i的存储单元。

可以通过如下图来表示：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsF7A5.tmp.jpg) 

在以上图中，一定要注意：指针变量p存储的是变量i的地址。

大家可以思考一个问题：

既然指针变量p指向了变量i的存储单元，那么是否可以通过指针变量p，来操作变量i中存储的数据？

答案是可以的，具体操作方式如下：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsF7B5.tmp.jpg) 

注意：在使用指针变量p来修改变量i的值的时候，前面一定要加上*.（通过指针访问目标对象）

现在打印变量i的值已经有100变为80.

当然，也可以通过指针变量p来输出，变量i中的值，输出的方式如下所示：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsF7B6.tmp.jpg) 

所以，*p的作用就是根据存储的变量的地址，来操作变量的存储单元（包括输出变量存储单元中的值，和对值进行修改）

##### 3.注意事项

在使用指针变量时，要注意以下两点。

1. 默认值为nil

   ```go
   var p *int
   fmt.Println(p)
   ```

   直接执行上面的程序，结果是nil

2. 不要操作没有合法指向的内存。

   在上面的案例中，我们定义了指针变量p，但是没有让指针变量指向任何一个变量，那么直接运行如下程序，会出现异常。

   ```go
   var p *int
   *p = 56 //没有指向，直接操作
   fmt.Println(p)
   ```

   出现的错误信息如下：

   ![image-20220117225323927](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220117225323927.png)

   所以，在使用指针变量时，一定要让指针变量有正确的指向。以下的操作是合法的：

   ```go
   var a int
   var p *int
   p = &a //指向变量a
   *p = 56
   fmt.Println(p)
   ```

   在该案例中，定义了一个变量a, 同时定义了一个指针变量p, 将变量a的地址赋值给指针变量p,也就是指针变量p指向了变量a的存储单元。给指针变量p赋值，影响到了变量a.最终输出变量a中的值也是56.

3. 可以使用new()函数

   指针变量，除了以上介绍的指向以外(p=&a)，还可以通过new( )函数来指向。

   具体的应用方式如下:

   ```go
   var p *int
   p = new(int)
   *p = 57
   fmt.Println(*p)
   ```

    

   new(int)作用就是创建一个整型大小的空间

   然后让指针变量p指向了该空间，所以通过指针变量p进行赋值后，该空间中的值就是57.

   new( )函数的作用就是C语言中的动态分配空间。但是在这里与C语言不同的地方，就是最后不需要关系该空间的释放。GO语言会自动释放。这也是比C语言使用方便的地方。

   也可以使用自动推导类型的方式：

   ```go
   q := new(int)
   *q = 56
   fmt.Println(*q)
   ```

    

##### 4.数组指针

前面在讲解数组的时候，我们用数组作为函数参数，但是数组作为参数进行传递是值传递，如果**想引用传递，可以使用数组指针**。具体使用方式如下：

 ![image-20220117230154569](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220117230154569.png)

定义一个数组，作为函数Swap的实参进行传递，但是这里传递的是数组的地址，所以Swap的形参是数组指针。，

这时指针p，指向了数组a,对指针p的操作实际上是对数组a的操作，所以如果直接执行如下语句：fmt.Println(*p),会输出数组a中的值。也可以通过*p结合下标将对应的值取出来进行修改。最终在main函数中输出数组a，发现其元素也已经修改。

当然，我们也可以通过循环的方式来将数组指针中的数据打印出来：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsA2FE.tmp.jpg)

##### 5.指针数组

上一小节，讲解到的是数组指针，也就是让一个指针指向数组 ，然后可以通过该指针来操作数组。还有一个概念叫指针数组，这两个概念很容混淆，**指针数组指的是一个数组中存储的都是指针（也就是<u>地址</u>）**。也就**是一个存储了地址的数组**。

<img src="https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220117230301850.png" alt="image-20220117230301850" style="zoom:200%;" />

指针数组的定义方式，与数组指针定义方式是不一样的，注意指针数组是将“*”放在了下标的后面。

由于指针数组存储的都是地址，所以将变量i,与变量j的地址赋值给了指针数组p。

最后输出指针数组p中存储的地址。

### 8.面向对象

对于面向对象编程的支持Go语言设计的非常简洁而优雅。因为，Go语言并没有沿袭传统的面向对象编程中的诸多概念，比如继承（不支持继承，尽管匿名字段的内存布局和行为类似继承，但它并不是继承）、虚函数、构造函数和析构函数、隐藏的this指针等。

尽管Go语言中并没有封装、继承、多态这些概念，但同样通过别的方式实现这些特性：

- 封装：通过方法实现
- 继承：通过匿名字段实现
- 多态：通过接口实现

1：GO语言中的面向对象

严格意义上说，GO语言中没有类(class)的概念,但是我们可以将结构体比作为类，因为在结构体中可以添加属性（成员），方法（函数）。

#### 1.匿名字段

##### 1.匿名字段创建与初始化

- 怎样实现属性的继承呢？

可以通过匿名字段(也叫匿名组合)来实现，如下：

```go
type Person struct{
    id int
    name string
    age int
}
type Student struct{
    Person //匿名字段
    score float64
}
```

以上代码通过匿名字段实现了继承，将公共的属性封装在Person中，在Student中直接包含Person,那么Student中就有了Person中所有的成员，Person就是匿名字段。注意：Person匿名字段，只有类型，没有名字。

- 如何给Student赋值？

  ```go
  var s1 = Student{Person{101,"mike",18},98.6}
  
  //自动推导
  s2 := Student{Person{102,"zhangsan",18},90}
  
  //指定成员初始化，没有初始化的整型自动赋值为0，字符串为空
  s3 := Student{score:97,Person:Person{name:"mike"}}
  ```

##### 2.同名字段

现在将Student结构体与Person结构体，进行如下的修改：

```go
type Person struct{
    id int
    name string
    age int
}
type Student struct{
    Person //匿名字段
    name string //和Person的字段同名
    score float64
}
```

对Student中的name进行赋值，所以在操作同名字段时，有一个基本的原则：**如果能够在自己对象所属的类（结构体）中找到对应的成员，那么直接进行操作，如果找不到就去对应的父类（结构体）中查找**。这就是所谓的**就近原则**。

##### 3.指针类型匿名字段

结构体（类）中的匿名字段的类型，也可以是指针。

```go
type Person struct{
    id int
    name string
    age int
}
type Student struct{
    *Person //指针类型匿名字段
    score float64
}
func main(){
    s := Student{&Person{101,"lisi",19},100} //Person地址
    fmt.Println(s.id,s.name,s.age,s.score)
}
```

> ```go
> s.Person = new(Person) //使用new分配空间
> ```
>
> new( )的作用是分配空间，new( )函数的参数是一个类型，这里为Person结构体类型，返回值为指针类型，所以赋值给\*Person,这样*Person也就指向了结构体Person的内存。

##### 4.多重继承

多重继承指的是一个类可以继承另外一个类，而另外一个类又可以继承别的类，比如A类继承B类，而B类又可以继承C类，这就是多重继承。

```go
type Object struct{
    id int
    flag bool
}
type Person struct{
    Object
    name string
    age int
}
type Student struct{
    Person
    name string //和Person中字段同名
    score float64
}
```

如何对多重继承中的成员进行操作？

```go
var s1 Student
s1.name = "zhangsan"
s1.Person.name = "lisi"
s1.Person.Object.id = 101
```

注意：尽量在程序中，减少多重继承，否则会增加程序的复杂度。

**通过函数来实现封装性！**

#### 2.方法

##### 1.基本方法创建

下面是一个简单的方法的定义：

```go
package main

import "fmt"

type Integer int //为int定义别名 Integer,别名可以随便起，只要符合GO语言的命名规则就可以。
//指定别名后，后面可以用Integer来代替int 来使用。
func (a Integer) Test(b Integer)Integer{
	return a + b
}
func main() {
	//var result Integer = 3
	result := Integer(3)  
	r := result.Test(4)
	fmt.Println(r)
}
```

在关键字后面加上( a Integer), 这个在方法中称之为**接收者**，所谓的接受者就是**接收传递过来的第一个参数**，然后复制a， a的类型是Integer ,由于Integer是int的别名，所以a的类型为int。

在表示参数的类型时，都使用了对应的别名。通过方法的定义，可以看出**方法其实就是给某个类型绑定的函数**。在该案例中，是为整型绑定的函数，只不过在给整型绑定函数(方法)时，一定要通过type来指定一个别名，因为int类型是系统已经规定好了，无法直接绑定函数，所以只能通过别名的方式。

方法与函数的调用方式不同，通过result变量，完成方法的调用。因为，Test( )方法，是为int类型绑定的函数，而result变量为int类型。所以可以调用Test( )方法。result变量的值会传递给Test( )方法的接受者，也就是参数a,而实参Test( 3),会传递形参b。当然，我们也可以将Test( )方法，理解成是为int类型扩展了，追加了的方法。因为系统在int类型时，是没有该方法的。

##### 2.给结构体添加方法

上面给整型创建了一个方法，那么直接通过整型变量加上“点”，就可以调用该方法了。

如果给结构体(类)加上了方法，那么根据结构体（类）创建完成对象后，是不是就可以通过对象加上“点”，就可以完成方法的调用，这与调用类中定义的属性的方式是完全一样的。这样就完成了通过方法与属性来描述一个对象的操作。

给结构体添加方法，语法如下：

```go
type Man struct {
	name string
	sex byte
	age int
}
// PrintShow stu是方法接受者
func (stu Man) PrintShow(){
	fmt.Println(stu.name)
}
func main(){
    stu := Man{name: "小落"}
	stu.PrintShow() //创建完对象，调用方法
}
```

给结构体添加方法的方式与前面给int类型添加方法的方式，基本一致。唯一不同的是，**不需要**给结构体指定别名，因为结构体Student就是相当于其所有成员属性的别名（id,name,score）,所以这里不要在给结构体Student创建别名。

**调用方式：根据结构体(类)创建的对象，完成了方法的调用。**

##### 3.注意事项

1. **只要接收者类型不一样，这个方法就算同名，也是不同方法，不会出现重复定义函数的错误，但是，如果接收者类型一样，但是方法的参数不一样，是会出现错误的。GO中没有方法重载**。

   ```go
   /*下面是正确的*/
   type long int //给int取别名为long,本质接受者类型还是int
   func (tmp long) test(){} 
   type char byte 
   func (tmp char) test(){}
   
   /*下面是错误的*/
   type long int
   func (tmp long) test(){}
   func (result long) test(a,b int){}
   ```

   

2. **接收者不能为指针类型**

   ```go
   /*下面是正确的定义*/
   type long int
   func (tmp *long) test(){}
   
   /*下面是错误定义*/
   type point *int
   //point为接收者类型，它本身不能是指针类型
   func (tmp point) test(){}
   ```

   

3. **接收者为普通变量，非指针，值传递；接收者为指针变量，引用传递**

   ```go
   /*下面是值传递*/
   func (stu Man) PrintShow(){
   	fmt.Println(stu.name)
   }
   func main(){
       stu := Man{name: "小落"}
   	stu.PrintShow() //创建完对象，调用方法
   }
   /*下面是引用传递*/
   func (point *Man) EditInfo(name string,sex byte,age int){
   	point.name = name
   	point.sex = sex
   	point.age = age
   }
   func main(){
       stu := Man{name: "小落"}
       (&stu).EditInfo("落落",'m',21)
   	stu.PrintShow()
   }
   ```

#### 3.方法继承

现在我们已经实现了为结构体添加成员(属性)，和方法，并且实现了成员属性的继承，那么方法能否继承呢？

思路：

1. 找出公共的属性，定义父类(结构体)
2. 找出公共的方法，定义在父类(结构体)
3. 找出独有的属性，定义在自己的结构体(类)中。
4. 找出独有的方法，定义在自己的结构体(类)中
5. 完成调用

举个栗子:

```go
package main

import "fmt"

//公共属性 Person类
type Person struct{
    name string
    age int
    sex byte
}
//为Person结构体定义方法，完成公共属性的赋值
func (p *Person) SetValue(name string,sex byte,age int){
    p.name = name
    p.sex = sex
    p.age = age
}

type reporter struct{
    Person //匿名字段
    hobby string //爱好
}
func (r *reporter) reporterSayHello(hobby string){
    r.hobby = hobby
    fmt.Printf("我叫%s,我是一名狗仔，我的爱好是%s,我是%c生,我今年%d岁了",r.name,r.hobby,r.sex,r.age)
}

type programmer struct{
    Person
    workYear int //工作年限
}
func (p *programmer) programmerSayHello(workYear int){
    p.workYear = workYear
    fmt.Printf("我叫%s,我是一名程序猿，我是%c生,我今年%d岁了,我的工作年限是%d年",p.name,p.sex,p.age,p.workYear)
}
```

#### 4.方法重写

子类(结构体)可以继承父类中的方法，但是，如果父类中的方法与子类的方法是重名方法会怎样呢？

如果子类(结构体)中的方法名与父类(结构体)中的方法名同名，在调用的时候是先调用子类(结构体)中的方法，这就方法的重写。所谓的重写：**就是子类(结构体)中的方法，将父类中的相同名称的方法的功能重新给改写了。**

#### 5.方法值与方法表达式

调用结构体的方法还有一种方式：

```go
var dog Dog
dFunc := dog.Bark //保存方法地址
dFunc() 
```

以上调用的方式就称为方法值。这种方式隐藏了接受者。

还有一种调用的方式就是通过方法表达式：

```go
p := Person("mike",'m',18)
f := (*Person).SetInfoPointer //引用传递
f(&p)//显式的把接收者传递过去
f2 := (Person).SetInfoValue
f2(p) //值传递
```

方法表达式需要显式的把接收者传递过去。

#### 6.接口

接口类型是由一组方法定义的集合。接口类型的值可以存放实现这些方法的任何值。只要类(结构体)实现对应的接口，那么根据该类创建的对象，可以赋值给对应的接口类型。

接口的命名习惯以er结尾。

```go
type Abser interface {
    Abs() float64
}
```

类型通过实现定义的方法来实现接口， 不需要显式声明实现某接口：

```go
type MyFloat float64

func (f MyFloat) Abs() float64 {
    if f < 0 {
        return float64(-f)
    }
    return float64(f)
}
```

##### 1.多态

**接口有什么好处呢？实现多态。**

所谓多态指的是多种表现形式，如下图所示：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wps4246.tmp.png) 

该拖拉机既可以扫地又可以当风扇。功能非常强大。

使用接口实现多态的方式如下：

<img src="https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220118185336076.png" alt="image-20220118185336076" style="zoom:200%;" />

如果没有接口，那么只能一个一个的调用其方法。**在使用面向对象思想解决问题时，一定要先分析，定义哪些类，哪些接口，哪些方法。把这些分析定义出来，然后在考虑具体实现。**

##### 2.接口继承与转换（了解）

接口也可以实现继承:

```go
//定义接口类型
type Humaner interface {
	//方法只有声明，没有实现，由别的类型（自定义类型实现）
	sayHi()
}
type teacher struct {
	addr, group string
}
//接口继承
type Personer interface {
	Humaner //匿名字段，继承了sayhi()
	sing(lrc string)
}
	var p Personer
	s1 := &stu{666, "mike"}
	p = s1

	p.sayHi() //继承过来的方法
	p.sing("全是爱")

	//接口转换注意一句话:爷爷可以当孙子，但是孙子不可以当爷爷
	var iSon Personer
	var iFather Humaner

	//iSon = iFather //报错

	iFather = iSon //没问题
	iFather = &stu{666, "mike"}
	iFather.sayHi()
```

**接口转换注意一句话:爷爷可以当孙子，但是孙子不可以当爷爷**

##### 3.空接口

空接口(interface{})不包含任何的方法，正因为如此，所有的类型都实现了空接口，因此空接口可以存储任意类型的数值。

例如：

```go
//空接口万能类型，保存任意类型的值
	var num interface{} = 1
	fmt.Println(" num =", num)

	num = "hhh"
	fmt.Println(" num =", num)
```

当函数可以接受任意的对象实例时，我们会将其声明为interface{}，最典型的例子是标准库fmt中PrintXXX系列的函数，例如：

```go
func Printf(fmt string, args ...interface{})

func Println(args ...interface{})

```

如果自己定义函数，可以如下：

```go
func Test(arg ...interface{}) {
}
```

Test( )函数可以接收任意个数，任意类型的参数。

##### 4.类型断言

我们知道interface的变量里面可以存储任意类型的数值(该类型实现了interface)。那么我们怎么反向知道这个变量里面实际保存了的是哪个类型的对象呢？目前常用的有两种方法：

1. **comma-ok断言**

   Go语言里面有一个语法，可以直接判断是否是该类型的变量： value, ok = element.(T)，这里value就是变量的值，ok是一个bool类型，element是interface变量，T是断言的类型。

   如果element里面确实存储了T类型的数值，那么ok返回true，否则返回false。

   ```go
   any := make([]interface{}, 3)
   	any[0] = 1                         //int
   	any[1] = "hello"                   //string
   	any[2] = teacher{addr: "shanghai"} //teacher
   
   	//类型查询，类型断言,if实现
   	for index, data := range any {
   		//第一个返回的是值，第二个返回的是判断结果的真假
   		if value, ok := data.(int); ok == true {
   			fmt.Printf("any[%d] 类型为int,内容为%d\n", index, value)
   		}
   		if value, ok := data.(string); ok == true {
   			fmt.Printf("any[%d] 类型为string,内容为%s\n", index, value)
   		}
   		if value, ok := data.(teacher); ok == true {
   			fmt.Printf("any[%d] 类型为int,内容为addr=%s,group=%s\n", index, value.addr,value.group)
   		}
   	}
   ```

2. switch断言

   ```go
   //类型查询，类型断言,switch实现
   	for index,data :=range any{
   		switch value := data.(type){
   		case int:
   			fmt.Printf("any[%d] 类型为int,内容为%d\n", index, value)
   		case string:
   			fmt.Printf("any[%d] 类型为string,内容为%s\n", index, value)
   		case teacher:
   			fmt.Printf("any[%d] 类型为int,内容为addr=%s,group=%s\n", index, value.addr,value.group)
   		}
   	}
   ```

### 9.异常处理

#### 1.error接口

Go语言引入了一个关于错误处理的标准模式，即error接口，它是Go语言内建的接口类型，该接口的定义如下：

```go
type error interface {
	Error() string
}
```

Go语言的标准库代码包errors为用户提供如下方法：

```go
package errors
type errorString struct {
	text string
}
func New(text string) error {
	return &errorString{ text }
}
func (e *errorString) Error() string {
	return e. text
}
```

通过以上代码，可以发现error接口的使用是非常简单的（error是一个接口，该接口只声明了一个方法Error()，返回值是string类型，用以描述错误)

下面看一下基本使用:

```go
func MyDiv(a,b int)(result int,err error){
	if b==0{
		err = errors.New("除数不可以为0！")
	}else{
		result = a/b
	}
	return result,err
}
func main(){
    result,err := MyDiv(10,0)
	if err!=nil{
		fmt.Println(err)
	}else{
		fmt.Println(result)
	}
}
```

#### 2.panic函数

error返回的是一般性的错误，但是panic函数返回的是让程序崩溃的错误。

也就是当遇到不可恢复的错误状态的时候，如数组访问越界、空指针引用等，这些运行时错误会引起painc异常，在一般情况下，我们不应通过调用panic函数来报告普通的错误，而应该只把它作为报告致命错误的一种方式。当某些不应该发生的场景发生时，我们就应该调用panic。

一般而言，当panic异常发生时，程序会中断运行。随后，程序崩溃并输出日志信息。日志信息包括panic value和函数调用的堆栈跟踪信息。

当然，如果直接调用内置的panic函数也会引发panic异常；panic函数接受任何值作为参数。

我们在**实际的开发过程中并不会直接调用panic( )函数，但是当我们编程的程序遇到致命错误时，系统会自动调用该函数来终止整个程序的运行，也就是系统内置了panic函数**。

#### 3.延迟调用 defer

##### 1.defer基本使用

函数定义完成后，只有调用函数才能够执行，并且一经调用立即执行。例如：

```go
fmt.Println("nihao")
fmt.Println("go")
```

先输出"nihao"，然后输出"go"。

但是**关键字 defer ⽤于延迟一个函数（或者当前所创建的匿名函数）的执行**。

注意，**defer语句只能出现在函数的内部。**

```go
defer fmt.Println("nihao")
fmt.Println("go")
```

先输出"go"，然后输出"nihao"

defer的应用场景：<u>文件操作，先打开文件，执行读写操作，最后关闭文件。为了保证文件的关闭能够正确执行，可以使用defer.</u>

<u>网络编程时，最后也要关闭整个网络的链接，也会用到defer。</u>

**代码逻辑越复杂，defer使用越重要。**

##### 2.defer执行顺序

```go
defer fmt.Println("nihao")
defer fmt.Println("go")
defer fmt.Println("java")
```

执行结果是：

java

go

nihao

总结：**如果一个函数中有多个defer语句，它们会以LIFO（后进先出）的顺序执行。**

即使函数或某个延迟调用**发生错误，这些调用依旧会被执⾏。**

##### 3.defer与匿名函数结合使用

```go
func err(){
    defer func() {
		//recover()可以打印panic的错误信息,并且使得程序不会中断
		if err := recover();err !=nil{
			fmt.Println(err)
		}
	}() //别忘了(),调用此函数
}
```

再举个栗子：

```go
func double(x int)int{
	return x+x
}
func triple(x int)(r int){
	defer func() {
		r+=x
	}()
	//先执行double(x)
	return double(x)
}
func main(){
    fmt.Println(triple(3)) //输出结果是9
}
```

#### 4.recover

运行时panic异常一旦被引发就会导致程序崩溃。这当然不是我们愿意看到的，因为谁也不能保证程序不会发生任何运行时错误。

Go语言为我们提供了专用于“拦截”运行时panic的内建函数——recover。它可以是当前的程序从运行时panic的状态中恢复并重新获得流程控制权。

```go
func recover() interface{}
```

**注意：recover只有在defer调用的函数中有效。**

```go
func err(){
    defer func() {
		//recover()可以打印panic的错误信息,并且使得程序不会中断
		if err := recover();err !=nil{
			fmt.Println(err)
		}
	}() //别忘了(),调用此函数
}
```

### 10.文本文件处理

#### 1.JSON处理

Go语言内建对JSON的支持。使用Go语言内置的`encoding/json`标准库，开发者可以轻松使用Go程序生成和解析JSON格式的数据。

##### 1.结构体与JSON

```go
package main

import (
	"encoding/json"
	"fmt"
)
//通过结构体生成JSON,成员变量名首字母必须大写
type IT struct {
	Company string `json:"-"` //此字段不会输出到屏幕
	Subject []string `json:"subject"`
	IsOK bool `json:"isOK,string"` //将布尔值输出为字符串
	Price float64 `json:"price"`
}
type it struct {
	Company string `json:"company"` //此字段不会输出到屏幕
	Subjects []string `json:"subjects"`
	IsOK bool `json:"isok"` //将布尔值输出为字符串
	Price float64 `json:"price"`
}
func main() {
	//定义一个结构体变量并初始化
	s := IT{
		Company: "落迁忆",
		Subject: []string{"JAVA","GO","PYTHON"},
		IsOK: true,
		Price: 666.66,
	}
	//编码，根据内容生成json文本
	//buf,err := json.Marshal(s)
	buf,err := json.MarshalIndent(s,""," ") //格式化编码
	if err != nil{
		fmt.Println("err=",err)
		return
	}
	fmt.Println("buf=",string(buf))
    /*json到结构体*/
    jsonBuf := `
{
 "company": "luoqianyi",
 "isok": true,
 "price": 666.66,
 "subjects": [
  "JAVA",
  "PYTHON",
  "GO"
 ]
}`
	//推荐：json到结构体
	var tmp it //定义一个结构体变量
	err2 := json.Unmarshal([]byte(jsonBuf), &tmp) //第二个参数需要地址传递
	if err2 != nil {
		fmt.Println("err",err2)
		return
	}
	fmt.Printf("%+v\n",tmp)
}
```

##### 2.map与JSON

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main(){
    //通过map生成JSON
	m := make(map[string]interface{},4)
	m["company"] = "luoqianyi"
	m["subjects"] = []string{"JAVA","PYTHON","GO"}
	m["isok"] = true
	m["price"] = 666.66
	//编码成json
	//result,err1 := json.Marshal(m)
	result,err1 := json.MarshalIndent(m,""," ")
	if err1 != nil{
		fmt.Println("err1:",err1)
		return
	}
	fmt.Println("result",string(result))
    
    /*不推荐：json到map*/
    jsonBuf := `
{
 "company": "luoqianyi",
 "isok": true,
 "price": 666.66,
 "subjects": [
  "JAVA",
  "PYTHON",
  "GO"
 ]
}`
    //json到map
	ma := make(map[string]interface{},4) //创建一个map
	err3 := json.Unmarshal([]byte(jsonBuf),&ma)
	if err3 != nil {
		fmt.Println("err",err3)
		return
	}
	fmt.Printf("%+v\n",ma)

    //需要类型断言，取出来的结果不是我们想要的
	var str string
	for key,value := range ma{
		switch data := value.(type) {
		case string:
			str = data
			fmt.Printf("ma[%s]值为string,值是%s\n",key,str)
		case bool:
			fmt.Printf("ma[%s]值为bool,值是%v\n",key,value)
		case float64:
			fmt.Printf("ma[%s]值为float64,值是%f\n",key,value)
		case []interface{}:
			fmt.Printf("ma[%s]值为[]interface,值是%v\n",key,value)
		}
	}
}
```



#### 2.文件处理

存储在内存中的数据有什么问题吗？

当断电，或者是关闭电脑时，数据就丢失了。但是我们希望一些数据能够长期的存储在电脑的磁盘上，怎样存储在磁盘上呢？

通过文件来存储。

##### 1.创建文件

将数据存储到文件之前，先要创建文件。GO语言中提供了一个`Create( )`函数专门创建文件.

> **该函数在创建文件时，首先会判断要创建的文件是否存在，如果不存在，则创建，如果存在，会先将文件中已有的数据清空。**

同时，当文件创建成功后，该文件会默认的打开，所以不用在执行打开操作，可以直接向该文件中写入数据。

创建文件的步骤：

1. 导入“os”包，创建文件，读写文件的函数都在该包

2. 指定创建的文件存放路径以及文件名。

3. 执行Create( )函数，进行文件创建

4. 关闭文件

   ```go
   package main
   
   import (
   	"fmt"
   	"os"
   )
   func CreateFile(path string){
   	//创建文件，返回两个值，一是创建的文件，二是错误信息
   	f,err := os.Create(path)
   	if err!=nil{
   		fmt.Println(err)
   		return
   	}
   	defer func(f *os.File) { //在退出整个函数时，关闭文件
   		err := f.Close()
   		if err != nil {
   			fmt.Println(err)
   			return
   		}
   	}(f)
   }
   ```

##### 2.写入数据

文件打开以后，可以向文件中写数据，可以使用常用的`WriteString( )`方法。除此之外，还有`Write()`方法、`WriteAt()`方法，具体实现见下代码：

```go
func CreateFile(path string){
	//创建文件，返回两个值，一是创建的文件，二是错误信息
	f,err := os.Create(path)
	if err!=nil{
		fmt.Println(err)
		return
	}
	defer func(f *os.File) { //在退出整个函数时，关闭文件
		err := f.Close()
		if err != nil {
			fmt.Println(err)
			return
		}
	}(f)
	var str string
	for i := 1; i < 10; i++ {
		str = strconv.Itoa(i)+"\tHello World\n"
		//n, err := f.WriteString(str)  //通过WriteString函数实现
		buf := []byte(str)
		//n, err := f.Write(buf) //通过Write函数实现，参数为字节切片
		a,_ := f.Seek(0,os.SEEK_END) //Seek( )函数返回值存储到变量n中，值为文件末尾的位置。WriteAt( )也返回的是写入的数据长度。
		//从末尾的偏移量开始写入内容
		n,err := f.WriteAt(buf,a)
		fmt.Println(n)
		if err != nil {
			fmt.Println(err)
			return
		}
	}
}
```

##### 3.追加数据

对已经存在的文件不能再执行`Create( )`,而是要执行`OpenFile( )`。

```go
func WriteFile(path string){
	f, err := os.OpenFile(path,os.O_APPEND,6) //追加模式让此文件可读写
	if err != nil {
		fmt.Println(err)
		return
	}
	_, err1:= f.WriteString("World Hello!")
	if err1 != nil {
		fmt.Println(err1)
		return 
	}
	defer func(f *os.File) {
		err := f.Close()
		if err != nil {
			fmt.Println(err)
			return
		}
	}(f)
}

```

`OpenFile( )`这个函数有三个参数，第一个参数表示打开文件的路径，第二个参数表示模式，常见的模式有

`O_RDONLY`(只读模式)，`O_WRONLY`(只写模式),`O_RDWR`( 可读可写模式)，`O_APPEND`(追加模式)。

**第三个参数，表示权限，取值范围（0-7）**

表示如下：

0:没有任何权限

1：执行权限(如果是可执行文件，是可以运行的)

2：写权限

3:写权限与执行权限

4：读权限

5:读权限与执行权限

6:读权限与写权限

7:读权限，写权限，执行权限

##### 4.读取文件

###### 1.Read函数读取文件

如果文件已经存在，并且也已经有数据了，那么可以直接读取该文件中的内容。

读取文件的基本流程如下：

1. 打开要读取的文件
2.  对文件进行读取
3.  关闭文件

在向文件中写数据的时候，使用的是Write,那么读取文件中的数据，使用的是Read.

```go
func ReadFile(path string){
	f, err := os.Open(path)
	if err != nil {
		fmt.Println(err)
		return
	}
	buf := make([]byte,1024*2) //2K大小
	n, err1 := f.Read(buf)
	if err1 != nil && err1 != io.EOF {
		fmt.Println(err1)
		return
	}
	fmt.Println(string(buf[:n])) //输出前n行数据
	defer func(f *os.File) {
		err := f.Close()
		if err != nil {
			return
		}
	}(f)
}
```

解释一下上面代码：

**Open( )是打开文件，与OpenFile( )的区别是，Open( )只有读的权限**

在使用Read( )函数读取文件中的内容时，需要一个切片类型，而定义切片时类型为字符数组，将文件中的内容保存在切片中，同时除了对其判断是否出错时以外，还要判断是否到文件末尾(这里需要导入io包)。

Read( )函数返回的是从文件中读取的数据的长度。最后，输出切片中存储的文件数据，注意，读取的是从最开始到整个数据长度，因为有可能存储到切片中的数据达不到切片的总长度（也是切片时2k,但是从文件中读取的数据有可能只有1k）

###### 2.ReadBytes行读取

上面我们是将文件的内容全部读取出来，然后存放在切片中，我们也可以每次只读取一行数据。

这需要用到`bufio`包中的`ReadBytes`函数。具体如下：

1. 打开文件

   ```go
   f,err := os.Open(path)
   if err != nil{
       fmt.Prinln(err)
       return
   }
   ```

2. 创建缓冲区

   在使用`ReadBytes( )`函数读取数据时，需要用到缓冲区,所谓缓冲区就是存储数据的区域，也就是先将从文件中读取的数据存储在该区域内，然后在将区域中的数据取出来，写到磁盘上。提供缓冲区的原因是：

   **为了缓和 CPU 与 磁盘设备之间速度不匹配矛盾。文件缓冲区是用以暂时存放读写期间的文件数据而在内存区预留的一定空间。**

   ```go
   //新建一个缓冲区，把内容先放到缓冲区里
   r := bufio.NewReader(f)
   ```

   

3. 循环读取文件中的内容，直到文件末尾位置

   在使用`ReadBytes( )`函数时，传递的参数是‘\n’,表示遇到’\n’就结束，所以使用了死循环（每循环一次，读取一行数据），只有到文件末尾了，才退出整个循环。最后，将读取的数据打印出来，注意ReadBytes( )返回的是**字节切片**，所以在打印时要转换成字符串。

   ```go
   for{
       //遇到'\n'结束读取，但是'\n'也读取进入
       buf,err := r.ReadBytes('\n')
       if err != nil && err != io.EOF{
           break
       }
       fmt.Printf("buf=#%s#\n",string(buf))
   }
   ```

4. 最后关闭文件

   ```go
   defer func(f *os.File) {
   		err := f.Close()
   		if err != nil {
   			return
   		}
   	}(f)
   ```

##### 5.综合案例

> 文件拷贝，将已有的文件复制一份，同时重新命名。

```go
/*
基本的思路：

(1) 让用户输入要拷贝的文件的名称(源文件)以及目的文件的名称

(2) 创建目的文件

(3) 打开源文件，并且读取该文件中的内容

(4) 将从源文件中读取的内容写到目的文件中。
*/
package main
import (
	"fmt"
	"io"
	"os"
)

func main() {
	var srcFileName string
	var dstFileName string
	fmt.Printf("请输入源文件名称：")
	_, err := fmt.Scan(&srcFileName)
	if err != nil {
		return 
	}
	fmt.Println("请输入目的文件名称：")
	_, err1 := fmt.Scan(&dstFileName)
	if err1 != nil {
		return 
	}
	//只读方式打开文件
	sF,err1 := os.Open(srcFileName)
	if err1 != nil{
		return
	}
	//新建目的文件
	dF,err2 := os.Create(dstFileName)
	if err2 != nil{
		return
	}

	//操作完毕，需要关闭文件
	defer func() {
		err := sF.Close()
		if err != nil {
			return
		}
		err1 := dF.Close()
		if err1 != nil {
			return
		}
	}()
	//核心处理，从源文件读取内容，往目的文件写，读多少写多少
	buf := make([]byte,1024*4)
	for {
		n, err3 := sF.Read(buf)
		if err3 != nil && io.EOF==err3{
			break
		}
		_, err4 := dF.Write(buf[:n])
		if err4 != nil {
			return
		}
	}	
}
```

