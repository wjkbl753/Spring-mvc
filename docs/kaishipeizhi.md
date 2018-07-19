`web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">

    <display-name>mybatisplus-spring-mvc</display-name>

    <!-- 字符集 过滤器 -->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <!-- 加载Spring配置文件 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/spring.xml</param-value>
    </context-param>

    <!-- Spring监听器 -->
    <listener>
        <description>Spring监听器</description>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!-- 防止Spring内存溢出监听器 -->
    <listener>
        <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
    </listener>

    <!-- Spring MVC -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <description>SpringMVC</description>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!-- Session超时时间 -->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```
`spring-mvc.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <mvc:default-servlet-handler/>

    <!-- Controller包(自动注入) -->
    <context:component-scan base-package="com.baomidou.springmvc.controller"/>

    <!-- FastJson注入 -->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <!-- 避免IE执行AJAX时,返回JSON出现下载文件 -->
            <!-- FastJson -->
            <bean id="fastJsonHttpMessageConverter"
                  class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <!-- 这里顺序不能反，一定先写text/html,不然ie下出现下载提示 -->
                        <value>text/html;charset=UTF-8</value>
                        <value>application/json;charset=UTF-8</value>
                    </list>
                </property>
                <property name="features">
                    <array value-type="com.alibaba.fastjson.serializer.SerializerFeature">
                        <!-- 避免循环引用 -->
                        <value>DisableCircularReferenceDetect</value>
                        <!-- 是否输出值为null的字段 -->
                        <value>WriteMapNullValue</value>
                        <!-- 数值字段如果为null,输出为0,而非null -->
                        <value>WriteNullNumberAsZero</value>
                        <!-- 字符类型字段如果为null,输出为"",而非null  -->
                        <value>WriteNullStringAsEmpty</value>
                        <!-- List字段如果为null,输出为[],而非null -->
                        <value>WriteNullListAsEmpty</value>
                        <!-- Boolean字段如果为null,输出为false,而非null -->
                        <value>WriteNullBooleanAsFalse</value>
                    </array>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>

    <!-- 静态资源配置 -->
    <mvc:resources mapping="/resources/**" location="/resources/"/>

    <!-- 对模型视图名称的解析,即在模型视图名称添加前后缀 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <!-- 上传限制 -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 上传文件大小限制为31M，31*1024*1024 -->
        <property name="maxUploadSize" value="32505856"/>
    </bean>
</beans>
```

