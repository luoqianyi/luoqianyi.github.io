# Gin框架学习

### 1.Gin简介

#### 1.是什么？

​		Gin是一个golang的微框架，封装比较优雅，API友好，源码注释比较明确，已经发布了1.0版本。具有快速灵活，容错方便等特点。其实对于golang而言，web框架的依赖要远比Python, Java之类的要小。自身的net/http足够简单，性能也非常不错。框架更像是一些常用函数或者工具的集合。借助框架开发，不仅可以省去很多常用的封装带来的时间，也有助于团队的编码风格和形成规范。

​		Gin框架是开源的，可以在github上下载及其源码库。

​		Gin源码地址：https://github.com/gin-gonic/gin

​		Gin官网：https://gin-gonic.com/zh-cn/

#### 2.它的特点？

- **速度**: Gin之所以被很多企业和团队所采用，第一个原因是因为其速度快，性能表现出众。
- **中间件**:和iris类型， gin在处理请求时，支持中间件操作，方便编码处理。
- **路由**:在gin中可以非常简单的实现路由解析的功能，并包含路由组解析功能。
- **内置渲染**: Gin支持JSON、XML和HTML等多种数据格式的渲染，并提供了方便的操作API。



### 2.Gin开发环境搭建

gin框架需要go语言版本在1.6及以上。可以通过go version查看自己的go语言版本是否符合要求。

