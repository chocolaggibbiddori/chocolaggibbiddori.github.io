---
layout: single
title: "JWT - 라이브러리 사용법(기초)"
tag: java, jwt, library
---

# Java에서 JWT 라이브러리 사용하는 방법

&nbsp;&nbsp; 스프링 부트 프로젝트를 진행하면서 인증 토큰의 필요성을 느껴 많이 사용되고 있는 JWT를 도입하게 되었다.
이 글은 JWT 라이브러리 사용법을 정리하기 위한 글이다.

**개발 환경**
- Java 17
- Spring Boot 3.1.7
- Gradle 8.5

## JWT 라이브러리 선택

1. [jwt.io](https://jwt.io/libraries?language=Java) 접속
2. 원하는 라이브러리 선택 (이 게시글은 io.jsonwebtoken 라이브러리를 기준으로 설명함. View Repo를 클릭하면 라이브러리마다 자세한 사용법이 기술되어 있음)

## 의존성 추가

&nbsp;&nbsp; io.jsonwebtoken 라이브러리는 JDK 프로젝트에서 의존성 추가하는 방법을 다음과 같이 설명하고 있다.

### Maven

```xml
<dependencies>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-api</artifactId>
        <version>0.12.5</version>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-impl</artifactId>
        <version>0.12.5</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt-jackson</artifactId> <!-- or jjwt-gson if Gson is preferred -->
        <version>0.12.5</version>
        <scope>runtime</scope>
    </dependency>
</dependencies>
<!-- Uncomment this next dependency if you are using:
     - JDK 10 or earlier, and you want to use RSASSA-PSS (PS256, PS384, PS512) signature algorithms.
     - JDK 10 or earlier, and you want to use EdECDH (X25519 or X448) Elliptic Curve Diffie-Hellman encryption.
     - JDK 14 or earlier, and you want to use EdDSA (Ed25519 or Ed448) Elliptic Curve signature algorithms.
     It is unnecessary for these algorithms on JDK 15 or later.
<dependency>
    <groupId>org.bouncycastle</groupId>
    <artifactId>bcprov-jdk18on</artifactId> or bcprov-jdk15to18 on JDK 7
    <version>1.76</version>
    <scope>runtime</scope>
</dependency>
-->
```

### Gradle

```groovy
dependencies {
    implementation 'io.jsonwebtoken:jjwt-api:0.12.5'
    runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.5'
    runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.5' // or 'io.jsonwebtoken:jjwt-gson:0.12.5' for gson
    /*
      Uncomment this next dependency if you are using:
       - JDK 10 or earlier, and you want to use RSASSA-PSS (PS256, PS384, PS512) signature algorithms.
       - JDK 10 or earlier, and you want to use EdECDH (X25519 or X448) Elliptic Curve Diffie-Hellman encryption.
       - JDK 14 or earlier, and you want to use EdDSA (Ed25519 or Ed448) Elliptic Curve signature algorithms.
      It is unnecessary for these algorithms on JDK 15 or later.
    */
    // runtimeOnly 'org.bouncycastle:bcprov-jdk18on:1.76' // or bcprov-jdk15to18 on JDK 7
}
```

## 라이브러리 사용법

&nbsp;&nbsp; 해당 라이브러리의 상세한 사용 방법은 [링크](https://github.com/jwtk/jjwt)에 접속하여 확인할 수 있다.
이 포스팅에서는 간단한 부분만 추출해서, 내가 사용했던 기능 위주로 정리하겠다.

### Example Code:
```
Jwts.builder()
        .header()
        .type(HEADER_TYPE)
        .and()
        
        .claims()
        .add(payload)
        .issuer(PAYLOAD_ISSUER)
        .issuedAt(iat)
        .expiration(exp)
        .and()
        
        .signWith(secretKey)
        .compact();
```

### Jwts 클래스

&nbsp;&nbsp; 이 라이브러리의 기능은 대부분 `Jwts` 클래스에서부터 시작된다. `Jwts` 클래스는 정적 메서드로 다음과 같은 기능들을 제공한다.

- header(): HeaderBuilder - 토큰 헤더 객체를 생성하기 위한 메서드 (헤더 객체만 먼저 만들어놓고 추후에 조립하는 용도인 듯)
- claims(): ClaimsBuilder - 토큰 클레임 객체를 생성하기 위한 메서드 (클레임 객체만 먼저 만들어놓고 추후에 조립하는 용도인 듯)
- builder(): [JwtBuilder](#jwtbuilder-인터페이스) - 토큰을 생성하기 위한 메서드
- parser(): [JwtParserBuilder](#jwtparserbuilder-인터페이스) - 토큰 검증, 파싱을 담당하는 `JwtParser` 객체를 만들기 위한 메서드

### 토큰 생성

#### JwtBuilder 인터페이스

&nbsp;&nbsp; `JwtBuilder` 인터페이스는 JWT 객체를 생성하기 위한 인터페이스이다. 메서드 체이닝을 지원하기 때문에 사용자는 쉽게 JWT 객체를 생성할 수 있다.  
&nbsp;&nbsp; 주요 기능은 다음과 같다.

##### Header 설정

- header(): BuilderHeader - 토큰의 헤더를 설정하기 위한 `BuilderHeader` 인터페이스 반환 메서드. `Jwts` 클래스의 `header()` 메서드와 아주 유사하나(거의 똑같다),
`Jwts` 클래스의 `HeaderBuilder` 인터페이스는 오직 `Header` 객체를 만들기 위한 빌더 인터페이스이고(빌더 인터페이스이기 때문에 `build()` 메서드로 `Header` 객체를 만들 수 있다),
`JwtBuilder` 인터페이스의 `BuilderHeader` 인터페이스는 `Header` 객체를 만드는 것이 아니라 `JwtBuilder` 내의 헤더 값을 설정하기 위한 인터페이스이다.
즉, Builder 인터페이스는 아니기 때문에 `build()` 메서드가 없고, 대신 `and()` 메서드로 헤더 구성을 완료한 후 다음 단계로 넘어갈 수 있다.

###### BuilderHeader 인터페이스

- keyId(String): BuilderHeader - kid 설정
- type(String): BuilderHeader - typ 설정
- contentType(String): BuilderHeader - cty 설정
- add(String, Object): BuilderHeader - 임의의 Header 설정
- add(Map<String, Object>): BuilderHeader - 임의의 Header 설정
- delete(String): BuilderHeader - 해당 키 제거
- empty(): BuilderHeader - 모든 값 제거
- and(): JwtBuilder - 헤더 설정 종료

##### Claims 설정

- issuer(String): JwtBuilder - iss 설정
- subject(String): JwtBuilder - sub 설정
- expiration(java.util.Date): JwtBuilder - exp 설정
- notBefore(java.util.Date): JwtBuilder - nbf 설정
- issuedAt(java.util.Date): JwtBuilder - iat 설정
- id(String): JwtBuilder - jti 설정
- audience(): AudienceCollection - aud 설정, 컬렉션 타입으로서 추가와 삭제가 가능함
- claim(String, Object): JwtBuilder - 임의의 Claim 설정
- claims(Map<String, Object>): JwtBuilder - 임의의 Claim 설정
- claims(): BuilderClaims - 토큰의 클레임들을 설정하기 위한 `BuilderClaims` 인터페이스 반환 메서드.
`BuilderClaims` 인터페이스는 `BuilderHeader` 인터페이스와 마찬가지로 `Jwts` 클래스의 `HeaderBuilder` 인터페이스와 아주 유사하나,
`Claims` 객체를 만들기 위한 Builder 인터페이스가 아닌 `JwtBuilder` 내의 클레임 값들을 설정하기 위한 인터페이스라는 차이가 있다.

###### BuilderClaims 인터페이스

&nbsp;&nbsp; 기본적으로 `BuilderHeader` 인터페이스와 유사하다. 등록 클레임에 대해서는 앞서 설명한 메서드들(ex. `issuer()`, `subject()`)과 동일하며,
임의 클레임 추가를 `add(String, Object)` 메서드로 하는 차이밖에 없다.  
&nbsp;&nbsp; 이 인터페이스를 사용하게 되면, [위 예시](#example-code)처럼 Header와 Claim을 구분해서 설정할 수 있는 장점이 있다. (코드가 깔끔해진다!)

- issuer(String): BuilderClaims - iss 설정
- subject(String): BuilderClaims - sub 설정
- expiration(java.util.Date): BuilderClaims - exp 설정
- notBefore(java.util.Date): BuilderClaims - nbf 설정
- issuedAt(java.util.Date): BuilderClaims - iat 설정
- id(String): BuilderClaims - jti 설정
- audience(): AudienceCollection - aud 설정, 컬렉션 타입으로서 추가와 삭제가 가능함
- add(String, Object): BuilderClaims - 임의의 Claim 설정
- add(Map<String, Object>): BuilderClaims - 임의의 Claim 설정
- delete(String): BuilderClaims - 해당 키 제거
- empty(): BuilderClaims - 모든 값 제거
- and(): JwtBuilder - 클레임 설정 종료

##### 발급

- compact(): String - 토큰 발급

##### 서명

&nbsp;&nbsp; 여기까지만 진행하고 `compact()` 메서드를 호출한다면, JWT를 발급할 수 있다. 그러나 서명되지 않는 JWT 토큰을 발급받게 될 것이다.
테스트해 본 결과 마지막 Signature 부분이 없는 토큰이 발급되었다.

`jwt = eyJhbGciOiJub25lIn0.eyJpc3MiOiJjaG9jb2xhIn0.` (끝에 마침표(.)가 찍히는 것도 주의!!)

&nbsp;&nbsp; 안전한 토큰을 발급받고 싶다면 서명을 추가해야 한다.

- signWith(java.security.Key): JwtBuilder - 서명 설정. 매개변수로 서명 키를 넣어주면 해당 알고리즘으로 토큰을 안전하게 발급할 수 있다.
키 객체는 `Jwts` 클래스의 내부 클래스 `SIG` 클래스를 통해 쉽게 생성할 수도 있고,

   ex) `.signWith(Jwts.SIG.HS256.key().build())`

   라이브러리에서 제공하는 `Keys` 유틸 클래스를 통해 생성할 수도 있다.

   ex) `.signWith(Keys.hmacShaKeyFor(Base64.getDecoder().decode(secretKey)))` - secretKey: 사용할 비밀 키 값 (문자열 혹은 byte 코드)

- signWith(java.security.Key, SecureDigestAlgorithm<Key, Object>): JwtBuilder - 서명 알고리즘을 직접 적용하고 싶은 경우에는 이 메서드를 사용하면 된다.

&nbsp;&nbsp; 서명까지 진행한다면 마지막 Signature 부분을 포함한 토큰이 반환된다.

`jwt = eyJhbGciOiJIUzI1NiJ9.eyJuYW1lIjoiSm9obiBEb2UifQ.pSwfjsXzbXeB5aBIH76OPCy9i5Pgv5W0mZKl9_4C0Qg`

### 토큰 검증

#### JwtParserBuilder 인터페이스

&nbsp;&nbsp; JWT를 검증하거나 파싱하기 위해서는 `JwtParser` 객체가 필요한데, 이 객체를 생성해주는 Builder 인터페이스가 바로 `JwtParserBuilder` 인터페이스이다.
`JwtParserBuilder` 객체는 `Jwts.parser()` 메서드를 통해 얻을 수 있다. `JwtBuilder` 인터페이스와 마찬가지로 메서드 체이닝을 지원한다.

##### 토큰 클레임 제약 조건 설정

- requireId(String): JwtParserBuilder - jti 값이 매개변수로 넣어준 값과 일치하는지 검증
- requireSubject(String): JwtParserBuilder - sub 값이 매개변수로 넣어준 값과 일치하는지 검증
- requireAudience(String): JwtParserBuilder - aud 값이 매개변수로 넣어준 값과 일치하는지 검증
- requireIssuer(String): JwtParserBuilder - iss 값이 매개변수로 넣어준 값과 일치하는지 검증
- requireIssuedAt(java.util.Date): JwtParserBuilder - iat 값이 매개변수로 넣어준 값과 일치하는지 검증
- requireExpiration(java.util.Date): JwtParserBuilder - exp 값이 매개변수로 넣어준 값과 일치하는지 검증
- requireNotBefore(java.util.Date): JwtParserBuilder - nbf 값이 매개변수로 넣어준 값과 일치하는지 검증
- require(String, Object): JwtParserBuilder - 해당 키 값이 매개변수로 넣어준 값과 일치하는지 검증

##### 토큰 서명 키 조건 설정

- verifyWith(javax.crypto.SecretKey): JwtParserBuilder - 서명 키 조건 설정. 토큰 발급 시 넣어준 키값을 넣어주면, 키 일치 여부를 검증함

##### JwtParser 객체 생성

&nbsp;&nbsp; 위의 메서드들이 토큰을 검증해 주는 것이 아니다.
토큰 검증은 `JwtParser` 객체가 담당하며 `JwtParserBuilder` 인터페이스는 단지 `JwtParser` 객체가 어떤 값들을 검증해야 하는지 지정해 줄 뿐이다.

- build(): JwtParser - `JwtParser` 객체 반환

#### JwtParser 인터페이스

&nbsp;&nbsp; 실제로 토큰을 검증하고, 파싱할 수 있도록 돕는 인터페이스. 서명되지 않은 토큰이라면 `Jwt` 인터페이스를, 서명된 토큰이라면 `Jws` 인터페이스를 반환해 준다.

- parseSignedClaims(CharSequence): Jws<Claims> - 매개변수로 JWS를 넣어주면, 토큰 검증 후 `Jws` 객체를 반환해 준다. 토큰 검증 시에는 다음과 같은 예외가 발생할 수 있다.
   - `UnsupportedJwtException` – 토큰이 서명되지 않은 토큰일 경우
   - `JwtException` – 토큰이 `JwtParser` 객체의 검증 기준에 통과하지 못했을 경우
   - `IllegalArgumentException` – 토큰이 `null`이거나 빈 문자열 혹은 공백인 경우

### 토큰 파싱

&nbsp;&nbsp; [토큰 검증](#토큰-검증) 단계를 통해 `Jws` 객체를 반환 받았다면, 이제 쉽게 헤더나 클레임 값들을 파싱할 수 있다.

#### Jws 인터페이스

- getHeader(): JwsHeader - Header 객체 반환
- getPayload(): Claims - Claims 객체 반환

&nbsp;&nbsp; `JwsHeader`, `Claims` 인터페이스는 모두 `java.util.Map<String, Object>` 인터페이스를 상속받고 있으므로, 내부 값들을 꺼내는 것은 설명이 필요하지 않을 것이다.

___
**[참고]**  
[https://github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)