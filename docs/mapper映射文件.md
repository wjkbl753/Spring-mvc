## resultMap

有时候普通的resultType满足不了开发需要，不能个性化的定义javabean和列值间的对应关系，我们需要替换成另一个属性resultMap

?> resultMap标签中，`type`和`id`都为必须属性。type为对应的bean类型，id为唯一标示，供`其他select标签`引用。id列对应关系要用`id标签`，其他列都用`result标签`，可以选择需要对应的特殊列进行对应，其他对应还是会遵循默认对应方式

> 示例：先把之前最普通的查询替换为resultMap,可以发现我们不需要开启配置文件的驼峰式配置即可完成映射

```java
public interface UserMapper {
	public User selectById(int id);
}
```
```xml
 <resultMap type="User" id="userResultMap">
  </resultMap>
  <select id="selectById" resultMap="userResultMap">
  		select * from user where id=#{id}
  </select>   
```
下面单独定义对应关系：
```xml
<resultMap type="User" id="userResultMap">
  <id property="id" column="id"/>
  <result property="userName" column="user_name"/>
</resultMap>
```

?> 上面的需求不需要resultMap也可以完成，但是接下来的需求普通的resultType将无法完成

*banji.sql*

```sql
DROP TABLE IF EXISTS `classroom`;
CREATE TABLE `classroom` (
  `c_id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`c_id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of classroom
-- ----------------------------
INSERT INTO `classroom` VALUES ('1', '一班');
INSERT INTO `classroom` VALUES ('2', '二班');
INSERT INTO `classroom` VALUES ('3', '三班');

--修改user表，增加一个外检字段 c_id
```

> 查询用户的同时查询用户所在的班级信息(多对一)

*User.java*

```java
private int id;
private String userName;
private String gender;
private String email;
private ClassRoom classRoom;
```

*UserMapper.java*

```java
public User selectUserClassRoomById(int id);
```
> 写法1 (级联方式)

```xml
  <resultMap type="User" id="userClassRoomResultMap">
  	<id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	<result property="classRoom.cId" column="c_id"/>
  	<result property="classRoom.name" column="cname"/>
  </resultMap>

  <select id="selectUserClassRoomById" resultMap="userClassRoomResultMap">
  		select user.*,classroom.name cname
  		from user left join classroom
  		on user.c_id=classroom.c_id
  		where id=#{id}
  </select> 
```

> 写法2 (描述单个对象用association)

```xml
 <resultMap type="User" id="userClassRoomResultMap">
  	<id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	<association property="classRoom" javaType="ClassRoom">
  		<id property="cId" column="c_id"/>
  		<result property="name" column="cname"/>
  	</association>
 </resultMap>

<!-- select标签写法不变  -->
```

> 写法3 (Mybatis特有的分布查询+延迟加载) -- `最优`

*UserMapper.xml*

```xml
  <resultMap type="User" id="userClassRoomResultMap">
  	 <id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	<association property="classRoom" column="c_id" select="com.weixin.dao.ClassRoomMapper.selectById"></association>
  </resultMap>
  
   <select id="selectUserClassRoomById" resultMap="userClassRoomResultMap" >
  		select * from user where id=#{id}
  </select>  
```
*ClassRoomMapper.xml*

```xml
  <select id="selectById" resultType="ClassRoom">
    select * from classroom where c_id=#{id}
  </select>
```

结果：可以看到分步执行了2次查询
![](img/分步1.png)

>延迟加载：需要在总配置文件中设置：

*mybatis-config.xml*
```xml
<settings>
    <!--开启延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!--关闭积极加载-->
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```

> 设置延迟加载后，sql会按需查询，可以观察到sql只执行了一次

![](img/分步2.png)

!> 延迟加载可以降低mysql负载，如果是一对多方式。比如查询所有的教室，有时候不需要查询学生，有时候才需要查询学生(查询学生对性能损耗比较大)，延迟加载显得更为重要
