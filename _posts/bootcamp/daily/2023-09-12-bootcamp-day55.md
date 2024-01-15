---
layout: single
title: "부트캠프 55일차"
tag: [ bootcamp, spring framework, spring boot, communication ]
---

# 서버 간 통신

&nbsp;&nbsp; 이번 시간에는 다른 서버로 웹 요청을 보내고 응답을 받는 방법에 대해 학습하였다.

## RestTemplate

> RestTemplate이란?
>
> &nbsp;&nbsp; 스프링에서 HTTP 통신 기능을 쉽게 사용하도록 설계된 템플릿이다.
> RestTemplate을 이용하면 Restful 서비스를 편리하게 만들 수 있다.
> 하지만, 지원 중단(deprecated)된 상태라서 후술할 WebClient 방식도 함께 알아둘 것을 권장한다.

### RestTemplate의 대표적인 메서드

| 메서드             | HTTP 형식 | 설명                                           |
|-----------------|---------|----------------------------------------------|
| getForObject    | GET     | 요청한 결과를 객체로 반환                               |
| getForEntity    | GET     | 요청한 결과를 ResponseEntity 객체로 반환                |
| postForLocation | POST    | 요청한 결과를 헤더에 저장된 URI로 반환                      |
| postForObject   | POST    | 요청한 결과를 객체로 반환                               |
| postForEntity   | POST    | 요청한 결과를 ResponseEntity 객체로 반환                |
| delete          | DELETE  | DELETE 형식으로 요청                               |
| put             | PUT     | PUT 형식으로 요청                                  |
| patchForObject  | PATCH   | 요청한 결과를 객체로 반환                               |
| optionsForAllow | OPTIONS | 해당 URI에서 지원하는 HTTP 메서드를 조회                   |
| exchange        | any     | HTTP 헤더를 임의로 추가할 수 있고, 어떤 메서드 형식에서도 사용할 수 있음 |
| execute         | any     | 요청과 응답에 대한 콜백을 수정                            |

### RestTemplate 사용하기

1. UriComponentsBuilder
   - URI 객체를 만들기 위한 빌더 클래스
   - path() : 요청할 서버의 경로를 설정
   - encode() : 기본값 UTF-8
   - queryParam() : 요청 파라미터를 설정
   - build() : UriComponents 객체로 변환
2. UriComponents
   - expand() : 경로 변수 설정
3. RestTemplate
   - HTTP 통신 기능을 손쉽게 사용하도록 설계된 템플릿
   - RestTemplate에서 제공하는 메서드들을 통해 여러 요청 및 응답을 처리할 수 있음

```java
package com.chocola.springboot.service;

import com.chocola.springboot.data.dto.MemberDto;
import org.apache.http.client.HttpClient;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import java.net.URI;

@Service
public class RestTemplateService {

    public String getName() {
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/v1/crud-api")
                .encode()
                .build()
                .toUri();

        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<String> responseEntity = restTemplate.getForEntity(uri, String.class);
        return responseEntity.getBody();
    }

    public String getNameWithPathVariable() {
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/v1/crud-api/{name}")
                .encode()
                .build()
                .expand("Chocola")
                .toUri();

        RestTemplate restTemplate = new RestTemplate();
        return restTemplate.getForObject(uri, String.class);
    }

    public String getNameWithParameter() {
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/v1/crud-api/param")
                .queryParam("name", "Chocola")
                .encode()
                .build()
                .toUri();

        RestTemplate restTemplate = new RestTemplate();
        return restTemplate.getForObject(uri, String.class);
    }

    public ResponseEntity<MemberDto> postWithParamAndBody() {
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/v1/crud-api")
                .queryParam("name", "Chocola")
                .queryParam("email", "didrkd684@naver.com")
                .queryParam("organization", "Encore")
                .encode()
                .build()
                .toUri();

        MemberDto memberDto = new MemberDto("Nafla", "nafla@gmail.com", "makitrain");
        RestTemplate restTemplate = new RestTemplate();
        return restTemplate.postForEntity(uri, memberDto, MemberDto.class);
    }

    public ResponseEntity<MemberDto> postWithHeader() {
        URI uri = UriComponentsBuilder
                .fromUriString("http://localhost:9090")
                .path("/api/v1/crud-api/add-header")
                .encode()
                .build()
                .toUri();

        MemberDto memberDto = new MemberDto("Chocola", "didrkd684@naver.com", "Encore");

        RequestEntity<MemberDto> requestEntity = RequestEntity
                .post(uri)
                .header("my-header", "Wikibooks API")
                .body(memberDto);

        RestTemplate restTemplate = new RestTemplate();
        return restTemplate.exchange(requestEntity, MemberDto.class);
    }
}
```

