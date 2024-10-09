---
layout: single
title:  "10/9 [Spring] ClassPathXmlApplicationContext 경로 오류"
categories: [Programming, 오류, Spring]
tag: [Programming, 오류, Spring]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

![image-20241009232919660](/images/2024-10-09-springError/image-20241009232919660.png)

```java 
ApplicationContext context =
                new ClassPathXmlApplicationContext("spring/di/setting.xml");
```

처음에 이렇게 코드를 작성을 하고 xml 파일은

![image-20241009233244464](/images/2024-10-09-springError/image-20241009233244464.png)

java/spring/di 경로에 저장했지만 오류를 읽어보니 xml파일의 저장 경로가 잘못된 것 같아 검색을 해봤다.

![image-20241009233405260](/images/2024-10-09-springError/image-20241009233405260.png)

그림과 같이 resources 폴더 안에 xml파일을 넣고,

```java
ApplicationContext context =
                new ClassPathXmlApplicationContext("setting.xml");
```

이렇게 고치면 정상적으로 실행된다.

![image-20241009233504204](/images/2024-10-09-springError/image-20241009233504204.png)