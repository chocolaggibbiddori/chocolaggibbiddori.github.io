---
layout: single
title: "부트캠프 49일차"
tag: [bootcamp, spring framework, spring boot, jpa]
---

# Spring Boot

## Test

### 서비스 객체의 테스트

1. Mockito.mock() - Mock 객체 생성
2. Mockito.when(), thenReturn() - BDDMockito 클래스의 given(), willReturn() 메서드와 사용 방법이 동일함

### 리포지토리 객체의 테스트

1. @DataJpaTest
   - JPA와 관련된 설정만 로드해서 테스트를 진행
   - @Transactional을 포함하고 있어 테스트 코드가 종료되면 자동으로 rollback 됨
   - 기본값으로 임베디드 DB를 사용함
2. 실제 애플리케이션의 DB를 사용하고 싶다면 @AutoConfigureTestDatabase(replace = Replace.NONE) 추가

<br>

## Spring Data JPA

### 쿼리 메서드

&nbsp;&nbsp; Spring Data JPA는 간단히 메서드 이름을 정의하는 것만으로 쿼리를 만들 수 있는 기능을 제공한다.
메서드 명명 규칙은 정해져 있으며, 해당 규칙을 지켜 인터페이스에 추상 메서드로 정의하면 해당 메서드를 구현하지 않아도 동작한다.

#### 주제 키워드

1. 조회 : Optional, Collection, Stream 등으로 반환할 수 있음
   - find...By
   - read...By
   - get...By
   - query...By
   - search...By
   - stream...By
2. 확인
   - exists...By : boolean
   - count...By : long
3. 삭제 : void, long(삭제된 row의 개수를 반환)으로 반환할 수 있음
   - delete...By
   - remove...By
4. 개수 제한 : 주제 키워드와 By 사이에 위치
   - ...First\<number\>...
   - ...Top\<number\>...
   - \<number\> : 제한할 숫자를 입력. 1일 경우 생략 가능
   - ex) `findFirst5ByName(String name);`

#### 조건자 키워드

1. Is, Equals : 값의 일치를 조건으로 사용하는 키워드
   - `findByIdIs(Long id);`
   - `findByIdEquals(Long id);`
2. (Is)Not : 값의 불일치를 조건으로 사용하는 키워드
   - `findByIdIsNot(Long id);`
   - `findByIdNot(Long id);`
3. (Is)Null, (Is)NotNull : Null 값을 조건으로 사용하는 키워드
   - `findByPriceNull();`
   - `findByPriceIsNull();`
   - `findByPriceNotNull();`
   - `findByPriceIsNotNull();`
4. (Is)True, (Is)False : boolean 타입의 컬럼을 확인하는 키워드
   - `findByActiveTrue();`
   - `findByActiveIsTrue();`
   - `findByActiveFalse();`
   - `findByActiveIsFalse();`
5. Or, And : 여러 조건을 묶을 때 사용하는 키워드
   - `findByIdAndName(Long id, String name);`
   - `findByIdOrName(Long id, String name);`
6. (Is)GreaterThan, (Is)LessThan, (Is)Between : 숫자나 datetime 컬럼을 대상으로 하는 비교 연산에 사용되는 키워드
   - `findByPriceIsGreaterThan(int price);`
   - `findByPriceGreaterThan(int price);`
   - `findByPriceGreaterThanEqual(int price);`
   - `findByPriceIsLessThan(int price);`
   - `findByPriceLessThan(int price);`
   - `findByPriceLessThanEqual(int price);`
   - `findByPriceIsBetween(ins lowPrice, int highPrice);`
   - `findByPriceBetween(ins lowPrice, int highPrice);`
7. (Is)StartingWith(=StartsWith), (Is)EndingWith(=EndsWith), (Is)Containing(=Contains), (Is)Like
   - StartsWith : 문자열 뒤에 자동으로 `%`가 붙음
   - EndsWith : 문자열 앞에 자동으로 `%`가 붙음
   - Contains : 문자열 양 옆에 자동으로 `%`가 붙음
   - Like : 문자열에 직접 `%`를 넣어 줘야함

#### 정렬 키워드

1. OrderBy : Asc(기본, 생략 가능), Desc 사용 가능
   - `findByNameOrderById(String name);`
   - `findByNameOrderByIdAsc(String name);`
   - `findByNameOrderByIdDesc(String name);`
   - `findByNameOrderByIdAscPriceDesc(String name);`
2. OrderBy(여러 개) : 정렬 기준이 여러 개 있을 경우 순차적으로 기준을 나열하면 됨(단, 각 기준 사이에는 Asc, Desc를 꼭 명시해야함. 맨 끝 기준에서는 Asc 생략 가능)
   - `findAllByOrderByNamePrice()` : (X) 실패
   - `findAllByOrderByNameAscPrice()` : (O) 성공
   - `findAllByOrderByNameDescPriceDesc()` : (O) 성공
3. Sort 매개변수
   - `findByName(String name, Sort sort);`
   > Sort 클래스
   > 
   > &nbsp;&nbsp; by() 메서드를 통해 정렬 기준을 정의할 수 있다.
   > 
   > 1. by(String... properties) : 정렬 기준들을 나열한다. 나열된 순서대로 기본 정렬(오름차순) 된다.
   > 2. Sort by(Direction direction, String... properties) : 위와 같으나, 정렬 방향을 결정할 수 있다.
   > 3. by(List<Order> orders)
   > 4. by(Order... orders)

#### 페이징

&nbsp;&nbsp; Spring Data JPA는 페이징 기능을 제공한다. 이 기능을 통해 페이지 번호나, 개수 등을 편리하게 조회할 수 있다.
주의할 점은 페이지 번호는 0부터 시작한다!

- 반환 타입 : Page<T>
- 매개변수 : Pageable
- Pageable의 구현체 : PageRequest

> PageRequest의 정적 팩터리 멕서드 `of()`
>
> 1. of(int page, int size)
> 2. of(int page, int size, Sort sort)
> 3. of(int page, int size, Direction direction, String... properties)
> <br><br>
>    - int page : 페이지 번호(0부터 시작)
>    - int size : 페이지 당 데이터 개수
>    - Sort sort : 정렬
>    - Direction direction : 정렬 방향
>    - String... properties : 정렬 기준

<br>

___

**[참고]**  
장정우, 스프링 부트 핵심 가이드(스프링 부트를 활용한 애플리케이션 개발 실무)  
깃허브 주소 : [https://github.com/chocolaggibbiddori/springboot-wikibooks](https://github.com/chocolaggibbiddori/springboot-wikibooks)

[<== 부트캠프 48일차](/bootcamp-day48) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 50일차 ==>](/bootcamp-day50)