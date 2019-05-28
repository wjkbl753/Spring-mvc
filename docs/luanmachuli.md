表单发post请求发送中文时，后端接收中文时乱码，我们可以使用spring提供的一个编码过滤器

```xml
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```


!> @ResponseBody 返回中文字符串时也会乱码，但是返回包含中文的对象不会乱码，不需要额外做处理