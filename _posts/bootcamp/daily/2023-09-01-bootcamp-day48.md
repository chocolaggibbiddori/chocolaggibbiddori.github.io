---
layout: single
title: "부트캠프 48일차"
tag: [bootcamp, spring framework, spring boot, jpa, test]
---

# Spring Boot

## ORM

&nbsp;&nbsp; ORM(Object Relational Mapping)이란, 객체와 RDB(Relational Database)의 테이블을 자동으로 매핑하는 기술이다.
객체 지향 언어의 객체와 RDB의 테이블은 패러다임 불일치가 존재하기 때문에 곧바로 이 둘을 매핑하기란 쉽지 않다.  
&nbsp;&nbsp; 때문에 객체와 테이블 사이에서 이 둘을 매핑시켜주는 역할이 필요한데 그것을 가능케 하는 기술이 바로 ORM이다.

> 장점

- DB의 쿼리를 객체지향적으로 조작할 수 있다.
- 재사용 및 유지보수에 용이하다.
- DB에 대한 종속성이 줄어든다.

> 단점

- ORM만으로 모든 쿼리를 구현하는 데에는 한계가 있다.
- 애플리케이션의 객체 관점과 DB의 관계 관점의 불일치가 발생한다.

<br>

## JPA

&nbsp;&nbsp; JPA(Java Persistence API)는 JAVA에서 채택한 ORM 기술 표준으로, 인터페이스의 모음이다.
JPA은 내부적으로 JDBC를 사용해서 개발자 대신 적절한 SQL을 생성해준다.  
&nbsp;&nbsp; JPA의 구현체는 대표적으로 세 가지가 있다. 이 중 가장 많이 사용되는 구현체는 Hibernate이다.

1. Hibernate
2. EclipseLink
3. DataNucleus

### Spring Data JPA

&nbsp;&nbsp; Spring Data JPA는 JPA를 편리하게 사용할 수 있도록 도와주는 스프링의 프로젝트이다.
Spring Data JPA는 하이버네이트에서 자주 사용되는 기능을 더 쉽게 사용할 수 있게 구현한 라이브러리이며, 기본적인 기능들은 인터페이스로 제공된다.

#### Entity 설계

1. `@Entity` : 해당 클래스를 엔티티로 설정
2. `@Table` : 엔티티 이름과 테이블 이름이 다를 경우 사용
3. `@Id` : 기본값
4. `@GeneratedValue` : 기본값을 자동으로 생성하는 방법 설정
   - 애플리케이션에서 자체적으로 값을 생성할 경우 사용하지 않음
   - AUTO(default) : DB에 맞게 자동 생성
   - IDENTITY : 기본값 생성을 DB에 위임(AUTO_INCREMENT)
   - SEQUENCE : @SequenceGenerator 어노테이션으로 식별자 생성기를 설정하고 값을 자동 주입 받음
   - TABLE : 어떤 DBMS를 사용하더라도 동일하게 동작하기를 원할 경우 사용
5. `@Column` : 필드를 DB 컬럼에 매핑할 때 필요한 설정을 추가하고 싶을 때 사용
6. `@Transient` : DB와 매핑하지 않도록 설정

#### Repository 설계

1. `JpaRepository<T, ID>`를 상속 받는 인터페이스를 생성(따로 구현하지 않아도 된다.)
   - T : 엔티티 타입
   - ID : 기본값 타입
2. Repository 메서드명 규칙
   - findBy{fieldName} : findById(Long id)
   - And/Or : findByIdAndName(Long id, String name)
   - Like/NotLike : SQL의 like와 동일한 기능
   - StartsWith/StartingWith : 특정 키워드로 시작하는 문자열 조건
   - EndsWith/EndingWith : 특정 키워드로 끝나는 문자열 조건
   - IsNull/IsNotNull : 레코드 값이 Null이거나 Null이 아닌 값을 검색
   - True/False : boolean 타입의 레코드를 검색
   - Before/After : 시간을 기준으로 검색
   - LessThan/GreaterThan : 특정 숫자를 기준으로 비교
   - Between : 두 값 사이의 데이터를 조회
   - OrderBy : SQL의 order by와 동일한 기능 : findByNameOrderByPriceAsc(String name)
   - countBy : SQL의 count와 동일한 기능

