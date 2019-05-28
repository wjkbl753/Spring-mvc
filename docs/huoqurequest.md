MVC的fandler方法可以接受ServletAPI类型作为参数
- `HttpServletRequest`
- `HttpServletRespons`
- `HttpSession`
- `InputStream`
- `OutputStream`
- `BufferedReader`
- `PrintWriter`

参数上直接写，就可以直接使用


<!-- ## @SessionAttribute--

@SessionAttribute只能放在类上

它有两个属性 value  type

@SessionAttributes(value= {"gender"},types= {String.class}) -->

所有类获得Request和ServletContext(推荐):
```java
@Autowired
private ServletContext servletContext;

@Autowired
private HttpServletRequest request;
```

还可以采用下面方式：

```java
HttpServletRequest request = 
((ServletRequestAttributes) (RequestContextHolder.currentRequestAttributes())).getRequest();

// 这个必须spring容器支持  (ContextLoaderListener)
ServletContext servletContext = ContextLoader.getCurrentWebApplicationContext().getServletContext();
```