<br>

## WebClient

&nbsp;&nbsp; 스프링 부트 최신 버전에서는 RestTemplate이 지원 중단되어 WebClient를 사용할 것을 권고하고 있다.

### WebClient 사용하기

1. create() 메서드를 이용한 생성
2. builder() 메서드를 이용한 생성
   - defaultHeader() : WebClient의 기본 헤더 설정
   - defaultCookie() : WebClient의 기본 쿠키 설정
   - defaultUriVatiable() : WebClient의 기본 URI 확장값 설정
   - filter() : WebClient에서 발생하는 요청에 대한 필터 설정

```java
package com.chocola.springboot.service;

import com.chocola.springboot.data.dto.MemberDto;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.reactive.function.client.WebClient;
import reactor.core.publisher.Mono;

@Service
public class WebClientService {

    public String getName() {
        WebClient webClient = WebClient.builder()
                .baseUrl("http://localhost:9090")
                .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .build();

        return webClient.get()
                .uri("/api/v1/crud-api")
                .retrieve()
                .bodyToMono(String.class)
                .block();
    }

    public String getNameWithPathVariable() {
        WebClient webClient = WebClient.create("http://localhost:9090");

        ResponseEntity<String> responseEntity = webClient.get()
                .uri(ub -> ub.path("/api/v1/crud-api/{name}").build("Chocola"))
                .retrieve()
                .toEntity(String.class)
                .block();

        return responseEntity.getBody();
    }

    public String getNameWithParameter() {
        WebClient webClient = WebClient.create("http://localhost:9090");

        return webClient.get().uri(ub -> ub
                        .path("/api/v1/crud-api/param")
                        .queryParam("name", "Chocola")
                        .build())
                .exchangeToMono(cr -> {
                    if (cr.statusCode().equals(HttpStatus.OK)) {
                        return cr.bodyToMono(String.class);
                    } else {
                        return cr.createException().flatMap(Mono::error);
                    }
                })
                .block();
    }

    public ResponseEntity<MemberDto> postWithParamAndBody() {
        WebClient webClient = WebClient.builder()
                .baseUrl("http://localhost:9090")
                .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .build();

        MemberDto memberDto = new MemberDto("Chocola", "didrkd684@naver.com", "Encore");
        return webClient.post().uri(ub -> ub.path("/api/v1/crud-api")
                        .queryParam("name", "Nafla")
                        .queryParam("email", "nafla@gmail.com")
                        .queryParam("organization", "makitrain")
                        .build())
                .bodyValue(memberDto)
                .retrieve()
                .toEntity(MemberDto.class)
                .block();
    }

    public ResponseEntity<MemberDto> postWithHeader() {
        WebClient webClient = WebClient.builder()
                .baseUrl("http://localhost:9090")
                .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .build();

        MemberDto memberDto = new MemberDto("Chocola", "didrkd684@naver.com", "Encore");
        return webClient.post().uri(ub -> ub.path("/api/v1/crud-api/add-header").build())
                .bodyValue(memberDto)
                .header("my-header", "Wikibooks API")
                .retrieve()
                .toEntity(MemberDto.class)
                .block();
    }
}
```

___

**[참고]**  
장정우, 스프링 부트 핵심 가이드(스프링 부트를 활용한 애플리케이션 개발 실무)  
깃허브
주소 : [https://github.com/chocolaggibbiddori/springboot-wikibooks](https://github.com/chocolaggibbiddori/springboot-wikibooks)

[<== 부트캠프 54일차](/bootcamp-day54) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 65일차 ==>](/bootcamp-day65)