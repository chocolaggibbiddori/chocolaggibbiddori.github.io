---
layout: single
title: "자바 8(Stream)"
tag: [inflearn, java]
---

<br>

# 자바 8 주요 기능 3

## Stream

&nbsp;&nbsp; 스트림은 데이터 소스를 처리하기 위한 장치라고 할 수 있겠다. 스트림을 사용하면 연속된 데이터를 간결하고 쉽게 처리할 수 있으며 병렬 처리도 가능하다.
for 문과 같은 반복문을 통해 처리하는 것보다 코드가 훨씬 간결해지고 복잡한 연산도 쉽게 표현할 수 있다.

&nbsp;&nbsp; 스트림이 등장함으로써 비로소 여러 데이터 소스를 통일성 있게 다룰 수 있게 되었다.
컬렉션, 배열 등의 데이터 소스를 Stream으로 추상화 하여 모두 동일하게 처리할 수 있다는 뜻이다.

### Stream 소개

- 스트림은 실제 데이터가 담기는 저장소가 아니다. 단순히 입력된 데이터를 처리하여 결과로 출력해내는 도구일 뿐이다.
- 스트림은 데이터 소스를 직접 변경하지 않는다. 스트림에서 데이터들을 처리하더라도 소스 데이터는 변경되지 않는다.
- 스트림은 일회용이다. 한 번 사용된 스트림은 재사용 할 수 없다. (Stream 인터페이스는 AutoCloseable 인터페이스를 상속 받기 때문에 사용 즉시 자동으로 스트림이 닫힌다.)

#### Stream Pipeline

&nbsp;&nbsp; 파이프라인은 0개 이상의 중간 연산과 1개의 종료 연산으로 구성된다. 파이프라인은 종료 연산을 호출해야 종료되며, 중간 연산은 종료 연산을 호출하기 전까지는 실행되지 않는다.

- 스트림 파이프라인 생성
  - Collection 인터페이스의 `stream()` 메서드
  - Arrays 클래스의 `stream()` 메서드
  - Stream 인터페이스의 정적 팩터리 메서드(`of()`, `generate()`, `iterate()`)
- 중간 연산(intermediate operation)
  - Stream을 리턴한다. 즉, 메서드를 여러 번 연쇄적으로 호출할 수 있다.
  - `filter(), map(), skip(), sorted(), ...`
- 종료 연산(terminal operation)
  - Stream이 아닌 다른 결과를 리턴한다.
  - 단 한 번만 호출된다.
  - `collect(), anyMatch(), max(), min(), ...`

#### 기본형 Stream(IntStream, LongStream, DoubleStream)

- int, long, double과 같은 기본형 타입을 스트림으로 다루고 싶을 경우 사용
- 오토 박싱 & 언박싱의 비효율이 제거됨(ex. Stream<Integer> 대신 IntStream 사용)
- Stream 인터페이스에는 없는 숫자와 관련된 유용한 메서드를 제공
  
<br>

### Stream API

#### 스트림 파이프라인 생성

- Collection 인터페이스의 `stream()` 메서드

  &nbsp;&nbsp; Collection 인터페이스는 `default Stream<E> stream()` 메서드를 제공한다.
  따라서, 자바에서 제공하는 컬렉션 클래스들은 `stream()` 메서드를 호출해서 간편하게 스트림을 생성할 수 있다.

- Arrays 클래스의 `stream()` 메서드

  &nbsp;&nbsp; 배열을 다루는 유틸 클래스인 Arrays 클래스에서는 배열을 스트림으로 변환하기 위한 편의 메서드를 제공한다.
  이 메서드는 매개변수로 받은 배열을 스트림으로 변환해서 반환한다.
  특별히, int[], long[], double[] 타입은 각각 IntStream, LongStream, DoubleStream으로 반환해준다.

- Stream 인터페이스의 정적 팩터리 메서드(`of()`, `generate()`, `iterate()`)

  &nbsp;&nbsp; Stream 인터페이스에는 스트림을 생성할 수 있는 정적 팩터리 메서드를 제공한다.

  - `public static<T> Stream<T> empty()` : 빈 스트림을 생성한다.
  - `public static<T> Stream<T> of(T... values)` : 입력 받은 가변 인수를 스트림으로 변환. 내부적으로 `Arrays.stream(values);`를 호출한다.
  - `public static<T> Stream<T> generate(Supplier<? extends T> s)` : Supplier가 제공하는 값으로 무한 스트림을 생성한다.
  - `public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)` : seed(초기값)를 이용한 무한 스트림을 생성한다.
    - @param seed 초기값
    - @param f 다음 요소를 계산하기 위한 함수
  - `public static<T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next)` : seed(초기값)를 이용한 스트림을 생성한다. for문과 유사하다. ( `for (T index = seed; hasNext.test(index); index = next.apply(index)) { ... }` )
    - @param seed 초기값
    - @param hasNext 요소에 적용할 조건
    - @param next 다음 요소를 계산하기 위한 함수

