---
layout: single
title: "부트캠프 24일차"
tag: [bootcamp, servlet]
---

# Servlet

<br>

## Servlet 하나로 합치기

&nbsp;&nbsp; 기존에는 기능 하나당 서블릿 하나씩을 만들어야 해서 서블릿의 개수가 많아지는 문제가 있었다.
오늘은 서블릿 하나만으로 모든 요청을 처리하도록 설계하는 코드를 작성하는 시간을 가졌다.

> MemberServlet

```java
...

@Override protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    doHandle(req, resp);
}

@Override protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    doHandle(req, resp);
}

private void doHandle(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    ...

    if (command != null && command.equals("addMember")) {
        String id = req.getParameter("id");
        String pwd = req.getParameter("pwd");
        String name = req.getParameter("name");
        String email = req.getParameter("email");
  
        AddMemberVO vo = new AddMemberVO(id, pwd, name, email);
        dao.addMember(vo);
    } else if (command != null && command.equals("delMember")) {
        String id = req.getParameter("id");
        dao.delMember(id);
    }

    ...
}
```

&nbsp;&nbsp; 가운데 if 구문에서 command를 통해 각 기능을 분리하는 것을 볼 수 있다. 서블릿 하나의 크기는 늘어났지만 서블릿의 개수를 확연히 줄일 수 있다.

<br>

## DataSource

&nbsp;&nbsp; 기존에는 Connection을 얻어오기 위해 DriverManager를 이용하였는데 DataSource 인터페이스를 이용하여 간편하게 Connection을 얻어올 수 있었다.  
&nbsp;&nbsp; DataSource 인터페이스를 사용하여 Connection을 가져올 때는 매개변수를 따로 넣어줄 필요가 없었고,
기존에 넣어둔 URL, USERNAME, PASSWORD는 설정 파일에 적어두기만 하면 된다.

- 변경 전

> <p> 1. DriverManager </p>

   ```java
   @Slf4j
   @Component
   public class MemberDAO {
         
       private Connection con;
           
       private void connDB() {
           try {
               con = DriverManager.getConnection(URL, USERNAME, PASSWORD);
               log.info("get connection={}, class={}", con, con.getClass());
           } catch (SQLException e) {
               throw new RuntimeException(e);
           }
       }
   }
   ```

<br>

- 변경 후

> <p> 1. DataSource </p>

   ```java
   @Slf4j
   @RequiredArgsConstructor
   @Component
   public class MemberDAO {
         
       private final DataSource dataSource;
       private Connection con;
         
       private void connDB() {
           try {
               con = dataSource.getConnection();
               log.info("get connection={}, class={}", con, con.getClass());
           } catch (SQLException e) {
               throw new RuntimeException(e);
           }
       }
   }
   ```

> <p> 2. application.yaml </p>

   ```yaml
   spring:
     datasource:
       url: jdbc:h2:tcp://localhost/~/day24
       username: sa
       password:
       driver-class-name: org.h2.Driver
   ```

<br>

## Form 태그 다루기

&nbsp;&nbsp; form 태그 안에 이름을 설정한 후 js에서 쉽게 다루는 방법을 배웠다.

> addMemberForm.html

```html
...

<head>

    ...

    <script type="text/javascript">
        function fn_sendMember() {
            var frmMember = document.frmMember;
            var id = frmMember.id.value;
            var pwd = frmMember.pwd.value;
            var name = frmMember.name.value;
            var email = frmMember.email.value;

            if (id.length == 0 || id == "") {
                alert("아이디는 필수입니다.");
            } else if (pwd.length == 0 || pwd == "") {
                alert("비밀번호는 필수입니다.");
            } else if (name.length == 0 || name == "") {
                alert("이름은 필수입니다.");
            } else if (email.length == 0 || email == "") {
                alert("이메일은 필수입니다.");
            } else {
                frmMember.method="post";
                frmMember.action="/day24/memberV2";
                frmMember.submit();
            }
        }
    </script>
</head>

...

<form name="frmMember">
    <table>
        <th>회원 가입창</th>
        <tr>
            <td>아이디</td>
            <td><input type="text" name="id" /></td>
        </tr>
        <tr>
            <td>비밀번호</td>
            <td><input type="text" name="pwd" /></td>
        </tr>
        <tr>
            <td>이름</td>
            <td><input type="text" name="name" /></td>
        </tr>
        <tr>
            <td>이메일</td>
            <td><input type="email" name="email" /></td>
        </tr>
    </table>
    <input type="button" value="가입하기" onclick="fn_sendMember()" />
    <input type="reset" value="다시입력" />
    <input type="hidden" name="command" value="addMember" />
</form>

...
```

---
[<== 부트캠프 23일차](/bootcamp-day23) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 25일차 ==>](/bootcamp-day25)