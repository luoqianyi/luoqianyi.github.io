# Go并发与网络

## 一、并发编程

### 1.概述

#### 1.并行与并发

并行：指在同一时刻，有多条指令在多个处理器上同时执行。

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wpsB19D.tmp.png)

并发：指在同一时刻只能有一条指令执行，但多个进场指令被快速的轮换执行，使得在宏观上具有多个进程同时执行的效果，但在微观并不是同时执行的，只是把时间分成若干段，使多个进程快速交替的执行。

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wps2FED.tmp.png)

简单理解一下：

- 并行是两个队列**同时**使用**两台**咖啡机

- 并发是两个队列**交替**使用**一台**咖啡机

  ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wps4798.tmp.jpg)

#### 2.Go语言并发优势

- **Go从语言层面就支持并行**。
- **Go提供了自动垃圾回收机制。**
- Go语言为并发编程而内置的上层API基于CSP(communicating sequential processes, 顺序通信进程)模型。这就**意味着显式锁都是可以避免的**，因为Go语言通过相册安全的通道发送和接受数据以实现同步，这大大地简化了并发程序的编写。
- **可以让成千上万个goroutine进行资源竞争。**

### 2.掌握goroutine概念

#### 1.什么是goroutine?

​	goroutine是Go并行设计的核心。叫做**协程**，比线程更小，十几个goroutine可能体现在底层就是五六个线程，Go语言内部帮你**实现了这些goroutine之间的内存共享**。**执行goroutine只需极少的栈内存**(大概是4~5KB)，当然会根据相应的数据伸缩。**goroutine比thread更易用、更高效、更轻便。**

#### 2.创建goroutine

只需在函数调⽤语句前添加 **go 关键字**，就可**创建并发执⾏单元**。开发⼈员**无需了解任何执⾏细节**，**调度器会自动将其安排到合适的系统线程上执行**。

在并发编程里，我们通常想讲一个过程切分成几块，然后让每个goroutine各自负责一块工作。当一个程序启动时，其主函数即在一个单独的goroutine中运行，我们叫它main goroutine。新的goroutine会用go语句来创建。

```go
package main

import (
	"fmt"
	"time"
)
func newTask(){
	for{
		fmt.Println("this is a new task")
		time.Sleep(time.Second) //延时1s
	}
}
func main() {
	go newTask() //新建一个协程
	for{
		fmt.Println("this is a main goroutine")
		time.Sleep(time.Second) //延时1s
	}
}

```

#### 3.主goroutine先退出

**主goroutine退出后，其它的工作goroutine也会自动退出：**

```go
package main

import (
	"fmt"
	"time"
)

//主协程退出了，其它子协程也要跟着退出
func main() {
	go func() {
		i := 0
		for{
			i++
			fmt.Println("子协程 i=",i)
			time.Sleep(time.Second)
		}
	}()

	i := 0
	for{
		i++
		fmt.Println("main i=",i)
		time.Sleep(time.Second)
		if i==2 {
			break
		}
	}
}
```

输出结果：

```go
main i= 1
子协程 i= 1
子协程 i= 2
main i= 2
```

### 3.掌握runtime包

#### 1.Gosched

runtime.Gosched() 用于**让出CPU时间片**，让出当前goroutine的执行权限，调度器安排其他等待的任务运行，并在下次某个时候从该位置恢复执行。

