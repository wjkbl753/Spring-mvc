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