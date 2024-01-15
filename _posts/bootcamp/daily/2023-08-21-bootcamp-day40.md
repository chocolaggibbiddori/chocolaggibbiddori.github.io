---
layout: single
title: "부트캠프 40일차"
tag: [bootcamp, aop, spring framework]
---

<br>

# Spring AOP

&nbsp;&nbsp; 스프링에서 사용하는 AOP에 대해 공부하였다.
AOP(Aspect Oriented Programming)는 관점 지향 프로그래밍으로 애플리케이션을 바라보는 관점을 다르게 보자는 뜻인데, 기존 OOP 방식의 부족한 부분을 보조하는 목적으로 개발되었다.

&nbsp;&nbsp; 예를 들어, 모든 컨트롤러와 서비스에 정의된 메서드가 호출되면 해당 메서드에 대한 로그를 남기는 코드를 작성한다고 해보자.
그렇기 위해서는 모든 메서드에 똑같은 로그를 다 남겨야 할 것이다. OOP로 이렇게 코드를 작성한다면 다음과 같은 문제점이 발생한다.

1. 코드의 중복이 매우 많아진다.
2. 코드를 변경할 때 매우 많은 수정이 필요하다.
3. 코드 적용 대상을 변경할 때 복잡하다.

&nbsp;&nbsp; 애플리케이션에는 핵심 기능과 부가 기능이 있는데 로그를 남기는 기능은 부가적인 기능이라고 할 수 있다. 이런 부가 기능에 많은 비용이 들어가는 것은 매우 비효율적이다.  
&nbsp;&nbsp; 이를 해결하기 위해 나온 것이 AOP 개념이며 애플리케이션을 바라보는 관점을 하나하나의 기능에서 횡단 관심사(cross-cutting concerns) 관점으로 달리 보는 것이다.

<br>

## 핵심 개념

- Advice : 횡단 관심사. 부가 기능 로직을 이르는 말
- Pointcut : Advice가 적용될 위치를 결정
- Advisor : Advice와 Pointcut을 결합한 객체

<br>

## 스프링에서 AOP를 적용하는 방법

&nbsp;&nbsp; 다음과 같은 간단한 Advisor가 있다고 해보자.

```java
package com.chocola.aop;

import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Slf4j // lombok의 로그 기능
@Component // Advisor를 Spring Bean으로 등록해야함
@Aspect // 1. Advisor 객체로 인식됨
public class LogAdvisor {

    @Before("execution(* com.chocola.controller..*(..)) || execution(* com.chocola.service..*(..))") // 2. Pointcut
    public void doLog(JoinPoint joinPoint) { // 3. Advice
        log.info("call={}", joinPoint.getSignature());
    }
}
```

### Advisor

#### 개념

&nbsp;&nbsp; Pointcut과 Advice를 한 곳에 모아놓은 객체. 둘을 한 곳에서 간편하게 관리할 수 있다.

#### 정의하는 방법

&nbsp;&nbsp; Advisor로 지정하고 싶은 클래스에 `@Aspect`를 달아주면 Spring이 해당 객체를 Advisor로 인식해서 자동으로 등록해준다.
단, 스프링 빈으로 등록되는 것은 아니기 때문에 해당 객체를 빈으로 등록하거나 `@Import` 등을 사용하여 해당 객체를 스프링에게 알려주어야 한다.

<br>

### Pointcut

#### 개념

&nbsp;&nbsp; Advice 즉, 부가 기능의 로직을 적용할 대상을 지정한다.

#### 표현식

&nbsp;&nbsp; Pointcut은 AspectJ 표현식으로 작성한다.

##### execution

> execution(접근제어자 반환타입 패키지.타입명.메서드명(파라미터) 예외)

1. 접근제어자, 선언타입, 예외 생략 가능
2. `*` 패턴 사용 가능 : 어떤 값이든 들어와도 좋다는 뜻
3. 패키지
   - `.` : 정확하게 해당 위치의 패키지
   - `..` : 하위 패키지까지 포함
4. 파라미터
   - `()` : 파라미터가 없음
   - `(String)` : 정확하게 String 타입 파라미터
   - `(*)` : 모든 타입의 파라미터, 파라미터가 하나여야 한다.
   - `(*, *)` : 파라미터가 둘
   - `(String, *)` : 첫번째 파라미터는 String 타입이여야 한다. 파라미터는 두 개
   - `(String, ..)` : 첫번째 파라미터는 String 타입, 파라미터 개수는 상관 없음
   - `(..)` : 파라미터의 타입, 숫자에 무관
5. ex)
   - `execution(public String com.chocola.controller.StudyController.listGet(Model))`
   - `execution(* com.chocola.controller..*(..))` : controller 패키지(하위 패키지 포함) 모든 메서드
   - `execution(* *(..))`

##### within

> within(패키지.타입명)

1. execution의 패키지.타입명과 동일
2. 정확히 해당 타입만 허용한다. 즉, 다형성 성립하지 않기 때문에 인터페이스를 지정해서는 안된다(execution은 하위 타입을 허용함).
3. ex)
   - `within(com.chocola.controller.StudyController)`

##### target

> target(패키지.타입명)

