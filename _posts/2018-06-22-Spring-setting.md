---
layout: post
title: "Spring 초기 설정"
tags: [Spring]
comments: true
---
### servlet-context

```
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

	<!-- DispatcherServlet Context: defines this servlet's request-processing infrastructure -->

	<!-- Enables the Spring MVC @Controller programming model -->
	<annotation-driven />

	<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/" />

	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/views/" />
		<beans:property name="suffix" value=".jsp" />
	</beans:bean>

	<context:component-scan base-package="com.dev.test" />

	<beans:bean id="loginInterceptor" class="com.dev.test.comm.interceptor.LoginInterceptor"></beans:bean>
    <interceptors>
        <interceptor>
            <mapping path="/**"/>
            <exclude-mapping path="/login" />
            <exclude-mapping path="/loginProc" />
            <exclude-mapping path="/resources/*" />
            <exclude-mapping path="/main" />
            <beans:ref bean="loginInterceptor"/>
        </interceptor>
    </interceptors>
</beans:beans>
```

### root-context

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- Root Context: defines shared resources visible to all other web components -->
	<bean id="dataSource" class="org.springframework.jdbc.datasource.SimpleDriverDataSource">
        <property name="driverClass" value="org.mariadb.jdbc.Driver"></property>
        <property name="url" value="jdbc:mariadb://localhost:3306/sample"></property>
        <property name="username" value="root"></property>
        <property name="password" value="admin"></property>
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="configLocation" value="classpath:/mybatis/mybatis-config.xml"></property>
        <property name="mapperLocations" value="classpath*:/mybatis/sql/*.xml"></property>
    </bean>

    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"></constructor-arg>
    </bean>

    <!-- ajax 호출 시 406에러 잡기 -->
    <bean id="objectMapper" class="com.fasterxml.jackson.databind.ObjectMapper"/>
    <bean id="ajaxView" class="org.springframework.web.servlet.view.json.MappingJackson2JsonView">
        <property name="objectMapper" ref="objectMapper"/>
    </bean>
</beans>
```
