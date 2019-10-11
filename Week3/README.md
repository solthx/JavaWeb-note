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
1. 安全性，上传数据大小的区别
2. 从编程约定上来讲，Get操作是幂等的，而Post操作是非幂等的. (幂等的意思就是操作一次跟操作n次得到的结果一样， 例如读操作是幂等的，而写操作就不是幂等的)

ps: 超链接都是get请求

ps: Servlet里想同时实现doGet和doPost，一般是在doGet的代码里写doPost