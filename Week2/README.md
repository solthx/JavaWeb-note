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