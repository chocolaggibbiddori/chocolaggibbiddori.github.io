---
layout: single
title: "자바 8(그 외)"
tag: [inflearn, java]
---

# 자바 8 주요 기능 7

## 어노테이션의 변화

### ElementType 추가
&nbsp;&nbsp; 어노테이션의 `@Target` 범위가 추가되었다.
- TYPE_PARAMETER: 타입 파라미터 선언부
- TYPE_USE: 타입을 사용하는 모든 곳

### 어노테이션 반복 사용
&nbsp;&nbsp; `@Repeatable` 어노테이션을 통해 같은 어노테이션을 중복해서 사용할 수 있게 되었다.

1. 중복해서 사용할 어노테이션을 정의 (ex. `@Food`)
2. 컨테이너 어노테이션을 정의 (ex. `@Foods`)
3. `@Foods` 어노테이션에 `Food[]` 타입의 속성을 정의
4. `@Food` 어노테이션에 `@Repeatable` 어노테이션을 달아주고 값은 `@Foods` 어노테이션으로 지정
> 주의: 컨테이너 어노테이션은 중복 어노테이션보다 `@Retention`의 범위가 더 크거나 같아야 하며 `@Target`의 범위는 더 작거나 같아야 한다.

#### 예시
```java
@Retention(RetentionPolicy.SOURCE)
@Target({ElementType.TYPE, ElementType.PARAMETER}) // TYPE에 사용할 때는 반복 가능하나, PARAMETER에 사용할 때는 반복 불가
@Repeatable(Foods.class)
public @interface Food {
}

@Retention(RetentionPolicy.RUNTIME) // @Food 보다 범위가 크거나 같아야 함
@Target(ElementType.TYPE) // @Food 보다 범위가 작거나 같아야 함
public @interface Foods {

  Food[] value(); // 배열 타입의 속성 필수
}
```

<br>

## 배열 병렬 정렬

&nbsp;&nbsp; `Arrays` 클래스에 병럴 정렬 메서드 `parallelSort()`가 추가되었다.

- 시간 복잡도: O(N*logN)
- 공간 복잡도: O(N)

<br>

## Metaspace

&nbsp;&nbsp; JVM의 여러 메모리 영역 중에 PermGen 메모리 영역이 없어지고 Metaspace 영역이 생겼다.

### PermGen
- permanent generation, 클래스 메타데이터를 담는 곳
- Heap 영역에 속함
- 기본값으로 제한된 크기를 가지고 있음
- -XX:PermSize=N, PermGen 초기 사이즈 설정
- -XX:MaxPermSize=N, PermGen 최대 사이즈 설정

### Metaspace
- 클래스 메타데이터를 담는 곳
- Heap 영역이 아니라, Native 메모리 영역이다.
- 기본값으로 제한된 크기를 가지고 있지 않다. (필요한 만큼 계속 늘어난다.)
- 자바 8부터는 PermGen 관련 java 옵션은 무시한다.
- -XX:MetaspaceSize=N, Metaspace 초기 사이즈 설정
- -XX:MaxMetaspaceSize=N, Metaspace 최대 사이즈 설정

___

**[참고]**  
백기선, inflearn 강의 ['더 자바, Java 8'](https://www.inflearn.com/course/the-java-java8/dashboard)  

[<== section 6](/the-java-8-section-6)