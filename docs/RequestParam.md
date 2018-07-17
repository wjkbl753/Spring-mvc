@RequestParam绑定请求参数

## 拥有属性：
   - `value`
   - `required`
   - `defaultValue`


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