1. 安全gin框架，去github官网下载克隆到本地。

   安装过程中需要的其它依赖在这里找：[Go](https://github.com/orgs/golang/repositories)、[Google](https://github.com/google\repositories)

2. 简单的HelloWorld!

   ```go
   package main
   
   import (
   	"fmt"
   	"github.com/gin-gonic/gin"
   	"log"
   )
   
   func main() {
   	engine := gin.Default()
   	engine.GET("/hello", func(context *gin.Context) {
   		fmt.Println("请求路径:",context.FullPath())
   		_, err := context.Writer.Write([]byte("hello,gin\n"))
   		context.JSON(200,gin.H{
   			"message":"ok!",
   		})
   		if err != nil {
   			return 
   		}
   	})
   	if err := engine.Run(":8090");	err != nil{
   		log.Fatal(err.Error())
   	}
   
   }
   ```

3. 启动运行

### 3.网络请求与路由处理

#### 1.创建Engine

在gin框架中，Engine被定义为一个结构体，Engine代表gin框架的一个结构体定义，其中包含了路由组、中间件、页面渲染接口、框架配置设置等相关内容。默认的Engine可以通过gin.Default进行创建，或者使用gin.New()同样可以创建。两种方式如下所示：

```go
engine1 = gin.Default()
engine2 = gin.New()
```

gin.Default()和gin.New()的区别在于gin.Default也使用gin.New()创建engine实例，但是**会默认使用Logger和Recovery中间件**。推荐使用gin.Default()创建engine实例。

> Logger是负责进行打印并输出日志的中间件，方便开发者进行程序调试；Recovery中间件的作用是如果程序执行过程中遇到panic中断了服务，则Recovery会恢复程序执行，并返回服务器500内部错误。通常情况下，我们使用默认的gin.Default创建Engine实例。

#### 2.处理Http请求

在创建的engine实例中，包含很多方法可以直接处理不同类型的HTTP请求。OPTIONS、HEAD、GET、POST、PUT、DELETE、TRACE、CONNECT

1. 通常按照下面的方式进行处理：

通过engine中可以直接进行HTTP请求的处理，在engine中使用**`Handle`方法**进行http请求的处理。Handle方法包含三个参数，具体如下所示：

```go
func (group *RouterGroup) Handle(httpMethod, relativePath string, handlers ...HandlerFunc) IRoutes
```

- httpMethod：第一个参数表示要处理的HTTP的请求类型，是GET、POST、DELETE等8种请求类型中的一种。
- relativePath：第二个参数表示要解析的接口，由开发者进行定义。
- handlers：第三个参数是**处理对应的请求的代码的定义。**

举个栗子：

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

func main() {
	engine := gin.Default()
	//GET请求
	engine.Handle("GET","/hello", func(context *gin.Context) {
		path := context.FullPath()
		fmt.Println(path)
		name := context.DefaultQuery("name","hello")
		context.JSON(200,gin.H{
			"message":name,
		})
	})
	err := engine.Run(":9999")
	if err != nil {
		return 
	}

	//POST请求
	engine.Handle("POST","/login", func(context *gin.Context) {
		name := context.FullPath()
		fmt.Println(name)
		userName := context.PostForm("name")
		password := context.PostForm("password")
		context.JSON(200,gin.H{
			"name":userName,
			"password":password,
		})
	})

}
```

**`context.DefaultQuery`：** 除了`context.DefaultQuery`方法获取请求携带的参数数据以外，还可以使用`context.Query`方法来获取Get请求携带的参数。

Context是gin框架中封装的一个结构体，这是gin框架中最重要，最基础的一个结构体对象。该结构体可以提供我们操作请求，处理请求，获取数据等相关的操作，通常称之为上下文对象，简单说为我们提供操作环境。

可以通过context.Query和context.DefaultQuery获取GET请求携带的参数。

可以通过context.Writer.Write向请求发起端返回数据。

**`context.PostForm:POST`：请求是以form表单的方式提交数据的，可以通过`context.PostForm`获取表单中提交的数据字段。**

2. 也可以进行分类处理

   除了engine中包含的通用的处理方法以外，engine还可以按类型进行直接解析。engine中包含有get方法、post方法、delete方法等与http请求类型对应的方法。

   - engine中包含GET方法处理HTTP的**GET类型**的请求。engine的GET方法包含两个参数：

   ```go
   engine.GET("/hello", func(context *gin.Context) {
       fmt.Println(context.FullPath())
   
       username := context.Query("name")
       fmt.Println(username)
   
       context.Writer.Write([]byte("Hello," + username))
   })
   ```

   - engine.POST()处理**POST请求**

   ```go
   engine.POST("/login", func(context *gin.Context) {
       fmt.Println(context.FullPath())
       username, exist := context.GetPostForm("username")
       if exist {
           fmt.Println(username)
       }
       password, exists := context.GetPostForm("pwd")
       if exists {
           fmt.Println(password)
       }
       context.Writer.Write([]byte("Hello , " + username))
   })
   ```

   **`context.GetPostForm`获取表单数据：**POST请求以表单的形式提交数据,除了可以使用`context.PostForm`获取表单数据意外，还可以使用`context.GetPostForm`来获取表单数据。

   - engine.DELETE()处理**DELETE请求**

     在项目开发中，通常都是遵循RESTful标准进行接口开发。除了GET、POST以外，还会有DELETE等操作。

     比如要执行某个删除操作，会发送DELETE类型的请求，同时需要携带一些操作的参数。比如要删除用户，按照RESTful标准会进行如下所示：

     ```go
     engine.DELETE("/user/:id", DeleteHandle)
     func DeleteHandle(context *gin.Context) {
         fmt.Println(context.FullPath())
     
         userID := context.Param("id")
     
         fmt.Println(userID)
     
         context.Writer.Write([]byte("Delete user's id : " + userID))
     }
     ```

     **`context.Param`获取请求参数**:客户端的请求接口是DELETE类型，请求url为：http://localhost:9999/user/1。最后的1是要删除的用户的id，是一个变量。因此在服务端gin中，通过路由的:id来定义一个要删除用户的id变量值，同时使用`context.Param`进行获取。

#### 3.RouterGroup

之所以engine中包含通用型的Handle和分类处理的GET、POST等类型的方法，是因为Engine中有RouterGroup作为匿名字段。

RouteGroup可以称之为路由集合，在gin中定义为结构体：

```go
type RouterGroup struct {
    Handlers HandlersChain
    basePath string
    engine   *Engine
    root     bool
}
```

RouteGroup的作用就是为每一个服务请求提供解析功能，并指定每一个请求对应的处理程序。

### 4.请求参数绑定与多数据格式处理

像之前这种只有username和password两个字段的表单数据进行提交时，可以使用`context.PostForm`和`context.GetPostForm`获取。但是如果表单数据较多时，使用PostForm和GetPostForm一次获取一个表单数据，开发效率较慢。

**Gin框架提供给开发者表单实体绑定的功能，可以将表单数据与结构体绑定。**

#### 1.表单实体绑定

使用PostForm这种单个获取属性和字段的方式，代码量较多，需要一个一个属性进行获取。而表单数据的提交，往往**对应着完整的数据结构体定义**，其中对应着表单的输入项。gin框架提供了数据结构体和表单提交数据绑定的功能，提高表单数据获取的效率。如下所示：

以一个用户注册功能来进行讲解表单实体绑定操作。用户注册需要提交表单数据，假设注册时表单数据包含三项，分别为：username、phone和password。

```go
type UserRegister struct {
    Username string `form:"username" binding:"required"`
    Phone    string `form:"phone" binding:"required"`
    Password string `form:"password" binding:"required"`
}
```

 创建了UserRegister结构体用于接收表单数据，通过**tag标签的方式设置每个字段对应的form表单中的属性名，通过binding属于设置属性是否是必须**。

#### 2.常用的三种数据绑定方式

**注意：Shouldxxx和bindxxx区别就是bindxxx会在head中添加400的返回信息，而Shouldxxx不会**

##### 1.ShouldBindQuery为GET方式的绑定

```go
func main() {

    engine := gin.Default()

    // http://localhost:8080/hello?name=davie&classes=软件工程
    engine.GET("/hello", func(context *gin.Context) {

        fmt.Println(context.FullPath())

        var student Student
        err := context.ShouldBindQuery(&student)
        if err != nil {
            log.Fatal(err.Error())
        }

        fmt.Println(student.Name)
        fmt.Println(student.Classes)
        context.Writer.Write([]byte("hello," + student.Name))

    })

    engine.Run()
}

type Student struct {
    Name    string form:"name"
    Classes string form:"classes"
}
```

##### 2.ShouldBind为POST方式的绑定

```go
func main() {

    engine := gin.Default()

    engine.POST("/register", func(context *gin.Context) {
        fmt.Println(context.FullPath())
        var _register Register
        if err := context.ShouldBind(&_register); err != nil {
            log.Fatal(err.Error())
            return
        }

        fmt.Println(_register.UserName)
        fmt.Println(_register.Phone)
        context.Writer.Write([]byte(_register.UserName + " Register "))

    })

    engine.Run()
}

type Register struct {
    UserName string form:"name"
    Phone    string form:"phone"
    Password string form:"pwd"
}
```

##### 3.ShouldBindJSON

```go
package main

import (
	"encoding/json"
	"fmt"
	"github.com/gin-gonic/gin"
)

func main() {
	engine := gin.Default()

	engine.POST("/register", func(context *gin.Context) {
		fmt.Println(context.FullPath())
		var person Person
		//前端传json字符串的处理方式
		data, _ := context.GetRawData()
		context.ShouldBindJSON(json.Unmarshal(data,&person)).Error()
		fmt.Println(person.Name)
		fmt.Println(person.Age)
		context.JSON(200,person)
	})

	err := engine.Run(":8989")
	if err != nil {
		return
	}
}

type Person struct {
	Name string `json:"name"`
	Sex  string `json:"sex"`
	Age  int    `json:"age"`
}
```

### 5.多数据格式返回请求结果

我们都知道，**一个完整的请求*包含请求*、*处理请求*和*结果返回*三个步骤**，在服务器端对请求处理完成以后，会将结果返回给客户端。在gin框架中，支持返回多种请求数据格式如下。

1. **[]byte**

在之前的课程案例中，我们统一使用的请求返回数据格式为[]byte。通过context.Writer.Write方法写入[]byte数据。编码案例如下所示：

```go
engine := gin.Default()
engine.GET("/hello", func(context *gin.Context) {
        fullPath := "请求路径：" + context.FullPath()
        fmt.Println(fullPath)
        context.Writer.Write([]byte(fullPath))
})
engine.Run()
```

如上段代码所示，使用context.Writer.Write向客户端写入返回数据。Writer是gin框架中封装的一个ResponseWriter接口类型。ResponseWriter源码定义如下所示：

```go
type ResponseWriter interface {
    http.ResponseWriter
    http.Hijacker
    http.Flusher
    http.CloseNotifier

    // Returns the HTTP response status code of the current request.
    Status() int

    // Returns the number of bytes already written into the response http body.
    // See Written()
    Size() int

    // Writes the string into the response body.
    WriteString(string) (int, error)

    // Returns true if the response body was already written.
    Written() bool

    // Forces to write the http header (status code + headers).
    WriteHeaderNow()

    // get the http.Pusher for server push
    Pusher() http.Pusher
}
```

其中的write方法就是http.ResponseWriter中包含的方法。

2. **string**

除了write方法以外，ResponseWriter自身还封装了WriteString方法返回数据。

```go
// Writes the string into the response body.
WriteString(string) (int, error)
```

和[]byte类型调用一样，可以通过Writer进行调用。详细编程示例如下所示：

```go
engine.GET("/hello", func(context *gin.Context) {
        fullPath := "请求路径：" + context.FullPath()
        fmt.Println(fullPath)
        context.Writer.WriteString(fullPath)
})
```

3. **JSON**

除了使用context.Writer对象返回[]byte和string类型的数据意外。在项目开发中，JSON格式规范使用的更为普遍。gin为了方便开发者更方便的使用该框架进行项目开发，直接支持将返回数据组装成JSON格式进行返回。

gin框架中的context包含的JSON方法可以将结构体类型的数据转换成JSON格式的结构化数据，然后返回给客户端。

4. **map类型**

编程调用如下所示：

```go
...
engine := gin.Default()
engine.GET("/hellojson", func(context *gin.Context) {
    fullPath := "请求路径：" + context.FullPath()
    fmt.Println(fullPath)

    context.JSON(200, map[string]interface{}{
        "code":    1,
        "message": "OK",
        "data":    fullPath,
    })
})
engine.Run(":9000") 
...COPY
```

如上,调用JSON将map类型的数据转换成为json格式并返回给前端，第一个参数200表示设置请求返回的状态码。和http请求的状态码一致。

5. **结构体类型**

除了map以外，结构体也是可以直接转换为JSON格式进行返回的。结构体转换编程如下所示：

```go
//通用请求返回结构体定义
type Response struct {
    Code    int         json:"code"
    Message string      json:"msg"
    Data    interface{} json:"data"
}

engine.GET("/jsonstruct", func(context *gin.Context) {
    fullPath := "请求路径：" + context.FullPath()
    fmt.Println(fullPath)
    resp := Response{Code: 1, Message: "Ok", Data: fullPath}
    context.JSON(200, &resp)
})
```

6. **HTML模板**

除了JSON格式以外，gin框架还支持返回HTML格式的数据。可以直接渲染HTML页面。举例如下：

```go
engine := gin.Default()
//设置html的目录
engine.LoadHTMLGlob("./html/*")
engine.GET("/hellohtml", func(context *gin.Context) {
    fullPath := "请求路径:" + context.FullPath()

    context.HTML(http.StatusOK, "index.html", gin.H{
        "title":    "Gin教程",
        "fullpath": fullPath,
    })
})
engine.Run(":9000")
```

如上可以通过context.HTML来加载HTMl页面或者模板。同时需要在项目目录下有相应的html模板文件，如下所示:

```html
{{.title}}

Gin教程

{{.fullpath}}
```

7. **加载静态资源文件**

在上面的index.html的基础上，添加一张img进行展示。需要将img所在的目录进行静态资源路径设置才可能会生效，如下所示：

```go
engine.Static("/img", "./img")
```

在工程项目的根目录下创建img目录，用于存放静态的img资源。

同理，在项目开发时，一些静态的资源文件如html、js、css等可以通过静态资源文件设置的方式来进行设置。

### 6.使用路由组分类处理请求

在实际的项目开发中，均是模块化开发。同一模块内的功能接口，往往会有相同的接口前缀。比如如下所示：

例如在系统中有用户模块，用户有不同注册、登录、用户信息、

```go
注册：http://localhost:9000/user/register
登录：http://localhost:9000/user/login
用户信息：http://localhost:9000/user/info
删除：http://localhost:9000/user/1001
```

类似这种接口前缀统一，均属于相同模块的功能接口。可以使用路由组进行分类处理。

**gin框架中可以使用路由组来实现对路由的分类**。

路由组是`router.Group`中的一个方法，用于对请求进行分组。如下案例所示：

```go
engine := gin.Default()
userGroup := engine.Group("/user")
userGroup.GET("/register", registerHandle)
userGroup.GET("/login", loginHandle)
userGroup.GET("/info", infoHandle)
engine.Run(":9000")

func registerHandle(context *gin.Context) {
    fullPath := " 用户注册功能 " + context.FullPath()
    fmt.Println(fullPath)
    context.Writer.WriteString(fullPath)
}

func loginHandle(context *gin.Context) {
    fullPath := " 用户登录功能 " + context.FullPath()
    fmt.Println(fullPath)
    context.Writer.WriteString(fullPath)
}

func infoHandle(context *gin.Context) {
    fullPath := " 信息查看功能 " + context.FullPath()
    fmt.Println(fullPath)
    context.Writer.WriteString(fullPath)
}COPY
```

Group返回一个`RouterGroup`指针对象，而`RouterGroup`是gin框架中的一个路由组结构体定义。我们来看一下`RouterGroup`的定义如下所示:

```go
type RouterGroup struct {
    Handlers HandlersChain
    basePath string
    engine   *Engine
    root     bool
}COPY
```

RouterGroup实现了IRoutes中定义的方法，包含统一处理请求的Handle和分类型处理的GET、POST等。IRouters定义如下所示：

```go
type IRoutes interface {
    Use(...HandlerFunc) IRoutes

    Handle(string, string, ...HandlerFunc) IRoutes
    Any(string, ...HandlerFunc) IRoutes
    GET(string, ...HandlerFunc) IRoutes
    POST(string, ...HandlerFunc) IRoutes
    DELETE(string, ...HandlerFunc) IRoutes
    PATCH(string, ...HandlerFunc) IRoutes
    PUT(string, ...HandlerFunc) IRoutes
    OPTIONS(string, ...HandlerFunc) IRoutes
    HEAD(string, ...HandlerFunc) IRoutes

    StaticFile(string, string) IRoutes
    Static(string, string) IRoutes
    StaticFS(string, http.FileSystem) IRoutes
}
```

### 7.中间件的使用

在实际的业务开发和处理中，会有更负责的业务和需求场景。一个完整的系统可能要包含鉴权认证、权限管理、安全检查、日志记录等多维度的系统支持。

**鉴权认证、权限管理、安全检查、日志记录等这些保障和支持系统业务属于全系统的业务，和具体的系统业务没有关联，对于系统中的所有业务都适用。**由此，在业务开发过程中，为了更好的梳理系统架构，可以将上述描述所涉及的一些通用业务单独抽离并进行开发，然后以插件化的形式进行对接。这种方式既保证了系统功能的完整，同时又有效的将具体业务和系统功能进行解耦，并且，还可以达到灵活配置的目的。

这种通用业务独立开发并灵活配置使用的组件，一般称之为"中间件"，因为其位于服务器和实际业务处理程序之间。其含义就是相当于在请求和具体的业务逻辑处理之间增加某些操作，这种以额外添加的方式不会影响编码效率，也不会侵入到框架中。中间件的位置和角色示意图如下图所示：

![image-20220125195126072](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220125195126072.png)

#### 1.Gin的中间件

在gin中，中间件称之为middleware,中间件的类型定义如下所示：

```go
// HandlerFunc定义gin middleware使用的处理程序作为返回值。
type HandlerFunc func(*Context)
```

`gin.Default`创建了gin引擎engins变量，其中，就使用了中间件。如下源码所示：

```go
func Default() *Engine {
    debugPrintWARNINGDefault()
    engine := New()
    engine.Use(Logger(), Recovery())
    return engine
}
//Log中间件
func Logger() HandlerFunc {
    return LoggerWithConfig(LoggerConfig{})
}
//Recovery中间件
func Recovery() HandlerFunc {
    return RecoveryWithWriter(DefaultErrorWriter)
}
```

在Default函数中，engine调用Use方法设置了Logger中间件和Recovery中间件。**Use函数接收一个可变参数，类型为HandlerFunc，恰为中间件的类型**。Use方法定义如下：

```go
func (engine *Engine) Use(middleware ...HandlerFunc) IRoutes {
    engine.RouterGroup.Use(middleware...)
    engine.rebuild404Handlers()
    engine.rebuild405Handlers()
    return engine
}
```

#### 2.自定义中间件

我们可以自己定义实现一个特殊需求的中间件，中间件的类型是函数，有两条标准：

- func函数
- 返回值类型为HandlerFunc

举个栗子：

在前面所学的内容中，在处理请求时，为了方便代码调试，通常都将请求的一些信息打印出来。有了中间件以后，**为了避免代码多次重复编写，使用统一的中间件来完成**。定义一个名为RequestInfos的中间件，在该中间件中打印请求的path和method。

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)


//打印请求信息的中间件
func RequestInfos() gin.HandlerFunc{
	return func(context *gin.Context) {
		path := context.FullPath()
		method := context.Request.Method

		fmt.Println("请求路径:",path)
		fmt.Println("请求方法:",method)

		fmt.Println("状态码:",context.Writer.Status())
		context.Next()
		fmt.Println("状态码:",context.Writer.Status())
	}
}

func main() {
	engine := gin.Default()
	engine.Use(RequestInfos())

	//单独使用就不需要使用Use方法了
	engine.GET("/test", RequestInfos(),func(context *gin.Context) {
		_, err := context.Writer.WriteString("成功！")
		if err != nil {
			return
		}
	})
	engine.GET("/test1", func(context *gin.Context) {

		fmt.Println("看看next起作用了没")

		context.JSON(404, map[string]interface{}{
			"message":"成功！",
		})
	})

	engine.Run(":9999").Error()
}

```

**这里的`context.Next`函数可以将中间件代码的执行顺序一分为二，Next函数调用之前的代码在请求处理之前之前，当程序执行到context.Next时，会中断向下执行，转而先去执行具体的业务逻辑，执行完业务逻辑处理函数之后，程序会再次回到context.Next处，继续执行中间件后续的代码。**

上述程序执行结果如下：

```go
//请求test1时：
请求路径: /test1
请求方法: GET
状态码: 200
看看next起作用了没
状态码: 404
[GIN] 2022/01/25 - 20:17:53 | 404 |       496.3µs |             ::1 | GET      "/test1"

//请求test时：
请求路径: /test
请求方法: GET
状态码: 200
请求路径: /test
请求方法: GET
状态码: 200
状态码: 200
状态码: 200
[GIN] 2022/01/25 - 20:20:36 | 200 |            0s |             ::1 | GET      "/test"
```

通过打印的顺序可以看到，**Next函数将中间件程序的代码执行分为了前后连个部分，Next之前的按照顺序执行，Next之后会在业务逻辑处理完毕后再执行。**而在test接口里直接执行这个中间件方法也是可以成功执行的。

- 关于Next函数的作用及代码执行流程示意图如下图所示：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/WX20191012-163117@2x.png)程序先执行①和②。执行到③时，转而去执行业务处理程序。返回到中间件中，执行④。