#### DAO 설계

&nbsp;&nbsp; 일반적으로 인터페이스 - 구현으로 설계함

1. `getById()` : 프록시 객체를 반환
2. `findById()` : 실제 객체를 반환

<br>

## Test

> 테스트 코드를 작성하는 이유

- 개발 과정에서 문제를 미리 발견할 수 있다.
- 리팩터링의 리스크가 줄어든다.
- 애플리케이션을 가동해서 직접 테스트하는 것보다 빠르다.
- 하나의 명세서로서 사용될 수 있다.
- 코드가 작성된 목적을 명확하게 표현할 수 있으며, 불필요한 내용이 추가되는 것을 방지한다.

### Test 코드를 작성하는 방법

#### Given-When-Then

&nbsp;&nbsp; Given-When-Then 패턴은 테스트 코드를 표현하는 방법 중 하나이며, 각 단계의 목적에 맞게 코드를 작성하는 기법이다.

1. Given - 테스트를 수행하기 전 필요한 환경을 설정하는 단계. 테스트에 필요한 변수를 정의하거나 Mock 객체를 통해 특정 상황에 대한 행동을 정의한다.
2. When - 테스트의 목적을 보여주는 단계. 실제 테스트 코드가 포함되며, 테스트를 통한 결과값을 가져온다.
3. Then - 테스트의 결과를 검증하는 단계. When 단계에서 나온 결과값을 검증한다.

#### 좋은 테스트를 작성하는 5가지 속성(FIRST)

1. Fast : 테스트는 빨라야한다.
2. Isolated : 각 테스트는 독립적이어야 한다.
3. Repeatable : 테스트는 반복 가능해야 한다.
4. Self-Validating : 테스트는 그 자체만으로 검증이 완료돼야 한다. 즉, 테스트의 성공 여부만으로 판단할 수 있어야 하며 개발자가 직접 값을 확인하는 등의 작업이 필요하다면 좋은 테스트 코드라고 할 수 없다.
5. Timely : 테스트 코드는 애플리케이션 코드를 구현하기 전에 완성돼야 한다. 다만, 이 개념은 TDD(Test-Driven-Development) 원칙을 따르는 테스트 작성 규칙이라서 제외될 때도 있다.

### JUnit

&nbsp;&nbsp; JUnit은 JAVA에서 사용하는 대표적인 테스트 프레임워크로서 단위 테스트, 통합 테스트를 위한 도구를 제공한다.
JUnit의 가장 큰 특징은 어노테이션 기반의 테스트 방식을 지원한다는 것이다. 또한, JUnit을 활용하면 단정문(assert)을 통해 기댓값을 검토할 수 있다.

> JUnit에서 사용하는 어노테이션

1. `@Test` : 테스트 메서드
2. `@BeforeAll` : 테스트가 시작되기 전에 실행되는 메서드
3. `@AfterAll` : 테스트가 모두 끝난 후 실행되는 메서드
4. `@BeforeEach` : 각 테스트 메서드가 시작되기 전에 실행되는 메서드
5. `@AfterEach` : 각 테스트 메서드가 끝난 후 전에 실행되는 메서드

### Mock 객체를 이용한 컨트롤러 테스트

> 어노테이션

1. `@WebMvcTest(대상 클래스)` : 웹에서 사용되는 요청과 응답에 대한 테스트.  대상 클래스를 지정하지 않으면 `@Controller`, `@RestController`, `@ControllerAdvice` 등의 컨트롤러 관련 빈 객체가 모두 로드된다.
2. `@MockBean` : Mock 객체를 생성해서 주입. 실제 객체가 아니기 때문에 개발자가 Mockito의 `given()` 메서드를 통해 동작을 정의해야 한다.
3. `@DisplayName` : 테스트의 이름을 정의할 수 있는 어노테이션

> Mockito

