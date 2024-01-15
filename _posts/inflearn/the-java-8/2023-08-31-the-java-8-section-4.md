---
layout: single
title: "자바 8(Optional)"
tag: [inflearn, java]
---

# 자바 8 주요 기능 4

## Optional

&nbsp;&nbsp; 자바에서 가장 많이 등장하는 예외 중 하나는 NullPointerException이 아닐까 한다.
NullPointerException이 발생하는 이유는 null 체크를 하지 않았기 때문인데 사람이 매번 null 체크를 꼬박꼬박 다 하기란 쉽지 않다.
또한, 메서드가 null을 반환한다면? 그것도 쉽지 않다. 이에 자바에서는 null을 좀 더 효과적으로 다루기 위해 Optional 클래스가 등장했다.

### Optional 소개

- 값을 Optional 클래스로 한 번 감싼다는 개념
- Optional은 하나의 값만 가지고 있는 불변 클래스이다.
- Optional 안의 값이 있을 수도 있고, 없을 수도 있다.
- 메서드가 Optional을 반환한다면, 클라이언트에게 null이 반환될 수 있음을 명시적으로 알려줄 수 있다.

> Optional의 모습
> 
> ```java
> public final class Optional<T> {
>     
>     private static final Optional<?> EMPTY = new Optional<>(null); // 빈 옵셔널 객체
>     private final T value; // 실제 값
> }
> ```

### 주의할 점
- 리턴 값으로만 쓰기를 권장 (다른 경우에서는 Optional 변수 자체가 null일 가능성이 생긴다.)
- Optional을 리턴하는 메서드에서는 null을 리턴하면 안된다. (Optional의 취지가 무색해진다.)
- 기본 타입을 다루기 위한 OptionalInt, OptionalLong, OptionalDouble이 존재하므로 박싱된 기본 타입을 담은 Optional을 반환하지 말자.
- Collection, Map, Array, Stream, Optional은 Optional로 감싸지 말 것

### API

#### Optional 정적 팩터리 메서드

1. `public static <T> Optional<T> empty()` : 빈 Optional을 반환
2. `public static <T> Optional<T> of(T value)` : value가 담긴 Optional을 반환(value가 null일 경우 NullPointerException 발생)
3. `public static <T> Optional<T> ofNullable(T value)` : `of()` 메서드와 동일하나 value가 null일 경우 빈 Optional을 반환

#### 내부 객체 확인하기

1. `public boolean isPresent()` : 내부에 값이 존재하면(null이 아니라면) true를 반환
2. `public boolean isEmpty()` : 내부에 값이 비어있으면(null이라면) true를 반환 - ***JAVA 11***
3. `public void ifPresent(Consumer<? super T> action)` : 내부에 값이 존재하면, action을 실행

#### 내부 객체 꺼내기

1. `public T get()` : value를 반환. 빈 Optional 즉, value가 null일 경우 NoSuchElementException 발생
2. `public T orElse(T other)` : 기본값 설정 메서드. value를 반환하되 value가 null일 경우 매개변수 other를 반환
3. `public T orElseGet(Supplier<? extends T> supplier)` : `orElse()` 메서드와 동일하나 매개변수로 람다를 입력 받음
4. `public T orElseThrow()` : `get()` 메서드와 동일하나 value가 null일 경우 NoSuchElementException 발생 - ***JAVA 10***
5. `public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X` : value를 반환하되 value가 null일 경우 매개변수로 입력 받은 예외를 던짐

#### 내부 객체 다루기

1. `public Optional<T> filter(Predicate<? super T> predicate)` : 내부 객체가 매개변수로 입력받은 조건에 부합하지 않다면 빈 Optional이 반환됨
2. `public <U> Optional<U> map(Function<? super T, ? extends U> mapper)` : 다른 Optional로 변환하는 메서드
3. `public <U> Optional<U> flatMap(Function<? super T, ? extends Optional<? extends U>> mapper)` : Optional 안에 Optional이 있을 경우 사용

<br>

___

**[참고]**  
백기선, inflearn 강의 ['더 자바, Java 8'](https://www.inflearn.com/course/the-java-java8/dashboard)  
남궁성의 정석코딩, 유튜브 강의 ['자바의 정석'](https://www.youtube.com/watch?v=W_kPjiTF9RI)  
Joshua Bloch, 이펙티브 자바(아이템 55, 옵셔널 반환은 신중히 하라)

[<== section 3](/the-java-8-section-3) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [section 5 ==>](/the-java-8-section-5)