### 8.Gin框架中使用数据库

1. 安装MySQL驱动
   想要在Gin中操作MySQL，首先要有MySQL驱动程序。该驱动程序需要下载：[https://github.com/go-sql-driver/mysql](https://github.com/go-sql-driver/mysql)

2. 创建数据库

3. Gin编程连接数据库

   1. ​	引入mysql驱动程序：使用import将mysql驱动默认引入

      ```go
      import _ "github.com/go-sql-driver/mysql"
      ```

   2. 拼接链接字符：在程序中链接mysql，需要按照一定的规则进行用户名，密码等信息的组合

      ```go
      connStr := "root:luoqianyi@tcp(127.0.0.1:3306)/ginsql"
      ```

   3. 使用sql.Open创建数据库连接

      ```go
      db, err := sql.Open("mysql", connStr)
          if err != nil {
              log.Fatal(err.Error())
              return
          }
      ```

   4. 操作数据库

      1. 创建数据库表

         ```go
           _, err = db.Exec("create table person(" +
                             "id int auto_increment primary key," +
                             "name varchar(12) not null," +
                             "age int default 1" +
                             ");")
         if err != nil {
         	log.Fatal(err.Error())
         	return
         }
         ```

      2. 插入数据

         ```go
           _, err = db.Exec("insert into person(name,age) "+
                                 "values(?,?);", "Lily", 15)
         if err != nil {
             log.Fatal(err.Error())
             return
         } else {
             fmt.Println("数据插入成功")
         }
         ```

      3. 查询数据库记录

         ```go
         scan:
         	if query.Next(){
         		//columns, err := query.Columns()
         		//if err != nil {
         		//	return
         		//}
         		//fmt.Println(columns)
         		person := new(Person)
         		err := query.Scan(&person.Id,&person.Name,&person.Age) //必须按照查询的字段顺序来，不然查不到
         		if err != nil {
         			log.Fatal(err.Error())
         			return
         		}
         		fmt.Println(person.Id,person.Age)
         		goto scan
         	}
         
         ```

### **9.使用Gin框架项目开发**

#### 1.简单的HelloWorld接口

1. 创建项目

   在gopath的src目录下，创建OnlineRestaurant目录，作为服务端项目。

   ```go
   mkdir CloudRestaurant
   ```

2. 创建配置文件目录

   ```go
   mkdir config
   ```

   config目录中，配置app.json配置文件：

   ```go
   {
     "app_name": "qianfengmall",
     "app_mode": "debug",
     "app_host": "localhost",
     "app_port": "8000"
   }
   ```

   并创建Config.go文件，用于解析项目配置信息。Config.go如下：

   ```go
   type Config struct {
       AppName  string         json:"app_name"
       AppMode  string         json:"app_mode"
       AppHost  string         json:"app_host"
       AppPort  string         json:"app_port"
   }
   var _cfg *Config = nil
   func GetConfig() *Config {
       return _cfg
   }
   
   func ParseConfig(path string) (*Config, error) {
       file, err := os.Open(path)
       if err != nil {
           panic(err)
       }
       defer file.Close()
   
       reader := bufio.NewReader(file)
       decoder := json.NewDecoder(reader)
       if err := decoder.Decode(&_cfg); err != nil {
           return nil, err
       }
       return _cfg, nil
   }
   ```

3. 编写main.go

   ```go
   func main() {
       //读取配置文件
       cfg, err := toolbox.ParseConfig("./config/app.json")
       if err != nil {
           toolbox.Error(err.Error())
           return
       }
       app := gin.Default()
       app.Run(cfg.AppHost + ":" + cfg.AppPort)
   }
   ```

4. 编写HelloWorld

   - 编写Controller：创建controller，并创建HelloController。

     ```go
     package controller
     
     import "github.com/gin-gonic/gin"
     
     type HelloController struct {
     }
     
     func (hello *HelloController) Router(engine *gin.Engine) {
         engine.GET("/hello", hello.Hello)
     }
     
     func (hello *HelloController) Hello(context *gin.Context) {
         context.JSON(200, map[string]interface{}{
             "message": "hello world",
         })
     }
     ```

   - 在main.go程序中添加路由设置

     ```go
     func registerRouter(router *gin.Engine) {
         new(controller.HelloController).Router(router)
     }
     ```

#### 2.业务搭建：集成阿里云短信发送功能

##### 1.常见短信平台

很多云平台，比如阿里云，腾讯云，七牛云等云厂商，向程序开发者提供了短信验证码套餐服务。借助云平台的短信服务，程序开发者可以非常简单方便的将短信服务集成到自己的程序中。

以接入和集成阿里云的短信服务sdk为例进行讲解。

##### 2.登录阿里云

登录阿里云：https://www.aliyun.com/

选择短信服务模块：https://www.aliyun.com/product/sms

注明：云平台的短信服务是收费的，有不同类别的套餐。开发者可以按照自己的需求进行选择。如下图：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/WX20191027-211926@2x.png)

