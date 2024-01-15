---
layout: single
title: "자바 8(인터페이스의 변화)"
tag: [inflearn, java]
---

<br>

# 자바 8 주요 기능 2

## 인터페이스의 변화

### 인터페이스 기본(default) 메서드와 스태틱 메서드

#### 기본(default) 메서드

&nbsp;&nbsp; 이미 널리 사용된, 그래서 여러 클래스들이 구현하고 있는 인터페이스는 추가적인 메서드를 쉽게 추가할 수 없다.
메서드가 추가되는 순간 그 모든 구현 클래스들이 해당 메서드를 override 해야 하기 때문이다.  
&nbsp;&nbsp; default 메서드는 인터페이스가 직접 메서드를 구현해서 제공하며, 기본적으로 구현되어 있기 때문에 구체 클래스가 따로 구현하지 않아도 컴파일 에러가 발생하지 않는다.

&nbsp;&nbsp; default 메서드는 메서드 앞에 default 예약어를 붙혀서 선언한다.

```java
public interface Foo {
    
    void printName(String name);
    
    default void printNameToUpperCase(String name) {
        System.out.println("name = " + name.toUpperCase());
    }
}
```

- 특징
  1. 구현 클래스를 깨트리지 않고 신규 메서드를 추가할 수 있다.
  2. 구현 클래스가 알 수 없게 추가되는 것이므로 리스크가 있다. (내부 로직에 따라 예외가 발생할 수 있다) - **반드시 문서화 할 것!!** (@implSpec 자바독 태그 사용)
  3. Object 클래스에서 제공되는 메서드는 기본 메서드로 제공할 수 없다. (컴파일 에러)
  4. default 메서드가 있는 인터페이스를 상속 받는 인터페이스에서 다시 추상 메서드로 변경할 수 있다.
  5. 여러 인터페이스를 구현할 때 default 메서드가 겹치는 경우, 구현 클래스는 반드시 충돌되는 메서드를 override 해야한다.

- 예시
  - 예외가 발생할 수 있다.
    ```java
    public interface Foo {
    
        String getName();
    
        default void printNameToUpperCase() {
            System.out.println("name = " + getName().toUpperCase()); // getName()이 null을 반환할 경우 NullPointerException 발생!
        }
    }
    ```
  - 다시 추상 메서드로 변경할 수 있다.
    ```java
    public interface Foo {
    
        default void printNameToUpperCase(String name) {
            System.out.println("name = " + name.toUpperCase());
        }
    }
    
    public interface Bar extends Foo {
    
        void printNameToUpperCase(String name); // 추상 메서드로 재정의. Bar를 구현한 클래스는 이 메서드를 재정의 해야한다.
    }
    ```
  - 메서드가 충돌되면 반드시 재정의 해야한다.
    ```java
    public interface Foo {
    
        default void print() {
            System.out.println("Foo");
        }
    }
    
    public interface Bar {
    
        default void print() {
            System.out.println("Bar");
        }
    }
    
    public class TestImpl implements Foo, Bar {
    
        @Override
        public void print() { // 재정의 하지 않으면 컴파일 에러 발생
            System.out.println("TestImpl");
        }
    }
    ```

<br>

#### 스태틱(static) 메서드

&nbsp;&nbsp; 인터페이스에서도 스태틱 메서드를 사용할 수 있도록 바뀌었다. 선언 및 사용 방법은 일반 클래스에서 사용하던 방식과 동일하다.

<br>

### 자바 8 API의 기본 메서드와 스태틱 메서드

#### Iterable

- default void forEach(Consumer<? super T> action) : 각 요소를 돌면서 매개변수로 넣어준 로직을 수행하는 메서드
  - @param Consumer<? super T> action : for 문을 돌면서 수행할 로직
- default Spliterator<T> spliterator() : Spliterator를 반환하는 메서드
  - @return Spliterator를 반환

선언부

```java
public interface Iterable<T> {

    ...

    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action); // action이 null이면 NullPointerException 발생
        for (T t : this) {
            action.accept(t);
        }
    }

    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
}
```

> Spliterator에 대해서
> 
> &nbsp;&nbsp; Spliterator는 Iterator와 유사한 기능을 가지면서도 split의 기능까지 겸비한 인터페이스이다.  
> Spliterator를 사용하면 분할 및 순회를 효율적으로 사용할 수 있다.
> 
> - 주요 메서드
>   - boolean tryAdvance(Consumer<? super T> action) : 다음 요소가 있다면, 해당 요소를 가지고 action을 수행한다.
>   - Spliterator<T> trySplit() : 요소들을 분할한다. 기존 Spliterator을 절반으로 분할해서 반환한다. 테스트 결과 기존 Spliterator는 절반 중 뒷부분, 새로 할당된 Spliterator는 앞부분을 가진다.
>   - default void forEachRemaining(Consumer<? super T> action) : 모든 요소를 순회하면서 action을 수행한다. 내부적으로 `do { } while (tryAdvance(action));`을 호출함

<br>

#### Collection

- default boolean removeIf(Predicate<? super E> filter) : 각 요소를 순회하면서 filter 조건에 부합하는 요소를 제거한다. (정확히는 Iterator의 remove() 메서드를 호출함)
  - @param Predicate<? super E> filter : 요소 제거 조건. true를 반환하는 요소들을 제거한다.
  - @return 요소가 하나라도 제거되면 true를 반환, 하나도 제거되지 않으면 false를 반환한다.
- default Spliterator<E> spliterator() : Spliterator를 반환하는 메서드
  - @return Spliterator를 반환

선언부

```java
public interface Collection<E> {

    ...

    default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter); // filter가 null이면 NullPointerException 발생
        boolean removed = false;
        final Iterator<E> each = iterator();
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }

    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }
}
```

<br>

#### Comparator

- default Comparator<T> reversed() : 비교 순서를 반대로 바꿈
- default Comparator<T> thenComparing(Comparator<? super T> other) : 비교 후 추가로 비교할 때 사용
  - @param 추가로 비교할 비교자
- static <T extends Comparable<? super T>> Comparator<T> reverseOrder() / naturalOrder() : 역순 비교자 / 기본 비교자를 반환
- static <T> Comparator<T> nullsFirst(Comparator<? super T> comparator) : 비교할 요소 중 null이 있다면 null을 앞으로 정렬함
- static <T> Comparator<T> nullsLast(Comparator<? super T> comparator) : 비교할 요소 중 null이 있다면 null을 뒤로 정렬함

<br>

___

**[참고]**  
백기선, inflearn 강의 ['더 자바, Java 8'](https://www.inflearn.com/course/the-java-java8/dashboard)

[<== section 1](/the-java-8-section-1) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [section 3 ==>](/the-java-8-section-3)