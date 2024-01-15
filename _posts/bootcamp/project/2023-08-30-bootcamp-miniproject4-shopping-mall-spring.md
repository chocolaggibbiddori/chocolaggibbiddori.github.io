---
layout: single
title: "부트캠프 미니 프로젝트 4 shopping-mall"
tag: [bootcamp, miniproject]
---

# 미니 프로젝트(withJ)

> 인원 소개

```java
public class Main {
    public static void main(String[] args) {
        Member member1 = new Member("김준영", 23, "https://github.com/kimjunyo");
        Member member2 = new Member("이승준", 27, "https://github.com/tmdwns2760");
        Member member3 = new Member("임제정", 27, "https://github.com/JEONG253");
        Member member4 = new Member("강동희", 28, "https://github.com/chocolaggibbiddori");
        
        System.out.println(member1);
        System.out.println(member2);
        System.out.println(member3);
        System.out.println(member4);
    }
}

// console
Member{name='김준영', age=23, gitHubLink='https://github.com/kimjunyo'}
Member{name='이승준', age=27, gitHubLink='https://github.com/tmdwns2760'}
Member{name='임제정', age=27, gitHubLink='https://github.com/JEONG253'}
Member{name='강동희', age=28, gitHubLink='https://github.com/chocolaggibbiddori'}
```

<br>

## 목차

1. Servlet 클래스 변경
2. Service 클래스 변경
3. DAO 클래스 변경
4. 필터 적용
5. AOP 적용
6. 문자열 상수화

<br>

## Servlet 클래스 변경

- @WebServlet -> @Controller
- @RequestMapping 어노테이션을 이용하여 요청 경로 매핑
- @Autowired 어노테이션을 이용하여 Service 객체 자동 주입
- @RequestParam, @PathVariable 등등 사용

```java
@EnableAspectJAutoProxy
@RequestMapping("/cart")
@Controller
public class CartController {

    @Autowired
    private CartService cartService;
}
```

<br>

## Service 클래스 변경

- @Service 어노테이션으로 빈 등록
- @Transactional 사용
- @Autowired 어노테이션을 이용하여 DAO 객체 자동 주입

```java
@EnableAspectJAutoProxy
@Transactional
@Service
public class CartService {

    @Autowired
    private CartDAO cartDAO;

    public void deleteOptions(String[] cseqArr) {
        for (String cseq : cseqArr) {
            cartDAO.deleteCart(Integer.parseInt(cseq));
        }
    }

    public void insertOption(String userId, int pseq, int quantity) {
        CartVO cartVO = new CartVO();
        cartVO.setId(userId);
        cartVO.setPseq(pseq);
        cartVO.setQuantity(quantity);

        cartDAO.insertCart(cartVO);
    }

    @Transactional(readOnly = true)
    public List<CartVO> getCartList(String userId) {
        return cartDAO.listCart(userId);
    }

    @Transactional(propagation = NOT_SUPPORTED)
    public int getTotalPrice(List<CartVO> cartList) {
        int totalPrice = 0;

        for (CartVO cartVO : cartList) {
            totalPrice += cartVO.getPrice2() * cartVO.getQuantity();
        }

        return totalPrice;
    }
}
```

<br>

## DAO 클래스 변경

<br>

## 필터 적용

```xml
<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

<filter>
    <filter-name>sessionFilter</filter-name>
    <filter-class>com.withJ.sts.filter.SessionFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>sessionFilter</filter-name>
    <url-pattern>/member/myPage</url-pattern>
    <url-pattern>/member/logout</url-pattern>
    <url-pattern>/cart/*</url-pattern>
    <url-pattern>/order/*</url-pattern>
    <url-pattern>/qna/*</url-pattern>
</filter-mapping>
```

```java
public class SessionFilter implements Filter {

    private final Logger log = LoggerFactory.getLogger(getClass());

    @Override
    public void init(FilterConfig filterConfig) {
        log.info("[Filter]={}", filterConfig.getFilterName());
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        log.info("======사용자 인증을 진행합니다.======");
        HttpServletRequest httpServletRequest = (HttpServletRequest) request;
        HttpSession session = httpServletRequest.getSession();
        String path = "/WEB-INF/member/login.jsp";

        MemberVO loginUser = (MemberVO) session.getAttribute(SessionConst.USER);
        if (isNull(loginUser)) {
            request.getRequestDispatcher(path).forward(request, response);
            return;
        }

        chain.doFilter(request, response);
    }
}
```

<br>

## AOP 적용

- 로그
- 시간 측정

```java
@Component
@Aspect
public class LogAdvisor {

    private final Logger log = LoggerFactory.getLogger(getClass());

    @Before("com.withJ.sts.aop.Pointcuts.controller() || com.withJ.sts.aop.Pointcuts.service()")
    public void doLog(JoinPoint joinPoint) {
        log.info("call={}", joinPoint.getSignature());
    }
}

@Component
@Aspect
public class TimeAdvisor {

    private final Logger log = LoggerFactory.getLogger(getClass());

    @Around("com.withJ.sts.aop.Pointcuts.controller()")
    public void calTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long beforeTime = System.currentTimeMillis();
        
        Object proceed = joinPoint.proceed();
        
        long afterTime = System.currentTimeMillis();
        long totalTime = afterTime - beforeTime;
        
        log.info("걸린 시간 : {}", totalTime);
        return proceed;
    }
}

public class Pointcuts {

    @Pointcut("execution(* com.withJ.sts.controller..*(..))")
    public void controller() {
    }

    @Pointcut("execution(* com.withJ.sts.service..*(..))")
    public void service() {
    }
}
```

