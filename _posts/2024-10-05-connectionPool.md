---
layout: single
title:  "10/5 ConnectionPool"
categories: [Programming, JSP]
tag: [Programming, JSP]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

# MySQL

 커넥션풀을 사용하기 위해서 설정을 해줘야 한다.

1. webapp 폴더에 META-INF 폴더 생성 => context.xml 다음과 같이 작성

```xml
<?xml version="1.0" encoding="utf-8" ?>
<context>
    <Resource auth="Container"
          driverClassName="com.mysql.cj.jdbc.Driver"
          type="javax.sql.DataSource"
          initialSize="0"
          minIdle="5"
          maxTotal="20"
          maxIdle="20"
          maxWaitMillis="5000"
          url="jdbc:mysql://localhost:3306/스키마이름"
          name="dbcp_mysql"
          username="mysql아이디"
          password="mysql비밀번호" />
</context>
```

2. WEB-INF 폴더의 web.xml 다음과 같이 작성

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <resource-ref>
        <description>Oracle Datasource example</description>
        <res-ref-name>dbcp_mysql</res-ref-name>
        <res-type>javax.sql.DataSource</res-type> <res-auth>Container</res-auth>
    </resource-ref>
</web-app>

```

3. 코드 예시

```jsp
Context initctx = new InitialContext();
Context envctx = (Context) initctx.lookup("java:comp/env");
DataSource ds = (DataSource) envctx.lookup("dbcp_mysql");
con = ds.getConnection();
```

<b> 여기서 세군데에 적혀있는 dbcp_mysql은 이름을 일치시켜야 한다.</b>