---
layout: single
title: "부트캠프 18일차"
tag: [bootcamp, java web, jsp]
---

# Java Web : JSP

## EL(Expression Language)

&nbsp;&nbsp; EL : JSP에서 변수를 보다 편리하게 사용하기 위한 표현법

### 사용 방법

- ${...}와 같이 사용

기존 JSP

```
<% int id = Integer.parseInt(request.getAttribute("id")); %>
<%= id %>
```

EL 적용

```
${param.id}
${param["id"]}
${empty param.id}
```

<br>

## JSTL(Jsp Standard Tag Library)

- jstl은 별도의 라이브러리가 필요하고, 반드시 선언이 필요
- WEB-INF/lib -> jstl.jar / standard.jar 파일 필요

### 사용 목적

- JSP는 HTML 내부 코드 가독성이 떨어짐
- 웹 디자이너, 퍼블리셔가 작업 시 실수로 JSP 관련 코드를 수정 또는 삭제하는 것을 방지

### jstl이 제공하는 라이브러리

1. core : 출력, 제어문, 반복문에 대한 tag 제공
2. fmt : 출력 형식에 관련된 tag 제공
3. function : jstl이 제공하는 함수들을 tag 형태로 제공
4. xml : xml 문서를 읽거나 내보내기 위한 tag 제공
5. sql : sql 쿼리문을 작성할 수 있는 tag 제공

### jstl 라이브러리 사용 방법

- <%@ taglib 으로 사용할 라이브러리 선언(<%@ taglib uri="참조 uri" prefix="태그 이름" %>)

```
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<c:out></c:out>
```

- core : <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

  1. 출력
     - \<c:out>  
     - \<c:out value="출력값" default="기본값">  
     - \<c:out value="출력값" default="기본값" escapeXml="true/false">(출력, 기본값 내부에 HTML 태그가 존재할 경우)

     ```html
     <c:out value="${age}" />
     ```

  2. 변수 선언
     - \<c:set>
     - \<c:set var="변수명" value="초기값" scope="변수 선언 위치">

     ```html
     <c:set var="msg" value="hello"/>
     \${msg} : ${msg} <!-- ${msg] : hello -->
     
     <c:set var="age">100</c:set>
     \${age} : ${age} <!-- ${age] : 100 -->
     
     <c:set var="add" value="${10 + 5}"/>
     \${add} : ${add} <!-- ${add] : 15 -->
     
     <c:set var="flag" value="${10 > 5}"/>
     \${flag} : ${flag} <!-- ${flag] : true -->
      
     <c:set var="member" value="<%=new MemberBean()%>"/>
     \${member} : ${member} <!-- ${member] : Member[...] -->
     ```

  3. 변수 제거
     - \<c:remove>
     - \<c:remove var="제거할 변수명" scope="변수 선언 위치">

     ```html
     <c:remove var="age" scope="page" />
     <c:out value="${age}" /> <!-- 출력되지 않음 -->
     ```

  4. 예외 처리
     - \<c:catch>
     - \<c:catch var="변수명">

     ```html
     <c:catch var="errorMsg">
     예외 발생 전 <br>
     <%= 10 / 0 %>		
     예외 발생 후 <br> <!-- 출력 되지 않음 -->
     </c:catch>
     <c:out value="${errorMsg}" />
     ```

  5. if
     - \<c:if>
     - \<c:if test="조건" var="조건 처리 변수명" scope="범위"></c:if>
     
     ```html
     <c:if test="${!status.last}">, </c:if>
     ```

  6. switch
     - \<c:choose>
     - \<c:when test="조건">
     - \<c:otherwise>

     ```html
     <c:choose>
        <c:when test="${status.first}">
            <li style="color: red; font-weight: bold">${movie}</li>
        </c:when>
        <c:otherwise>
            <li>${movie}</li>
        </c:otherwise>
     </c:choose>
     ```

  7. for
     - \<c:forEach>
     - \<c:forEach var="변수명" items="객체" begin="시작 인덱스" end="끝 인덱스" step="건너 뛸 값">

     ```html
     <c:forEach var="movie" items="${movieList}" varStatus="status">
        <tr>
            <td>${status.index}</td>
            <td>${status.count}</td>
            <td>${movie}</td>
        </tr>
     </c:forEach>
     ```

  8. 파라미터 전달
     - \<c:param>
     - \<c:param name="파라미터명" value="전달값">

  9. 페이지 이동(sendRedirect)
     - \<c:redirect>
     - \<c:redirect url="이동할 주소">

     ```html
     이동 전
     <c:redirect url="jstl_url.jsp" />
     이동 후
     ```

  10. url 지정
      - \<c:url>
      - \<c:url value="경로" var="변수명">

      ```html
      <c:url value="./images/pic.jpg" var="picture" />
      <img src="${picture}" width="150" height="150"/>
      ```

  11. import
      - \<c:import>
      - \<c:import url="경로" var="변수명">

      ```html
      <c:import url="http://localhost:8080/jstl_all/jstl_url.jsp" var="data" />
      ${data}
      ```