- 스트림 생성 예시

```java
public class StreamTest {
    public static void main(String[] args) {
        // 1. Collection
        List<String> list = new ArrayList<>();
        Set<Integer> set = new HashSet<>();
        Stream<String> listStream = list.stream();
        Stream<Integer> setStream = set.stream();
        
        // 2. Array
        int[] intArr = {1, 2, 3, 4, 5};
        long[] longArr = {1L, 2L, 3L, 4L, 5L};
        double[] doubleArr = {1.0, 2.0, 3.0, 4.0, 5.0};
        String[] strArr = {"1", "2", "3", "4", "5"};
      
        IntStream intStream = Arrays.stream(intArr);
        LongStream longStream = Arrays.stream(longArr);
        DoubleStream doubleStream = Arrays.stream(doubleArr);
        Stream<String> stringStream = Arrays.stream(strArr);
        
        // 3. Stream 정적 팩터리 메서드
        Stream<Integer> integerStream = Stream.of(1, 3, 5);
      
        int[] intArr = {1, 2, 3, 4, 5};
        Stream<int[]> intArrStream = Stream.of(intArr); // 배열의 요소들이 스트림에 담기는 것이 아니라 배열 자체가 담긴다. 주의!!!

        Stream<Integer> generate = Stream.generate(() -> 1); // 1이 무한히 담긴 무한 스트림
        Stream<Integer> iterated1 = Stream.iterate(10, i -> i + 1); // 10부터 시작하여 1씩 증가하는 수들이 담긴 무한 스트림
        Stream<Integer> iterated2 = Stream.iterate(10, i -> i <= 20, i -> ++i); // 10부터 시작하여 1씩 증가하면서 20까지의 수들이 담긴 스트림
    }
}
```

#### 중간 연산(intermediate operation)

&nbsp;&nbsp; 스트림의 중간 연산은 n번 반복할 수 있다. 즉, 아예 하지 않아도 되고 여러 번 호출할 수도 있다. 
스트림의 중간 연산은 무한 스트림을 일정 기준으로 자르거나 내가 원하는 특정한 요소들을 걸러내는 용도 등 다양하게 사용된다.
스트림의 중간 연산은 Stream을 반환하기 때문에 메서드를 연쇄적으로 호출할 수 있어서 코드가 깔끔해진다.  
&nbsp;&nbsp; 다음은 Stream 인터페이스의 중간 연산 메서드들이다.

- `Stream<T> filter(Predicate<? super T> predicate)` : 조건에 부합하는 요소들만 걸러내주는 필터 메서드
  - @param predicate 필터 조건. true를 반환하는 요소들만 스트림에 남는다.
- `<R> Stream<R> map(Function<? super T, ? extends R> mapper)` : 요소들을 다른 요소로 변환하는 메서드. 단순히 값을 바꿔주는 용도로 사용할 수도 있다.
  - @param mapper 매핑 로직. 요소들을 다른 요소로 변환하는 로직을 작성한다.
  - @return 변환된 요소들이 담긴 스트림을 반환한다.
- `<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)` : Stream 내부의 요소들이 컬렉션, 배열과 같은 데이터 소스라면 그 안쪽 요소들을 전부 담아내는 스트림으로 변환하는 메서드. 즉, 2차원적으로 표현되어 있는 스트림을 1차원으로 평평(flat)하게 해준다.
  - @param mapper 매핑 로직. 요소들을 스트림으로 변환해주는 로직을 작성한다.
  - @return 모든 요소들이 1차원적으로 담긴 스트림을 반환한다.
- `Stream<T> distinct()` : 중복된 요소들을 제거해주는 메서드. 중복은 `Object.equals()` 메서드로 판단한다.
- `Stream<T> sorted()` : 정렬 메서드. 요소들을 natural order로 정렬해준다. 정렬 기준은 Comparable 인터페이스에 의한다.
  - @throw ClassCastException 해당 요소가 Comparable의 구현체가 아니라면 ClassCastException 예외가 발생한다.
- `Stream<T> sorted(Comparator<? super T> comparator)` : 정렬 메서드. 비교 기준을 매개변수로 직접 넣어줄 수도 있다.
- `Stream<T> peek(Consumer<? super T> action)` : 요소들을 소비하면서 action 로직을 수행하는 메서드. 주로 요소를 관찰하는 데에 사용한다.
  - @return `peek()` 메서드를 호출하기 이전의 원본 스트림을 반환한다.
