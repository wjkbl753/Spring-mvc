## 注解位置
- `类上`
- `方法上`

?>请求的地址将会是   /项目名/类上注解/方法上注解

```java
@RequestMapping("/spring")
@Controller
public class HelloWorld {
	
	/**
	 * 1. 使用@RequestMapping注解映射请求的url
	 * 2. 返回值会通过视图解析器解析为实际的物理视图 
	 * prefix+ return值 + suffix 得到实际的物理视图，然后转发
	 * @return
	 */
	@RequestMapping("/hello")
	public String hello(){
		System.out.println("hello");
		return "success";
	}
}
```

## 拥有属性：
   - `value`
   - `method`
   - `params`
   - `headers`

## 方法限制(method)
  如：
  
@RequestMapping(value="/hello",`method=RequestMethod.GET`)   --请求地址是/hello，而且必须是get请求

@RequestMapping(value="/hello",`method=RequestMethod.POST`)  --请求地址是/hello，而且必须是post请求

## 参数限制|请求头限制(了解)：
@RequestMapping(value = "/hello",params = {"name=1","age"},headers ="Connection=keep-alive" )

## 通配符支持
如：
@RequestMapping("/aa/*/cc")   --请求地址可以是/aa/xxx/cc

## @PathVariable

@RequestMapping和@PathVariable结合用于获取pathinfo

```java
@RequestMapping("testvariable/{id}")
public String testPathVariable(@PathVariable("id")int id) {
    System.out.println(id);
    return "success";
}
```

## 直接访问：

标准工程项目jsp页面都要放入WEB-INF下，是不能直接访问的，我们可以配一个简单的路径来直接访问

`<mvc:view-controller path="/aaa" view-name="success"/>`

但是写上之后，我们发现上面的 @RequestMapping注解全部失效：所以还得再加一个

`<mvc:annotation-driven/>`