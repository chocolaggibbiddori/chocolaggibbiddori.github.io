---
layout: single
title: "부트캠프 14일차"
tag: [bootcamp, java web]
---

<br>

# 자바 웹

## Mac에서 톰캣 설치

&nbsp;&nbsp; 웹을 배우기에 앞서 먼저 톰캣 서버를 설치했다. 나는 9버전을 설치하였다.

### 다운로드

> [https://tomcat.apache.org/download-90.cgi](https://tomcat.apache.org/download-90.cgi)

1. 왼쪽 Download에서 원하는 버전을 선택한다.
2. 아래 Core: 에서 tar.gz를 다운로드 받는다.
3. 다운로드 받은 파일을 원하는 경로에 압축 해제!

### 이클립스에서 톰캣 연동하기

1. Dynamic Web Project 선택 후 next <br>
   ![Create Dynamic Web Project](/images/day14/Dynamic Web Project.png)
2. Project name 입력 후 New Runtime... 클릭 <br>
   ![Project Name](/images/day14/Project Name.png)
3. Tomcat 지정 후 next <br>
   ![New Server Runtime Environment](/images/day14/Runtime Environment.png)
4. 다운로드 받았던 Tomcat 파일 경로 지정 <br>
   ![Select Tomcat Path](/images/day14/Tomcat Path.png)
5. next 하다가 web.xml 생성 체크 <br>
   ![Generate web.xml](/images/day14/Generate web(xml).png)
6. 그럼 다음과 같이 프로젝트가 생성된다. <br>
   ![Create Project](/images/day14/Create Project.png)
   ![web.xml](/images/day14/web(xml).png)

<br>

## 웹 용어

### 프로토콜

&nbsp;&nbsp; 프로토콜이란, 컴퓨터 사이에 데이터 교환을 위한 통신 규약이다. 프로토콜의 종류로는,

- http(hypertext transfer protocol)
- https(hypertext transfer protocol over secure sockets layer) - http에서 보안 강화
- ftp - 파일 데이터를 다룸

&nbsp;&nbsp; 위 세 가지 프로토콜은 기본적으로 TCP/IP 통신을 사용한다. 사내 인트라넷 같은 경우 UDP 통신을 사용한다.

### 도메인

&nbsp;&nbsp; 인터넷은 IP 주소를 가지고 통신을 하는데, 이는 사람이 읽고 쓰기 어렵다.
따라서, 이를 사람이 쉽게 다룰 수 있게 해주는 것이 DNS(Domain Name System)이다.

&nbsp;&nbsp; DNS는 도메인을 IP로 바꿔주는 역할을 한다. 웹은 www로 시작하고, 모바일은 m으로 시작한다.  
`localhost -> DNS -> (127, 0, 0, 1)`

<br>

## Java

- Servlet : HttpServlet.class를 상속 받아서 정의한 클래스. http 요청을 처리한다.
- JSP(Java Server Pages) : 웹에서 자바를 편리하게 사용하기 위한 기술. .jsp 파일로 웹 페이지를 생성한다.
- 쿠키 : 브라우저에서 데이터를 임시로 저장하는 것
- 세션 : 서버에서 데이터를 임시로 저장하는 것

<br>

## html

- form : 데이터 전달을 위한 태그
  * action : form 데이터를 전송할 경로를 지정
  * method : http 메서드 형식을 지정
- input : 입력 값을 받을 수 있도록 만들어 줌
  * placeholder : 입력값을 제한
  * required : 필수값으로 지정
  * readonly : 읽기 전용
  * type : 입력 타입을 지정
    + button : 누름 버튼
    + text : 텍스트 입력란
    + email : 이메일 입력란
    + url : url 입력란
    + checkbox : 체크 박스(중복 가능)
    + radio : 라디오 박스(중복 불가)
    + date : 날짜 입력란
    + datetime : 날짜 및 시간 입력란(크롬에서는 아직 지원 X)
    + datetime-local : 날짜 및 시간 입력란
    + password : 입력해도 보이지 않는 텍스트 입력란
    + textarea : 글 작성란
    + color : 색 입력란
    + hidden : 보이지 않는 입력란
    + select : 선택란
      + size : 보이는 옵션의 갯수
      + multiple : 다중 선택 가능
      - option : 선택 가능한 옵션
    + reset : 모든 입력 데이터 취소
    + submit : 데이터 전송

<br>

## 톰캣 실행 과정

&nbsp;&nbsp; 톰캣 서버는 webapp 하위에 있는 web.xml 파일을 읽어서 첫 화면을 보여준다.
webapp/index.html을 만든 후 아래 링크로 들어가면 index.html로 들어간다.  
`http://localhost:8080/join`

- localhost : Tomcat Server
- 8080 : Gate
- join : Project name

&nbsp;&nbsp; 프로젝트에서 서버를 실행하면 도메인이 다음과 같이 프로젝트 이름까지만 나온다. 기본적으로 index.html 파일을 찾아서 띄워주기 때문에 문제가 없다.  
`http://localhost:8080/join`

&nbsp;&nbsp; index.html 파일 내에서 서버를 실행하면 도메인이 다음과 같이 나온다.  
`http://localhost:8080/join/index.html`

&nbsp;&nbsp; form 데이터 입력 후 전송을 누르게 되면, 다음과 같은 경로를 호출한다.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>로그인 화면</title>
</head>

<body>
<form action="log">
  <label>아이디</label><input type="text" name="id" required/><br>
  <label>비밀번호</label><input type="password" name="pw" required/><br>
  <input type="submit" value="전송"/>
  <input type="reset" value="취소"/>
</form>
</body>
</html>
```

`http://localhost:8080/log?id=didrkd684&pw=1234`

- log : action value
- ? : 주소과 파라미터를 구분
- & : 파라미터와 파라미터를 구분

> 주의!  
> action value가 /log일 경우 주소가 localhost:8080/join/log가 아니라 localhost:8080/log가 되어 경로를 찾을 수 없게 되어버린다.
항상 /를 빼줄것!

- get 방식은 url에 form 데이터를 노출시킨다. form 태그에서 method 생략 시 자동으로 get 방식으로 호출한다.  
또한, get 방식은 한글이 깨지지 않는다.
- post 방식은 form 데이터를 url에 노출시키지 않는다.  
post 방식은 한글이 깨진다! request.setCharacterEncoding("UTF-8")을 호출해주면 한글이 깨지지 않게 된다.

<br>

## jsp

1. <% 자바 코드 %>
2. <%= 단일 연산 또는 변수값 출력 %>
3. <%@ 지시자 %>  
지시자 : page
   - 현 JSP 페이지에 대산 전체적인 설정  
   `<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>`
   - 현 JSP 페이지에 외부 클래스 import)  
   `<%@ page import="java.util.List, java.util.ArrayList" %>`

---
[<== 부트캠프 11일차](/bootcamp-miniproject-kktalk) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 15일차 ==>](/bootcamp-day15)