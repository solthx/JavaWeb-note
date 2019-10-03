# Week1

## 1. Tomcat解压后的目录：
1. bin : 可执行文件( startup.sh  shutdown.bat )
2. conf: 配置文件( servel.xml )
3. lib: tomcat依赖的jar文件
4. log: 日志文件( 记录出错时的信息 )
5. temp: 临时文件
6. webapps : 可执行的项目 ( 将我们开发的项目放入该目录 )
7. work: 特殊的临时文件， 存在由jsp翻译成的java,以及编辑成的class 文件 （ jsp -> java -> class  ） 

ps: tomcat的默认端口号为8080, 建议修改.   在servel.xml里，<Connector port="8080">... 这个地方修改

默认访问的是webapps里的ROOT

访问顺序为web.xml里配置的<welcome-file-list>这一栏里

## 2. 常见状态码

1. 404 资源不存在
2. 200 正常
3. 403 权限不足
4. 300/301 页面重定向( 跳转 )
5. 500 服务器内部错误( 代码写错了 )
...

## 3. jsp: 在html中嵌套java代码
```jsp
<html>
	<head>
		<title>my jsp project</title>
	</head>
	<body>
		hello jsp.. 
		<%
			out.print("hello world"); // <% .. %> ..里面为嵌套的java代码
		%>
	</body>
</html>
```

## 4. 设置初始页面的默认访问顺序:
在web.xml文件里
```xml
....
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.xhtml</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
....
```
访问顺序为一次从上往下 (设置可能不会立刻生效，因为有缓存问题)

## 5. 虚拟路径
### 将web项目配置到 webapps以外的目录 

#### 方法一 (需要重新启动服务器)
在conf/servel.xml中， 找到<Engine>标签，并在<Engine>标签中，<Host>标签的里面 ，添加<Context docBase="" path = ""/> 

其中， docBase和path是必须有的, 

docBase ： 实际路径
path ：虚拟路径 ( 绝对路径 or 相对路径(工作目录为webapps下) )

也就是我们访问的是path, 实际访问的是docBase(相当于一个重定向)

#### 方法二 ( 不用重新启动服务器 )
如果项目名为JspProject
那么在``conf/Catalina/localhost/``文件夹下，加一个``JspProject.xml``文件， 然后把<Context docBase="" path = ""/> 写进去，保存.

这样就可以不用重启服务器来更改项目路径。

## 6. 虚拟主机
通过www.test.com来访问主机

在网址->ip地址这一环节， 在去DNS服务器查询之前，会先在本地搜索有没有网址和ip的映射，如果找到了，那就不需要去向DNS查询了.

这里就利用这一点，通过www.test.com来访问主机

#### 1. 修改conf/servel.xml
第一步 : 在<Enginee>标签中，再增加一个Host标签, 
```xml
<Host appBase="项目的实际路径" name="www.test.com">
	<Context docBase="项目的实际路径"  path="/">  <!-- 注意，这里的path是虚拟路径，如果是/的话，就是直接访问www.test.com就可以了-->
</Host>
```

第二步 : 更改<Enginee>标签中的defaultHost, 从localhost改成www.test.com

#### 2. 修改完servel.xml， 在本地的host文件中，增加一行
```xml
127.0.0.1 www.test.com
```

流程解释:

	www.test.com -> host文件找映射关系 -> server.xml找Enginee的defaultHost -> 找到defaultHost里的那个Host之后，通过path(这里是"/") 映射到 docBase(项目的实际位置) 

## 7. Jsp的执行流程
	第一次访问: 服务器将jsp翻译成java, 再将Java编译成class文件
	第二次访问: 直接访问class 文件
	(如果jsp代码被修改了, 会重新翻译java, 并编译为class！)

	jsp -> java(servlet文件) -> class

	被翻译的java和class文件被放在work的子目录下

	jsp和servlet可以互相转换(早期只有serlvet，但太麻烦了， 所以jsp出现了，帮我们翻译成servlet)。

## 8. 使用eclipse开发web项目
这一段用文字记录较为困难， 就不记得特别细了。

### 1. 这里记录一个bug 
在配置完tomcat后，启动的时候，会报错"Server Tomcat v9.0 Server at localhost failed to start" 

解决方法是:
	在Eclipse的Run -> Run Configurations的界面里 有一个设置参数里Arguments页面里的VM arguments的参数里面把最后的有-Djava.endorsed.dirs="D:\java\tomcat\apache-tomcat-9.0.10\endorsed"的参数删除，然后点击Apply，再点Run就行了

参考链接: https://bbs.csdn.net/topics/392277991

### 2. 在eclipse中创建的web项目: 浏览器可以直接访问WebRobot中的文件，
例如``http://localhost:8080/MyJspProject/index1.jsp``其中的index1.jsp就是WebRobot中的;

但是，WEB—INF中的文件，无法通过客户端直接访问！！！

只能通过请求转发来访问. 

注意：并不是任何的内部跳转都可以访问WEB—INF； 原因是 跳转有两种方式:
- 请求转发
- 重定向

### 3. 配置tomcat运行时环境
尽管jsp和serlvet可以互相转换，但是创建的动态web项目只能编译jsp，不能编译servlet， 因此需要配置一个运行时环境来运行servlet，下面有几个方法：
1. 将tomcat/lib中的servlet-api.jar 加入项目的构建路径. (把jar放到项目的src里，然后右键点build-path就行了) (一般用第二个方法)

2. 右键项目名(MyJspProject) -> Build-Path -> Config Build Path -> Library -> Add Library -> server Library 

### 4. 统一字符集编码
编码分类：
设置jsp文件的编码(jsp文件中的pageEncoding属性): jsp -> java 
设置浏览器读取jsp文件的编码(jsp文件中的content属性里的charset) 
这两个要设置成一样的.. 推荐使用utf-8

文本编码: 统一项目内的文件编码: 右键项目名->properties->resource 就能看到了

### 5. 保证tomcat的一致性
在server面板 新建一个tomcat实例，再在该实例中部署项目( 右键-add and remove) 之后运行

注意: 一般建议将eclipse中的tomcat于本地tomcat的配置信息保持一致, 将eclipse中的tomcat设置成托管模式（双击tomcat, 在server location里选择）
![pic1](https://github.com/solthx/JavaWeb-note/blob/master/Week1/pic/pic1.png)

### 6. JSP的页面元素有哪些:
#### 1. 脚本Scriplet:
- java代码  ( <% ... %> 局部变量、 java语句 )
```jsp
<%
	String s = "hehe";
	out.println(s);
%>
```
- 全局变量、定义方法 ( <%! ...  %> )
```jsp
<%!
	public String bookName; // 全局变量
	public void init(){     // 全局方法
		bookName = "hehe";
	}
%>
```
- 输出表达式
```jsp
<%="hello.."%>  <=> <% out.println("hello.. ") %>
```