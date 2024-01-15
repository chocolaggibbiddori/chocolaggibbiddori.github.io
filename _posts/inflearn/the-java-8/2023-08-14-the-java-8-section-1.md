---
layout: single
title: "자바 8(함수형 인터페이스와 람다)"
tag: [inflearn, java]
---

<br>

# 자바 8 주요 기능 1

## 함수형 인터페이스와 람다 소개

### 함수형 인터페이스란?

&nbsp;&nbsp; 추상 메서드를 딱 하나만 가지고 있는 인터페이스이다. 버전이 업그레이드 되면서 인터페이스에 static 메서드와 default 메서드를 선언할 수 있게 되었는데 static 메서드와 default 메서드에 상관없이 추상 메서드가 하나만 존재한다면 함수형 인터페이스라고 부를 수 있다.

&nbsp;&nbsp; 또한, 함수형 인터페이스는 자바에서 제공하는 어노테이션 @FunctionalInterface를 다음과 같이 인터페이스 선언부에 작성할 수 있다.

```java
@FunctionalInterface
public interface PrintMessage {
  
    void printToConsole();
}
```

### 람다 표현식

&nbsp;&nbsp; 람다 표현식은 함수형 인터페이스를 조금 더 간결하게 사용하기 위한 자바 문법이다. 기존에는 익명 클래스를 사용할 때 아래와 같이 사용했었는데,

```java
PrintMessage pm = new PrintMessage() {

    @Override
    public void printToConsole() {
        System.out.println("Hello Lambda!");
    }
};
```

&nbsp;&nbsp; 람다 표현식을 사용하면 위의 코드를 다음과 같이 변경할 수 있다.

```java
PrintMessage pm = () -> System.out.println("Hello Lambda!");
```

&nbsp;&nbsp; 물론 람다 표현식도 매개변수나 리턴 타입을 가질 수 있다.

```java
@FunctionalInterface
interface Add {

    int add(int a, int b);
}

public class Main {
    public static void main(String[] args) {
        Add addByLambDa = (a, b) -> a + b;

        int result = addLambDa.add(3, 5);
        System.out.println("result = " + result);
    }
}
```

`result = 8`

&nbsp;&nbsp; 즉, 람다 표현식을 사용하면 코드를 더 간결하게 줄일 수 있다는 장점이 있다.

- 주의할 점

1. 같은 입력일 때는 항상 같은 출력이 나와야 한다.
2. 함수 밖에 있는 값은 사용하지 말자.
3. 함수 밖에 있는 값을 변경하지 말자.

<br>

## 자바에서 제공하는 함수형 인터페이스

&nbsp;&nbsp; 자바에서는 기본적으로 많이 사용될 만한 함수형 인터페이스를 미리 만들어놓았다.

1. Function<T, R>

   - 선언부

   ```java
   @FunctionalInterface
   public interface Function<T, R> {

       R apply(T t);
   }
   ```

   - 함수 조합용 메서드

      1. default <V> Function<V, R> compose(Function<? super V, ? extends T> before) : 이 함수 이전에 실행할 함수를 매개변수로 넣어준다.
      2. default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) : 이 함수 이후에 실행할 함수를 매개변수로 넣어준다.

2. BiFunction<T, U, R>

   - 선언부

   ```java
   @FunctionalInterface
   public interface BiFunction<T, U, R> {

       R apply(T t, U u);
   }
   ```

3. Consumer<T>

   - 선언부

   ```java
   @FunctionalInterface
   public interface Consumer<T> {

       void accept(T t);
   }
   ```

   - 함수 조합용 메서드

      1. default Consumer<T> andThen(Consumer<? super T> after) : 이 함수 이후에 실행할 함수를 매개변수로 넣어준다.

4. Supplier<T>

   - 선언부

   ```java
   @FunctionalInterface
   public interface Supplier<T> {

       T get();
   }
   ```

5. Predicate<T>

   - 선언부

   ```java
   @FunctionalInterface
   public interface Predicate<T> {

       boolean test(T t);
   }
   ```

   - 함수 조합용 메서드

      1. default Predicate<T> negate() : 결과 반전
      2. default Predicate<T> and(Predicate<? super T> other) : 매개변수와의 and 조건
      3. default Predicate<T> or(Predicate<? super T> other) : 매개변수와의 or 조건

