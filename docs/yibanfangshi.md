方法上的形参名前端参数变量名一致会自动赋值：
```jsp
<form action="${pageContext.request.contextPath}/spring/testParam">
    <input type="text" name="name" id="" />
    <input type="text" name="age" id="" />
</form>
```
```java
@RequestMapping("/testPojo")
public String testPojo(String name,int age){
    System.out.println(name);
    System.out.println(age);
    return "success";
}
```


获取pojo参数:形参直接写类名就可以,表单直接写类的各属性，支持级联
```jsp
<form action="${pageContext.request.contextPath}/spring/testPojo">
    <input type="text" name="name" id="" />
    <input type="text" name="age" id="" />
    <input type="text" name="address.province" id="" />
    <input type="text" name="address.city" id="" />
    <input type="submit" value="tijiao" />
</form>
```
```java
@RequestMapping("/testPojo")
public String testPojo(User user){
    System.out.println(user);
    return "success";
}
```

时间格式化：如果表单有时间格式，那么javabean如何赋值？

```java
public class User {
    private Integer id;
    private String name;
    private String gender;
    private ClassRoom classRoom;

    @DateTimeFormat(pattern = "yyyy-MM-dd")
    @JsonFormat(pattern = "yyyy-MM-dd",timezone="GMT+8")
    private Date birthday;
}
```