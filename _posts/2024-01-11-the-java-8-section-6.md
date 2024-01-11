---
layout: single
title: "자바 8(CompletableFuture)"
tag: [inflearn, java]
---

# 자바 8 주요 기능 6

## 자바 Concurrent 프로그래밍 소개

### Concurrent 소프트웨어
- 동시에 여러 작업을 할 수 있는 소프트웨어
  - 웹 브라우저로 유튜브를 보면서 키보드로 문서에 타이핑을 할 수 있다.
  - 녹화를 하면서 인텔리J로 코딩을 하고 워드에 적어둔 문서를 보거나 수정할 수 있다.
- 자바에서 지원하는 concurrent 프로그래밍
  - 멀티프로세싱 (ProcessBuilder)
  - 멀티쓰레드
- 자바 멀티쓰레드 프로그래밍
  - Thread 상속
  - Runnable 구현

### 쓰레드 주요 기능
- 현재 쓰레드 멈춰두기 (sleep): 다른 쓰레드가 처리할 수 있도록 기회를 주지만 그렇다고 락을 놔주진 않는다. (잘못하면 데드락 걸릴 수 있음)
- 다른 쓰레드 깨우기 (interrupt): 다른 쓰레드를 깨워서 InterruptedException을 발생 시킨다. 그 에러가 발생했을 때 할 일은 코딩하기 나름. 종료 시킬 수도 있고 계속 하던 일 할 수도 있고
- 다른 쓰레드 기다리기 (join): 다른 쓰레드가 끝날 때까지 기다린다.

### 문제점
- 너무 복잡하다
- 쓰레드가 많아질수록 복잡해지고 직접 코드로 쓰레드를 제어하는 것은 불가능

<br>

## Executors

&nbsp;&nbsp; 위의 저수준의 API를 직접 사용하는 것은 어렵고 복잡하기 때문에 이를 다루기 쉽게 나온 고수준의 API가 바로 `Executor`

### 주요 인터페이스
- Executor: `execute(Runnable)`
- ExecutorService: `Executor` 인터페이스를 상속 받은 인터페이스
- ScheduledExecutorService: `ExecutorService` 인터페이스를 상속 받은 인터페이스. 특정 시간 이후 또는 주기적인 작업을 수행할 수 있음

### ExecutorService
- execute, submit: 쓰레드 실행
- shutdown: 쓰레드 멈추기. 진행하던 작업이 모두 종료되면 쓰레드를 멈춤
- shutdownNow: 쓰레드 곧바로 멈추기

<br>

## Callable과 Future

### Callable

&nbsp;&nbsp; `Callable` 인터페이스는 `Runnable` 인터페이스와 거의 동일하다.
`Runnable` 인터페이스와의 차이점은 `Runnable` 인터페이스의 `run()` 메서드는 반환 타입이 없는 반면, `Callable` 인터페이스의 `call()` 메서드는 반환 타입이 있다는 것이다.

&nbsp;&nbsp; `Callable` 인터페이스는 다음과 같이 간단하게 정의되어 있다.

```java
package java.util.concurrent;

@FunctionalInterface
public interface Callable<V> {

    V call() throws Exception;
}
```

### Future

&nbsp;&nbsp; `ExecutorService` 인터페이스에는 `Future` 타입 인터페이스를 반환하는 메서드가 있다.
쓰레드를 실행한 후 반환 받는 `Future` 인터페이스를 이용해 추가적인 작업을 진행할 수 있다.

#### Method
- get(): 결과를 반환 받는다. blocking call
- isDone(): 작업이 끝나면 `true`, 그렇지 않으면 `false`를 반환
- cancel(boolean): 매개변수로 `true`를 넣어주면 interrupt, `false`를 넣어주면 기다림. 일단 `cancel()` 메서드를 호출하면 `get()`으로 가져올 수 없음. 작업을 끝내는 것이므로 `isDone()` 메서드도 `true`를 반환함

<br>

## CompletableFuture 1
&nbsp;&nbsp; 자바에서 비동기(Asynchronous) 프로그래밍을 가능케하는 인터페이스.
`Future` 인터페이스를 사용해도 어느정도 가능했지만 하기 힘든 일들이 많았다.

### `Future` 인터페이스로는 하기 어려웠던 작업들
- `Future`를 외부에서 완료 시킬 수 없다. 취소하거나, `get()`에 타임아웃을 설정할 수는 있다.
- 블로킹 코드(ex. `get()`)를 사용하지 않고서는 작업이 끝났을 때 콜백을 실행할 수 없다.
- 여러 `Future`를 조합할 수 없다, ex) Event 정보 가져온 다음 Event에 참석하는 회원 목록 가져오기
- 예외 처리용 API를 제공하지 않는다.

### 비동기로 작업 실행하기
- 리턴값이 없는 경우: runAsync()
- 리턴값이 있는 경우: supplyAsync()

### 콜백 제공하기
- thenApply(Function): 리턴값을 받아서 다른 값으로 바꾸는 콜백
- thenAccept(Consumer): 리턴값으로 또 다른 작업을 처리하는 콜백 (리턴없이)
- thenRun(Runnable): 리턴값을 사용하지 않고 다른 작업을 처리하는 콜백
- 콜백 자체를 또 다른 쓰레드에서 실행할 수 있다.
  - `thenApplyAsync()` 등

<br>

## CompletableFuture 2

### 조합하기
- thenCompose(): 두 작업을 서로 이어서 실행하도록 조합
- thenCombine(): 두 작업을 독립적으로 실행하고 둘 다 종료 했을 때 콜백 실행
- allOf(): 여러 작업을 모두 실행하고 모든 작업 결과에 콜백 실행
- anyOf(): 여러 작업 중에 가장 빨리 끝난 하나의 결과에 콜백 실행

### 예외처리
- exceptionally(Function)
- handle(BiFunction)

___

**[참고]**  
백기선, inflearn 강의 ['더 자바, Java 8'](https://www.inflearn.com/course/the-java-java8/dashboard)  

[<== section 5](/the-java-8-section-5)