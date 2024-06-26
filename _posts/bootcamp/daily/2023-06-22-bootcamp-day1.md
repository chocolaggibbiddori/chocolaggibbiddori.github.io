---
layout: single
title: "부트캠프 1일차"
tag: [bootcamp, java]
---

<br>

## 패키지

&nbsp;&nbsp; 패키지란, 쉽게 얘기해서 자바 파일의 경로를 뜻한다. '폴더'의 개념과 같다고 봐도 될 것 같다. 모든 자바 파일은 최상위에 다음과 같이 패키지 경로를 입력한다.

`package com.chocola.animal;`

&nbsp;&nbsp; 자바 프로젝트는 src 폴더에서 시작하는데, 이 파일은 src 폴더 하위에 있는 com 패키지 -> chocola 패키지 -> animal 패키지에 있다는 말이 된다. 패키지의 이름은 항상
소문자이며 도메인의 역순으로 적는다.

ex) 네이버 웹툰의 도메인인 comic.naver.com의 패키지는 com.naver.comic이 된다.

> **패키지 작성 규칙**

1. 숫자로 시작해서는 안 되고 _, $를 제외한 특수문자를 사용해서는 안 된다.
2. java로 시작하는 패키지는 자바 표준에서만 사용하므로 사용해서는 안 된다.
3. 모두 소문자로 작성하는 것이 관례이다.

<br>

## 변수

&nbsp;&nbsp; 컴퓨터의 데이터들은 메모리에 저장된다. 이때 메모리에 데이터를 저장하고 메모리로부터 데이터를 읽어오기 위해서는 데이터가 저장되어 있는 메모리 번지를 알아야 하는데, 이것을 사람이 직접
다루기에는 아주 큰 어려움이 따른다!

&nbsp;&nbsp; 때문에 자바에서는 '변수'라는 개념을 사용하는데, 다루기 어려운 메모리 번지에 이름을 붙여 사람이 다루기 보다 쉽게 해주는 것이다. 모든 데이터는 변수를 통해 저장되고 읽을 수 있다.

&nbsp;&nbsp; 자바는 변수마다 타입을 지정해주어야 한다! 지정된 타입 외의 값을 저장하려고 하면 컴파일러가 오류를 일으킨다. 또한, 하나의 변수에는 하나의 데이터만 저장할 수 있다.

### 변수 선언

&nbsp;&nbsp; 변수를 선언할 때는 타입과 변수명을 차례로 선언해주면 된다.

```java
int age;
double height;
```

여기에서 int는 정수형, double은 실수형이라고 부른다. 정수형 변수에는 정수 데이터만, 실수형 변수에는 실수 데이터만 담을 수 있다.

&nbsp;&nbsp; 같은 타입 끼리의 변수는 한 번에 선언할 수도 있다.

```java
int x,y,z;
```

&nbsp;&nbsp; 변수의 이름은 자바에서 정한 규칙을 따라야 한다. 아래 표는 변수명을 지을 때 참고할만한 것들을 정리해놓은 것이다.

| 작성 규칙                                            | 필수 여부 |
|:-------------------------------------------------|:-----:|
| 첫 번째 글자는 문자이거나 '$', '_'이어야 하고 숫자로 시작할 수 없습니다.    |  필수   |
| 영어 대소문자를 구분합니다.                                  |  필수   |
| 자바 예약어는 사용할 수 없습니다.                              |  필수   |
| 첫 문자는 영어 소문자로 시작하되, 다른 단어가 붙을 경우 첫 문자를 대문자로 합니다. |  관례   |
| 문자 수(길이)의 제한은 없습니다.                              |       |

&nbsp;&nbsp; 또한, **변수명은 누구나 쉽게 알아볼 수 있도록 의미 있는 이름을 지어주는 것이 좋다!** 변수명의 길이는 프로그램의 실행과 무관하기 때문에 길이에 구애받지 않고 적절한 이름을 짓는 것이
후에 도움이 될 때가 많다.

