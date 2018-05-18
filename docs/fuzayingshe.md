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

## 多对一 --查询单对象
查询一个用户，同时查询用户所在的班级信息

*User.java*

```java
private int id;
private String userName;
private String gender;
private String email;
private ClassRoom classRoom;
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
 <resultMap type="User" id="userClassRoomResultMap2">
  	<id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	<association property="classRoom" javaType="ClassRoom">
  		<id property="cId" column="c_id"/>
  		<result property="name" column="cname"/>
  	</association>
 </resultMap>
 <select id="selectUserClassRoomById" resultMap="userClassRoomResultMap2" >
  		select * from user where id=#{id}
  </select> 

<!-- select标签写法不变  -->
```
!>本身resultMap可以使用默认映射，但是当resultMap中出现association之类的其他特殊标签时，默认映射会失效，所以建议写全属性

> 写法3 (Mybatis特有的分布查询+延迟加载) -- `最优`

*UserMapper.xml*

```xml
  <resultMap type="User" id="userClassRoomResultMap3">
  	 <id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	<association property="classRoom" column="c_id" select="com.weixin.dao.ClassRoomMapper.selectById"></association>
  </resultMap>
  
   <select id="selectUserClassRoomById" resultMap="userClassRoomResultMap3" >
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

!> 延迟加载可以降低mysql开销，多对一相对开销还小，如果是一对多方式。比如查询所有的教室，对应的查询所有教室的学生，延迟加载就显得更为重要

## 多对一 --查询List
查询所有的用户，同时查询用户所在的班级信息

> 写法1 (级联方式)

```xml  
   <select id="selectAllPlus" resultMap="selectAllPlusResultMap">
  	 select user.*,classroom.name cname from user left join classroom
  	 on user.c_id=classroom.c_id
  </select> 
```
> 写法2 (association)

```xml  
   <select id="selectAllPlus" resultMap="selectAllPlusResultMap2">
  	 select user.*,classroom.name cname from user left join classroom
  	 on user.c_id=classroom.c_id
  </select> 
```
>写法3 (分步+延迟)

```xml
   <select id="selectAllPlus" resultMap="selectAllPlusResultMap3">
  	  select * from user
  </select>
```

关闭延迟加载：👉

![](img/分步3.png)

?> 虽然没有要求查询班级信息，但是仍然去查了班级，list中有3个用户，每个用户再分别去查自己的班级信息

---

开启延迟加载：👉

![](img/分步4.png)

?> 没有用到班级信息，所以只发了一次sql请求

## 一对多 --查询单对象

查询一个班级，同时查询班级里的用户信息

*ClassRoom.java*

```java
private int cId;
private String name;
private List<User> userList;
```

> 写法1 (collection)  --对应assocition  此处已经没有级联写法

```xml
  <resultMap type="ClassRoom" id="classRoomUserResultMap">
  	<id property="cId" column="c_id"/>
  	<result property="name" column="name"/>
  	<collection property="userList" ofType="User">
  		<id property="id" column="id"/>
  	<result property="userName" column="user_name"/>
  	<result property="gender" column="gender"/>
  	<result property="email" column="email"/>
  	</collection>
  </resultMap>
  <select id="selectClassRoomUserById" resultMap="classRoomUserResultMap">
    select classroom.*,user.id,user.user_name ,user.gender,user.email from classroom left join user
    on classroom.c_id=user.c_id
    where classroom.c_id=#{c_id}
  </select>
```

> 写法2 (分步+延迟)  --对应assocition  此处已经没有级联写法

*ClassRoomMapper.xml*

```xml
   <resultMap type="ClassRoom" id="classRoomUserResultMap2">
  	<id property="cId" column="c_id"/>
  	<result property="name" column="name"/>
  	<collection property="userList" select="com.weixin.dao.UserMapper.selectUsersByClassRoomId" column="c_id"></collection>
  </resultMap>
  
  <select id="selectClassRoomUserByIdStep" resultMap="classRoomUserResultMap2">
  	select * from classroom where c_id=#{c_id}
  </select>
```
```xml
   <select id="selectUsersByClassRoomId" resultType="User">
  		select * from user where c_id=#{c_id}
  </select>  
```

## 一对多 --查询List

> 写法1 (collection)  --对应assocition  也已经没有级联写法

```xml
  	<select id="selectClassRoomUser" resultMap="classRoomUserResultMap">
  		select classroom.*,user.id,user.user_name,user.gender,user.email
  		from classroom left join user
  		on classroom.c_id=user.c_id
  	</select>
```

> 写法2 (分步+延迟)  --对应assocition  也已经没有级联写法

```xml
  	<select id="selectClassRoomUserStep" resultMap="classRoomUserResultMap2">
  		select * from classroom
  	</select>
```

!>mybatis还提出了个一对一，其实就是一表分成了2表，按多对一写法即可