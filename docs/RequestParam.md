@RequestParam绑定请求参数

```java
@RequestMapping("/requestTest")
public String requestTest(@RequestParam("name")String name,@RequestParam("age")int age) {
    System.out.println(name);
    System.out.println(age);
    return "success";
}
```

这样写，参数值必须，不然的话就报404，如果参数值不是必须：此写法不支持基本变量
```java
@RequestMapping("/requestTest")
public String requestTest(@RequestParam(value="name",required=false)String name,@RequestParam(value="age",required=false)Integer age) {
    System.out.println(name);
    System.out.println(age);
    return "success";
}
```

参数给默认值：此时不需要再写required=false
```java
@RequestMapping("/requestTest")
public String requestTest(@RequestParam(value="name",required=false)String name,@RequestParam(value="age",defaultValue="20")int age) {
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