##### 3. Go短信服务的SDK

可以访问https://api.aliyun.com/?spm=5176.12207334.0.0.54d71cbe3NE29f#/?product=Dysmsapi&lang=GO查看go语言版本的短信服务sdk代码。

1. 安装阿里云SDK：https://github.com/aliyun/alibaba-cloud-sdk-go

2. 创建签名和短信模板：

   - 在使用go sdk集成短息服务之前，需要首先创建短信签名和短信模板。

   - **签名：**短信签名是短信服务提供的一种快捷、方便的个性化签名方式。当发送短信时，短信平台会根据设置，在短信内容里附加个性签名，再发送给指定手机号码。比如，下图红框中的"招商银行”就是这条短信的签名，用以标识发送者的类别等内容。

     ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/WX20191027-213103@2x.png)

   - 在阿里云后台的短信服务控制面板的左侧功能栏中，选择国内消息，如下图所示，然后选择签名管理的TAB，然后选择添加签名，可以创建新的短信签名。如下图:

![img](https://www.qfgolang.com/wp-content/uploads/2019/11/WX20191027-213457@2x.png)
    

![img](https://gitee.com/luoqianyi/static-image/raw/master/图床/WX20191027-213715@2x.png)

 ![img](https://www.qfgolang.com/wp-content/uploads/2019/11/WX20191027-214149@2x.png)

 **短信模板：**短信模版，即具体发送的短信内容。短信模版可以支持验证码、短信通知、推广短信三种模式。验证码和短信通知，通过变量替换实现个性短信定制。
 与创建签名同理，可以在模板管理TAB中，选择添加模板，用来创建新的短信模板。创建完成后，会进行审核。

 ![img](https://gitee.com/luoqianyi/static-image/raw/master/图床/WX20191027-214149@2x-1.png)

 创建成功后的短信模板的模版CODE需要记住，后续会使用到。

3. 创建AccessKey和AccessKeySercet


   ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/WX20191027-214336@2x.png)

   在短信服务管理平台的概览界面，可以选择AccessKey按钮进行创建集成短信服务所需要的AccessKey和AccessKeySecret。

   ![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/WX20191027-214648@2x.png)

4. 在程序中集成短信服务

   官方写法：

   ```go
   package main
   
   import (
       "fmt"
         "github.com/aliyun/alibaba-cloud-sdk-go/services/dysmsapi"
   
   )
   
   func main() {
       client, err := dysmsapi.NewClientWithAccessKey("dysmsapi", "<accessKeyId>", "<accessSecret>")
   
       request := dysmsapi.CreateSendSmsRequest()
       request.Scheme = "https"
   
       request.PhoneNumbers = "1503871****" //接收短信的手机号码
       request.SignName = "阿里大于测试专用" //短信签名名称
       request.TemplateCode = "SMS_209335004" //短信模板ID
       request.TemplateParam = "{\"code\":\"1111\"}" //短信模板变量对应的实际值，JSON格式
   
       response, err := client.SendSms(request)
       if err != nil {
           fmt.Print(err.Error())
       }
       fmt.Printf("response is %#v\n", response)
   }
   ```

   

##### 4.创建Controller解析请求

无论是短信登录，还是用户名和密码登录，均属于用户功能模块。因此，创建MemberController用于解析用户模块的各个请求：

```go
type MemberController struct {
}

func (mc *MemberController) Router(engine *gin.Engine) {
    //发送手机验证码
    engine.GET("/api/sendcode", mc.sendSmsCode)
}

func (mc *MemberController) sendSmsCode(context *gin.Context) {
    phone, exist := context.GetQuery("phone")
    if !exist {
        toolbox.Failed(context, "参数解析失败")
        return
    }

    service := impl.NewMemberService()
    code := service.SendCode(phone)
    if code != "" {
        toolbox.Success(context, code)
        return
    }
    toolbox.Failed(context, "验证码发送失败")
}
```

创建用户控制层程序MemberController,使用GET方法解析客户端发送短信验证码的请求，请求接口为：/api/sendcode，同时指定sendSmsCode方法处理接口请求。

在实际的开发中，程序功能往往是分层来进行开发的，controller层只负责控制和处理接口请求的逻辑，具体的功能调用，往往由另外称为service层的功能服务层来进行实现。

在service层中，依然按照模块化开发的理念，将用户模块的服务统一由UserServicej进行提供。因此，创建MemberService,并提供SendCode方法。

```go
type MemberService struct {
}

func (msi *MemberService) SendCode(phone string) string {
    code := fmt.Sprintf("%06v", rand.New(rand.NewSource(time.Now().UnixNano())).Int31n(1000000))

    smsConfig := toolbox.GetConfig().Sms
    //调用阿里云短信接口发送短信
    client, err := dysmsapi.NewClientWithAccessKey(smsConfig.RegionId, smsConfig.AppKey, smsConfig.AppSecret)
    if err != nil {
        toolbox.Error(err.Error())
        return ""
    }

    request := dysmsapi.CreateSendSmsRequest()
    request.Scheme = "https"

    request.SignName = smsConfig.SignName
    request.TemplateCode = smsConfig.TemplateCode
    request.PhoneNumbers = phone
    par, err := json.Marshal(map[string]interface{}{
        "code": code,
    })
    request.TemplateParam = string(par)

    response, err := client.SendSms(request)

    fmt.Println( response)
    if err != nil {
        toolbox.Error(err.Error())
        return ""
    }

    if response.Code != "OK" {
        return ""
    }
    return code
}
```

##### 5.测试

略

#### 3.数据库XORM框架整合使用

在用户接受到验证码以后，输入验证码进行登录，我们需要验证用户输入的验证码是否正确。

因此，我们需要将发送过的验证码通过持久化的方式保存下来，方便我们进行校验。

我们这里可以先选择通过数据库来存储用户手机验证码。



在项目开发过程中，我们会使用一些成熟的框架来操作数据库。xorm就是一个比较流行的数据库操作orm框架。

1. xorm安装及mysql驱动

   xorm:https://gitea.com/xorm/xorm

   mysql驱动下载地址：https://github.com/go-sql-driver/mysql

2. 连接数据库

   在连接数据库之前，首先要创建数据库。在mysql中创建cloudrestaurant数据库：

   ```go
   create database cloudrestaurant;
   ```

   创建完数据库并安装好xorm库以后，使用xorm进行连接数据库。具体的连接操作如下所示：

   ```go
   import (
       "github.com/go-xorm/xorm"
       _ "github.com/go-sql-driver/mysql"
   )
   database := cfg.Database
   conn := database.User + ":" + database.Password + "@tcp(" + database.Host + ":" + database.Port + ")/" + database.DbName + "?charset=" + database.Charset
   engine, err := xorm.NewEngine(database.Driver, conn)
   if err != nil {
       return nil, err
   }
   ```

   连接数据库有些参数需要自己指定，比如说驱动类型，登录数据库的用户名，密码，数据库名等。将这些变量配置在app.json配置文件中，如下所示：

   ```go
   {
   "database": {
       "driver": "mysql",
       "user": "root",
       "password": "12345678",
       "host": "127.0.0.1",
       "port": "3306",
       "db_name": "cloudrestaurant",
       "charset": "utf8mb4",
       "show_sql": true
   }
   }
   ```

   并在Config结构体中添加对database配置的解析：

   ```go
   type Config struct {
       AppName  string         `json:"app_name"`
       AppMode  string         `json:"app_mode"`
       AppHost  string         `json:"app_host"`
       AppPort  string         `json:"app_port"`
       Database DatabaseConfig `json:"database"`
       Sms      SmsConfig      `json:"sms"`
   }
   type DatabaseConfig struct {
       Driver   string `json:"driver"`
       User     string `json:"user"`
       Password string `json:"password"`
       Host     string `json:"host"`
       Port     string `json:"port"`
       DbName   string `json:"db_name"`
       Charset  string `json:"charset"`
       ShowSql  bool   `json:"show_sql"`
   }
   ```

3. 创建包含xorm的tag的结构体

   要存储验证码，需要在数据库中创建表结构进行存储。我们可以创建SmsCode结构体，并通过tag设置数据库字段约束，具体的SmsCode定义如下:

   ```go
   package model
   
   type SmsCode struct {
       Id         int64  `xorm:"pk autoincr" json:"id"`
       Phone      string `xorm:"varchar(11)" json:"phone"`
       BizId      string `xorm:"varchar(30)" json:"biz_id"`
       Code       string `xorm:"varchar(4)" json:"code"`
       CreateTime int64  `xorm:"bigint" json:"create_time"`
   }
   ```

   通过tag的xorm设置字段数据类型以及约束。

   - pk：表示主键
   - autoinc：表示自增
   - bigint：整形变量
   - varchar：字符串类型

   4. Sync2同步生成数据库表

   可以调用engine.Sync2方法，将结构体类型同步映射到数据库中，生成数据库表。

   ```go
   err = engine.Sync2(new(model.SmsCode))
       if err != nil {
           return nil, err
   }
   ```

   5. 将验证码数据保存到数据库中

   在MemberService的SendCode方法，添加保存验证码到数据库的操作：

   ```go
   func (msi *MemberServiceImpl) SendCode(phone string) string {
       code := fmt.Sprintf("%06v", rand.New(rand.NewSource(time.Now().UnixNano())).Int31n(1000000))
       dao := impl.NewMemberDao()
       smsCode := model.SmsCode{Phone: phone, Code: code, BizId: response.BizId, CreateTime: time.Now().Unix()}
   
       if result := dao.InsertCode(smsCode); result > 0 {
           return code
       }
       return ""
   }
   ```

#### 4.用户数据管理

##### 1.功能和背景介绍

在项目的登录功能中，如果在登录时发现用户名和密码在用户表中不存在，会自动将用户名和密码保存在用户表中，创建一个新的用户。

因此，除了使用手机号和验证码登录以外，还支持使用用户名、密码进行登录。

如果首次使用手机号和验证码进行登录，会默认将手机号作为用户名创建新的用户，将用户结构体对象的数据保存在数据库中。

因此，我们有必要创建用户表。

##### 2.用户数据结构体定义

在项目中，使用结构体定义用户数据结构。结构体定义如下所示：

```go
type Member struct {
    Id           int64   `xorm:"pk autoincr" json:"id"`
    UserName     string  `xorm:"varchar(20)" json:"user_name"`
    Mobile       string  `xorm:"varchar(11)" json:"mobile"`
    Password     string  `xorm:"varchar(255)" json:"password"`
    RegisterTime int64   `xorm:"bigint" json:"register_time"`
    Avatar       string  `xorm:"varchar(255)" json:"avatar"`
    Balance      float64 `xorm:"double" json:"balance"`
    IsActive     int8    `xorm:"tinyint" json:"is_active"`
    City         string  `xorm:"varchar(10)" json:"city"`
}
```

通过定义Member结构体，表示应用的用户信息。通过TAG中的xorm来指定结构体在数据库表中的约束。

##### 3.ORM映射

通过engine.Sync2方法将Member同步映射成为数据库中的member表：

```go
err = engine.Sync2(new(model.Member),
        new(model.SmsCode))
if err != nil {
    return nil,err
}
```

##### 4.插入数据

当用户获取完验证码，并填写验证码以后，用户点击登录，会发起登录请求。因此，我们需要来完成登录相关的逻辑操作和处理。用户手机号码和验证码登录的接口是api/login_sms，因此我们在已经创建的MemberController中解析短信验证码接口。如下所示：

```go
func (mc *MemberController) Router(engine *gin.Engine) {
    //发送手机验证码
    engine.GET("/api/sendcode", mc.sendSmsCode)
    //手机号和短信登录
    engine.OPTIONS("/api/login_sms", mc.smsLogin)
}
```

在MemberController中创建smsLogin方法完成用户手机号和密码登录的逻辑，详细实现如下：

```go
//短信登录
func (mc *MemberController) smsLogin(context *gin.Context) {

    var smsParam param.SmsLoginParam
    err := toolbox.Decode(context.Request.Body, &smsParam)

    fmt.Println(err.Error())

    fmt.Println(context.PostForm("phone"))
    fmt.Println(context.Query("code"))
    if err != nil {
        tool.Failed(context, "参数解析错误")
        return
    }

    us := service.NewMemberService()
    member := us.SmsLogin(smsParam)
    if member != nil {
        tool.Success(context, member)
        return
    }
    toolbox.Failed(context, "登录失败")
}
```

##### 5.用户服务层

在MemberService.go文件中，编写SmsLogin方法完成手机号和密码登录。

```go
func (msi *MemberService) SmsLogin(param param.SmsLoginParam) *model.Member {

    dao := dao.NewMemberDao()
    sms := dao.ValidateSmsCode(param.Phone, param.Code)

    if sms == nil || time.Now().Unix()-sms.CreateTime > 300 {
        return nil
    }

    member := dao.QueryByPhone(param.Phone)
    if member != nil {
        return member
    }

    user := model.Member{}
    user.UserName = param.Phone
    user.Mobile = param.Phone
    user.RegisterTime = time.Now().Unix()

    user.Id = dao.InsertMember(user)
    return &user
}
```

在MemberService中，首先验证手机号和验证码是否正确。如果通过了手机号和验证码的验证，通过手机号查询用户是否已经存在。如果用户记录不存在，则创建新的用户记录并保存到数据库中，如果用户记录已经存在，则表示登录成功，返回用户信息。

##### 6.数据库操作的MemberDao实现

在MemberDao中，实现用户模块的数据库操作。
首先是手机验证码验证功能,如下所示：

```go
func (md *MemberDao) ValidateSmsCode(phone string, code string) *model.SmsCode {
    var sms model.SmsCode

    if err := md.Where(" phone = ? and code = ? ", phone, code).Find(&sms); err != nil {
        log.Error(err.Error())
    }
    return &sms
}
```

其次是根据手机号查询用户数据库表中是否存在手机号对应的用户，如下所示：

```go
func (md *MemberDao) QueryByPhone(phone string) *model.Member {
    var member model.Member

    if err := md.Where(" phone = ? ", phone).Find(&member); err != nil {
        log.Error(err.Error())
    }
    return &member
}
```

最后，对于新手机号，新建用户，插入到数据库中：

```go
func (md *MemberDao) InsertMember(member model.Member) int64 {
    result, err := md.InsertOne(&member)
    if err != nil {
        toolbox.Error(err.Error())
    }
    return result
}
```

至此，我们使用手机号和验证码的登录方式就开发完成了。

#### 5.全局跨域请求处理设置

##### 1.OPTIONS请求

前端会在请求时发生了跨域访问，因此浏览器为了安全起见，会首先发起一个请求测试一下此次访问是否安全，这种测试的请求类型为OPTIONS，又称之为options嗅探，同时在header中会带上origin，用来判断是否有跨域请求权限。

然后服务器相应Access-Control-Allow-Origin的值，该值会与浏览器的origin值进行匹配，如果能够匹配通过，则表示有跨域访问的权限。

跨域访问权限检查通过，会正式发送POST请求。

##### 2.服务端设置跨域访问

可以在gin服务端，编写程序进行全局设置。**通过中间件的方式设置全局跨域访问**，用以返回Access-Control-Allow-Origin和浏览器进行匹配。

在服务端编写跨域访问中间件，详细内容如下：

```go
func Cors() gin.HandlerFunc {
    return func(context *gin.Context) {
        method := context.Request.Method
        origin := context.Request.Header.Get("Origin")
        var headerKeys []string
        for k, _ := range context.Request.Header {
            headerKeys = append(headerKeys, k)
        }
        headerStr := strings.Join(headerKeys, ",")
        if headerStr != "" {
            headerStr = fmt.Sprintf("access-control-allow-origin, access-control-allow-headers, %s", headerStr)
        } else {
            headerStr = "access-control-allow-origin, access-control-allow-headers"
        }

        if origin != "" {
            context.Writer.Header().Set("Access-Control-Allow-Origin", "*")
            context.Header("Access-Control-Allow-Origin", "*") // 设置允许访问所有域
            context.Header("Access-Control-Allow-Methods", "POST, GET, OPTIONS, PUT, DELETE,UPDATE")
            context.Header("Access-Control-Allow-Headers", "Authorization, Content-Length, X-CSRF-Token, Token,session,X_Requested_With,Accept, Origin, Host, Connection, Accept-Encoding, Accept-Language,DNT, X-CustomHeader, Keep-Alive, User-Agent, X-Requested-With, If-Modified-Since, Cache-Control, Content-Type, Pragma")
            context.Header("Access-Control-Expose-Headers", "Content-Length, Access-Control-Allow-Origin, Access-Control-Allow-Headers,Cache-Control,Content-Language,Content-Type,Expires,Last-Modified,Pragma,FooBar")
            context.Header("Access-Control-Max-Age", "172800")
            context.Header("Access-Control-Allow-Credentials", "false")
            context.Set("content-type", "application/json") //// 设置返回格式是json
        }

        if method == "OPTIONS" {
            context.JSON(http.StatusOK, "Options Request!")
        }
        //处理请求
        context.Next()
    }
}
```

其中的Access-Control-Allow-Origin的设置，表示允许进行跨域访问，*表示可以访问所有域。同时，通过Header方法进行了其他的设置。

最后context.Next()是中间件使用的标准用法，表示继续处理请求。

在main函数中，调用编写好的跨域访问。调用如下:

```go
func main(){
    app := gin.Default()
    app.Use(Cors()) //使用这个中间件方法
}
```

#### 6.图形化验证码生成和验证

在使用用户名和密码登录功能时，需要填写验证码，验证码是以图形化的方式进行获取和展示的。

##### 1.验证码使用原理

验证码的使用流程和原理为：

​			**在服务器端负责生成图形化验证码，并以数据流的形式供前端访问获取，同时将生成的验证码存储到全局的缓存中，在本案例中，我们使用redis作为全局缓存，并设置缓存失效时间。当用户使用用户名和密码进行登录时，进行验证码验证。验证通过即可继续进行登录。**

##### 2.验证码库安装

借助开源的验证码工具库可以生成验证码。
首先，安装开源的验证码生成库：**使用老版本的!!!新版本有问题**

```go
go get -u github.com/mojocn/base64Captcha
```

在下载后的base64Captcha库的目录中，可以看到有_example和_example_redis两个目录。第一个example是用于演示生成验证码和验证码的示例代码。

按照示例代码的说明，运行程序并在浏览器进行端口访问：

```go
go run main.go
//浏览器中访问：http://localhost:8777
```

如下图所示：	

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/WX20191108-183533@2x.png)

通过自定义配置，可以选择不同的生成验证码的参数，并刷新验证码，同时还可以对验证码进行验证。

通过exmaple目录下的main.go程序可以看到生成验证码和验证验证码的逻辑，此处不再赘述。

##### 3.**项目集成验证码生成和Redis缓存**

通常来说，验证码都是有一定的实效性的，过期验证码也就无效了。

因此，我们考虑在项目中引入Redis作为数据缓存。当验证码生成后，将验证码存放在Redis中，并根据配置文件对Redis进行设置。

###### 1.安装go-redis库

在项目中使用redis,需要安装go-redis库，可以在https://github.com/go-redis/redis中查看如何下载go-redis和配置。

###### 2.增加redis配置

在配置文件app.json中新增redis配置：

```go
"redis_config": {
    "addr": "127.0.0.1",
    "port": "6379",
    "password": "",
    "db": 0
}
```

同时，新增RedisConfig结构体定义，如下所示：

```go
type RedisConfig struct {
    Addr string `json:"addr"`
    Port string `json:"port"`
    Db   int    `json:"db"`
}
```

###### 2.Redis初始化操作

进行了redis配置以后，需要对redis进行初始化。可以封装redis初始化操作函数如下所示：

```go
type RedisStore struct {
    redisClient *redis.Client
}

var Redis *redis.Client

func InitRediStore() *RedisStore {
    config := GetConfig().RedistConfig

    Redis = redis.NewClient(&redis.Options{
        Addr:     config.Addr + ":" + config.Port,
        Password: "",
        DB:       config.Db,
    })

    customeStore := &RedisStore{Redis}
    base64Captcha.SetCustomStore(customeStore)

    return customeStore
}
```

同时，为customeStore提供Set和Get两个方法，如下所示：

```go
func (rs *RedisStore)Set(id string, value string){
	err := rs.redisClient.Set(ctx,id, value, time.Minute*10).Err()
	if err != nil {
		log.Println(err.Error())
	}
}

func (rs *RedisStore)Get(id string, clear bool) string{
	val, err := rs.redisClient.Get(ctx,id).Result()
	if err != nil {
		log.Println(err)
		return ""
	}
	if clear {
		err := rs.redisClient.Del(ctx,id).Err()
		if err != nil {
			log.Println(err)
			return ""
		}
	}
	return val
}

```

对Redis进行初始化和定义完成以后，需要在main中调用一下初始化操作InitRediStore:

```go
func main(){
    //Redis配置初始化
     tool.InitRediStore()
}
```

##### 4.验证码生成和验证

本项目中采用的验证码的生成库支持三种验证码，分别是：audio，character和digit。我们选择character类型。

定义Captcha.go文件，实现验证码的生成和验证码函数的定义。在进行验证码生成时，默认提供验证码的配置，并生成验证码后返回给客户端浏览器。如下是生成验证码的函数定义：

```go
//生成验证码
func GenerateCaptchaHandler(w http.ResponseWriter, r *http.Request) {

    // parse request parameters
    // 接收客户端发送来的请求参数

    postParameters := ConfigJsonBody{
        CaptchaType: "character",
        ConfigCharacter: base64Captcha.ConfigCharacter{
            Height:             60,
            Width:              240,
            Mode:               3,
            ComplexOfNoiseText: 0,
            ComplexOfNoiseDot:  0,
            IsUseSimpleFont:    true,
            IsShowHollowLine:   false,
            IsShowNoiseDot:     false,
            IsShowNoiseText:    false,
            IsShowSlimeLine:    false,
            IsShowSineLine:     false,
            CaptchaLen:         6,
            BgColor: &color.RGBA{
                R: 3,
                G: 102,
                B: 214,
                A: 254,
            },
        },
    }
    // 创建base64图像验证码

    var config interface{}
    switch postParameters.CaptchaType {
    case "audio":
        config = postParameters.ConfigAudio
    case "character":
        config = postParameters.ConfigCharacter
    default:
        config = postParameters.ConfigDigit
    }
    captchaId, captcaInterfaceInstance := base64Captcha.GenerateCaptcha(postParameters.Id, config)
    base64blob := base64Captcha.CaptchaWriteToBase64Encoding(captcaInterfaceInstance)

    // 设置json响应
    w.Header().Set("Content-Type", "application/json; charset=utf-8")
    body := map[string]interface{}{"code": 1, "data": base64blob, "captchaId": captchaId, "msg": "success"}
    json.NewEncoder(w).Encode(body)
}
```

##### 5.验证码接口解析

图形化验证码是用户名和密码登录功能的数据，属于Member模块。因此在MemberController中增加获取验证码的接口解析，如下：

```go
func (mc *MemberController) Router(engine *gin.Engine){
    //获取验证码
    engine.GET("/api/captcha", mc.captcha)
}
```

测试结果如下，能够正常获取到数据：

![img](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/WX20191108-185430@2x.png)

##### 6.验证码的验证

同理，可以对客户端提交的验证码进行验证，具体实现逻辑如下:

```go
func CaptchaVerify(r *http.Request) bool {
    //parse request parameters
    //接收客户端发送来的请求参数
    decoder := json.NewDecoder(r.Body)
    var postParameters ConfigJsonBody
    err := decoder.Decode(&postParameters)
    if err != nil {
        log.Println(err)
    }

    defer r.Body.Close()

    //比较图像验证码
    verifyResult := base64Captcha.VerifyCaptcha(postParameters.Id, postParameters.VerifyValue)
    return verifyResult
}
```

#### 7.用户名密码登录

用户名和密码的登录接口为：

```go
/api/login_pwd
```

接口请求类型为POST，接口参数有三个：name，pwd，captcha。其中：captcha为验证码。

定义登录参数结构体LoginParam：

```go
//用户名，密码和验证码登录
type LoginParam struct {
    Name     string                json:"name" //用户名
    Password string                json:"pwd"  //密码
    Id       string                json:"id"// captchaId 验证码ID
    Value    string                json:"value" //验证码
}
```

1. 在MemberController.go文件中，编写方法用于处理用户名密码登录的解析方法如下所示：

   ```go
   func (mc *MemberController) Router(engine *gin.Engine){
       engine.POST("/api/login_pwd", mc.nameLogin)
   }
   ```

2. 定义新的func并命名为nameLogin，实现登录流程逻辑控制：

   ```go
   func (mc *MemberController)nameLogin(context *gin.Context){
   	var loginParma *param.LoginParam
   	err := tool.Decode(context.Request.Body, &loginParma)
   	if err != nil {
   		tool.Faild(context,"参数解析失败")
   		return
   	}
   	validate := tool.VertifyCaptcha(loginParma.Id, loginParma.Value)
   	if !validate{
   		tool.Faild(context,"验证码错误，请重新验证")
   		return
   	}
   	member := GetMemberService().Login(loginParma.Name, loginParma.Password)
   	if member.Id != 0{
   		tool.Success(context,&member)
   		return
   	}
   	tool.Faild(context,"登录失败")
   }
   ```

   在控制层的nameLogin方法中，主要有3个逻辑处理：

   - 1、通过*gin.Context解析请求登录请求携带的参数。
   - 2、从携带的参数中得到提交的验证码数据，调用验证码判断验证码方法对验证码进行判断。验证码验证失败或者验证码失效，直接返回登录失败信息。
   - 3、使用用户名、密码参数进行登录，判断登录结果。如果登录成功，返回用户登录信息，否则返回登录失败。

3. Service层MemberService文件中，定义和实现用户名密码登录的Login方法。

   ```go
   func (ms *MemberService)Login(name,pwd string) *model.Member{
      //1.使用用户名和密码查询用户信息，如果存在直接返回
      memberDao := dao.MemberDao{Orm: tool.DbEngine}
      member := memberDao.QueryByPhoneAndPwd(name, pwd)
      if member.Id != 0{
         return member
      }
      //2.如果不存在，插入新数据到数据库
      user := model.Member{}
      user.UserName = name
      user.Password = tool.EncoderSha256(pwd)
      user.RegisterTime = time.Now().Unix()
      result := memberDao.InsertMember(user)
      user.Id = result
      return &user
   }
   ```

   在service层的Login方法中，分为两步逻辑判断：

   - 1、先查询是否已经存在该用户，如果该用于已经存在，则直接将查询到的用户信息返回。
   - 2、如果用户不存在，将用户信息作为新记录保存到数据库中，新增一条记录。并返回用户信息。

4. 最后，涉及到操作数据库的两个方法分别是：Query和InsertMember方法。InsertMember方法之前已经编写过，只需要重新编写一个Query方法即可，Query方法实现如下所示：

   ```go
   func (md *MemberDao) QueryByPhoneAndPwd(phone,pwd string) *model.Member{
      var member model.Member
      _, err := md.Where(" user_name = ? and password = ?", phone, pwd).Get(&member)
      if err != nil{
         log.Println(err)
         return nil
      }
      return &member
   }
   ```

#### 8.用户头像上传

在用户中心中，允许用户更换自己的头像。因此，需要学习开发上传一张图片到服务器，并保存成为用户的头像。

头像上传的接口为：

```go
/api/upload/avator
```

在文件上传过程中，后台服务器需要确认该头像文件是哪位用户上传的。前端在上传文件时，并将用户id上传至服务器。服务器需要确认该用户是否已经登录，只有登录的用户才有权限上传。最通常的做法是**通过session来获取用户是否已经登录，并进行权限判断。**

##### 1.Session功能集成

1. 安装session库

   go语言和gin框架都没有实现session库，可以通过第三方实现的session库来集成session功能。安装如下session库:https://github.com/gin-conic/contrib

2. 初始化session

   在项目中，集成session功能，首先要进行初始化。我们选择将session数据持久化保存到redis中，因此需要与redis相结合。

   新建SessionStore.go文件，并定义session初始化函数如下:

   ```go
   // InitSession 初始化Session操作
   func InitSession(engine *gin.Engine){
   	config := GetConfig().Redis
   	store, err := redis.NewStore(10, "tcp", config.Addr+":"+config.Port, config.Password, []byte("secret"))
   	if err != nil {
   		log.Println(err)
   		return
   	}
   	engine.Use(sessions.Sessions("mysession",store))
   }
   ```

3. main.go文件中在gin.Default()后调用即可，通过中间件调用方式。

4. 封装设置获取Session的方法

   ```
   func SetSession(ctx *gin.Context,key interface{},value interface{}) error{
      session := sessions.Default(ctx)
      if session == nil{
         return nil
      }
      session.Set(key,value)
      return session.Save()
   }
   
   func GetSession(ctx *gin.Context, key interface{}) interface{} {
      session := sessions.Default(ctx)
      return session.Get(key)
   }
   ```

##### 2.文件上传Controller实现

​	在MemberController中，创建uploadAvator方法， 用于实现用户头像上传的业务流程控制。该方法其实主要有几个步骤:第一步是获取到用户端上传的文件,接下来将文件保存到对应的目录中，因为要知道该文件对应的是哪位用户的数据，因此需要将文件路径更新到用户数据库中的对应记录中:

```go
func (mc *MemberController)uploadAvator(context *gin.Context){
	//1. 解析上传的参数 file,user_id
	userId := context.PostForm("user_id")
	file, err := context.FormFile("avatar")
	if err != nil {
		tool.Faild(context,"参数解析失败")
		return
	}
	//2. 判断user_id是否登录，然后是的话file保存到本地
	session := tool.GetSession(context, "user_"+userId)
	fmt.Println(session)
	if session == nil{
		tool.Faild(context,"参数不合法")
		return
	}
	var member model.Member
	err = json.Unmarshal([]byte(session.(string)), &member)
	if err != nil{
		log.Println(err)
	}
	//3. 将保存后的文件的本地路径，放到用户表的avatar字段
	fileName := "./uploadfile/"+strconv.FormatInt(time.Now().Unix(),10)+file.Filename
	err = context.SaveUploadedFile(file, fileName)
	if err != nil {
		tool.Faild(context,"头像更新失败")
		return
	}
	path := GetMemberService().UploadAvatar(member.Id, fileName[1:])
	if path != ""{
		tool.Success(context,"http://localhost:8000"+path)
		return
	}
	tool.Faild(context,"上传失败")
}
```

##### 3.Service更新用户数据信息

```go
func (ms *MemberService)UploadAvatar(id int64,fileName string) string{
	memberDao := dao.MemberDao{Orm: tool.DbEngine}
	result := memberDao.UpdateMember(id, fileName)
	if result == 0{
		return ""
	}
	return fileName
}
```

#### 9.搭建FastDFS分布式文件系统

在实际的开发中，涉及到文件上传的功能，往往单独搭建一个文件 服务器用于文件的存储。因此我们接下来就搭建一个分布式的文件系统， 并将已完成的文件上传功能进行优化，将文件存储到分布式文件系统中。

##### 1.FastDFS介绍

目前已经有很多开源的分布式文件系统。而fastDFS正是其中一员， **fastDFS是基 于http协议的分布式文件系统，其设计理念是一切从简**。也正是因为其简单至上的设计理念，使其运维和扩展变得更加简单，同时还具备**高性能，高可靠，无中心以及免维护**等特点。

fastDFS分布式文件存储系统主要解决了海量数据的存储问题，利用fastDFS系统， 特别适合系统中的**中小文件的存储和在线服务**。**中小文件的范围大致为4KB ~ 500MB大小之间。**

组件构成及工作原理

在fastDFS分布式文件存储系统中，由三中角色的组件组成，分别称之为:

- **存储服务器**(Storage Server)
  - 存储服务器顾名思义就是用于存储数据的服务器，主要提供**存储容量和备份**服务。存储服务器**为了保证数据安全不丢失，会多台服务器组成一个组**， 又称group。**同一个组内的服务器上的数据互为备份。**

- **跟踪服务器**(TrackerServer)
  - 跟踪服务器的主要作用是做**调度**工作,担负起**均衡**的作用;跟踪服务器主要负责**管理所有的存储服务器**，以及服务器的分组情况。存储服务器启动后会与跟踪服务器保持链接,并进行**周期性信息同步**。
- **客户端**(Client) 
  - 主要负责上传和下载文件数据。客户端所部署的机器就是实际项目部署所在的机器。

其可以如下图所示理解工作原理：

![image-20220128152339802](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220128152339802.png)

如上图所示，FastDFS在整个存储数据的过程中，整个存储服务器集群，是按照group为 单位进行分组的，也可以称之为卷(volume) 。

**整个存储系统分为多个组，组与组之间是相互独立的，并可以进行编号排序等，方便管理**，所有的分组服务器组合起来就构成了整个fastDFS文件存储系统。

对某个组而言，其中可能包含以台或者多台文件存储服务器。比如上图中的Volume1,有称之为卷1,里面包含storage server11. storage server 12、.... 1x共x台服务器。组成卷1的x台服务器上存储的文件内容是相同的。之所以每组由多台服务器组成，并保持文件内容相同,主要的目的是为了实现冗余备份，以及负载均衡。.除此之外，在增加某个组的服务器机器时，FastDFS可以自动完成文件同步，自动切换服务器提供线上服务。当然,也可以新增新的组，完成对文件系统容量的扩容, FastDFS也是支持的。

##### 2.文件上传下载过程

###### 1.FastDFS文件上传过程

![image-20220128152737244](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220128152737244.png)

1. Storage Sekver会定期的向Tracker Server发送自己的存储信息。

2. 当Tracker Server Cluster中的Tracker Server不止一个时，各个Tracker之间的关系是对等的，因此客户端上传时可以选择任意一个Tracker.

3. 当Tracker收到客户端上传文件的请求时，会为该文件分配一个可以存储文件的group,当选定了group后就要决定给客户端分配group中的哪一个storage server.

4. 当分配好storage server后，客户端向storage发送写文件请求，storage将会为文件分配一个数据存储目录。

5. 然后为文件分配一个filed, 最后根据以上的信息生成文件名存储文件。


###### 2.FastDFS文件下载过程

   ![image-20220128153101768](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220128153101768.png)

1. 在下载文件时，客户端可以选择任意tracker server.
2. tracker发送下载请求给某个tracker,并携带着文件名信息。
3. tracker从文件 名中解析出文件所存储的group、文件大小、创建时间等信息，然后为该请求选择一个storage用来服务下载的请求。

##### 3.安装FastDFS并启动

fastDFS是一个分布式文件存储系统，可以大规模部署在多台服务器上。通常使用云服务器来搭建fastDFS,这样方便访问。如果自己没有云服务器的条件，也可以选择
自己的电脑本地环境，其原理和操作都是相同的。

- 环境要求

  ​	CentOS 7 版本

- 安装文件

  1. 下载libfastcommon解压并安装

     libfastcommon是由fastDFS官方提供的软件库，其中包含了运行fastDFS所需要的一些基础库。可以直接通过浏览器访问并下载该文件: https://github.com/happyfish100/libfastcommon/archive/V1.0.7.tar.gz, 下载后上传到Cent OS服务器上。也可以在centos终端中直接下载。
     
  2. 编译并安装libfastcommon
  
     ```shell
     cd libfastcommon-1.0.7
     ./make.sh
     ```
  
     ./make.sh是执行编译脚本。编译过程需要系统有gcc环境，如果centos系统没有，会提示错误。需要执行以下命令：
  
     ```shell
     sudo yum -y install gcc automake autoconf libtool make
     ```
  
     等待安装完成即可，重新执行./make.sh.编译完成，编译过程会有输出。编译结束后进行安装：
  
     ```shell
     ./make.sh install
     ```
     
  3. 下载fastDFS解压并安装
  
     安装完成libfastcommon之后，安装fastDFS. fastDFS可以通过如下链接进行访问和下载: https://github.com/happyfish100/fastdfs/archive/V5.05.tar.gz，下 载后上传到CentOS服务器上。当然也可以使用命令在CentOS服务器上进行下载。
  
  4. 编译并安装fastDFS
  
     ```shell
     cd fastdfs-5.05
     ./make.sh
     sudo ./make.sh install
     ```
  
  5. 配置Tracker服务
  
     1. 首先进入/etc/fdfs目录,在该目录下能够看到有三个默认的配置模板文件，并修改三个配置文件,去掉.sample后缀为:
  
        - client.conf
        - storage.conf
        - tracker.conf
  
     2. 编辑tracker.conf文件， 进行配置: vim tracker.conf, 需要配置的选项有以下几个:
  
        base_ path所指向的目录是指的tracker服务器数据存储的目录，如果不存在要首先创建。配置编辑完成后保存退出。
  
        ```shell
        #存放数据及日志的文件目录
        base_ path=
        # 端口
        http.server_port=
        # the tracker server port
        port=22122
        ```
  
     3. 启动Tracker服务，在终端中可以执行以下命令启动Tracker服务:
  
        ```shell
        <tracker.conf文件所在位置> start
        ```
  
        该条命令执行后，可能没有信息输出，我们不知道Tracker服务是否已经启动。可以通过以下命令查看fdfs的tracker服务的监听:
  
        ```sh
        netstat -unltp|grep fdfs
        ```
  
        如果看到输出结果中tracker服务正在22122端口监听，说明已经正常启动了。
  
  6. 配置Storage服务
  
     配置完成Tracker服务器以后，还要配置真正存储文件的存储服务器，即Storage服务。
  
     1. 修改上文重命名的storage.conf配置文件，对以下配置修改并保存：
  
        ```shell
        base_path=
        #分组存储的路径
        storage_path0=
        tracker_server=<ip>:22122
        #the storage server port
        port=23000
        ```
  
        
  
     2. 启动Storage服务，在终端中可以执行以下命令启动Tracker服务:
  
        ```sh
        <storage.conf文件所在位置> start
        ```

##### 4.配置Nginx并测试

在服务器上搭建好了fastDFS系统后，**只能上传**，但是没有办法查看。**如果想要查看,需要配置好nginx才能进行访问和测试。**

1. 安装Nginx。

   略

2. 下载并安装fastdfs-nginx-module

   下载地址：[https://sourceforge.net/projects/fastdfs/files/FastDFS%20Nginx%20Module%20Source%20Code/fastdfs-nginx-module_v1.16.tar.gz](https://sourceforge.net/projects/fastdfs/files/FastDFS Nginx Module Source Code/fastdfs-nginx-module_v1.16.tar.gz)

   添加fastdfs模块到nginx:

   ```sh
   ./configure -add-module=../fastdfs-nginx-master/src/
   ```

3. 如果出现失败，确保以下依赖都安装了，然后再执行一遍命令

   ```shell
   sudo yum install gcc-c++
   sudo yum install -Y pcre pcre-devel
   sudo yum install -y zlib zlib-devel
   sudo yum install -y openssl openssl-devel
   ```

4. 配置fastdfs-nginx-module和nginx

   进入到fastdfs-nginx -module/src目录下面，修改其中的-一个名称为mod_fastdfs.conf的文件。修改该配置文件的三个配置选项: 

   ```sh
   tracker_server=<ip>:22122
   url_have_group_name=true
   store_path0=
   ```

   修改完成后，将该文件拷贝到/etc/fdsf目录中。

   进入fastdfs-5.05目录，将配置文件拷贝到/etc/fdfs中:

   ```sh
   cd /home/dc2-user/fastdfs-5.05
   cp anti-steal.jpg http.conf mime.types /etc/fdfs/
   ```

5. 修改nginx.conf配置文件参考如下：

   ![image-20220128165210779](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220128165210779.png)

由于我们配置了group1/M00的访问，我们需要建立一个group1文件夹， 并建立M00到data的软链接。

```sh
mkdir /home/dc2-user/data/fastdfs_storage/data/group1
ln -S /home/dc2-user/data/fastdfs_storage/data/home/dc2-user/data/fastdfs_ storage/data/group1/M00
```

6. 测试文件上传

   在任意目录下创建一个hello.txt文件，然后执行如下命令（记得修改client.conf目录）：

   ```shell
   <路径>/fdfs_test <路径>/client.conf upload <路径>/hello.txt
   ```

   出现如下图所示结果，即表示上传成功:

   ![image-20220128170445765](https://gitee.com/luoqianyi/static-image/raw/master/%E5%9B%BE%E5%BA%8A/image-20220128170445765.png)

##### 5.编程实现文件上传fastdfs

###### 1.安装fastdfs的golang库

在项目中进行文件.上传编程实现，需要安装一个go语言库,该库名称为fdfs_client, 通过如下命令进行安装。
https://github.com/tedcy/fdfs_client
下载后，找到fdfs_client库的源文件。

###### 2.编写fdfs.conf配置文件

在fdfs_client库中， 提供对文件的上传和下载方法，其中文件上传支持两种方式。要使用文件上传功能方法，首先需要构造一个fdfsClient实例。 如同我们前文讲的fastDFS的组件构成
一样， client需要连接tracker服务器,然后进行上传。

在构造fdfsClient实例时，首先需要编写配置文件fdfs.conf,在fdfs.conf文件 中进行配置选项的设置:

```txt
tracker_server=<服务器地址>
http_port=http://<服务id>:<端口号>
maxConns=100
```

###### 3.编写工具类FastDfsUtil.go

```go
func UploadFile(fileName string)string{
    client,err:=fdfs_client.NewClientWithConfig("./config/fastdfs.conf")
    defer client.Destory()
    if err != nil{
        fmt.Println(err.Error())
        return ""
    }
    filePath,err := client.UploadByFilename(fileName)
    if err != nil{
         fmt.Println(err.Error())
        return ""
    }
    return filePath
}
```

###### 4.读取配置文件fdfs.conf文件

```go
func FileServerAdd() string {
    file, err := os.Open(name:"./config/fastdfs.conf")
    if err!=nil{
    	return ""
	}
    reader := bufio.NewReader(file)
    for {
        line, err := reader.ReadString('\n')
        line = strings.TrimSuffix(line,"\n")
        str := strings.SpLitN(Line,"=",2)
        switch str[0] {
            case "http_ server_ port":
            return str[1]
    	}
    if err!=nil{
   	 	return ""
   	 }
  }
}

```

#### 10.获取用户信息

前端index.js文件中定义了获取用户信息的接口API，全称为:

```go
/api/userinfo
```


​	该获取用户信息的接口请求类型为GET类型，在该接口访问时，是**根据当前登录用户的会话状态**进行请求的。**所谓会话，其实就是客户端会存储登录用户信息的cookie. Cookie可 以在服务端通过代码进行设置。**

##### 1.Cookie的设置

在用户调用登录功能时，完成cookie的设置，比如：

```go
context.SetCookie("cookie_id","login_success",10*60,"/","localhost",true,true)
```

- cookie_ id: 第一个参数表示的要即将设置的cookie的名称。
- login_ success: 第二个字段表示的是cookie里面设置的具体的值的内容。在实际的使用中，该值开发者可以自己生成。
- 10*60: 第三个值表示的是cookie的有效时间， 当该值<0时，会立刻清除cookie, 当该值>0时，表示cookie数据的有效期会持续n秒。
- "/":该参数是一个可选项，表示cookie所在的目录。
- "localhost":该参数表示cookie所在的域，可以理解为cookie的有效范围。

##### 2.Cookie中间件

在设置了Cookie的值后，可以在服务端编写中间件获取Cookie的值。服务端编写的cookie的处理逻辑如下：

```go
const CookieName = "cookie_user"
const CookieTimeLength = 10 * 60 //10min

func CookieAuth(context *gin.Context) (*http.Cookie, error) {
	cookie, err := context.Request.Cookie(CookieName)
	if err == nil {
		context.SetCookie(cookie.Name, cookie.Value, cookie.MaxAge, cookie.Path, cookie.Domain, cookie.Secure, cookie.HttpOnly)
		return cookie, nil
	} else {
		return nil, err
	}
}
```