1. `BDDMockito.given()` : 이 객체에서 어떤 메서드가 호출되고 어떤 파라미터를 주입받는지 가정한 후 `willReturn()` 메서드를 통해 어떤 결과를 리턴할 것인지 정의한다.
2. `perform()` : 컨트롤러의 API를 테스트하기 위해 사용되는 객체. 정확하게는 서블릿 컨테이너의 구동 없이 가상의 MVC 환경에서 모의 HTTP 서블릿을 요청하는 유틸리티 클래스이다.
`MockMvcRequestBuilders` 클래스에서 제공하는 HTTP 메서드로 URL을 정의해서 사용한다.
3. `verify()` : 지정된 메서드가 잘 실행됐는지 검증하는 역할. 일반적으로 `given()`에 정의된 동작과 대응한다.

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    ProductService productService; // Mock 객체 주입

    @Test
    @DisplayName("MockMvc를 통한 Product 데이터 가져오기 테스트")
    void getProductTest() throws Exception {
        //g
        given(productService.getProduct(123L))
                .willReturn(new ProductResponseDto(123L, "pen", 5000, 2000)); // 123L을 입력 받으면 이 ProductResponseDto 객체를 반환해야한다.

        //w
        String productId = "123";
        mockMvc.perform(get("/product?id=" + productId)) // 경로 지정, post, put, delete 메서드도 존재함
                .andExpect(status().isOk()) // andExpect() 메서드로 결과값을 검증
                .andExpect(jsonPath("$.id").exists())
                .andExpect(jsonPath("$.name").exists())
                .andExpect(jsonPath("$.price").exists())
                .andExpect(jsonPath("$.stock").exists())
                .andDo(print());

        //t
        verify(productService).getProduct(123L);
    }

    @Test
    @DisplayName("Product 데이터 생성 테스트")
    void createProductTest() throws Exception {
        //g
        given(productService.saveProduct(new ProductDto("pen", 5000, 2000))) // 1. productService의 saveProduct() 메서드는 이 객체를 입력 받으면
                .willReturn(new ProductResponseDto(1L, "pen", 5000, 2000)); // 2. 이 객체를 반환해야한다.
    
        //w
        ProductDto productDto = new ProductDto("pen", 5000, 2000);
        String content = mapper.writeValueAsString(productDto);
    
        // Gson gson = new Gson();
        // String gsonContent = gson.toJson(productDto); <-- 위의 content와 같다.
    
        mockMvc.perform(
                        post("/product")
                                .content(content)
                                .contentType(MediaType.APPLICATION_JSON)) // 3. Json 데이터를 파싱한 ProductDto 객체를 1.에서 입력한 객체와 equals() 메서드로 동등 비교한다.
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.id").exists())
                .andExpect(jsonPath("$.name").exists())
                .andExpect(jsonPath("$.price").exists())
                .andExpect(jsonPath("$.stock").exists())
                .andDo(print());
    
        //t
        verify(productService).saveProduct(new ProductDto("pen", 5000, 2000));
    }
}
```

> **_주의!!!_**
> 
> &nbsp;&nbsp; `createProductTest()`를 진행할 때 테스트가 계속 실패해서 애를 많이 먹었다. 기댓값을 찾을 수 없다는 오류가 계속 발생했는데,
> 이유인즉슨, `/product` 경로에 해당 json 데이터를 넣어도 반환 객체(`ProductResponseDto`)가 계속 `null`이 나오는 것이였다.
> 분명히 `given()`에서 `new ProductDto("pen", 5000, 2000)`을 입력 값으로 넣어주면 `new ProductResponseDto(1L, "pen", 5000, 2000)`를 반환하라고 설정했는데...  
> &nbsp;&nbsp; 여러 가지 테스트를 해봤는데 `given()`에서 설정한 입력 객체와 `mockMvc`에 입력해준 객체를 동등 비교하는 것 같다.
> 즉, `given()`에서 입력해준 객체와 동등 비교를 해서 `true`가 나왔을 때만 `ProductResponseDto` 객체를 반환해준다는 말이다.
> 이 때, 동등성 비교는 `equals()`를 기준으로 삼는다. 이렇게 생각한 이유는 `ProductDto` 클래스에 `equals()` 메서드를 override 했더니 테스트가 통과했기 때문이다.

<br>

___

**[참고]**  
장정우, 스프링 부트 핵심 가이드(스프링 부트를 활용한 애플리케이션 개발 실무)  
깃허브 주소 : [https://github.com/chocolaggibbiddori/springboot-wikibooks](https://github.com/chocolaggibbiddori/springboot-wikibooks)

[<== 부트캠프 46일차](/bootcamp-day46) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 49일차 ==>](/bootcamp-day49)