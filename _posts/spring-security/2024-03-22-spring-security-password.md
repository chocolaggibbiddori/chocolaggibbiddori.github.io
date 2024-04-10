---
layout: single
title: "Spring Security 비밀번호 암호화"
tag: spring security
---

# 비밀번호 저장

&nbsp;&nbsp; `Spring Security`는 가장 기본적인 사용자 인증 방법인 `username`, `password` 입력 인증을 지원한다.
그 중 비밀번호를 암호화 하여 저장하는 것은 아주 중요한데, `Spring Security`는 `PasswordEncoder` 인터페이스를 통해 이를 간단하게 사용할 수 있도록 해준다.

&nbsp;&nbsp; `PasswordEncoder` 인터페이스는 단방향 알고리즘을 사용하며, 비밀번호 암호화, 비교 등의 역할을 수행한다.

## PasswordEncoder 인터페이스

- String encode(CharSequence rawPassword) - 비밀번호를 암호화한다. 주로 해시 알고리즘을 사용하며, Salt 기법을 적용한다.
- boolean matches(CharSequence rawPassword, String encodedPassword) - 비밀번호를 비교한다.

&nbsp;&nbsp; `Spring Security`에서 권장하는 인코더는 다음과 같다.

- **BCryptPasswordEncoder**
   - bcrypt 알고리즘 사용
   - `strength` 필드를 조절하여 비밀번호 검증 시간 조절 (default 10)
- **Argon2PasswordEncoder**
   - Argon2 알고리즘 사용
   - BouncyCastle 라이브러리 필요
- **Pbkdf2PasswordEncoder**
   - PBKDF2 알고리즘 사용
   - FIPS 인증이 필요할 때 좋다고 함
- **SCryptPasswordEncoder**
   - scrypt 알고리즘 사용
   - BouncyCastle 라이브러리 필요
- ***DelegatingPasswordEncoder*** [Default]
   - `Spring Security` 권장 기본 인코더
   - 레거시 인코더 사용, 향후 업그레이드 등을 고려해서 나온 클래스
   - 다양한 알고리즘 지원 (기본적으로 bcrypt 알고리즘 사용)
   - `PasswordEncoderFactories.createDelegatingPasswordEncoder()` 메서드로 쉽게 인스턴스 생성 가능

___
**[참고]**  
[Spring Security 공식 문서](https://docs.spring.io/spring-security/reference)