这就像跑接力赛，A跑了一会碰到代码runtime.Gosched() 就把接力棒交给B了，A歇着了，B继续跑。

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	go func() {
		for i := 0; i < 5; i++ {
			fmt.Println("go")
		}
	}()

	for i := 0; i < 2; i++ {
		//让出时间片，先让别的协程执行，它执行完，再回来执行此协程
		runtime.Gosched()
		fmt.Println("Hello")
	}
}
```

如果不设置Goshced的话结果永远是，注协程先执行完，而另一个协程没有执行完就退出了。

而设置后，上面程序的结果是：两个都可以执行完，确保主协程最后一个结束。

#### 2.Goexit

**调用 `runtime.Goexit()` 将立即终止当前 goroutine 执⾏，调度器确保所有已注册 defer延迟调用被执行。**

```go
func main() {
    go func() {
        defer fmt.Println("A.defer")
        func() {
            defer fmt.Println("B.defer")
            runtime.Goexit() // 终止当前 goroutine, import "runtime"
            fmt.Println("B") // 不会执行
        }()

        fmt.Println("A") // 不会执行
    }() //别忘了()

    //死循环，目的不让主goroutine结束
    for {
    }
}
```

程序运行结果：

```go
B.defer
A.defer
```

#### 3.GOMAXPROCS

调用` runtime.GOMAXPROCS()`用来**设置可以并行计算的CPU核数的最大值，并返回之前的值。**

```go
func main() {
    //n := runtime.GOMAXPROCS(1) //打印结果：111111111111111111110000000000000000000011111...
    n := runtime.GOMAXPROCS(2)     //打印结果：010101010101010101011001100101011010010100110...
    fmt.Printf("n = %d\n", n)

    for {
        go fmt.Print(0)
        fmt.Print(1)
    }
}
```

在第一次执行(`runtime.GOMAXPROCS(1)`)时，最多同时只能有一个goroutine被执行。所以

会打印很多1。过了一段时间后，GO调度器会将其置为休眠，并唤醒另一个goroutine，这时候就开始**打印很多0**了，在打印的时候，goroutine是被调度到操作系统线程上的。

在第二次执行(`runtime.GOMAXPROCS(2)`)时，我们使用了两个CPU，所以两个goroutine可以一起被执行，以同样的频率**交替**打印0和1。

### 5.掌握引用类型channel

goroutine运行在相同的地址空间，因此访问共享内存必须做好同步。**goroutine 奉行<u>通过通信</u>来共享内存，而不是共享内存来通信。**

引⽤类型 channel 是 CSP 模式的具体实现，用于多个 goroutine 通讯。其内部实现了同步，确保并发安全。

#### 1.什么是channel?

和map类似，channel也一个对应**make创建**的底层数据结构的引用。

当我们复制一个channel或用于函数参数传递时，我们只是拷贝了一个channel引用，因此调用者和被调用者将引用同一个channel对象。和其它引用类型一样，**channel的零值也是nil。**

定义一个channel时，也需要定义发送到channel的值的类型。channel可以使用内置的make()函数来创建：

```go
make(chan Type) //等价于make(chan Type,0)
make(chan Type,capacity)
```

**当 capacity= 0 时，channel 是无缓冲阻塞读写的，当capacity> 0 时，channel 有缓冲、是非阻塞的，直到写满 capacity个元素才阻塞写入。**

channel通过操作符<-来接收和发送数据，发送和接收数据的语法：

```go
var ch = make(chan string) //发送value到channel
ch <- value //接收并将其丢弃
x := <-ch //从channel中接收数据，并赋值给x
x,ok := <- ch //功能同上，同时检查通道是否已关闭或者是否为空
```

**默认情况下，channel接收和发送数据都是阻塞的，除非另一端已经准备好，这样就使得goroutine同步变的更加的简单，而不需要显式的lock。**

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	//创建channel
	var ch = make(chan string)
	defer fmt.Println("主协程结束了")
	go func() {
		defer fmt.Println("子协程调用完毕")
		for i := 0; i < 2; i++ {
			fmt.Println("子协程i =",i)
			time.Sleep(time.Second)
		}
		ch <- "牛逼结束了！"
	}()
	str := <-ch //没有数据会阻塞
	fmt.Println(str)
}

```

程序执行结果：

```go
子协程i = 0
子协程i = 1
子协程调用完毕
牛逼结束了！
主协程结束了
```

#### 2.无缓存的channel

无缓冲的通道（unbuffered channel）是指在接收前没有能力保存任何值的通道。

这种类型的通道要求发送 goroutine 和接收 goroutine 同时准备好，才能完成发送和接收操作。如果两个goroutine没有同时准备好，通道会导致先执行发送或接收操作的 goroutine 阻塞等待。

这种对通道进行发送和接收的交互行为本身就是同步的。其中任意一个操作都无法离开另一个操作单独存在。

下图展示两个 goroutine 如何利用无缓冲的通道来共享一个值：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wps316B.tmp.jpg)

- 在第 1 步，两个 goroutine 都到达通道，但哪个都没有开始执行发送或者接收。
- 在第 2 步，左侧的 goroutine 将它的手伸进了通道，这模拟了向通道发送数据的行为。这时，这个 goroutine 会在通道中被锁住，直到交换完成。
- 在第 3 步，右侧的 goroutine 将它的手放入通道，这模拟了从通道里接收数据。这个 goroutine 一样也会在通道中被锁住，直到交换完成。
-  在第 4 步和第 5 步，进行交换，并最终，在第 6 步，两个 goroutine 都将它们的手从通道里拿出来，这模拟了被锁住的 goroutine 得到释放。两个 goroutine 现在都可以去做别的事情了。

无缓冲的channel创建格式：

```go
make(chan Type)  //等价于make(chan Type,0)
```

如果没有指定缓冲区容量，那么该通道就是同步的，因此会阻塞到发送者准备好发送和接收者准备好接收。

栗子和上面的差不多。

#### 3.有缓存的channel

有缓冲的通道（buffered channel）是一种在被接收前能存储一个或者多个值的通道。

这种类型的通道并不强制要求 goroutine 之间必须同时完成发送和接收。通道会阻塞发送和接收动作的条件也会不同。只有在通道中没有要接收的值时，接收动作才会阻塞。只有在通道没有可用缓冲区容纳被发送的值时，发送动作才会阻塞。

