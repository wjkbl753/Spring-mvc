导入jar

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.4</version>
</dependency>
```

在需要返回json的controller方法上直接加注解@ResponseBody
```java
	@ResponseBody
	@RequestMapping("/testJson")
	public List toJson() {
		Map<String,Object> map = new HashMap<String, Object>();
		List list = new ArrayList<>();
		map.put("hello", "123");
		list.add(map);	
		return list;
	}
```

## 新注解
@RestController

##返回一般对象R
```java
public class R extends HashMap<String,Object> {
    public static R ok(){
         R r = new R();
         r.put("code",200);
         r.put("message","success");
         return r;
    }

    public static R ok(String message){
        R r = new R();
        r.put("code",200);
        r.put("message",message);
        return r;
    }

    public static R error(){
        R r = new R();
        r.put("code",500);
        r.put("message","error");
        return r;
    }

    public static R error(String message){
        R r = new R();
        r.put("code",500);
        r.put("message",message);
        return r;
    }
    public R put(String k,Object v){
        super.put(k,v);
        return this;
    }
}
```


