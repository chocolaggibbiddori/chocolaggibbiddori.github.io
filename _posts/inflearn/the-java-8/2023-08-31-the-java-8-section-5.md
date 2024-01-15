---
layout: single
title: "자바 8(Date/Time)"
tag: [inflearn, java]
---

# 자바 8 주요 기능 5

&nbsp;&nbsp; 기존에 사용하던 java.util.Date, java.util.Calendar 같은 클래스들은 mutable 하기 때문에 쓰레드에 안전하지 않았다.
또한, 클래스나 메서드의 이름이 명확하지 않았으며 버그가 발생할 여지가 아주 많았다.
여러 문제점이 지적되면서 자바에서는 날짜와 시간을 다루는 클래스를 새로 만들었다.

## Date와 Time 소개

- Clear! 클래스나 메서드 이름이 명확하며, 쉽게 예측 가능하다.
- Fluent! 코드를 읽거나 사용하기 쉽게 만들어졌다. 메서드 연쇄 호출 또한 가능하도록 설계되었다.
- Immutable! **불변 객체이다**. 해당 객체는 변경할 수 없으며, 내부 값을 변경하는 메서드들은 모두 새로운 객체를 반환한다. 때문에, 쓰레드에 안전하다.
- Extensible! 확장에 용이하다. 나만의 달력 시스템을 만들고 사용할 수도 있다.

<br>

## API

### Instant

&nbsp;&nbsp; 기계용 시간을 표현하는 객체

#### 팩터리 메서드

- `now()` : 기준시 (= UTC, GMT)
- `ofEpochSecond(long epochSecond)` : EPOCH(= 1970-01-01T00:00:00Z)로부터 epochSecond(s) 지난 시점의 시간
- `ofEpochMilli(long epochMilli)` : EPOCH로부터 epochMilli(ms) 지난 시점의 시간
- `parse(CharSequence text)` : text를 파싱한다. 포맷은 '2023-10-02T21:32:30Z'과 같은 양식을 따른다.

#### 주요 메서드

- `Instant plus(long amountToAdd, TemporalUnit unit)` : unit에 해당하는 값에 amountToAdd만큼 더한 시간을 Instant 객체로 반환한다.
이 때, 반환되는 객체는 기존 Instant 객체가 아닌 새로운 객체이므로 사용에 주의해야한다.
   - 사용 예시 : `instant.plus(5, ChronoUnit.DAYS);`
- `Instant plusSeconds(long secondsToAdd)`
- `Instant plusMillis(long millisToAdd)`
- `Instant plusNanos(long nanosToAdd)`
- `Instant minus(long amountToSubtract, TemporalUnit unit)`
- `Instant minusSeconds(long secondsToSubtract)`
- `Instant minusMillis(long millisToSubtract)`
- `Instant minusNanos(long nanosToSubtract)`

> **_여기서 잠깐!_**
> 
> &nbsp;&nbsp; TemporalUnit 타입은 인터페이스다. 구현 클래스로는 enum 타입의 ChronoUnit 클래스가 있는데, 해당 클래스에는 다음과 같은 인스턴스들이 정의되어있다.
> 
> - NANOS
> - MICROS
> - MILLIS
> - SECONDS
> - MINUTES
> - HOURS
> - HALF_DAYS
> - DAYS
> - WEEKS
> - MONTHS
> - YEARS
> - ...

- `ZonedDateTime atZone(ZoneId zone)` - zone에 해당하는 시간을 반환

### LocalDateTime

&nbsp;&nbsp; 인간용 시간을 표현하는 객체

#### 팩터리 메서드

- `now()` - 시스템 시간을 반환함
- `of()` - 날짜, 시간을 직접 매개변수로 넣어줄 수 있음

### ZonedDateTime

&nbsp;&nbsp; 특정 지역의 시간을 표현하는 객체. API를 살펴보면 `ZoneId`라는 객체를 매개변수로 받고 있는걸 볼 수 있는데, 여기에 원하는 지역을 넣어주면 된다.
한국 시간은 `ZoneId.of("Asia/Seoul");`로 표현된다.

### Period, Duration

&nbsp;&nbsp; 기간을 표현하는 객체

### DateTimeFormatter

&nbsp;&nbsp; 날짜 및 시간을 원하는 포맷으로 표현할 수 있다.

<br>

___

**[참고]**  
백기선, inflearn 강의 ['더 자바, Java 8'](https://www.inflearn.com/course/the-java-java8/dashboard)  

[<== section 4](/the-java-8-section-4) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [section 6 ==>](/the-java-8-section-6)