- `Stream<T> limit(long maxSize)` : 최대 maxSize 개의 요소만 담아낸다. 즉, maxSize 개 이후의 요소들을 전부 제거한다.
- `Stream<T> skip(long n)` : n 개의 요소들을 스킵한다. 즉, n 개까지의 요소들을 전부 제거한다.

#### 종료 연산(terminal operation)

&nbsp;&nbsp; 스트림의 종료 연산은 딱 한 번만 호출할 수 있다. 종료 연산이 호출되기 전에는 중간 연산도 실행되지 않으며(lazy) 종료 연산이 호출되면 스트림이 자동으로 닫혀 다시 사용할 수 없다.
스트림의 종료 연산은 중간 연산으로 연산된 요소들을 모아서 Collection으로 반환하거나, 갯수를 세는 등의 최종 연산을 수행한다.  
&nbsp;&nbsp; 종료 연산은 Stream이 아닌 다른 타입을 반환하기 때문에 이후에 연쇄적으로 다른 Stream 메서드들을 호출할 수 없다.  
&nbsp;&nbsp; 다음은 Stream 인터페이스의 종료 연산 메서드들이다.

- `void forEach(Consumer<? super T> action)` : 각 요소들에 대해 action 로직을 수행한다. 병렬 스트림인 경우 순서가 보장되지 않는다.
- `void forEachOrdered(Consumer<? super T> action)` : 각 요소들에 대해 action 로직을 수행한다. 병렬 스트림인 경우 순서가 보장된다.
- `Object[] toArray()` : 스트림 요소들을 `Object[]` 배열에 담아 반환한다.
- `<A> A[] toArray(IntFunction<A[]> generator)` : 스트림 요소들을 매개변수로 지정해준 타입의 배열에 담아 반환한다. (ex. `.toArray(String[]::new)`)
- `T reduce(T identity, BinaryOperator<T> accumulator)` : 각 요소들을 하나씩 돌면서 작업을 수행한다.
  - @param identity result 변수의 초기값
  - @param accumulator 작업할 로직. 첫번째 매개변수는 result, 두번째 매개변수는 스트림 요소이다.
  - @return result
- `Optional<T> reduce(BinaryOperator<T> accumulator)` : 위의 reduce 메서드와 달리 초기값이 없다. 즉, 스트림이 비어있으면 result가 null일 수 있기 때문에 Optional을 반환한다.

  > reduce 메서드는 다음과 같은 로직으로 구현된다.
  > 
  > ```java
  > T result = identity;
  > for (T element : this stream)
  >     result = accumulator.apply(result, element);
  > return result;
  > ```

- `<R, A> R collect(Collector<? super T, A, R> collector)` : 스트림의 요소들을 하나로 수집하는 메서드. 보통 매개변수에 Collectors 클래스의 메서드를 넣어준다.
- `Optional<T> min(Comparator<? super T> comparator)` : 스트림 요소들 중 가장 작은 값을 Optional에 담아 반환한다. 빈 스트림일 경우 빈 Optional이 반환된다. 비교 기준은 매개변수로 받는다.
- `Optional<T> max(Comparator<? super T> comparator)` : 스트림 요소들 중 가장 큰 값을 Optional에 담아 반환한다. 빈 스트림일 경우 빈 Optional이 반환된다. 비교 기준은 매개변수로 받는다.
- `long count()` : 스트림 요소의 갯수를 반환한다.
- `boolean anyMatch(Predicate<? super T> predicate)` : 스트림 요소들 중 조건에 맞는 요소가 단 하나라도 있다면 true, 그렇지 않으면 false를 반환한다.
- `boolean allMatch(Predicate<? super T> predicate)` : 빈 스트림이거나 스트림 요소들 중 모든 요소가 조건에 충족된다면 true, 그렇지 않으면 false를 반환한다.
- `boolean noneMatch(Predicate<? super T> predicate)` : 빈 스트림이거나 스트림 요소들 중 모든 요소가 조건에 충족되지 않는다면 true, 그렇지 않으면 false를 반환한다.
- `Optional<T> findFirst()` : 스트림 요소들 중 첫번째 요소를 반환한다.
- `Optional<T> findAny()` : 스트림 요소들 중 무작위 요소를 반환한다. 주로 병렬 스트림에서 사용한다.

<br>

___

**[참고]**  
백기선, inflearn 강의 ['더 자바, Java 8'](https://www.inflearn.com/course/the-java-java8/dashboard)  
남궁성의 정석코딩, 유튜브 강의 ['자바의 정석'](https://www.youtube.com/watch?v=7Kyf4mMjbTQ&list=PLW2UjW795-f6xWA2_MUhEVgPauhGl3xIp)

[<== section 2](/the-java-8-section-2) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [section 4 ==>](/the-java-8-section-4)