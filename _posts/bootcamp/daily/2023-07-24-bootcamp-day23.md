---
layout: single
title: "부트캠프 23일차"
tag: [bootcamp, servlet, cookie]
---

# Servlet

<br>

## 서블릿 실행 순서

&nbsp;&nbsp; 클라이언트가 서블릿을 호출할 경우 서블릿은 다음과 같은 순서로 호출된다.

1. init() - 서블릿 초기화 메서드. 가장 먼저 호출된다.
2. service() - 실제 로직을 작성하는 메서드. public 메서드와 protected 메서드가 있다.
   - public void service(ServletRequest req, ServletResponse res)  
     - 클라이언트의 request를 protected void service()로 전달
     - HttpServletRequest로 타입 캐스팅이 실패할 경우 ServletException을 발생시킴
   - protected void service(HttpServletRequest req, HttpServletResponse resp)
     - 실제 로직이 작성되는 메서드
     - 직접 구현하지 않으면 method 방식에 따라 doGet(), doPost(), doDelete(), ... 를 자동으로 호출한다.
3. destroy() - 서블릿 소멸 메서드.

<br>

## 쿠키

- 쿠키 추가하기
  
&nbsp;&nbsp; 브라우저에 쿠키를 설정해 줄 수 있다. 브라우저에 내보내는 동작이므로 HttpServletResponse 객체가 담당한다.

```java
Cookie cookie1 = new Cookie("ID", "user");
Cookie cookie2 = new Cookie("PW", "1234");

response.addCookie(cookie1);
response.addCookie(cookie2);
```

&nbsp;&nbsp; 다음과 같이 브라우저에서 쿠키가 잘 설정된 것을 확인할 수 있다.

![addCookie](./../images/day23/쿠키%20추가.png)

- 쿠키 가져오기

&nbsp;&nbsp; 브라우저에 저장된 쿠키를 가져올 수 있다. 브라우저에서 가져오는 동작이므로 HttpServletRequest 객체가 담당한다.

```java
Cookie[] cookies = req.getCookies();
for (Cookie cookie : cookies) {
    System.out.println("cookie name = " + cookie.getName());
    System.out.println("cookie value = " + cookie.getValue());
}
```

&nbsp;&nbsp; 아까 설정했던 쿠키가 출력되는 것을 확인할 수 있다.

![getCookies](./../images/day23/쿠키%20가져오기.png)

<br>

## form 태그 내 파라미터 다루기

- getParameter(String name) : 이름에 해당하는 값을 String으로 반환한다.
- getParameterValues(String name) : 이름에 해당하는 값이 여러 개일 경우(ex. checkbox) 그 값들을 String[]에 담아 반환한다.
- getParameterNames() : 파라미터의 이름을 모를 경우, 넘어오는 데이터의 파라미터 이름을 Enumeration<String>에 담아서 반환한다.

---
[<== 부트캠프 19일차](/bootcamp-miniproject2-best_board) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 24일차 ==>](/bootcamp-day24)