<br>

## 문자열 상수화

- Session, Model, return 경로 등에서 다소 중복된 문자열이 존재
- 경로, 뷰 파일, 파라미터 이름 변경 시 많은 변경을 해야하는 문제 발생
- 해당 값들의 설정과 사용을 분리할 필요성을 느낌

> 설정

```java
public class SessionConst {

    public static final String USER = "loginUser";
    public static final String ID = "id";

    private SessionConst() {
    }
}

public class ModelConst {

    public static final String ID = "id";
    public static final String CSEQ = "cseq";
    public static final String KIND = "kind";
    public static final String USE_YN = "useyn";
    public static final String BEST_YN = "bestyn";
    public static final String TOTAL_PRICE = "totalPrice";
    public static final String PSEQ = "pseq";
    public static final String NAME = "name";
    public static final String PRICE1 = "price1";
    public static final String PRICE2 = "price2";
    public static final String CONTENT = "content";
    public static final String IMAGE = "image";
    public static final String NON_MAKE_IMAGE = "nonmakeImg";
    public static final String TITLE = "title";
    public static final String MESSAGE = "message";
    public static final String RESULT = "result";
    public static final String KEY = "key";
    public static final String T_PAGE = "tpage";
    public static final String PAGING = "paging";
    public static final String ADDRESS_LIST = "addressList";
    public static final String CART_LIST = "cartList";
    public static final String MEMBER_LIST = "memberList";
    public static final String KIND_LIST = "kindList";
    public static final String ORDER_LIST = "orderList";
    public static final String ORDER_DETAIL = "orderDetail";
    public static final String PRODUCT_LIST = "productList";
    public static final String PRODUCT_LIST_SIZE = "productListSize";
    public static final String PRODUCT_NEW_LIST = "newProductList";
    public static final String PRODUCT_BEST_LIST = "bestProductList";
    public static final String PRODUCT_KIND_LIST = "productKindList";
    public static final String PRODUCT_DETAIL = "productDetail";
    public static final String PRODUCT_VO = "productVO";
    public static final String QNA_LIST = "qnaList";
    public static final String QNA_VO = "qnaVO";

    private ModelConst() {
    }
}

public enum Path {

    HOME("/"),
    INDEX("/index"),
    CART("/cart"),
    CART_LIST("/mypage/cartList"),
    MEMBER_CONTRACT("/member/contract"),
    MEMBER_FIND_ZIP_NUM("/member/findZipNum"),
    MEMBER_ID_CHECK("/member/idcheck"),
    MEMBER_JOIN("/member/join"),
    MEMBER_LOGIN("/member/login"),
    MEMBER_LOGIN_FAIL("/member/login_fail"),
    MEMBER_MYPAGE("/member/myPage"),
    MEMBER_LOGOUT("/member/logout"),
    ADMIN_MAIN("/admin/main"),
    ADMIN_LOGIN_FORM("/admin/login/form"),
    ADMIN_MEMBER_LIST("/admin/member/memberList"),
    ADMIN_ORDER_LIST("/admin/order/orderList"),
    ADMIN_PRODUCT_LIST("/admin/product/list"),
    ADMIN_PRODUCT_DETAIL("/admin/product/productDetail"),
    ADMIN_PRODUCT_UPDATE("/admin/product/productUpdate"),
    ADMIN_PRODUCT_WRITE("/admin/product/productWrite"),
    ADMIN_QNA_LIST("/admin/qna/qnaList"),
    ADMIN_QNA_DETAIL("/admin/qna/qnaDetail"),
    MYPAGE("/mypage/mypage"),
    MYPAGE_ORDER_LIST("/mypage/orderList"),
    MYPAGE_ORDER_DETAIL("/mypage/orderDetail"),
    PRODUCT_DETAIL("/product/productDetail"),
    PRODUCT_KIND("/product/productKind"),
    PRODUCT_NEW_LIST("/product/newProductList"),
    PRODUCT_BEST_LIST("/product/bestProductList"),
    QNA_LIST("/qna/qnaList"),
    QNA_VIEW("/qna/qnaView"),
    QNA_WRITE("/qna/qnaWrite"),
    QNA_ALL("/qna/*"),
    ORDER_ALL("/order/*");

    private final String path;

    Path(String path) {
        this.path = path;
    }

    public String forward() {
        return path;
    }

    public String redirect() {
        return "redirect:" + path;
    }

    @Override
    public String toString() {
        return forward();
    }
}
```

<br>

> 사용

```java
@RequestMapping("/member")
@Controller
public class MemberController {
    
    ...
    
    @RequestMapping("/idCheck")
    public String idCheck(@RequestParam String id, Model model) {
        int message = memberService.duplicateCheckId(id);

        model.addAttribute(ModelConst.MESSAGE, message);
        model.addAttribute(ModelConst.ID, id);
        return Path.MEMBER_ID_CHECK.forward();
    }

    @RequestMapping("/join")
    public String joinForm() {
        return Path.MEMBER_JOIN.forward();
    }

    @RequestMapping(value = "/join", method = POST)
    public String join(@ModelAttribute MemberVO memberVO,
                       @RequestParam String addr1,
                       @RequestParam String addr2,
                       HttpSession session) {
        memberVO.setAddress(addr1 + addr2);
        memberService.joinMember(memberVO);

        session.setAttribute(SessionConst.ID, memberVO.getId());
        return Path.MEMBER_LOGIN.redirect();
    }
    
    ...
}
```

___

[<== 부트캠프 42일차](/bootcamp-day42) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 46일차 ==>](/bootcamp-day46)