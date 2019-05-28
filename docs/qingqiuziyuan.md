springmvc默认只能访问jsp，其他任何资源css,js,html都不能直接访问

<!-- 我们需要在springmvc.xml中加配置 

    <mvc:default-servlet-handler/>

意思是如果springmvc中没有映射的路径。会交给tomcat默认处理

但是加了此配置后，发现@RequestParameter注解失效
需要加配置 <mvc:annotation-driven/>


或者： -->
<!--静态资源-->
在web目录下新建resources文件夹，把静态资源都放到此文件夹

    <mvc:resources mapping="/resources/**" location="/resources/"/>