### 대입

&nbsp;&nbsp; 변수에 값을 저장할 때는 대입 연산자(=)를 사용한다. 자바에서 등호(=)는 오른쪽의 값을 왼쪽 변수에 대입한다는 의미로 사용된다.

```java
int x;
x = 5; // 변수 선언 후 대입

int y = 10; // 변수 선언과 동시에 대입

int z = x; // x에 저장된 값 5를 변수 z에 대입
```

&nbsp;&nbsp; 변수에 최초로 값이 저장되는 것을 '초기화'라고 한다. 초기화가 되지 않는 변수를 사용하면 컴파일 에러가 발생한다.

### 변수의 사용 범위

&nbsp;&nbsp; 자바에서 모든 변수는 중괄호 {} 블록 내에서 선언되고 사용된다. 중괄호는 여러번 겹칠 수 있는데, 상위 중괄호에서 선언된 변수는 하위 중괄호에서 사용할 수 있으나 그 반대는 성립되지 않는다.
즉, 변수를 사용하는 위치를 잘 따져서 변수 선언 위치를 선정해야 한다.

&nbsp;&nbsp; 메서드 내에 선언된 변수는 지역 변수(Local Variable), 클래스 내에 선언된 변수는 전역 변수(Global Variable)이라고 부른다.

```java
public class Scope {
    int x = 5; // 전역 변수

    public static void main(String[] args) {
        int a = 10; // 로컬 변수
        int b = 20;

        if (...){
            int sum = a + b; // 상위 변수 사용 가능
        }

        System.out.println(sum); // 컴파일 에러 : 변수 sum은 if 블록 내에 선언된 변수이므로 블록 외에서는 사용할 수 없다.
    }
}
```

<br>

## 기본 타입

&nbsp;&nbsp; 자바에서는 다음과 같이 정수, 실수, 논리값을 저장할 수 있는 기본 타입(primitive type)을 제공한다.

| 값  |             타입(메모리 사용 크기 byte)              |
|:--:|:-------------------------------------------:|
| 정수 | byte(1), char(2), short(2), int(4), long(8) |
| 실수 |             float(4), double(8)             |
| 논리 |                 boolean(1)                  |

<br>

## 타입 변환

### 자동 타입 변환

&nbsp;&nbsp; 말 그대로 타입 변환이 묵시적으로 일어난다. 기본 타입에서 자동 타입 변환은 큰 범위의 타입에 작은 범위의 타입이 저장될 때 발생한다.
보기 쉽게 정리하면 다음과 같다.

`byte < short < int < long < float < double`

&nbsp;&nbsp; char 타입의 값이 int 이상의 타입에 저장되면, 해당 값의 유니코드값이 저장된다.

```java
char a = '5';
int b = a; // 변수 b에는 53이 저장됨
```

#### 정수 연산에서의 자동 타입 변환

&nbsp;&nbsp; 정수 연산에서 byte, short 타입의 변수는 자동으로 int 타입으로 변환된다.
정확하게는, byte 혹은 short 타입의 변수가 산술 연산의 피연산자로 사용될 경우 해당 변수는 int 타입으로 자동 변환된다.
따라서, 다음 코드는 컴파일 에러를 일으킨다.

```java
byte x = 10;
byte y = 20;
byte result1 = x + y; // 컴파일 에러
int result2 = x + y; // 정상 동작
```

&nbsp;&nbsp; 때문에 정수 연산에 사용되는 변수는 int 타입으로 선언하는 것이 좋다. 다만, 변수 중 하나라도 long 타입이 있다면 모두 long 타입으로 변환되니 주의하자.

다음과 같이 변수를 사용하지 않는 연산이라면 자동 타입 변환이 일어나지 않는다.

```java
byte result = 10 + 20;
```

&nbsp;&nbsp; 위 연산은 자바 컴파일러가 미리 10 + 20을 연산한 후 그 값을 result 변수에 저장하기 때문에 int로 타입 변환을 하지 않는다!

