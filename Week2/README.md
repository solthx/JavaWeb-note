# Week2

# 1. JDBC (Java DataBase Connectivity)
可以为多种关系型数据库DBMS 提供统一的访问方式, 用Java操作
![jdbc1.png](https://github.com/solthx/JavaWeb-note/blob/master/Week2/pic/jdbc1.png)

1. JDBC API : 提供各种操作接口( Connection Statement PreparedStatement ResultSet  )
2. JDBC DriverManager: 管理不同的数据库驱动
3. 各种数据库驱动: 由相应的数据库厂商提供的(第三方提供)， 连接数据库的jar包

### 1.1 jdbc api主要完成的三个工作:
1. 与数据库建立连接
2. 给数据库发送sql语句
3. 返回处理结果

上面的三件事，具体是通过以下类/接口实现：
- DriverManager : 管理jdbc驱动
- Connection : 连接
- Statement( PreParedStatement ) : 增删改查
- CallableStatement : 调用数据库中的 存储过程/存储函数
- Result : 返回的结果集

### 1.2 jdbc访问数据库的具体步骤（重点）：
1. 导入驱动，加载具体的驱动类
2. 与相应的数据库建立连接
3. 发送sql语句，并执行
4. 处理返回的结果集

|            	|   驱动jar  	    |  具体驱动类   		|   连接字符串（数据库名+ip+端口）  |
|  ----      	| ----    		    |    ----  |     ----   |
|  Oracle	  |  ojdbc-x.jar    		| 	oracle.jdbc.OracleDriver  	| jdbc:oracle:thin:@loaclhost:1521:ORCL|
|  MySQL 	  | mysql-connector-java-x.jar    		| 	com.mysql.jdbc.Driver  	|jdbc:mysql://localhost:3306/数据库实例名 |
|  SqlServer 	  |  sqljdbc-x.jar    		| 	com.microsoft.sql.server.jdbc.SQLServerDriver 	| jdbc:microsoft:sqlserver:localhost:1433;databasename=数据库实例名|

上图只是举个例子，真要用到某个数据库的驱动类或者连接字符串的话，还是先查一下最好.

**sql8.0的版本里, com.mysql.jdbc.Driver 更换为 com.mysql.cj.jdbc.Driver，** 

**connector的版本一定要和数据库的版本对上号！！！！**

**MySQL 8.0 以上版本不需要建立 SSL 连接的，需要显示关闭。**

**最后还需要设置 CST。**

下面以jdbc连接mysql为例：

#### 第一步：安装sql

#### 第二步: 下载sql版本对应的 数据库驱动程序的的jar包(mysql-connector-java.版本号.jar)

#### 第三步: 把jar包放到src中，右键点击add build path

#### 第四步: 见下图(注释里写的很详细):
```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class JDBCDemo {
	private static final String PWD = "**********";
	private static final String USERNAME  = "root";
	private static final String URL = "jdbc:mysql://localhost:3306/student?useSSL=false&serverTimezone=UTC";   // "jdbc:mysql://localhost:3306/数据库名?useSSL=false&serverTimezone=UTC"
	
	// 增删改
	public static void update() { 
		Statement stmt = null;
		Connection connect = null;
		try {
			// 1. 导入驱动，加载具体的驱动类
			Class.forName("com.mysql.cj.jdbc.Driver"); // 8.0之后，中间多了  ‘.cj’
			// 2. 与数据库建立连接
			connect =  DriverManager.getConnection(URL, USERNAME, PWD);
			// 3. 发送sql,  ( 增删改是"executeUpdate", 查询是"executeQuery" )
			stmt = connect.createStatement();
			String sql = "insert into course values(5,\"政治\", 5)";
			// 执行sql语句
			int count = stmt.executeUpdate(sql);
			// 4. 处理结果
			if ( count>0 ){
				System.out.println("操作成功! ");
			}
		}catch( ClassNotFoundException e ){
			e.printStackTrace();
		}catch( SQLException e ){
			e.printStackTrace();
		}catch (Exception e){
			e.printStackTrace();
		}finally{
			try {
				if ( stmt!=null )
					stmt.close(); 
				if ( connect!=null )
					connect.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
	
	// 查询
	public static void query(){
		Statement stmt = null;
		Connection connect = null;
		ResultSet rs = null;
		try {
			// 1. 导入驱动，加载具体的驱动类
			Class.forName("com.mysql.cj.jdbc.Driver"); // 8.0之后，中间多了  ‘.cj’
			// 2. 与数据库建立连接
			connect =  DriverManager.getConnection(URL, USERNAME, PWD);
			// 3. 发送sql, 执行(增删改、查)
			stmt = connect.createStatement();
			String sql = "select * from course";
			// 执行sql语句 ( 增删改是"executeUpdate", 查询是"executeQuery" )
			rs = stmt.executeQuery(sql);
			// 4. 处理结果 打印出来
			while( rs.next() ){
                // 等价写法
				//int course_id = rs.getInt("course_id");
				//String course_name = rs.getString("course_name");
				//int student_idx = rs.getInt("student_id");
				int course_id = rs.getInt(1);
				String course_name = rs.getString(2);
				int student_idx = rs.getInt(3);
				System.out.println(course_id + "--" + course_name + "--"+student_idx);
			}
			
		}catch( ClassNotFoundException e ){
			e.printStackTrace();
		}catch( SQLException e ){
			e.printStackTrace();
		}catch (Exception e){
			e.printStackTrace();
		}finally{
			try {
				if ( rs!=null )
					rs.close();
				if ( stmt!=null )
					stmt.close(); 
				if ( connect!=null )
					connect.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
	
	public static void main( String [] args ){
		//update();
		query();
	}
}
```

查询得到的resultSet：
![resultSet.png](https://github.com/solthx/JavaWeb-note/blob/master/Week2/pic/jdbc%E6%9F%A5%E8%AF%A2.png)

#### 小结：
1. DriverManager ： 管理jdbc驱动
2. Connection： 连接（通过DriverManager产生）
3. Statement(PreparedStatement) ： 增删改查 ( 通过Connection产生 )
4. CallableStatement : 调用数据库中的存储过程/存储函数 ( 通过Connection产生 )
5. ResultSet ：  返回查询的数据集 （由Statement产生）

---
### 1.3 Connection产生三个操作数据库的对象：
- Connection产生Statement对象:  connect.createStatement()
- Connection产生PreparedStatement对象:  connect.prepareStatement()
- Connection产生CallableStatement对象:  connect.prepareCall()

#### 1.3.1 PreparedStatement操作数据库 (推荐使用PreparedStatement, 无论是从可扩展性， 还是预编译的角度， 还是安全的角度，PreparedStatement更好！) :
public interface PerparedStatement extends Statement

当你想把程序里变量的值作为内容，存到数据库里的时候，Statement就显得有些无力了，
这时候就需要PreparedStatement.

所以PreparedStatement 在 Statement的基础上多了 **赋值操作** setXxx(); (例如setInt()) , 配合
sql查询语句的占位符'?' 一起使用，就能完成上面的的任务.

关于PreparedStatement对象的使用，和Statement略有一点不同，看下面的用法:
```java
    // 把 (7,"计算机", 7) 插入数据库
...
    String sql = "insert into course values(?,?,?)"; // ?为占位符，后面赋值
    stmt = connect.prepareStatement(sql); // 这里和Statement不同的是，要把sql作为参数传进去
    // 进行赋值
    stmt.setInt(1, 7); // 把第1个问号，替换成7
    stmt.setString(2, "计算机"); 
    stmt.setInt(3, 7);
    // 执行
    int count = stmt.executeUpdate();  // 上面已经传过sql了，这里就不用传了
...
```

另外，这种用set来填补占位符?的用法，在查询里也可以使用，如下:
```java
    // 实现查询student_idx为7的所有信息
    。。。
    String sql = "select * from course where student_id=?";
    stmt = connect.prepareStatement(sql);
    stmt.setInt(1, 7); // 把第一个问号换成7
    // 执行
    rs = stmt.executeQuery();  // 使用方法和上面一样 ，因为sql里面包含占位符?, 因此需要提前把sql传入prepareStatement(sql), 进行预编译。 然后因为之前传入过了，后面执行的时候就不用再传入sql语句了.

    // 每个PreparedStatement对应一个sql语句
    // 每个Statement，可以查询很多条sql语句

    。。。
```




#### 1.3.2 ResultSet常用函数:
```java
1. rs.next(); // 光标下移, 返回移动后指向的位置是否为空(即判断下一个位置是否还存在元素)

2. rs.previous();  //光标上移,和next对应

3. getXxx();  // 获取具体的字段值


```

#### 1.3.3 CallableStatement：调用存储过程、存储函数
connect.preparedCall(参数)

参数格式:
	存储过程(无返回值):  { call 存储过程名( 参数列表 ) }
	存储函数(有返回值):  { ? = call 存储函数名( 参数列表 ) }

例子:
```java
...
// 一、存储过程
	// 假设已有 addTwoNum( in a int , in b int , out c int ) ; // c = a + b
	Class.ForName(..);
	connect = DriverManager.getConnection(..);
	CallableStatement cstmt = null;
	// 传入调用函数的语句
	cstmt = connect.prepareCall( "{ call addTwoNum(?,?,?) }" );
	// 放置参数
	cstmt.setInt(1, 10);
	cstmt.setInt(1, 12);
	// 设置输出类型
	cstmt.registerOutParameter( 3, Types.INTEGER ); // 第三个参数是输出参数，其类型为INTEGER(枚举类型)
	// 执行
	cstmt.execute(); // 执行a+b ->c
	// 获取参数
	int result = cstmt.getInt(3);
...
...
	if ( cstmt!=null )
		cstmt.close();
...

//二、存储函数:
	cstmt = connect.prepareCall( "？ = { call addTwoNum(?,?) }" );
	// 后面都一样，就是占位符的位置变一下而已.

```

**小结: JDBC调用存储过程的步骤**
1. 产生存储过程的对象 ``CallableStatement cstmt= connect.prepareCall( "{ call addTwoNum(?,?,?) }" );``
2. 通过setXxx()处理 输出参数值 cstmt.setInt(1,30); ...
3. 通过cstmt.registerOutParameter( 3, Types.INTEGER ); 来处理输出参数类型
4. 执行cstmt.execute();
5. 获取结果 cstmt.getXxx(..);
6. cstmt.close();

---


# 2. JSP访问数据库
JSP就是在html中嵌套Java代码，因此java代码可以写在jsp中，下面记录一下在jsp中和java中的不同之处.

1. 导包操作:
	java项目是把jar包丢到src里，然后右键add build path。
	web项目: 只要把jar包赋值到WEB-INF/lib里就可以了.

2. 如果一个类是访问数据库的, 那么这个类的后缀就是Dao


# 3. JavaBean
javaBean满足:
1. 所有属性为private
2. 提供默认构造方法
3. 提供getter和setter
4. 实现serializable接口

目的是为了向下兼容，代码可扩展性更好, 提高代码复用 ... 

使用层面上，JavaBean分为两大类：
1. 封装**业务逻辑**的JavaBean ( 例如封装登陆逻辑, 封装注册逻辑等 )
2. 封装**数据**的JavaBean	( 实体类，例如人, 水果, 车 等等， 对应数据库的一张表 )


# 4. MVC设计模式:
M: Model 模型 : 实现逻辑功能. (JavaBean实现)
V: View  视图 : 用于展示、以及与用户交互. (前端技术实现)
C: Controller 控制器 : 接收用户请求，将请求跳转到模型进行处理; 模型处理完毕后，再把处理结果返回给请求页面.   可以用jsp实现，但一般用Servlet实现.

# 5. Servlet:
Servlet是一些类型需要符合的规范:
1. 必须继承 javax.servlet.http.HttpServlet
2. 必须重写其中的doGet()或doPost()方法
3. 编写web.xml的映射关系
```xml
...
<servlet>
	<servlet-name>abc</servlet-name>
	<servlet-class>com.Mytest.servlet.WelcomeServlet</servlet-class>
</servlet>
<servlet-mapping>
	<servlet-name>abc</servlet-name>
	<url-pattern>/WelcomeServlet</url-pattern>
</servlet-mapping>
...
```

## 5.1 使用Servlet的时候需要配置
1. servlet 2.5 : 配置web.xml
2. servlet 3.0 : 不需要配置web.xml的映射关系， 只需要在Servlet的定义处上面写@WebSerlvet("url-pattern")  ，记住，不要加分号！！！一定要从根目录开始写！！！！！("xxx")错误！！  ("/xxx")正确！！

## 5.2 根目录
Web项目的根目录是: 
1. WebRobot 
2. 所有的构建路径(src, 或自己创建一个source folder)
( 配置web.xml时，类名，路径，都一定要全！！ )

如果: index.jsp中请求<a href="abc">..</a>, 则寻找范围: 既会在src根目录中找也会在WebRobot中找 "abc"

如果: index.jsp中请求<a href="a/abc"></a>，寻找范围:现在src或WebContent中找a目录，然后再在a目录中找"abc" ，注意: 把url-pattern写成"/a/abc" 那么即使没有a目录，也可以正常访问！！

### /:
1. web.xml： 在web.xml中(包括Webservlet(..)中) / 代表的是项目路径 , 也就是 "http://localhost:9999/ServletDemo/a/WelcomeServlet"中的 "http://localhost:9999/ServletDemo/" 即 "地址/项目名/"，也就是项目根路径

2. jsp: jsp中， / 代表的是 "http://localhost:9999/" ，即 "地址/"， 也就是服务器根路径

## 5.3 Servlet生命周期（5个阶段）
1. 加载
2. 初始化: init(), 该方法会在Servlet被加载并实例化的以后执行
3. 服务:  service() -> doGet() doPost()
4. 销毁:  destroy(), Servlet被系统回收时执行
5. 卸载

### 5.3.1 init()方法:
1. 第一次访问servlet的时候会被执行，只执行一次.
2. 可以修改为Tomcat加载时，启动时进行初始化，具体修改方法为:
	- Servlet 2.5 : 在web.xml里，加一个标签<load-on-startup>1</load-on-startup> 加完之后，就会在Tomcat启动的时候执行init
	- Servlet 3.0 : @WebServlet( value="/...(servlet-name))" , loadOnStartup=1 );  ``loadOnStartup=1``表示在所有的Servlet中第一个执行这个Servlet的Init方法。

### 5.3.2 service()-> doGet(), doPost()
访问一次，执行一次

### 5.3.3 destory()
关闭服务器的时候，会执行destory()