这导致有缓冲的通道和无缓冲的通道之间的一个很大的不同：**无缓冲的通道保证进行发送和接收的 goroutine 会在同一时间进行数据交换；有缓冲的通道没有这种保证。**

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/wps2902.tmp.jpg)



- 在第 1 步，右侧的 goroutine 正在从通道接收一个值。
- 在第 2 步，右侧的这个 goroutine独立完成了接收值的动作，而左侧的 goroutine 正在发送一个新值到通道里。
- 在第 3 步，左侧的goroutine 还在向通道发送新值，而右侧的 goroutine 正在从通道接收另外一个值。这个步骤里的两个操作既不是同步的，也不会互相阻塞。
- 最后，在第 4 步，所有的发送和接收都完成，而通道里还有几个值，也有一些空间可以存更多的值。

有缓存的channel创建格式：

```go
make(chan Type,capacity)
```

**如果给定了一个缓冲区容量，通道就是异步的**。只要缓冲区有未使用空间用于发送数据，或还包含可以接收的数据，那么其通信就会无阻塞地进行。

举个栗子：

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	c := make(chan int, 3) //带缓冲的通道
	//内置函数 len 返回未被读取的缓冲元素数量， cap 返回缓冲区大小
	fmt.Printf("len(c)=%d, cap(c)=%d\n", len(c), cap(c))
	go func() {
		defer fmt.Println("子协程结束")
		for i := 0; i < 3; i++ {
			c <- i
			fmt.Printf("子协程正在运行[%d]: len(c)=%d, cap(c)=%d\n", i, len(c), cap(c))
		}
	}()

	time.Sleep(2 * time.Second) //延时2s
	for i := 0; i < 3; i++ {
		num := <-c //从c中接收数据，并赋值给num
		fmt.Println("num = ", num)
	}
	fmt.Println("main协程结束")
}
```

输出结果为：

```go
len(c)=0, cap(c)=3
子协程正在运行[0]: len(c)=1, cap(c)=3
子协程正在运行[1]: len(c)=2, cap(c)=3
子协程正在运行[2]: len(c)=3, cap(c)=3
子协程结束
num =  0
num =  1
num =  2
main协程结束
```

#### 4.range和close

如果发送者知道，没有更多的值需要发送到channel的话，那么让接收者也能及时知道没有多余的值可接收将是有用的，因为**接收者可以停止不必要的接收等待。这可以通过内置的close函数来关闭channel实现**。

```go
package main
import "fmt"
func main() {
	ch := make(chan int)  //创建一个无缓存的channel
	//新建一个goroutine
	go func() {
		for i := 0; i < 5; i++ {
			ch <- i  //往通道写数据
		}
		//不需要再写数据时，关闭channel
		close(ch)
	}()
	for  {
		//如果ok为true,说明管道没有关闭
		if num,ok := <-ch;ok==true{
			fmt.Println(num)
		}else { //管道关闭
			break
		}
	}
}
```

> 注意点：
>
> - channel不像文件一样需要经常去关闭，只有当你确实没有任何发送数据了，或者你想显式的结束range循环之类的，才去关闭channel；
> - **关闭channel后，无法向channel 再发送数据**(引发 panic 错误后导致接收立即返回零值)；
> - **关闭channel后，可以继续向channel接收数据**；
> -  对于nil channel，无论收发都会被阻塞。

可以使用 range 来迭代不断操作channel：

```go
c := make(chan int)
for data,_ := range(c){
    fmt.Println(data)
}
```

#### 5.单方向的channel

默认情况下，通道是双向的，也就是，既可以往里面发送数据也可以同里面接收数据。

但是，**我们经常见一个通道作为参数进行传递而只希望对方是单向使用的，要么只让它发送数据，要么只让它接收数据，这时候我们可以指定通道的方向。**

单向通道的声明如下：

```go
var ch1 chan int //正常channel
var ch2 chan <- float64 //发送方channel，仅可以写入float64
var ch3 <- chan int //接收方channel，仅用于读取int数据
```

- `chan<-` 表示数据进入管道，要把数据写进管道，对于调用者就是输出。

- `<-chan` 表示数据从管道出来，对于调用者就是得到管道的数据，当然就是输入

可以将 channel 隐式转换为单向队列，只收或只发，不能将单向 channel 转换为普通 channel：

```go
c := make(chan int,3)
var send chan <- int = c //普通channel转只发
var recive <- chan = c //普通channel转只收
send <- 3
<-recive
```

举个栗子：

```go
//chan <- //只写
func counter(out chan <- int){
    defer Close(out)
    for i:=0;i<5;i++{
        out <- i  //如果对方不读，会阻塞
    }
}

