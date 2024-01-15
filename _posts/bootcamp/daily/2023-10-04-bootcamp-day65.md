---
layout: single
title: "부트캠프 65일차"
tag: [ bootcamp, docker, jpa ]
---

# Docker 컨테이너 사용

&nbsp;&nbsp; 도커 실습을 진행하였다. 도커는 '컨테이너'를 사용하여 여러 가지 편리한 기능들을 제공하는데,
오늘은 일단 이 컨테이너를 사용해보는 실습을 진행하였다. 컨테이너 내부에서는 MySQL을 사용하였고, host에서 컨테이너 내부의 MySQL 서버에 접속하여 사용해보았다.

## 컨테이너 생성

`docker run -p 10000:3306 -v ~/Desktop/study/docker:/root --name mysql -e MYSQL_ROOT_PASSWORD=1234 -d mysql`

&nbsp;&nbsp; 위 명령어의 각 의미는 다음과 같다.

- `docker run` : 도커에서 컨테이너를 생성한 후 실행하는 명령어
- `-p 10000:3306` : host의 port 10000을 컨테이너의 포트 3306에 매핑한다. 즉, 자바 코드에서는 10000번 포트를 통해 컨테이너 내부의 MySQL 서버로 접속할 수 있다.
- `-v ~/Desktop/study/docker:/root` : host의 `~/Desktop/study/docker` 디렉토리를 컨테이너 내의 `/root` 경로에 마운트한다. 즉, host의 `~/Desktop/study/docker` 디렉토리와 컨테이너 내의 `/root` 디렉토리가 동기화된다. 이로써, 컨테이너와 host 사이에 데이터를 공유할 수 있다.
- `--name mysql` : 컨테이너의 이름을 mysql로 설정한다.
- `-e MYSQL_ROOT_PASSWORD=1234` : `-e`는 환경 변수를 설정하는 명령어인데 지금 상황에서는 MySQL의 루트 사용자의 비밀번호를 1234로 설정하겠다는 뜻이다.
- `-d mysql` : Docker Hub에서 공식 MySQL 이미지를 다운로드하여 컨테이너를 백그라운드에서 실행한다.

## 컨테이너에 접속

&nbsp;&nbsp; 컨테이너를 생성했으니 해당 컨테이너를 조작할 줄 알아야한다. 컨테이너 내에서 터미널을 여는 명령은 다음과 같다.

`docker exec -it mysql bash`

- `docker exec -it` : 컨테이너에서 터미널을 열도록 한다.
- `mysql` : 컨테이너의 이름
- `bash` : 컨테이너 내에서 실행한 shell

위 명령어를 실행하면 해당 컨테이너 내에서 bash shell에 접속할 수 있다. 여기서 MySQL 서버로 접속하여 DB를 만들어준다.

- `mysql -uroot -p1234` : MySQL 서버로 접속. 비밀번호는 컨테이너 생성 시 지정해준 1234이다.
- `CREATE DATABASE stock;` : stock DB 생성
- `exit` : MySQL 서버 접속 해제

stock DB에는 미리 저장되어 있던 sql 파일을 넣어주도록 한다. host에서 `~/Desktop/study/docker` 경로에 `data.sql` 파일을 넣어줬다.

- `mysql -uroot -p1234 stock < data.sql` : `data.sql` 파일을 stock DB에 넣어준다. 즉, `data.sql` 파일에 저장되어 있는 쿼리들이 stock DB에서 실행된다.

   > ***유의할 점!***
   > 
   > &nbsp;&nbsp; `data.sql` 파일의 경로를 잘 봐야 한다! 처음 컨테이너 생성 시 `~/Desktop/study/docker` 디렉토리를 컨테이너 내의 `/root` 디렉토리에 마운트 했으므로,
   > 우리가 host에서 넣어준 `~/Desktop/study/docker/data.sql` 파일이 컨테이너 내의 `/root/data.sql`로 있을 것이다. 내 현재 위치가 `/root`에 있다면 `mysql -uroot -p1234 stock < data.sql`를, `/`에 있다면 `mysql -uroot -p1234 stock < /root/data.sql`를 입력해야 할 것이다.

위 명령어를 실행하면 컨테이너 내 stock DB에 stock_day_table3 테이블이 생성된 것을 확인할 수가 있고, 그 안에 데이터가 7,517,741개 삽입된 것을 확인할 수 있다.

![데이터 삽입](../images/day65/데이터%20삽입.png)

## API 만들기

&nbsp;&nbsp; 이제 기초 작업은 끝났다! 이 컨테이너에 저장된 DB를 이용해서 아주 간단한 API를 만들어보았다. API 명세는
주식 코드와 시작 날짜 및 끝 날짜를 입력하면 두 날짜 사이에 있는 해당 주식 코드의 시간, 시가, 고가, 저가, 종가, 거래량을 반환하는 것이다.

> application.yml

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:10000/stock
    username: root
    password: 1234
    driver-class-name: com.mysql.cj.jdbc.Driver
```

설정 파일의 datasource.url을 살펴보면 포트 번호가 10000인 것을 확인할 수 있다. 컨테이너 생성 시 host의 10000 포트와 컨테이너 내 3306 포트를 매핑했기 때문이다. (3306 포트는 MySQL 서버 포트이다.)

&nbsp;&nbsp; Postman을 이용해서 테스트를 해보면 결과가 잘 나오는 것을 확인할 수 있다. API 만들기 성공!

- 요청 경로 : `http://localhost:8080/stock/A005930?startDate=2020-01-01&endDate=2023-02-01`
- 응답 :
![API 응답](../images/day65/API%20응답.png)

> ***새로 알게 된 JPA 지식!***
> 
> &nbsp;&nbsp; DB의 `stock_day_table3` 테이블을 살펴보면 다음과 같이 되어 있다.
> 
> ![테이블 정보](../images/day65/테이블%20정보.png)
> 
> PK가 두 컬럼으로 지정되어 있어 기존 엔티티 클래스에서 사용하던 `@Id`을 두 번 사용하게 되면 오류가 발생한다!
> 
> &nbsp;&nbsp; 이를 해결하는 위하여 다음과 같이 PK로 설정되는 복수 컬럼을 포함하는 클래스를 새로 정의하는 방법을 사용할 수 있다.
> 
> > Stock.java
> 
> ```java
> @Entity
> @IdClass(StockId.class)
> public class Stock {
> 
>     @Id
>     @Column(name = "st_id")
>     private String id;
>       
>     @Id
>     @Column(name = "st_day")
>     private Date day;
> 
>     @Column(name = "st_start")
>     private int start;
> 
>     ...
> }
> ```
> 
> ```java
> public class StockId implements Serializable {
> 
>     private String id;
>     private Date day;
> }
> ```
> 
> 이 때, `StockId` 클래스는 반드시 `Serializable` 인터페이스를 구현할 것! 그렇지 않으면 에러가 발생한다. 아마도 복수 컬럼을 직렬화하지 못하면 JPA가 해당 PK를 해석하지 못하기 때문인 것 같다.

___

[<== 부트캠프 55일차](/bootcamp-day55)
