---
layout: single
title: "부트캠프 미니 프로젝트 kktalk"
tag: [bootcamp, miniproject]
---

나만의 메신저 만들기
---

<br>

> **깃허브 주소** : [https://github.com/chocolaggibbiddori/bootcamp-project-kktalk](https://github.com/chocolaggibbiddori/bootcamp-project-kktalk)

---

## 개발 전 토의

&nbsp;&nbsp; 부트캠프 시작이래 처음으로 미니 프로젝트의 시간이 다가왔다. 주제는 자유이고 혼자하든지 팀을 짜서 하든지 모든게 자유! 다만 조건이 5가지가 있었다.

1. 컬렉션을 사용할 것
2. 쓰레드를 사용할 것
3. 결과는 html 파일로 출력할 것
4. 상속, 추상 클래스, 인터페이스 중 적어도 하나를 사용할 것
5. 데이터베이스를 사용할 것

&nbsp;&nbsp; 나는 활발한 형님과 둘이 한 팀이 되어 프로젝트를 진행하기로 했다. 첫 날 모여서 무엇을 만들어야 할지, 어떻게 만드는게 좋을지, 조건들을 어떤 식으로
사용할지는 논의했다.

### 아이디어 구상

- 동물 레이싱 참가자를 입력 받고 레이싱의 결과를 예측하는 프로그램
- 맛집 리스트를 등록하고 조회하는 프로그램
- 프로그램이 시작되면 콘솔로 아이디와 비밀번호를 입력 받아 로그인 인증을 한 후, 로그인에 성공하면 친구 리스트 html 파일을 생성하는 프로그램 **(결정!)**

### 큰 틀 짜기

1. Controller : 프로그램의 전반적인 처리를 담당하는 클래스. 이 클래스를 통하여 로그인 정보를 인증하고 결과 파일을 출력한다.
2. DBConnection : DB와의 통신을 담당하는 인터페이스. Controller는 DBConnection 인터페이스에만 의존하도록 설계했다.
3. DBConnectionUtil : DBConnection을 사용할 때 편의성을 제공하기 위한 유틸리티 클래스
4. FriendProfile : 친구 목록에 나타날 친구 프로필 정보
5. MyProfile : 나의 프로필 정보
6. Profile : 나와 친구의 프로필의 중복을 제거하기 위한 추상 클래스
7. UserInfo : 유저의 로그인 정보를 담는 클래스
8. FriendScreen : 친구 목록의 결과 화면을 html로 만들어주는 클래스
9. Tags : HTML 파일에 사용될 태그를 만들어주는 클래스

### DB 테이블 구상

&nbsp;&nbsp; DB를 구상함에 있어서 약간의 의견 차이가 생겼었다. 아래는 함께 토의했던 내용이다.

- 공통 의견
  * id와 password의 정보를 담고 있는 로그인 인증 테이블 login
  * 유저의 프로필 정보를 담고 있는 테이블 profile
- 개별 의견
  * 유저 별 친구 목록을 담고 있는 테이블 friend
    + 박정우 : 각 유저 별로 친구 테이블을 만들어서 친구 목록을 저장
    + 강동희 : 중복이 되더라도 하나의 테이블에 유저 별 아이디와 친구의 아이디를 저장

&nbsp;&nbsp; friend 테이블을 어떻게 설계 하는지에 대해 여러 의견이 있었는데, 결론적으로 서로 본인이 주장한 구조대로 각각 개발을 한 후에 적용해보면서
비교 & 결정하기로 하였다.

<br>

## 개발 과정

### DB 설계하기

&nbsp;&nbsp; DB 테이블은 다음과 같이 선언하였다.

![tables](/images/kktalk/tables.png)

- friend : 유저 별 친구 목록이 담긴 테이블
- login : 가입되어 있는 아이디와 비밀번호가 담긴 테이블
- profile : 각 유저 별 프로필 정보가 담긴 테이블

### DBConnection 구현하기

&nbsp;&nbsp; DBConnection 인터페이스는 아래와 같이 2개의 메서드로 정의되어 있다.

- 로그인 정보를 매개변수로 받아 기존에 가입된 계정인지 인증하는 **authenticateUser(UserInfo userInfo)** 메서드 (반환 타입 : boolean)
- 로그인 정보를 매개변수로 받아 친구 목록을 반환하는 **getFriends(UserInfo userInfo)** 메서드 (반환 타입 : List)

### HTML 파일 생성하기

- IndexScreen, FriendScreen : 유저의 로그인 정보와 친구 리스트를 이용하여 동적으로 html 파일을 생성하는 클래스
- Tags : html 파일을 편리하게 생성하기 위해 정의된 클래스

<br>

## 결과

![init](/images/kktalk/초기%20화면.png)

![login-fail](/images/kktalk/로그인%20실패.png)

![login-success](/images/kktalk/로그인%20성공.png)

![friends](/images/kktalk/친구%20리스트%20화면.png)

### 느낀 점

- 다형성을 활용하는 것이 얼마나 중요한 것인지 느낀 프로젝트였다.
- 초기 토의 과정이 얼마나 중요한 과정인지 깨닫는 시간이었다.

### 개선할 점

- 다음에는 git과 GitHub을 이용하여 버전 관리 및 협업하는 과정을 경험하고 싶다.
- 오류 상황을 잘 숙지해두어 같은 실수를 반복하지 말아야겠다.
  * 자바 예외
    + NullPointerException : Connection 변수에 null 값이 할당된 상태에서 사용하다가 발생한 예외
    + SQLException : ResultSet을 next() 메서드 호출 없이 사용하다가 발생한 예외
  * MySQL 에러
    + ERROR 1064 (42000) : 테이블 생성 시 한 컬럼에 타입을 지정해주지 않아 발생한 에러

---
[<== 부트캠프 10일차](../bootcamp-day10) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 14일차 ==>](../bootcamp-day14)