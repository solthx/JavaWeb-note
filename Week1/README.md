# Week1

## 第一课 jsp搭建及入门

### 1. Tomcat解压后的目录：
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

### 2. 常见状态码

1. 404 资源不存在
2. 200 正常
3. 403 权限不足
4. 300/301 页面重定向( 跳转 )
5. 500 服务器内部错误( 代码写错了 )
...

### 3. jsp: 在html中嵌套java代码
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

### 4. 设置初始页面的默认访问顺序:
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

### 5. 虚拟路径
#### 将web项目配置到 webapps以外的目录 

##### 方法一 (需要重新启动服务器)
在conf/servel.xml中， 找到<Engine>标签，并在<Engine>标签中，<Host>标签的里面 ，添加<Context docBase="" path = ""/> 

其中， docBase和path是必须有的, 

docBase ： 实际路径
path ：虚拟路径 ( 绝对路径 or 相对路径(工作目录为webapps下) )

也就是我们访问的是path, 实际访问的是docBase(相当于一个重定向)

##### 方法二 ( 不用重新启动服务器 )
如果项目名为JspProject
那么在``conf/Catalina/localhost/``文件夹下，加一个``JspProject.xml``文件， 然后把<Context docBase="" path = ""/> 写进去，保存.

这样就可以不用重启服务器来更改项目路径。

### 6. 虚拟主机
通过www.test.com来访问主机

在网址->ip地址这一环节， 在去DNS服务器查询之前，会先在本地搜索有没有网址和ip的映射，如果找到了，那就不需要去向DNS查询了.

这里就利用这一点，通过www.test.com来访问主机

1. 