1. within과 동일
2. within에서 다형성 성립을 원한다면 target을 사용하면 된다.

##### args

> args(파라미터)

1. execution 파라미터와 동일
2. execution은 정확한 타입만 허용하지만(클래스에 선언된 정보를 기반으로 판단), args는 하위 타입까지 허용한다(런타임에 넘어온 인스턴스를 보고 판단).

##### bean

> bean(studyService)  
> bean(*Service) // Service로 끝나는 빈

1. Spring Bean의 이름으로 지정할 수 있다.

#### 어노테이션

&nbsp;&nbsp; 어노테이션으로도 포인트컷을 정의할 수 있다.

##### @Pointcut

> @Pointcut(AspectJ 표현식)

1. 메서드에 `@Pointcut` 어노테이션을 붙혀서 만든다.
2. 메서드의 반환 타입은 `void`여야 한다.
3. 메서드 본문은 비워둔다.
4. 포인트컷 시그니처는 `메서드명(파라미터)`이다.
5. 접근 제어자는 해당 Aspect 내부에서만 사용하려면 `private`, 다른 Aspect에서도 사용하려면 `public`으로 한다.
6. ex)
   ```java
   package com.chocola.aop;

   import lombok.extern.slf4j.Slf4j;
   import org.aspectj.lang.JoinPoint;
   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;
   import org.aspectj.lang.annotation.Pointcut;
   import org.springframework.stereotype.Component;

   @Slf4j
   @Component
   @Aspect
   public class LogAdvisor {

       @Pointcut("execution(* com.chocola.controller..*(..)) || execution(* com.chocola.service..*(..))")
       private void log() {} // Pointcut Signature
    
       @Before("log()")
       public void doLog(JoinPoint joinPoint) {
           log.info("call={}", joinPoint.getSignature());
       }
   }
   ```

##### @target, @within 

> @target(패키지.어노테이션명)  
> @within(패키지.어노테이션명)

1. @target : 해당 어노테이션을 가지고 있는 인스턴스의 모든 메서드에 적용(부모 메서드 포함)
2. @within : 해당 어노테이션을 가지고 있는 인스턴스 자신의 메서드에만 적용(부모 메서드 미포함)

##### @annotation

> @annotation(패키지.어노테이션명)

1. 메서드(조인 포인트)가 해당 어노테이션을 가지고 있으면 매칭한다.

##### @args

> @args(패키지.어노테이션명)

1. 파라미터가 해당 어노테이션을 가지고 있으면 매칭한다.

<br>

### Advice

&nbsp;&nbsp; 부가 기능이 정의된 메서드를 의미한다. 같은 Aspect 안에 동일한 종류의 Advice가 있으면 순서가 보장되지 않는다.
모든 Advice는 첫번째 파라미터로 JoinPoint 인터페이스를 받을 수 있다.

> **JoinPoint 인터페이스의 기능**
> 1. getArgs() : 메서드 인수를 반환
> 2. getThis() : 프록시 객체를 반환
> 3. getTarget() : 대상 객체를 반환
> 4. getSignature() : 조인되는 메서드에 대한 설명을 반환
> 5. toString() : 조인되는 방법에 대한 유용한 설명을 반환

#### @Around

1. 메서드 호출 전후에 수행, 가장 강력한 어드바이스, 조인 포인트 실행 여부 선택, 반환 값 변환, 예외 변환 등 가능
2. `ProceedingJoinPoint` 인터페이스를 파라미터로 받아야 타겟을 호출할 수 있다(`ProceedingJoinPoint`의 `proceed()`를 호출하여 타겟 호출).

#### @Before

1. 조인 포인트 이전에 실행

#### @AfterReturning

1. 조인 포인트 정상 완료후 실행
2. returning 속성에 사용된 이름은 어드바이스 메서드의 매개변수 이름과 일치해야 한다.
3. returning 절에 지정된 타입의 값을 반환하는 메서드만 대상으로 실행한다. (부모 타입을 지정하면 모든 자식 타입은 인정된다.)

```java
@AfterReturning(value = "hello.aop.order.aop.Pointcuts.orderAndService()", returning = "result")
public void doReturn(JoinPoint joinPoint, Object result) {
    log.info("[return] {} return={}", joinPoint.getSignature(), result);
}
```

#### @AfterThrowing

1. 메서드가 예외를 던지는 경우 실행
2. throwing 속성에 사용된 이름은 어드바이스 메서드의 매개변수 이름과 일치해야 한다.
3. throwing 절에 지정된 타입과 맞는 예외를 대상으로 실행한다. (부모 타입을 지정하면 모든 자식 타입은 인정된다.)

```java
@AfterThrowing(value = "hello.aop.order.aop.Pointcuts.orderAndService()", throwing = "ex")
public void doThrowing(JoinPoint joinPoint, Exception ex) {
    log.info("[ex] {} message={}", joinPoint.getSignature(), ex.getMessage());
}
```

#### @After

1. 조인 포인트 실행 후 반드시 실행(finally)

<br>

___
**[참고]**
김영한, inflearn 강의 ['스프링 핵심 원리 - 고급편'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8)

[<== 부트캠프 35일차](/bootcamp-day35) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 42일차 ==>](/bootcamp-day42)