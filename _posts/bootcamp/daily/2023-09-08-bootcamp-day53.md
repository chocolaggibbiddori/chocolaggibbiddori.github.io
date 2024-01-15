---
layout: single
title: "부트캠프 53일차"
tag: [bootcamp, spring framework, spring boot, validation]
---

# 유효성 검사와 예외 처리

## Validation

&nbsp;&nbsp; 스프링 부트에서는 Hibernate Validator를 유효성 검사 표준으로 채택해서 사용하고 있다.
Hibernate Validator는 자바에서 제공하는 Bean Validation 프레임워크의 구현체이다.
검증하고자 하는 값에 @Valid 어노테이션을 붙여서 사용한다.

### 문자열 검증

- `@Null` : null 값만 허용
- `@NotNull` : null 값을 허용하지 않음
- `@NotEmpty` : null, 빈 문자열을 허용하지 않음 (빈 문자열 = `""`)
- `@NotBlank` : null, 빈 문자열, 공백을 허용하지 않음 (공백 = `" "`)
- `@Size(min = $number1, max = $number2)` : $number1 이상 $number2 이하의 범위를 허용

### 숫자 검증

#### 최대값/최소값 검증

- `@DecimalMax(value = "$numberStr")` : $numberStr보다 작거나 같은 값만 허용
- `@DecimalMin(value = "$numberStr")` : $numberStr보다 크거나 같은 값만 허용
- `@Max(value = $number)` : $number보다 작거나 같은 값만 허용
- `@Min(value = $number)` : $number보다 크거나 같은 값만 허용

#### 값의 범위 검증

- `@Positive` : 양수만 허용
- `@PositiveOrZero` : 양수, 0만 허용
- `@Negative` : 음수만 허용
- `@NegativeOrZero` : 음수, 0만 허용

#### 자리수 검증

- `@Digits(integer = $number1, fraction = $number2)` : $number1의 정수 자리수와 $number2의 소수 자리수를 허용

### 시간에 대한 검증

- `@Future` : 현재보다 미래의 날짜만 허용
- `@FutureOrPresent` : 현재, 미래의 날짜만 허용
- `@Past` : 과거의 날짜만 허용
- `@PastOrPresent` : 과거, 현재의 날짜만 허용

### 이메일 검증

- `@Eamil` : 이메일 형식을 검증. ""를 허용

### Boolean 검증

- `@AssertTrue` : true만 허용. null 값은 체크하지 않음
- `@AssertFalse` : false만 허용. null 값은 체크하지 않음

### 정규식 검증

- `@Pattern(regexp = "$expression")` : 정규식 검증

> ***정규식이란?***
> 
> &nbsp;&nbsp; 정규식(Regular Expression)이란 특정한 규칙을 가진 문자열 집합을 표현하기 위해 쓰이는 형식
> 
> - ^ : 문자열 시작
> - $ : 문자열 종료
> - . : 임의의 한 문자
> - \* : 앞 문자가 없거나 무한정 많음
> - \+ : 앞 문자가 하나 이상
> - ? : 앞 문자가 없거나 하나 존재
> - [,] : 문자의 집합이나 범위. 두 문자 사이는 - 기호로 범위를 표현
> - {, } : 횟수 또는 범위를 의미
> - (, ) : 괄호 안의 문자를 하나의 문자로 인식
> - | : 패턴 안에서 OR 연산을 수행
> - \ : 정규식에서 역슬래시는 확장문자로 취급하고, 역슬래시 다음에 특수문자가 오면 문자로 인식
> - \b : 단어의 경계
> - \B : 단어가 아닌 것에 대한 경계
> - \A : 입력의 시작 부분
> - \G : 이전 매치의 끝
> - \z : 입력의 끝
> - \Z : 종결자가 있는 경우 입력의 끝
> - \s : 공백 문자
> - \S : 공백 문자가 아닌 나머지 문자(= ^\s)
> - \w : 알파벳이나 숫자
> - \W : 알파벳이나 숫자가 아닌 문자(= ^\w)
> - \d : 숫자 [0-9]와 동일하게 취급
> - \D :숫자를 제외한 모든 문자(= ^[0-9])

### @Validated

&nbsp;&nbsp; JAVA에서 제공하는 `@Valid` 어노테이션 대신 Spring에서는 `@Validated` 어노테이션을 지원한다.
`@Validated` 어노테이션은 groups 속성을 가지고 있어 `@Valid` 어노테이션보다 유용하게 사용할 수 있다.

- `@Validated 어노테이션에 특정 그룹을 설정하지 않은 경우` : groups 속성이 설정되지 않은 필드에 대해 유효성 검사를 수행
- `@Validated 어노테이션에 특정 그룹을 설정한 경우` : 해당 그룹으로 지정된 필드에 대해서만 유효성 검사를 수행

### 커스텀 Validation

1. 어노테이션 생성
   - `@Constraint` 어노테이션을 포함해서 Validator 클래스를 넣어줌
      ```java
      @Target(ElementType.FIELD)
      @Retention(RetentionPolicy.RUNTIME)
      @Constraint(validatedBy = TelephoneValidator.class)
      public @interface Telephone {
 
          String message() default "전화번호 형식이 일치하지 않습니다.";
          Class<?>[] groups() default {};
          Class<?>[] payload() default {};
      }
      ```
2. ConstraintValidator<A, T> 구현체 생성
   - A : 사용할 어노테이션
   - T : 적용할 객체의 타입
   - isValid() : true가 리턴되면 유효성 검사 통과
3. 필요한 곳에 생성해준 어노테이션 사용

<br>

___

**[참고]**  
장정우, 스프링 부트 핵심 가이드(스프링 부트를 활용한 애플리케이션 개발 실무)  
깃허브 주소 : [https://github.com/chocolaggibbiddori/springboot-wikibooks](https://github.com/chocolaggibbiddori/springboot-wikibooks)

[<== 부트캠프 51일차](/bootcamp-day51) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 54일차 ==>](/bootcamp-day54)