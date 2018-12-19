    ssh整合和ssm整合类似,struts已经默认和web容器整合，那么现在只剩spring和hibernate的整合

`web.xml`
```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring.xml</param-value>
</context-param>

<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<filter>
    <filter-name>StrutsPrepareAndExecuteFilter</filter-name>
    <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>StrutsPrepareAndExecuteFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

`struts2.xml`
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
    "http://struts.apache.org/dtds/struts-2.0.dtd">
<struts>
	<!-- 创建一个default包，继承自Struts2的struts-default包 -->
	<package name="default" namespace="/" extends="struts-default">
		<action name="hello" class="com.weixin.action.HelloAction">
			<!-- 结果为"helloWorld"时，跳转至helloWorld.jsp页面 -->
			<result name="success">/hello.jsp</result>
		</action>
	</package>
</struts>
```

`spring.xml`
```
<context:property-placeholder location="classpath:db.properties"/>

<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
    <property name="driverClass" value="${jdbc.driverClass}"></property>
    <property name="user" value="${jdbc.user}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>

<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <property name="mappingLocations">
        <list>
            <value>classpath:com/weixin/bean/*.hbm.xml</value>
        </list>
    </property>
</bean>
<bean id="hibernateTemplate" class="org.springframework.orm.hibernate3.HibernateTemplate">
    <!-- 注入sessionFactory -->
    <property name="sessionFactory" ref="sessionFactory"></property>
</bean>


<bean id="transactionManager" class="org.springframework.orm.hibernate3.HibernateTransactionManager">
    <property name="sessionFactory" ref="sessionFactory"/>
</bean>
<!-- 事务属性-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="*"/>
    </tx:attributes>
</tx:advice>
<!-- 切入-->
<aop:config>
    <aop:pointcut id="txPoint" expression="execution(* com.weixin.service..*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPoint"/>
</aop:config>
```

`log4j.xml`
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
 
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
 
 <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
   <param name="Encoding" value="UTF-8" />
   <layout class="org.apache.log4j.PatternLayout">
    <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m  (%F:%L) \n" />
   </layout>
 </appender>
<logger name="org.hibernate.SQL" >
	<level value="DEBUG"></level>
</logger>
<logger name="org.hibernate.type" >
	<level value="trace"></level>
</logger>
<root>
   <level value="error" />
   <appender-ref ref="STDOUT" />
 </root>
</log4j:configuration>
```