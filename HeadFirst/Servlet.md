# Servlet专题

按知识点来记了...

# 1. Servlet的生命周期

### 1.1 加载类阶段 
1. 容器自动寻找servlet类( 部署的时候, web.xml文件里的servlet-mapping就是实现了这一点，尽管在servlet3.0中是用WebServlet(..)来做的 )

### 1.2 实例化Servlet对象
找到类之后，就根据已经设置的时机，就通过反射生成一个对象（调用构造方法，实例化），这个时机可以是第一次请求到来，也可以是服务器启动的时候，可以设置.  调用其构造函数，此时,Servlet就已经实例化完成了, 但目前还不能做什么事

### 1.3 调用init() 
Servlet变成对象以后，就调用init()函数进行初始化。  

初始化之后，Servlet才能做事(调用service)

记住，Servlet一旦变成对象，下一步就是调用init()方法，这两步尽管中间还有空隙，但是连着的.

### 1.4 调用service()
当接收到客户端发来的请求时，容器会开辟一个线程，然后调用Servlet的service方法，因此，Servlet的对象在一个虚拟机里只能有一个！这一个对象可以产生多个线程，而每一个线程就是对应一次请求，不管这个请求是来自不同的用户还是同一个用户。  

service()的作用就是作为中转站，根据头部信息来去调用doGet(), doPost() ... 

### 1.5 调用destroy()
调用这个方法后，servlet对象就被干掉了(垃圾回收, 可以理解成free掉了) ， init和destroy只会调用一次， 函数内部的代码也主要是打开xxx连接, 关闭xxx连接这样子.

# 2. 继承顺序
Servlet接口 -> GenericServlet类( 空实现 ) ->HttpServlet类(初步实现Http协议(doPost,doGet留给后面实现)) -> MyServlet类(重写doGet或doPost))

# 3. Post和Get的区别
1. 安全性，上传数据大小的区别( Get只有首部信息！ )
2. 从编程约定上来讲，Get操作是幂等的，而Post操作是非幂等的. (幂等的意思就是操作一次跟操作n次得到的结果一样， 例如读操作是幂等的，而写操作就不是幂等的)



ps: 超链接都是get请求
 
ps: Servlet里想同时实现doGet和doPost，一般是在doGet的代码里写doPost

# 4. Response
### - response.setContentType("MIME类型名称");
告诉浏览器要发挥的数据的类型，使得浏览器能够有一个正确的“举止”。 这个是必须要设定的.

(这里讨论的是在Servlet里的情况。 然而在Apache中，可以通过建立MIME类型，把一个特定的扩展名的文件，映射到一个特定的内容类型)

要求： 先调用``response.setContentType(..)`` 然后再调用获取输出流的方法``getWriter()``或者``getOutputStream()``

# 5. ServletConfig--提供初始化参数

最初的参数，是存在web.xml里的，Week2里介绍了如何设置。

容器会读取web.xml里预先设定好的参数，然后存到ServletConfig里，然后把存好数据的ServletConfig传给Servlet的init()方法.

init(ServletConfig)里 会调用init()方法

(ps: 通过getServletConfig来获取ServletConfig对象的引用，一个Servlet只能有一个ServletConfig对象，且该对象对参数的读取也只能读一次！ )

### 下面是初始化流程:
1. 容器读取部署文件， 其中包括了 最初的初始化参数；
2. 容器为这个Servlet创建一个新的ServletConfig实例。
3. 容器为每个参数创建一个String类型的 {name,key}的键值对，name和key都是String。
4. 容器向ServletConfig对象提供 name, key的引用 (这一步就是把读取的参数传给ServletConfig))
5. 容器创建Servlet类的一个实例 (new了一个Servlet)
6. 容器调用Servlet的``init(ServletConfig)``方法， 把刚得到数据的ServletConfig给穿过去

# 6. ServletContext
- 功能和ServletConfig类似，只是作用域不同，Week2也讲过了，这里不赘述

- 每个Servlet有一个ServletConfig，而每个Web应用有一个ServletContext，可以理解成项目内的全局变量。

ps: ServletContext 和 ServletConfig 不支持运行时修改参数, 可以理解成常量的赋值， 不存在set...方法

- ServletContext用于项目中Servlet之间和Web容器之间的连接..

- 可以通过两种方式得到ServletConfig：
    1. ServletConfig对象有``getServletContext()``方法 (在你的自定义Servlet里，既没有继承HttpServlet 也没有继承GenericServlet，这个时候可以通过这个方法来获得))
    2. 继承了HttpServlet或GenericServlet，就可以直接调用``getServletContext()``方法了。