// <- chan //只读
func printer(in <- chan int){
    for num := range in{
        fmt.Println(num)
    }
}

func main(){
    c := make(chan int)
    go counter(c) //生产者
    printer(c) //消费者
    fmt.Println("done")
}

```

### 6.掌握定时器

#### 1.Timer

**Timer是一个定时器，代表未来的一个单一事件，你可以告诉timer你要等待多长时间，它提供一个channel，在将来的那个时间那个channel提供了一个时间值。**

```go
package main

import (
	"fmt"
	"time"
)
//验证time.NewTimer()，时间到了，只会响应一次
func testReciveOnce(){
	timer := time.NewTimer(1*time.Second)
	for{
		<-timer.C
		fmt.Println("时间到")
	}
}

func main() {
	//testReciveOnce()
	//创建定时器，2s后,定时器就会向自己的C字节发送一个time.Time类型的元素值
	timer1 := time.NewTimer(time.Second*2)
	t1 := time.Now()
	fmt.Printf("t1:%v\n",t1)

	t2 := <-timer1.C
	fmt.Printf("t2:%v\n",t2)

	//如果只是想单纯的等待的话，可以使用time.Sleep来实现
	timer2 := time.NewTimer(time.Second*2) //延时功能
	<-timer2.C
	fmt.Println("2s后")
	time.Sleep(time.Second*2)
	fmt.Println("再一次2s后")
	<-time.After(time.Second*2) //通过After也可以实现延时功能，与Timer类似
	fmt.Println("再再一次2s后")

	timer3 := time.NewTimer(time.Second)
	go func() {
		<- timer3.C
		fmt.Println("Timer 3 失效了")
	}()
	stop := timer3.Stop()  //停止计时器
	if stop{
		fmt.Println("Timer 3 停止了")
	}

	fmt.Println("before")
	timer4 := time.NewTimer(time.Second*5) //原来设置5s
	timer4.Reset(time.Second*1) //重新设置时间
	<- timer4.C
	fmt.Println("after")
}
```

#### 2.Ticker

**Ticker是一个定时触发的计时器，它会以一个间隔(interval)往channel发送一个事件(当前时间)，而channel的接收者可以以固定的时间间隔从channel中读取事件。**

```go
package main

import (
	"fmt"
	"time"
)
func main() {
	ticker := time.NewTicker(1*time.Second)
	i:=0
	for  {
		<- ticker.C
		i++
		fmt.Println("i =",i)
		if i == 5{
			ticker.Stop()
			break
		}
	}
}
```

### 7.掌握select关键字

#### 1.select是什么？

它是Go里的一个关键字，**通过select可以监听channel上的数据流动。**

select的用法与switch语言非常类似，由select开始一个新的选择块，每个选择条件由case语句来描述。

与switch语句可以选择任何可使用相等比较的条件相比， select有比较多的限制，其中最大的一条限制就是**每个case语句里必须是一个IO操作**，大致的结构如下：

```go
select{
    case <- chan1:
    	//如果chan1成功读取到数据，则进行该case处理语句
    case chan2 <- 1:
    	//如果chan2成功写入数据1，则进行该case处理语句
    default:
    	//如果上面都没有成功，则进入default处理流程
}
```

**在一个select语句中，Go语言会按顺序从头至尾评估每一个发送和接收的语句。**

如果没有任意一条语句可以执行(即所有的通道都被阻塞)，那么有两种可能的情况：

-  如果给出了default语句，那么就会执行default语句，同时程序的执行会从select语句后的语句中恢复。
-  如果没有default语句，那么select语句将被阻塞，直到至少有一个通信可以进行下去。

举个栗子：

```go
package main

import (
	"fmt"
)

func fibonacci(ch chan<-int,quit <-chan bool){
	x,y := 1,1
	for{
		//监听channel数据的流动
		select {
		case ch <- x:
			x,y = y,x+y
		case <-quit:
			return
		}
	}
}
func main() {
	ch := make(chan int) //数字通信
	quit := make(chan bool) //程序是否结束
	//消费者，从channel中读取内容
	go func() {
		for i := 0; i < 8; i++ {
			num := <-ch
			fmt.Println(num)
		}
		//停止
		quit <- true
	}()
	//生产者，产生数字，写入channel
	fibonacci(ch,quit)
}

```

#### 2.select设置超时

有时候会出现goroutine阻塞的情况，那么我们如何避免整个程序进入阻塞的情况呢？我们可以利用select来设置超时，通过如下的方式实现：

```go
func main() {
    c := make(chan int)
    o := make(chan bool)
    go func() {
        for {
            select {
            case v := <-c:
                fmt.Println(v)
            case <-time.After(5 * time.Second):
                fmt.Println("timeout")
                o <- true
                break
            }
        }
    }()
    //c <- 666 // 注释掉，引发 timeout
    <-o
}
```





