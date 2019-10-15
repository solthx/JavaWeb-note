000# Week3
## 1. 三层架构
三层架构与MVC设计模式的目标一致：都是为了解耦合、提高代码复用；
区别： 二者对项目的理解角度不同。

## 2. 三层组成:
表示层(USL, User Show Layer : 视图层)
业务逻辑层(BLL, Business logic Layer ; service层 )
数据访问层 (DAL, Data Access Layer; Dao层 )

### 2.1 表示层:
    1. 表示层前台代码( jsp html; js css ) 位于WebRobot文件夹
    2. 表示层后台代码( Servlet )    位于Servlet类 xxx.servlet包

### 2.2 业务逻辑层 ( 逻辑，可拆 )
增删改查
封装功能的JavaBean,
调用数据访问层写业务逻辑

一般位于xxx.service包
或xxx.manager包， 或xxx.bll包

### 2.3 数据访问层 ( 原子性，不可拆 )
增删改查, 这一层可以理解成提供一个操作的接口。
具体访问数据库的操作，**不带逻辑，只是完成一个功能**（逻辑写在了业务逻辑层）

一般位于 xxx.dao包


### 实体类对应一个表.