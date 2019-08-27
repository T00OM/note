#MyBatis大纲
持久层框架，orm框架。提供输入输出映射，能够自动完成对结果映射，转换为java对象。使得持久层的重点在于sql语句。
## 原生JDBC
### 过程
1. 加载驱动
2. 创建连接(Connection)
3. 创建Statement对象，常用Preparement
4. 执行sql语句后，得到ResultSet对象
5. 对ResultSet对象中的结果处理
6. 释放资源(resultSet，statement,connection)
[示例](https://github.com/CookedFox/learnmybatis/blob/master/src/main/java/com/xxx/learnmybatis/jdbc/JdbcTest.java)
### 问题
1. 数据库连接使用后立即释放，消耗资源。
$使用数据库连接池解决$
2. 硬编码
$使用配置文件（sql语句、参数等）、自动映射java对象（结果集的分析）$

## MyBatis框架的执行过程
1. 配置MyBatis的主配置文件，SqlMapConfig.xml（名称不固定）
2. 通过配置文件加载运行环境，创建SqlSessionFactory会话工厂（使用SqlSessionFactoryBuilder创建，单例）
3. SqlSessionFactory创建SqlSession。SqlSession提供操作数据库方法的接口，并且线程不安全，所以SqlSession应该应用在方法体中。
4. 使用SqlSession对象中的方法操作数据，如果需要提交事务（更改数据），需要执行SqlSession中的Commit方法
5. 释放资源，关闭SqlSession
## 开发DAO的方法
<font color="red">两种方法都需要mapper.xml</font>
[mapper.xml](https://github.com/CookedFox/learnmybatis/blob/master/src/main/resource/mapper/UserMapper.xml)
1. 原始DAO开发方法
* 编写DAO接口和实现类
[DAO](https://github.com/CookedFox/learnmybatis/blob/master/src/main/java/com/xxx/learnmybatis/dao/UserDao.java)
* 在实现类中注入SqlSessionFactory工厂
[DAOIMPL](https://github.com/CookedFox/learnmybatis/blob/master/src/main/java/com/xxx/learnmybatis/dao/UserDaoImpl.java)
2. mapper代理
程序员编写mapper接口（DAO接口）
* mapper.xml中的namespace就是mapper.java的全限定名
* mapper.xml中的statement的id和mapper.java的方法名相同
* mapper.xml中的paramterType和mapper.xml的参数类型相同
* mapper.xml的resultType/resultMap和mapper.xml的返回类型相同