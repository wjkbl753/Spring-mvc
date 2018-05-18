##  单个普通参数

    直接使用#{参数名}进行取值，mybatis没做特殊处理，参数名可以随便写。

  ---

##  多个参数
    Mybatis会默认构建一个map，键是param1,param2....，或者0,1...值是对应传入的实参,拿参的时候直接写键名就可以，所以可以使用#{param1}，#{param2}取值
    
    `最优方案：我们可以使用注解(@Param("键名"))指定map的键名：`

```java
  public List<User>selectAllByCondition(@Param("id")int id,@Param("userName")String userName);
```
```xml
  <select id="selectAllByCondition" resultType="User">
  	select * from user where id=#{id} and user_name=#{userName}
  </select>
```

  ---

## 参数为map，和bean

```java
  public List<User>selectAllByConditionMap(Map map);
  public List<User>selectAllByConditionBean(User user);
```
```xml
  <select id="selectAllByCondition" resultType="User">
  	select * from user where id=#{id} and user_name=#{userName}
  </selec
```

  ---

## 传入list，数组的形式