`spring.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

    <!-- 引入属性文件 -->
    <context:property-placeholder location="classpath:config.properties"/>

    <!-- Service包(自动注入) -->
    <context:component-scan base-package="com.baomidou.springmvc.service"/>

    <import resource="classpath:spring/spring-mybatis.xml"/>
</beans>
```
`spring-mybatis.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 配置数据源 -->
    <bean name="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc_url}"/>
        <property name="username" value="${jdbc_username}"/>
        <property name="password" value="${jdbc_password}"/>

        <!-- 初始化连接大小 -->
        <property name="initialSize" value="0"/>
        <!-- 连接池最大使用连接数量 -->
        <property name="maxActive" value="20"/>
        <!-- 连接池最大空闲 -->
        <property name="maxIdle" value="20"/>
        <!-- 连接池最小空闲 -->
        <property name="minIdle" value="0"/>
        <!-- 获取连接最大等待时间 -->
        <property name="maxWait" value="60000"/>

        <property name="validationQuery" value="${validationQuery}"/>
        <property name="testOnBorrow" value="false"/>
        <property name="testOnReturn" value="false"/>
        <property name="testWhileIdle" value="true"/>

        <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
        <property name="timeBetweenEvictionRunsMillis" value="60000"/>
        <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
        <property name="minEvictableIdleTimeMillis" value="25200000"/>

        <!-- 打开removeAbandoned功能 -->
        <property name="removeAbandoned" value="true"/>
        <!-- 1800秒，也就是30分钟 -->
        <property name="removeAbandonedTimeout" value="1800"/>
        <!-- 关闭abanded连接时输出错误日志 -->
        <property name="logAbandoned" value="true"/>

        <!-- 监控数据库 -->
        <property name="filters" value="mergeStat"/>
    </bean>

    <!-- Spring整合Mybatis，更多查看文档：http://mp.baomidou.com -->
    <bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.spring.MybatisSqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- 自动扫描Mapping.xml文件 -->
        <property name="mapperLocations" value="classpath:mybatis/*/*.xml"/>
        <property name="configLocation" value="classpath:mybatis/mybatis-config.xml"/>
        <property name="typeAliasesPackage" value="com.baomidou.springmvc.model.*"/>
        <property name="typeEnumsPackage" value="com.baomidou.springmvc.model.enums"/>
        <property name="plugins">
            <array>
                <!-- 分页插件配置 -->
                <bean id="paginationInterceptor" class="com.baomidou.mybatisplus.plugins.PaginationInterceptor">
                </bean>
            </array>
        </property>
	    <!-- 全局配置注入 -->
	    <property name="globalConfig" ref="globalConfig" />
	</bean>
	<bean id="globalConfig" class="com.baomidou.mybatisplus.entity.GlobalConfiguration">
	    <!--
			AUTO->`0`("数据库ID自增")
		 	INPUT->`1`(用户输入ID")
			ID_WORKER->`2`("全局唯一ID")
			UUID->`3`("全局唯一ID")
		-->
	    <property name="idType" value="2" />
		<!--
			MYSQL->`mysql`
			ORACLE->`oracle`
			DB2->`db2`
			H2->`h2`
			HSQL->`hsql`
			SQLITE->`sqlite`
			POSTGRE->`postgresql`
			SQLSERVER2005->`sqlserver2005`
			SQLSERVER->`sqlserver`
		-->
		<!-- Oracle需要添加该项 -->
	    <!-- <property name="dbType" value="oracle" /> -->
	    <!-- 全局表为下划线命名设置 true -->
	    <!-- <property name="dbColumnUnderline" value="true" /> -->
        <property name="metaObjectHandler">
            <bean class="com.baomidou.springmvc.common.MyMetaObjectHandler" />
        </property>
	</bean>

    <!-- MyBatis 动态扫描  -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.baomidou.springmvc.mapper"/>
    </bean>

    <!-- 配置事务管理 -->
    <bean name="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 事务管理 属性 -->
    <tx:advice id="transactionAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="add*" propagation="REQUIRED"/>
            <tx:method name="append*" propagation="REQUIRED"/>
            <tx:method name="save*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="modify*" propagation="REQUIRED"/>
            <tx:method name="edit*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="remove*" propagation="REQUIRED"/>
            <tx:method name="repair" propagation="REQUIRED"/>

            <tx:method name="get*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="find*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="load*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="search*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="datagrid*" propagation="REQUIRED" read-only="true"/>

            <tx:method name="*" propagation="REQUIRED" read-only="true"/>
        </tx:attributes>
    </tx:advice>
    <!-- 配置切面 -->
    <aop:config>
        <aop:pointcut id="transactionPointcut" expression="execution(* com.baomidou.springmvc.service..*.*(..))"/>
        <aop:advisor pointcut-ref="transactionPointcut" advice-ref="transactionAdvice"/>
    </aop:config>
</beans>
```
`mybatis-config.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD SQL Map Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--
 |   plugins在配置文件中的位置必须符合要求，否则会报错，顺序如下:
 |   properties?, settings?,
 |   typeAliases?, typeHandlers?,
 |   objectFactory?,objectWrapperFactory?,
 |   plugins?,
 |   environments?, databaseIdProvider?, mappers?
 |-->
<configuration>
    <!--
     | 全局配置设置
     |
     | 可配置选项                   默认值,     描述
     |
     | aggressiveLazyLoading       true,     当设置为‘true’的时候，懒加载的对象可能被任何懒属性全部加载。否则，每个属性都按需加载。
     | multipleResultSetsEnabled   true,     允许和不允许单条语句返回多个数据集（取决于驱动需求）
     | useColumnLabel              true,     使用列标签代替列名称。不同的驱动器有不同的作法。参考一下驱动器文档，或者用这两个不同的选项进行测试一下。
     | useGeneratedKeys            false,    允许JDBC 生成主键。需要驱动器支持。如果设为了true，这个设置将强制使用被生成的主键，有一些驱动器不兼容不过仍然可以执行。
     | autoMappingBehavior         PARTIAL,  指定MyBatis 是否并且如何来自动映射数据表字段与对象的属性。PARTIAL将只自动映射简单的，没有嵌套的结果。FULL 将自动映射所有复杂的结果。
     | defaultExecutorType         SIMPLE,   配置和设定执行器，SIMPLE 执行器执行其它语句。REUSE 执行器可能重复使用prepared statements 语句，BATCH执行器可以重复执行语句和批量更新。
     | defaultStatementTimeout     null,     设置一个时限，以决定让驱动器等待数据库回应的多长时间为超时
     | -->
    <settings>
        <!-- 这个配置使全局的映射器启用或禁用缓存 -->
        <setting name="cacheEnabled" value="true"/>
        <!-- 全局启用或禁用延迟加载。当禁用时，所有关联对象都会即时加载 -->
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="multipleResultSetsEnabled" value="true"/>
        <setting name="useColumnLabel" value="true"/>
        <setting name="defaultExecutorType" value="REUSE"/>
        <setting name="defaultStatementTimeout" value="25000"/>
    </settings>
</configuration>
```