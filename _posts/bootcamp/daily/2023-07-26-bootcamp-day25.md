---
layout: single
title: "부트캠프 25일차"
tag: [bootcamp, servlet]
---

# Servlet

<br>

## 서블릿에서 다른 서블릿 호출하기

### HttpServletResponse 객체의 sendRedirect(String location) 메서드

```java
@WebServlet(name = "firstServlet", urlPatterns = "/first")
public class FirstServlet extends HttpServlet {

    /**
     * 단순 화면 이동
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        resp.sendRedirect("second"); // "second?name=java"으로 파라미터 지정
    }
}
```

### HttpServletResponse 객체의 addHeader(String name, String value) 메서드

```java
@WebServlet(name = "firstServlet", urlPatterns = "/first")
public class FirstServlet extends HttpServlet {

    /**
     * 단순 화면 이동
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        resp.addHeader("Refresh", "2;url=second");
    }
}
```

### RequestDispatcher

```java
@WebServlet(name = "firstServlet", urlPatterns = "/first")
public class FirstServlet extends HttpServlet {

    /**
     * 단순 화면 이동
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");

        req.setAttribute("name", "javaMachine");
        req.getRequestDispatcher("second").forward(req, resp);
    }
}
```

<br>

## ServletContext

&nbsp;&nbsp; 하나의 Application은 하나의 ServletContext를 가진다. ServletContext 안에는 여러 Servlet들이 들어있는데,
ServletContext 안에 저장된 값들은 모든 Servlet에서 사용 할 수 있다.

### ServletContext로 데이터 공유하기

> SetContextServlet

```java
@WebServlet("/cset")
public class SetContextServlet extends HttpServlet {

    /**
     * ServletContext : 서블릿끼리 자원 공유
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        ServletContext context = getServletContext();

        List<Object> member = new ArrayList<>();
        member.add("이순신");
        member.add(30);
        
        context.setAttribute("member", member);

        resp.sendRedirect("cget");
    }
}
```

> GetContextServlet

```java
@WebServlet("/cget")
public class GetContextServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        ServletContext context = getServletContext();

        List<Object> member = (List<Object>) context.getAttribute("member");
        String name = (String) member.get(0);
        int age = (Integer) member.get(1);

        PrintWriter out = resp.getWriter();
        out
                .append("<html><body>")
                .append(name).append("<br>")
                .append(String.valueOf(age))
                .append("</body></html>");
        
        out.close();
    }
}
```

### ServletContext를 이용하여 외부 파일 값 가져오기

```java
@WebServlet(value = "/cfile")
public class ContextFileServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");

        ServletContext context = getServletContext();
        InputStream is = context.getResourceAsStream("/WEB-INF/day25/bin/init.txt");
        BufferedReader br = new BufferedReader(new InputStreamReader(is));

        String menu;
        String menuMember = null;
        String menuOrder = null;
        String menuGoods = null;
        while ((menu = br.readLine()) != null) {
            StringTokenizer st = new StringTokenizer(menu, ",");
            menuMember = st.nextToken();
            menuOrder = st.nextToken();
            menuGoods = st.nextToken();
        }

        PrintWriter out = resp.getWriter();
        out
                .append("<html><body>")
                .append(menuMember).append("<br>")
                .append(menuOrder).append("<br>")
                .append(menuGoods).append("<br>")
                .append("</body></html>");
    }
}
```

<br>

## Servlet InitParams

&nbsp;&nbsp; Servlet에 초기 데이터를 삽입해서 사용할 수 있다.

```java
@WebServlet(
        urlPatterns = {"/sInit", "/sInit2"},
        initParams = {
                @WebInitParam(name = "email", value = "admin@free.com"),
                @WebInitParam(name = "tel", value = "010-1111-2222")
        }
)
public class InitParamServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");

        String email = getInitParameter("email");
        String tel = getInitParameter("tel");

        PrintWriter out = resp.getWriter();
        out
                .append("<html><body>")
                .append("<table>")
                .append("<tr><td>email:</td><td>").append(email).append("</td></tr>")
                .append("<tr><td>tel:</td><td>").append(tel).append("</td></tr>")
                .append("</table></body></html");

        out.close();
    }
}
```

<br>

## Load-On-Startup

&nbsp;&nbsp; Servlet은 브라우저에서 최초 요청 시 init() 메서드를 실행한 후 메모리에 로드되어 기능을 수행한다.
따라서, 최초 요청에 대해서는 실행 시간이 길어질 수밖에 없는데 이런 단점을 보완하기 위하여 사용하는 기능이 Load-On-Startup이다.  
&nbsp;&nbsp; loadOnStartup(default = -1)의 값이 0보다 크거나 같으면 앱 실행 시 서블릿을 미리 메모리에 로드시킨다.  
서블릿이 여러 개라면 각 서블릿의 loadOnStartup의 값은 달라야한다.(순서 보장)

```java
@WebServlet(
        value = "/loadConfig",
        loadOnStartup = 0,
        initParams = {
                @WebInitParam(name = "email", value = "admin@free.com"),
                @WebInitParam(name = "tel", value = "010-1111-2222")
        }
)
public class LoadAppConfigServlet extends HttpServlet {

    private ServletContext context;

    @Override
    public void init() {
        String email = getInitParameter("email");
        String tel = getInitParameter("tel");

        context = getServletContext();
        context.setAttribute("email", email);
        context.setAttribute("tel", tel);
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");
        resp.setContentType("text/html;charset=UTF-8");

        String email = (String) context.getAttribute("email");
        String tel = (String) context.getAttribute("tel");

        PrintWriter out = resp.getWriter();
        out
                .append("<html><body>")
                .append("<table>")
                .append("<tr><td>email:</td><td>").append(email).append("</td></tr>")
                .append("<tr><td>tel:</td><td>").append(tel).append("</td></tr>")
                .append("</table></body></html");

        out.close();
    }
}
```

---
[<== 부트캠프 24일차](/bootcamp-day24) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 28일차 ==>](/bootcamp-day28)