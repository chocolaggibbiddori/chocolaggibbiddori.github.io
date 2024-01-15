---
layout: single
title: "부트캠프 17일차"
tag: [bootcamp, java web, jsp]
---

# Java Web : JSP

- 정적 사이트 : 데이터가 변함이 없음
- 동적 사이트 : 사용자에 따라 다른 결과를 보여주는 사이트

<br>

### Tomcat 흐름도

> error.jsp => Tomcat => error_jsp.java(서블릿) => error_jsp.class => 서블릿 객체 실행 => 결과

&nbsp;&nbsp; jsp 파일이 Tomcat에 들어가면 Tomcat이 해당 파일을 서블릿으로 만들고 컴파일한 후에 사용한다.
서블릿 클래스 파일 (error_jsp.class)은

1. 최초 요청이 있을 경우
2. 기존 코드가 수정된 경우

에 만들어진다.

### 주석

- html : \<!-- -->
- html + jsp : <%-- --%>
- jsp : 자바와 동일
- css : /* */
- xml : \<!-- -->

### java web 데이터 전달 방식

- html --> jsp, servlet

  1. \<form>의 action
  2. 이벤트 발생 시 : onclick="location.href='경로'"
  3. 이벤트 발생 시 : onclick="js 함수"

  ```
  <script>
    function 함수명() {
      document.location="경로";
    }
  </script>
  ```

- jsp -> jsp, servlet

1. request에게 param 값을 전달
   - \<form> 이용
   - request.setParameter("이름", 데이터);

2. request에게 attribute 값을 전달
   - request.setAttribute("이름", 데이터);
   - request.getRequestDispatcher("대상").forward(request, response);
   - request.getRequestDispatcher("대상").include(request, response);

3. response.sendRedirect("경로");

### 지시어(Directive)

- `<%@ %>` 안에서 지시어 사용

  ```
  <%@ page language="java" contentType="text/html; charset=utf-8" %>
  <%@ page import="java.util.*" %>
  ```

- 디렉티브 종류
  1. page : 현재 JSP 페이지를 처리하기 위한 속성 지정
  2. taglib : 현재 JSP 페이지에서 사용할 커스텀 태그 라이브러리를 지정
  3. include : 현재 JSP 파일에 다른 JSP나 HTML 문서를 포함시킴

### 액션

- JSP 페이지 간 흐름 제어 : 어떤 동작이나 액션이 일어나는 시점에서 페이지 사이의 이동에 관련된 제어
- 자바 빈즈 컴포넌트와 상호작용 지원

|    액션 태그    | 설명                     |
|:-----------:|:-----------------------|
|   include   | 다른 페이지를 현재 페이지에 포함     |
|   forward   | 현재 페이지의 제어를 다른 페이지에 전달 |
|   plug-in   | 자바 애플릿 사용              |
|   useBean   | 자바 빈 객체 생성 시 사용        |
| setProperty | 자바 빈의 속성 값 저장          |
| getProperty | 자바 빈의 속성 값 리턴          |

---
[<== 부트캠프 16일차](/bootcamp-day16) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 18일차 ==>](/bootcamp-day18)