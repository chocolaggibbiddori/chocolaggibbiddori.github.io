---
layout: single
title: "부트캠프 54일차"
tag: [bootcamp, spring framework, spring boot, exception]
---

# 유효성 검사와 예외 처리

## Exception

> **_예외(Exception)란?_**
> 
> &nbsp;&nbsp; 개발자가 직접 처리할 수 있는 수준의 작은 오류. 모든 예외 클래스들은 Throwable 클래스를 상속 받아야한다.
> 
> > 개발자가 처리할 수 없는 수준의 심각한 오류는 '에러(error)'라고 부른다.

&nbsp;&nbsp; 예외는 크게 CheckedException과 UncheckedException으로 구분할 수 있다.

### Checked Exception

&nbsp;&nbsp; 컴파일 시점에서 반드시 처리해야 하는 예외. 처리하지 않으면 컴파일 에러가 발생한다.
Exception 클래스의 모든 자손 타입 중 RuntimeException 및 그 자손 타입을 제외한 예외들은 Checked Exception이다.

#### 예외 처리 방법

- 예외 복구 : try/catch 구문으로 예외를 직접 처리
- 예외 처리 회피 : throw 키워드를 사용해 예외 던지기
- 예외 전환 : try/catch 구문으로 예외를 처리하되 catch문에서 다른 예외로 전환해서 다시 던지기

### Unchecked Exception

&nbsp;&nbsp; 명시적으로 처리하는 것을 강제하지 않는 예외. RuntimeException 및 그 자손 타입의 모든 예외는 Unchecked Exception이다.

### 스프링 부트에서 제공하는 예외 처리

1. `@ControllerAdvice`, `@RestControllerAdvice`
   - 예외를 처리하는 컨트롤러
   - 이 어노테이션을 이용해서 예외가 발생하는 부분과 예외를 처리하는 부분을 분리할 수 있음
2. `@ExceptionHandler`
   - value 속성에 예외 클래스를 넣어줌
   - 배열 타입이므로 여러 예외를 넣을 수 있음
   - 컨트롤러에서 해당 예외가 발생하면 이 어노테이션이 달린 메서드를 호출함
   - 예외가 발생하는 컨트롤러 내에서 직접 사용해서 처리할 수도 있음
   - Advice 컨트롤러보다 클래스 내에서 직접 사용하는 예외 처리가 더 우선순위가 높음
   - 상위 타입의 예외보다 구체적인 하위 타입의 예외가 더 우선순위가 높음

#### 예외 처리 우선 순위

&nbsp;&nbsp; 스프링에서는 기본적으로 구체적일수록 우선 순위가 높다.
이는 예외 처리에서도 적용되는데, 우선 순위는 다음과 같이 부여된다.

1. `@(Rest)ControllerAdvice` 내 예외 처리 < `@(Rest)Controller` 내 예외 처리
   - 범용적으로 예외를 처리하는 Advice 보다는 예외가 발생하는 클래스 내에서 직접 예외를 처리하는 것이 더 우선 순위가 높다.
2. 상위 타입 예외 < 하위 타입 예외
   - 예를 들어, `NullPointerException`이 발생한다고 가정했을 때, `@ExceptionHandler` 어노테이션에 `RuntimeException`을 넣어도 해당 오류를 처리할 수 있을 것이다.
     왜냐하면 `NullPointerException`은 `RuntimeException`의 자손 타입이기 때문이다.   
     하지만 `@ExceptionHandler` 어노테이션에 `NullPointerException`을 직접 넣어준 메서드가 있다면, 해당 메서드가 우선 순위가 더 높다.

### Custom Exception

&nbsp;&nbsp; 자바에서 제공하는 표준 예외만 사용해도 모든 상황을 처리할 수 있다.
그럼에도 커스텀 예외를 만들어서 사용하는 이유는 여러 가지 이유가 있다.

1. 예외 이름에 개발자의 의도를 담을 수 있다.
   - 표준 예외만으로는 어떤 예외 상황인지 이해하기 어려울 수 있다.
   - 예외 이름만으로 의도가 전달된다면, 예외 메세지를 상세히 작성하지 않아도 된다.
2. 예외를 관리하기가 수월하다.
   - 커스텀 예외는 개발자가 직접 만든 예외이므로 특성 상황에 맞는 예외를 잘 만들 수 있다.
3. 예외 상황에 대한 처리가 용이하다.
   - 표준 예외를 사용할 경우 의도치 않은 예외 상황도 처리하게 되는데, 커스텀 예외를 사용할 경우 의도하지 않는 부분에 대해서는 예외를 처리하지 않는다.

<br>

___

**[참고]**  
장정우, 스프링 부트 핵심 가이드(스프링 부트를 활용한 애플리케이션 개발 실무)  
깃허브 주소 : [https://github.com/chocolaggibbiddori/springboot-wikibooks](https://github.com/chocolaggibbiddori/springboot-wikibooks)

[<== 부트캠프 53일차](/bootcamp-day53) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 55일차 ==>](/bootcamp-day55)