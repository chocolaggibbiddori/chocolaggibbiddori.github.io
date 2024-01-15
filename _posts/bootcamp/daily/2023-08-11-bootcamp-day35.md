---
layout: single
title: "부트캠프 35일차"
tag: [bootcamp, mvc, spring framework]
---

<br>

# Spring 프레임워크

&nbsp;&nbsp; 스프링 프레임워크(이하 스프링)는 기존에 배웠던 Servlet 개념을 더 편리하게 사용 할 수 있도록 여러 기능을 제공한다.
스프링은 컨테이너에서 직접 Servlet을 관리해주는데 이를 위하여 여러 개념이 도입된다.

<br>

## DispatcherServlet

&nbsp;&nbsp; 스프링은 모든 요청이 하나의 서블릿으로 들어오도록 구현되어 있다.
이는 요청 처리의 중복을 해결하고자 함이며, 스프링은 이 서블릿을 DispatcherServlet이라고 명명했다.

&nbsp;&nbsp; DispatcherServlet은 스프링 MVC 패턴의 핵심이며, 그 핵심 기능은 다음과 같다.

- HandlerMapping
- ModelAndView
- ViewResolver

DispatcherServlet은 요청에 대응하는 핸들러를 실행한 후 그 결과를 렌더링하여 클라이언트에게 응답해준다.

<br>

### HandlerMapping

1. DispatcherServlet은 가장 먼저 요청 URL에 해당하는 핸들러(컨트롤러)를 찾는다. 핸들러를 찾지 못하면 NOT FOUND 에러(404 에러)를 발생시킨다.
2. 해당 핸들러를 처리할 수 있는 핸들러 어댑터를 조회해서 핸들러를 실행시킨다.

<br>

### ModelAndView

1. 핸들러 어댑터는 핸들러 처리 결과를 ModelAndView로 변환해준다. 이로써 어떤 핸들러든지 항상 ModelAndView를 반환하도록 할 수 있다.
2. ModelAndView에는 사용할 데이터들과 View 이름이 있다.

<br>

### ViewResolver

1. ViewResolver가 ModelAndView에 담겨 있는 논리 경로를 실제 물리 경로로 바꿔준다.
2. 실제 뷰의 렌더링을 담당하는 View 객체를 반환한다.

<br>

## IoC(Inversion of Control)

&nbsp;&nbsp; 기존에는 객체 내에서 직접 필요한 객체를 생성하고 사용했었다.
그러나, 스프링에서는 스프링 컨테이너가 객체의 생성과 소멸을 담당하기 때문에 객체는 온전히 자신의 구현 로직만 담당할 수 있게 된다.
이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다.

### DI(Dependency Injection)

&nbsp;&nbsp; 스프링에서는 IoC를 구현하기 위하여 의존관게 주입(DI)이라는 기술을 사용한다.
객체를 생성하고 빈으로 저장하며 필요한 곳에 적절하게 해당 객체를 주입해준다. 덕분에 해당 객체를 사용하는 클래스에서는 어떤 객체가 주입 될지 알 수 없으며, 다른 구체적인 클래스에 의존하지 않고 개발할 수 있게 된다.

&nbsp;&nbsp; 의존관계 주입은 크게 4가지 방식이 있다.

- 생성자 주입
- 수정자 주입(Setter)
- 필드 주입
- 일반 메서드 주입(잘 사용하지 않는다!)

---
[<== 부트캠프 미니 프로젝트 3](/bootcamp-miniproject3-shopping_mall) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 40일차 ==>](/bootcamp-day40)