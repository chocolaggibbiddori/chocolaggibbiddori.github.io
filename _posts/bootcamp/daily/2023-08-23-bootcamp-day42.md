---
layout: single
title: "부트캠프 42일차"
tag: [bootcamp, spring framework, json]
---

<br>

# Spring Web

## Controller

&nbsp;&nbsp; 스프링은 웹 개발을 편리하게 할 수 있도록 지원해주는데, 특히 MVC 패턴을 잘 사용할 수 있도록 간편한 기능들을 제공한다.

### @Controller

&nbsp;&nbsp; 컨트롤러로 사용하고 싶은 클래스는 타입 레벨에 `@Controller`를 붙여주면 된다.
그럼 스프링이 자동으로 해당 컨트롤러를 빈으로 등록하며, 컨트롤러의 기능을 수행하는 클래스로 인식하게 된다.

1. `@Controller`는 `@Component`를 가지고 있어서 스프링이 ComponentScan으로 인식할 수 있다.
2. `@Controller`는 `String value()` 속성을 가지고 있는데, 해당 속성으로 컨트롤러에 이름을 부여할 수 있다.

### @RequestMapping

&nbsp;&nbsp; 컨트롤러의 경로를 매핑 해주는 어노테이션. 메서드 레벨에 사용한다.
이 어노테이션에 경로를 지정해주면, 해당 경로로 요청이 들어올 때 스프링이 알맞는 메서드를 실행해준다.

1. `String[] value()` 혹은 `String[] path()` 속성에 경로를 지정해줄 수 있다. 배열로 여러 개의 경로를 지정할 수도 있다.
2. `RequestMethod[] method()` 속성으로 HTTP Method를 설정할 수 있다. `RequestMethod.POST`로 설정하면, 해당 경로에 POST 방식으로 요청한 것을 매핑해준다. 역시 여러 개 지정할 수 있다.
3. 모든 메서드의 경로가 동일하게 시작된다면, 이 어노테이션을 타입 레벨에 붙여서 중복을 제거할 수 있다.

```java

@RequestMapping("/admin")
@Controller
public class AdminController {

    @RequestMapping("/add") //  "/admin/add" 로 매핑된다.
    public String add() {
        ...
    }

    @RequestMapping("/delete") //   "/admin/delete"로 매핑된다.
    public String delete() {
        ...
    }
}
```

### @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping

&nbsp;&nbsp; `@RequestMapping`을 좀 더 편리하게 사용할 수 있게 도와주는 어노테이션.
이름으로 알 수 있듯이 각각, Get, Post, Put, Delete, Patch 방식의 요청을 처리한다. 속성은 `@RequestMapping`과 동일하다.

```java
@RequestMapping(method = RequestMethod.GET) // @RequestMapping을 포함하고 있다.
public @interface GetMapping { ... }
```

### 파라미터

&nbsp;&nbsp; 스프링은 유용한 객체들을 파라미터로 자동으로 주입해준다.
또한, 요청 데이터를 받을 때 기존에는 HttpServletRequest 객체에서 값을 꺼내 사용했지만, 이를 직접 메서드의 파라미터로 주입받는 방법도 있다.

#### @PathVariable

&nbsp;&nbsp; 경로에 있는 값을 주입 받고 싶을 때 사용하는 파라미터 타입의 어노테이션. 해당 경로에 {}로 주입 받고자 하는 변수를 지정할 수 있다.

1. `value()` 혹은 `name()` 속성에 입력된 이름으로 변수를 찾는다.
2. 파라미터의 변수명과 일치하다면 생략 가능하다.

```java
@DeleteMapping("/{articleNo}")
public ResponseEntity<String> delArticle(@PathVariable Integer articleNo) { // 경로에 있는 articleNo 변수가 자동으로 주입된다.
    ...
}
```

#### @RequestParam

&nbsp;&nbsp; 클라이언트가 form 태그 등을 통해 보낸 데이터를 파라미터로 주입 받고 싶을 때 사용하는 어노테이션.

1. `value()` 혹은 `name()` 속성에 입력된 이름으로 변수를 찾는다.

```java
@GetMapping("/download")
public void download(@RequestParam("imageFileName") String imageFileName, HttpServletResponse response) {
    ...
}
```

#### 유용한 객체들

1. Model : Model 객체를 주입해준다. 뷰와 통신할 데이터들을 다룬다.
2. RedirectAttributes : 리다이렉트 시 통신할 데이터들을 다룬다.

### 반환 타입

1. ModelAndView

   &nbsp;&nbsp; `ModelAndView` 객체를 만들어 직접 반환할 수 있다. 반환 시 해당 객체를 렌더링하여 클라이언트에게 해당 뷰를 보여주게 된다.

2. String

   &nbsp;&nbsp; 뷰 이름을 직접 반환할 수 있다. 스프링의 View Resolver에 의해 실제 뷰의 경로로 변환되어 클라이언트에게 보여지게 된다.
   `ModelAndView` 객체와 달리 모델에 데이터를 넣어줄 수 없으므로 파라미터로 `Model` 객체를 받아서 따로 넣어주어야 한다.  
   `"redirect:/path"`로 시작하면 해당 경로(/path)로 리다이렉트한다.

## Service

### @Service

&nbsp;&nbsp; 스프링이 제공하는 서비스 계층에 사용하는 어노테이션. `@Component`가 붙어 있어 해당 객체는 스프링 빈으로 자동 등록된다.
별다른 기능은 없고 그냥 해당 객체가 Service 로직을 담당하고 있음을 나타낸다.

## Repository

### @Repository

&nbsp;&nbsp; 리포지토리로 설정하고 싶다면 `@Repository` 어노테이션을 달아주면 된다. 역시, `@Component`를 포함하고 있어 스프링이 해당 객체를 빈으로 등록해주며, 해당 객체를 리포지토리로 인식한다.

<br>

## JSON

&nbsp;&nbsp; 스프링은 Rest API를 지원한다. 따라서, Restful하게 응답을 할 수 있으며, 반환 값이 JSON 형태로 변환되어 응답된다.

### @ResponseBody

&nbsp;&nbsp; 해당 메서드가 Rest API를 처리하는 메서드임을 나타내는 어노테이션.
이 어노테이션이 있으면 반환 값이 뷰 리졸버를 거치지 않고 곧바로 응답 본문으로 작성된다.  
&nbsp;&nbsp; 이 어노테이션이 타입 레벨에 붙어있다면, 해당 클래스의 모든 메서드에 적용된다.

### @RestController

&nbsp;&nbsp; 해당 컨트롤러가 Rest API를 처리하는 컨트롤러임을 명시하는 어노테이션. `@Controller` + `@ResponseBody`라고 생각하면 된다.
실제로 `@RestController`는 두 어노테이션을 포함하고 있다.

### @ResponseStatus

&nbsp;&nbsp; 응답 코드를 지정하는 어노테이션. `value` 속성에 `HttpStatus` 인스턴스를 넣어서 설정한다.

### @RequestBody

&nbsp;&nbsp; 요청 데이터를 파라미터로 받을 때, 해당 데이터를 객체로 변환해준다.

<br>

___

[<== 부트캠프 40일차](/bootcamp-day40) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 46일차 ==>](/bootcamp-day46)