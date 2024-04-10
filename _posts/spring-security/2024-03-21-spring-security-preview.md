---
layout: single
title: "Spring Security 시작하기"
tag: spring security
---

# Spring Security

&nbsp;&nbsp; 이 글은 `Spring Security`를 사용하기에 앞서, 해당 프레임워크가 어떤 기능들을 제공하는지에 대해 학습하고 기록하기 위한 목적을 가지고 있다.

## 개요

&nbsp;&nbsp; `Spring Security`는 인증, 인가, 공격 방어 등에 대한 기능을 제공하고 있다.

## 전제 조건

- Java 8 이상

## 의존성 추가

&nbsp;&nbsp; 이 글은 `Spring Boot`와 함께 사용하는 것을 전제로 작성되고 있으나, 그 외 환경에서 `Spring Security` 의존성을 추가해야 한다면,
다음 글을 참고하길 바란다.

> [Getting Spring Security](https://docs.spring.io/spring-security/reference/getting-spring-security.html)

### Maven with Spring Boot

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
```

### Gradle with Spring Boot

```groovy
dependencies {
    implementation "org.springframework.boot:spring-boot-starter-security"
}
```

___
**[참고]**  
[Spring Security 공식 문서](https://docs.spring.io/spring-security/reference)