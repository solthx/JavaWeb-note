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

## 9. 统一字符集编码
编码分类：
设置jsp文件的编码(jsp文件中的pageEncoding属性): jsp -> java 
设置浏览器读取jsp文件的编码(jsp文件中的content属性里的charset) 
这两个要设置成一样的.. 推荐使用utf-8

文本编码: 统一项目内的文件编码: 右键项目名->properties->resource 就能看到了

## 10. 保证tomcat的一致性
在server面板 新建一个tomcat实例，再在该实例中部署项目( 右键-add and remove) 之后运行

注意: 一般建议将eclipse中的tomcat于本地tomcat的配置信息保持一致, 将eclipse中的tomcat设置成托管模式（双击tomcat, 在server location里选择）
![pic1](https://github.com/solthx/JavaWeb-note/blob/master/Week1/pic/pic1.png)

## 11. JSP的页面元素有哪些:
### 11.1. 脚本Scriplet:
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
<%="hello.."%>  <=> <% out.print("hello.. ") %>
```
（println不会打印回车！！！！！！！！！）
 (想打回车，就加个<br/>, 就像是'\n'一样！！！！！)
 （out.print()和<%= .. %> 的打印，其实就是把html代码给打印上去！！"<font color="red">hello</font>" 就会打印个红色的hello！！！）
ps: 一般而言，修改web.xml、配置文件、java  需要重新启动tomcat服务器，
但是如果修改jsp/html/css/js, 就不需要重启

### 11.2 指令 

<%@ 指令 %>

以page指令为例，
```
<%@ page ... %>
```
page指定的属性:
- language: jsp 页面使用的脚本语言
- import : 导入类
- pageEncoding: jsp文件的自身编码(jsp->java的编码)
- contentType: 浏览器解析jsp的编码 与pageEncoding保持一致
例如:
```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8" import="java.util.Date" %>
```

### 11.3 注释
- html注释 <!-- --> (可以被浏览器查看源代码所观察到, 其他两个看不到)
- java注释 
- jsp注释 <%-- -->

## 12. JSP九个内置对象 (自带的，不需要new 也可以使用的对象)
### 12.1. out对象
向客户端输出内容，输出对象
### 12.2 pageContext

### 12.3 request对象
请求对象， 存储“客户端向服务端发送的请求信息”

#### 12.3.1 request常用的方法
```java
1. String getParameter(String name); //根据请求的字段名key（input标签的name属性），返回字段值value（input标签的value属性值）

2. String [] getParameterValues(String name); //根据key, 返回字符串数组

3. void setCharacterEncoding("utf-8") ： //设置post方式的请求编码，默认编码为tomcat默认编码，tomcat8及之后是utf-8, 8之前是iso-8859-1

4  request.getRequestDispatcher("b.jsp").forward(request, response); ： //请求转发 从当前页面，跳转到b.jsp那个页面

5. ServletContext getServerContext(): 获取项目的ServletContext对象
```
#### 12.3.2 案例
实现一个提交表单并且打印表达的功能.
1. register.jsp
```jsp
....
 <body>
  	<form action="show.jsp" method="post">
	     用户名: <input type="text" name="uname"/><br/>
	     密码: <input type="password" name="upwd"/><br/>
	     年龄: <input type="text" name="uage"/><br/>
	     爱好: <br/>
	     	<input type="checkbox" name="hobbies" value="篮球">篮球<br/>
	     	<input type="checkbox" name="hobbies" value="足球">足球<br/>
	     	<input type="checkbox" name="hobbies" value="乒乓球">乒乓球<br/>
	     	<input type="submit" value="注册">
     </form>
  </body>
....
```
- name相当于标签的key, value就是标签的value.
- input标签的一些type： text, password, checkbox, submit...
- input标签的submit， value是button的内容(默认为“提交”)
- 提交之后的， 会把表单提交到action里，方式为method方式，在action里，使用request来接受表单信息。
- show.jsp文件放在WebRobot目录下，根目录下可以直接访问。

2. show.jsp
```jsp
....'<body>
  	<%
  		request.setCharacterEncoding("utf-8");
  		String uname = request.getParameter("uname");
  		String upwd = request.getParameter("upwd");
  		int age = Integer.parseInt(request.getParameter("uage"));
  		String [] hobbies = request.getParameterValues("hobbies");
  	 %>
  	 
  		=============信息如下==============<br/>
  	用户名: <%=uname+"<br/>"%>
  	密码: <%=upwd +"<br/>"%>
  	年龄: <%=age %><br/>
  	爱好:<br/>
  	<%
  		if (hobbies!=null)
	  		for ( String nms:hobbies )
	  			out.print(nms+"<br/>");
  	 %>
  	
  </body>
....

```
#### 12.3.3 post和get的区别
1. get的参数会显示在地址栏里， 地址栏能容纳的信息有限, 因此，大文件的上传需要用post
2. get不安全，因为参数都暴露出来了

#### 12.3.4 统一请求的编码 request
如果get请求出现了乱码，例如请求使用的编码是iso-8859-1, 但服务器端的编码是utf-8，
编码不一致就会出现乱码，
**get**方式的解决方法如下:
1. 统一每一个变量的编码
```java
new String(旧编码, 新编码)
name = new String( name.getBytes("iso-8859-1"), "utf-8" );
```

2. 修改server.xml，一次性的更改tomcat默认get提交的编码(utf-8)
(在修改端口号的那个标签，加一个属性 URIEncoding="utf-8" 然后就行了(即 之后所有的get请求都变成了utf-8的编码) ) 

**post**方式的解决方法如下：
request.setCharacterEncoding("utf-8")
setCharacterEncoding这个函数是设置post方式的请求编码

### 12.4 response
服务端发送给客户端的响应对象
#### 12.4.1 response对象的常用方法
```java
1. void addCookie( Cookie cookie ); // 服务端向客户端增加cookie对象

2. void sendRedirect( String location ) throws IOException; //重定向, 页面跳转的一种方式

3. void setContentType( String encodingtype ) ;  // 设置服务端响应的编码( 设置服务端的contentType类型 ) 

```
#### 12.4.2 案例一: 登陆
login/login.jsp -> login/check.jsp -> login/success.jsp
1. login/login.jsp
```jsp
...
<body>
	<form action="login/check.jsp" method="post">
	用户名：<input type="text" name="uname"><br/>
	密码：<input type="password" name="upwd"><br/>
	<input type="submit" value="登陆"><br/>
	</form>
</body>
...
```

2. login/check.jsp
```jsp
...
<%
	request.setCharacterEncoding("utf-8");
	String name = request.getParameter("uname");
	String pwd = request.getParameter("upwd");
	if ( name.equals("zs") && pwd.equals("abc") ){
		// 页面跳转: 重定向方法, 使用这条语句会导致数据丢失！！
		// response.sendRedirect("success.jsp"); 
		
		// 页面跳转: 请求转发，可以获取到数据，并且地址栏没有改变(依然是check.jsp，而不会变成success.jsp)
		request.getRequestDispatcher("success.jsp").forward(request, response);
	}else{
		//登陆失败
		out.print("用户名或密码有误！");
	}
%>
...
```

3. login/success.jsp
```jsp
<body>
    登陆成功！ <br>
    欢迎您:
    <%
    	String name = request.getParameter("uname");
     	out.print(name);
     %>
</body>
```

**请求转发和重定向的区别**

|            	| 请求转发  		|   重定向   |
|  ----      	| ----    		    |    ----  |
| 地址栏是否改变  | 不变( check.jsp )   | 改变(success.jsp)|
| 是否保留第一次request的数据      	  | 一直保留   |  不保留| 
| 请求次数      	 | 1次   |  2次| 
|  原理区别      |  在服务器内部进行跳转 | 响应客户端，告诉它应该访问另一个地址, 然后客户端再进行第二次请求 | 

### 12.5 session（服务端）
#### 12.5.1 简介
Cookie由服务端产生，在客户端保存.
```java
//Cookie : <key,value>

javax.servlet.http.Cookie

// 构造方法
public Cookie(String name, String value); 

// 成员方法简介
String getName();
String getValue();
void setMaxAge(int expiry); //设置最大有效期( 秒 )
```

#### 12.5.2 服务端发送给客户端cookie
```java
// 装入cookie
response.addCookie( Cookie cookie ); 
// 页面跳转后(转发，重定向)
...
// 客户端获取cookie
request.getCookies(); // 每次获取所有cookie，然后通过遍历去寻找自己要的那个cookie
```

#### 12.5.3 案例: 用cookie实现记住用户功能
cookie_test/login.jsp ->  cookie_test/check.jsp -> cookie_test/show.jsp
1. cookie_test/login.jsp
```jsp
。。。
	<%!
		String uname = ""; // 全局变量
	%>
	<%
		Cookie [] cookies = request.getCookies(); // 提取cookie
		for ( Cookie cookie:cookies ){
			if ( cookie.getName().equals("uname") )
				uname = cookie.getValue();
		}
	%>
	<form action="./cookie_test/check.jsp" method="post">
		<!-- value的三目表达式 -->
		用户名：<input type="text" name="uname", value=<%=(uname==null?"":uname) %>><br/> 
		密码：<input type="password" name="upwd"><br/>
		<input type="submit" value="登陆"><br/>
	</form>
。。。
```

2. cookie_test/check.jsp
```jsp
...
  <body>
       <%
	   		// 提取信息，把收到的信息存入cookie，然后再发给客户端
			// 本次只保存用户名
       		String uname = request.getParameter("uname");
       		Cookie cookie = new Cookie("uname",uname);
       		
			// cookie装入
       		response.addCookie(cookie);
       		
			// 通过重定向的“重新发送给客户端” 这一特点， 把cookie发到客户端
       		response.sendRedirect("show.jsp");  // show.jsp 就是打印信息，代码不放了
        %>
  </body>
...
```

#### 12.5.4 session
服务器端的每一个session对应客户端的一个应用程序， 而服务器之所以能够分辨它们，就是根据cookie中的JSESSIONID这个属性来分辨的. （所以，每一个cookie都有JSESSIONID这个属性.

	客户端程序第一次请求服务端时，服务端在内部会先产生一个session对象和一个cookie，这个session对象会自带一个sessionId（用于区分其他session），然后在把sessionId的值复制一份，复制给JSESSIONID，然后把JSESSIONID放到cookie里面 （sessionId的值就是JSESSIONID的值）

	因此客户端cookie的SESSIONID和 服务端的sessionId 一一对应

#### 12.5.5 session对象方法
```java
1. String getId();  // 获取sessionId

2. boolean isNew(); //判断是否是新用户(第一次访问)

3. void invalidate();  //使session失效

4. void setAttribute(String name, Object value );  //给session加一个kv对 

5. Object getAttribute( String name );  // 根据name获取对于的value

6. void setMaxiInactiveInterval(秒); // 设置最大有效 非活动时间

7. int getMaxiInactiveInterval(秒); // 获取最大有效非活动时间
```

#### 12.5.6 session的共享域
同一个程序都可以共享session的内容(例如同一个浏览器内).

客户端在第一次请求服务端时， 如果服务端发现此请求没有JSESSIONID，则会创建一个 name=JSESSIONID的cookie给客户端 .  **Cookie**: 不是内置对象，但服务端会自动new一个给客户端


#### 12.5.7 seesion和cookie的区别
|            	|   session  	    |  cookie   		|
|  ----      	| ----    		    |    ----  |
|  保存的位置 	  |  服务端    		| 	客户端  	|
|  安全性 	  | 较为安全    		| 	不安全  	|
|  保存的内容 	  |  Object    		| 	String  	|


### 12.6 application 全局对象
```java
1. String getContextPath(); // 获取虚拟路径
2. String getRealPath();   // 获取绝对路径 
```

### 12.7 config 配置对象( 服务器配置信息 )
### 12.8 page 输出对象 (相当于java中的this)
### 12.9 exception 异常对象

### 12.10

#### 12.10.1 四种范围对象（ 作用域 按 小->大 排序)
1. pageContext JSP页面容器（作用域仅仅为当前页面！！！）  （有些书上也会说这个是page对象， 但是跟上面的page对象是两个东西）
2. request   请求对象    同一次请求有效（作用域为同一次request！！！）
3. session   会话对象    同一次session有效 （作用域为同一次会话！！！）
4. application 全局对象   全局有效(整个项目有效) (作用域为整个项目，可以理解成全局变量) ( 关闭服务(重启服务器)、其他项目 则会无效 )
**重启后，多个项目共享，可以通过JNDI实现**

**上面的四个范围对象，大多就是用setAttribute和getAttribute这俩函数。 同时，对象作用域越大，开销就越大，因此用能满足功能的最小作用域.**

上面的四个对象共有的方法：
```java
1. Object getAttribute(String name); //根据属性名，或者属性值

2. void setAttribute(String name, Object value); //设置属性(可以新增，也可以修改)

3. void removeAttribute( String name ); 
```