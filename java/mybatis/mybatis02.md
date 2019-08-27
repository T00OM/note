## SqlSession使用范围
* `SqlSessionFactoryBuilder`
    通过`SqlSessionFactoryBuilder`创建会话工厂`SqlSessionFactory`
    将`SqlSessionFactoryBuilder`当成一个工具类使用即可，不需要使用单例管理。在需要创建`SqlSessionFactory`时，只需要new一次即可。
* `SqlSessionFactory`
    通过`SqlSessionFactory`创建`SqlSession`，使用单例模式管理。
* `SqlSession`
    * `SqlSession`是一个面向用户（程序员）的接口。提供了很多操作数据库的方法：如：`selectOne`(返回单个对象)、`selectList`（返回单个或多个对象）。
    * `SqlSession`是线程不安全的，在`SqlSesion`实现类中除了有接口中的方法（操作数据库的方法）还有数据域属性。
    * `SqlSession`最佳应用场合在方法体内，定义成局部变量使用。

## 两种开发DAO方法对比
### 原始DAO开发
* 实现类中存在大量模板方法
* sqlSession调用方法时，将statementId硬编码。如：sqlSession.selectOne("namespace.statementID","paramter");
### mapper代理方法
* 需要遵循一定的开发规范，比较方便。

## 输出映射
### resultType
* 使用`resultType`进行输出映射，只有查询出来的列名和`pojo`中的属性名一致，该列才可以映射成功。
* 如果查询出来的列名和 pojo 中的属性名全部不一致，没有创建 pojo 对象。
* 只要查询出来的列名和 pojo 中的属性有一个一致，就会创建 pojo 对象。
### resultMap
* 同上
* 如果查询出来的列名和pojo的属性名不一致，通过定义一个resultMap对列名和pojo属性名之间作一个映射关系。
#### 步骤
1. 定义resultMap
2. 使用resultMap作为statement的输出映射类型