#### 실수 연산에서의 자동 타입 변환

&nbsp;&nbsp; 두 피연산자가 같은 타입이라면 해당 타입으로 연산되지만, 하나라도 double 타입이라면 모두 double 타입으로 자동 변환된다.

#### + 연산에서의 문자열 자동 타입 변환

&nbsp;&nbsp; + 연산 중에 피연산자가 하나라도 문자열 타입이라면, 모두 문자열 타입으로 자동 변환된다.
자바에서 덧셈 연산은 앞에서부터 순차적으로 연산되므로, 변수의 위치에 따라 최종 결과가 달라질 수 있다.

```java
String str1 = 1 + 2 + "3"; // 1 + 2 = 3이 먼저 수행되어 str에는 "33"이 저장됨
String str2 = "1" + 2 + 3; // "1" + 2 = "12"가 먼저 수행되어 str에는 "123"이 저장됨
```

### 강제 타입 변환

&nbsp;&nbsp; 크기가 큰 타입을 작은 타입으로 강제 변환할 수 있다. 다만, 강제로 변환된 값은 제 값의 일부가 소실될 수 있다는 점을 유의하자.
강제 타입 변환에는 괄호 ()를 사용한다.

```java
int intValue = 10;
byte byteValue = (byte) intValue;
```

```java
int intValue = 65;
char charValue = (char) intValue; // 문자열로 출력하기 위해 char 타입으로 강제 변환
System.out.println(intValue); // 65
System.out.println(charValue); // "A"
```

```java
double doubleValue = 3.14;
int intValue = (int) doubleValue; // 소수점 이하 절상
System.out.println(intValue); // 3
```

#### 문자열을 기본 타입으로 강제 타입 변환

&nbsp;&nbsp; 문자열을 기본 타입으로 변환해주는 방법은 후술하는 메서드를 사용하는 것이다.

|       변환 타입       |                     사용 예                      |
|:-----------------:|:---------------------------------------------:|
|  String -> byte   |      byte value = Byte.parseByte("10");       |
|  String -> short  |    short value = Short.parseShort("100");     |
|   String -> int   |     int value = Integer.parseInt("1234");     |
|  String -> long   |     long value = Long.parseLong("12345");     |
|  String -> float  |    float value = Float.parseFloat("3.14");    |
| String -> double  | double value = Double.parseDouble("3.1415");  |
| String -> boolean | boolean value = Boolean.parseBoolean("true"); |

<br>

## 연산자와 연산식

### 연산자의 종류

| 연산자의 종류 | 연산자                              | 피연산자 수 |   산출값   |
|:-------:|:---------------------------------|:------:|:-------:|
|   산술    | +, -, *, /, %                    |   이항   |   숫자    |
|   부호    | +, -                             |   단항   |   숫자    |
|   문자열   | +                                |   이항   |   문자열   |
|   대입    | =, +=, -=, *=, /=, %=            |   이항   |   다양    |
|   증감    | ++, --                           |   단항   |   숫자    |
|   비교    | ==, !=, >, >=, <, <=, instanceof |   이항   | boolean |
|   논리    | !, &, \|, &&, \|\|               | 단항, 이항 | boolean |
|   조건    | (조건식) ? A : B                    |   삼항   |   다양    |

### 연산의 방향과 우선순위

1. 산술 연산식에서는 곱셈, 나눗셈이 우선 처리
2. 단항, 이항, 삼항 연산자 순으로 우선 처리
3. 산술, 비교, 논리, 대입 연산자 순으로 우선 처리
4. 단항, 부호, 대입 연산자를 제외한 모든 연산의 방향은 왼쪽에서 오른쪽
5. 괄호 ()를 사용하여 연산의 우선순위를 정할 수 있음

---
[<== 부트캠프 0일차](../bootcamp-day0)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 2일차 ==>](../bootcamp-day2)