<br>

- fmt : <%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

  1. requestEncoding
     - \<fmt:requestEncoding>
     - \<fmt:requestEncoding value="값">
     
     ```html
     <fmt:requestEncoding value="UTF-8" />
     ```
     
  2. formatDate
     - \<fmt:formatDate>
     - \<fmt:formatDate value="값" type="포맷 타입" dateStyle="날짜 스타일" timeStyle="시간 스타일">
     
     ```html
     기본 : <fmt:formatDate value="${now}" /> <!-- 기본 : 2023. 7. 18. -->
     날짜 : <fmt:formatDate value="${now}" type="date" /> <!-- 날짜 : 2023. 7. 18. -->
     시간 : <fmt:formatDate value="${now}" type="time" /> <!-- 시간 : 오전 2:43:06 -->
     날짜 + 시간 : <fmt:formatDate value="${now}" type="both" /> <!-- 날짜 + 시간 : 2023. 7. 18. 오전 2:43:06 -->

     default : <fmt:formatDate value="${now}" type="both" dateStyle="default" timeStyle="default" /> <!-- default : 2023. 7. 18. 오전 2:43:06 -->
     short : <fmt:formatDate value="${now}" type="both" dateStyle="short" timeStyle="short" /> <!-- short : 23. 7. 18. 오전 2:43 -->
     medium : <fmt:formatDate value="${now}" type="both" dateStyle="medium" timeStyle="medium" /> <!-- medium : 2023. 7. 18. 오전 2:43:06 -->
     long : <fmt:formatDate value="${now}" type="both" dateStyle="long" timeStyle="long" /> <!-- long : 2023년 7월 18일 오전 2시 43분 6초 KST -->
     full : <fmt:formatDate value="${now}" type="both" dateStyle="full" timeStyle="full" /> <!-- full : 2023년 7월 18일 화요일 오전 2시 43분 6초 대한민국 표준시 -->
     
     <fmt:formatDate value="${now}" pattern="yyyy년 MM일 dd일 hh시 mm분 ss초" /> <!-- 2023년 07일 18일 02시 43분 06초 -->
     
     Korea : <fmt:formatDate value="${now}" pattern="yyyy년 MM일 dd일 hh시 mm분 ss초" /> <!-- Korea : 2023년 07일 18일 02시 43분 06초 -->
     Swiss :
     <fmt:timeZone value="GMT">
        <fmt:formatDate value="${now}" pattern="yyyy년 MM일 dd일 hh시 mm분 ss초" />
     </fmt:timeZone> <!-- Swiss : 2023년 07일 17일 05시 43분 06초 -->
     NewYork :
     <fmt:timeZone value="GMT-8">
        <fmt:formatDate value="${now}" pattern="yyyy년 MM일 dd일 hh시 mm분 ss초" />
     </fmt:timeZone> <!-- NewYork : 2023년 07일 17일 09시 43분 06초 -->
     
     <fmt:setLocale value="ko-kr" />
     통화(화폐) : <fmt:formatNumber value="10000" type="currency" /> <!-- 통화(화폐) : ₩10,000 -->
     날짜 : <fmt:formatDate value="${now}" /> <!-- 날짜 : 2023. 7. 18. -->
     <fmt:setLocale value="ja-jp" />
     통화(화폐) : <fmt:formatNumber value="10000" type="currency" /> <!-- 통화(화폐) : ￥10,000 -->
     날짜 : <fmt:formatDate value="${now}" /> <!-- 날짜 : 2023/07/18 -->
     <fmt:setLocale value="en-us" />
     통화(화폐) : <fmt:formatNumber value="10000" type="currency" /> <!-- 통화(화폐) : $10,000.00 -->
     날짜 : <fmt:formatDate value="${now}" /> <!-- 날짜 : Jul 18, 2023 -->
     ```

---
[<== 부트캠프 17일차](/bootcamp-day17) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 19일차 ==>](/bootcamp-miniproject2-best_board)