## 准备数据库

新建数据库 `mybatis`

新建数据表 `user`

*user.sql*

```sql
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_name` varchar(255) DEFAULT NULL,
  `gender` char(1) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of user
-- ----------------------------
INSERT INTO `user` VALUES ('1', '小明', '1', 'xiaoming@163.com');
```

## 新建工程并导包

新建一个javase工程

![](img/工程.png)

?> 将[mybatis-3.4.1.jar](/jar/mybatis-3.4.1.jar),[mysql驱动](/jar/mysql-connector-java-5.1.14.jar) 和 [log4j.jar](/jar/log4j.jar) 置与 `classpath` 中

---

## 创建Mybatis总配置文件 `mybatis-config.xml`

*mybatis-config.xml(文件名可变)*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```
!>mybatis新版本支持`面向接口编程`，程序员`不需要`再写接口的实现类，转而用配置文件代替：

---

## 新建User实体类及dao文件

*User.java*

```java
public class User{
  private int id;
  private String userName;
  private String gender;
  private String email;
}
```

!> 上面实体类中`userName`对应数据表中的`user_name`字段

*UserMapper.java*

```java
public interface UserMapper {
	public List<Employee> selectAll()throws SQLException;
}
```

## 创建UserMapper的映射文件 UserMapper.xml：

*UserMapper.xml*  (`此文件需要和dao文件同名，并且放在同一目录下`)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.weixin.dao.UserMapper">
  <select id="selectAll" resultType="User">
    select * from user
  </select>
</mapper>
```

## 测试

```java
public class UserTest {
	@Test
	public void selectAll() throws IOException, SQLException {
    //加载mybatis-config.xml到输入流
		InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    //通过输入流构建SqlSessionFactory对象
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //通过SqlSessionFactory对象得到一个SqlSession对象，每个数据库操作都依靠SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();
    //通过SqlSession对象得到一个mapper的实现对象
		UserMapper mapper = sqlSession.getMapper(UserMapper.class);
		List<user> users = mapper.selectAll();
		System.out.println(users);
	}
}
```