6. UnaryOperator<T>

   &nbsp;&nbsp; Function을 상속 받은 특수한 형태로, 입력과 출력이 모두 같은 타입이라면 Function을 이 인터페이스로 간단하게 사용할 수 있다.

   - 선언부

   ```java
   @FunctionalInterface
   public interface UnaryOperator<T> extends Function<T, T> {
   }
   ```

7. BinaryOperator<T>

   &nbsp;&nbsp; BiFunction을 상속 받은 특수한 형태로, 두 개의 입력과 출력이 모두 같은 타입이라면 BiFunction을 이 인터페이스로 간단하게 사용할 수 있다.

   - 선언부

   ```java
   @FunctionalInterface
   public interface BinaryOperator<T> extends BiFunction<T,T,T> {
   }
   ```

<br>

## 람다 표현식

### 람다로 표현하는 방법

1. 매개변수가 없는 경우 : () -> { 실행문; }

   ex)
   ```java
   Supplier<Integer> get10 = () -> { return 10; } // 실행문이 한 줄일 경우 중괄호와 return 생략 가능
   ```

2. 매개변수가 하나인 경우 : (매개변수 1) -> { 실행문; }

   ex)
   ```java
   Consumer<Integer> add10 : i -> i + 10; // 매개변수에 타입 생략 가능(명시할 수도 있다)
   ```

3. 매개변수가 둘인 경우 : (매개변수 1, 매개변수 2) -> { 실행문; }

   ex)
   ```java
   BinaryOperator<Integer> sum = (a, b) -> a + b;
   ```

<br>

### 변수 캡처

- 로컬 변수 캡처

   &nbsp;&nbsp; 람다는 final이나 effective final인 변수만 참조할 수 있다. 그렇지 않은 경우 컴파일 에러가 발생한다.

- effective final

   &nbsp;&nbsp; final 키워드가 없어도 초기화 이후 값이 변하지 않는 사실상 final인 변수

- 쉐도윙(shadowing)하지 않음

   &nbsp;&nbsp; 위의 두 특징은 내부 로컬 클래스, 익명 클래스와의 공통점이다. 람다가 내부 로컬 클래스, 익명 클래스와 차이가 있다면 쉐도윙을 하지 않는다는 것이다.
즉, 바깥 scope에서 선언된 변수와 이름이 같은 변수를 선언하거나 매개변수로 받아서 사용하지 못한다.  
&nbsp;&nbsp; 이는 내부 로컬 클래스, 익명 클래스는 자신만의 별도의 scope를 갖는 반면에, 람다는 별도의 scope를 가지지 않기 때문이다.
따라서, 람다 내부에서 상위 scope에서 사용한 변수와 같은 이름의 변수를 선언하거나 매개변수로 선언한다면 컴파일 에러가 발생한다.

<br>

## 메소드 레퍼런스

&nbsp;&nbsp; 람다가 하는 일이 생성자 호출이거나 기존에 사용하는 메서드와 동일하다면, 람다식을 좀 더 간결하게 표현할 수 있다.

- 스태틱 메서드 참조 - 타입::메서드명
- 인스턴스 메서드 참조 - 객체 레퍼런스::메서드명
- 생성자 참조 - 타입::new

ex)
```java
public class Coffee {
    
    String name;
    
    public Coffee() {
    }
    
    public Coffee(String name) {
        this.name = name;
    }
    
    public void make(String name) {
        System.out.println("make coffee : " + name);
    }
    
    public static String menu() {
        return "americano";
    }
}

public class Main {
    
    public static void main(String[] args) {
        Supplier<String> menu = Coffee::menu; // 스태틱 메서드 참조

        Coffee coffee = new Coffee();
        Consumer<String> make = coffee::make; // 인스턴스 메서드 참조
      
        Supplier<Coffee> newCoffee = Coffee::new;
        Function<String, Coffee> namedCoffee = Coffee::new; // 생성자 참조
    }
}
```

<br>

___

**[참고]**  
백기선, inflearn 강의 ['더 자바, Java 8'](https://www.inflearn.com/course/the-java-java8/dashboard)

[section 2 ==>](/the-java-8-section-2)