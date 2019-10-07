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

#### 1.3.1 PreparedStatement操作数据库:
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

