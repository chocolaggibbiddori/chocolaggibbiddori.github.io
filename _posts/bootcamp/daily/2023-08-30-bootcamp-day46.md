---
layout: single
title: "부트캠프 46일차"
tag: [bootcamp, spring framework, spring boot, swagger]
---

# Spring Boot

## API를 작성하는 다양한 방법

### GET API 만들기

1. @RequestMapping(method = RequestMethod.GET)
2. @GetMapping
3. @PathVariable
   - URL 경로 상의 값을 매개변수로 받아올 수 있음
   - 매칭되는 값이 없으면 500 에러 발생
4. @RequestParam
   - URI에서 ? 이후의 파라미터를 매개변수로 받아올 수 있음
   - 매칭되는 값이 없으면 400 에러 발생
   - required 속성을 false로 지정해주면 매칭되지 않아도 에러가 발생하지 않음(기본값으로 초기화 됨)
   - required = false 시, defaultValue 속성으로 기본 값 직접 지정 가능
   - Map으로 모든 key=value 쌍을 받아올 수 있음
5. 객체로 직접 매핑 가능
   - 변수명으로 매핑
   - 매핑되지 않는 값들은 기본값으로 초기화 됨

### POST API 만들기

1. @RequestMapping(method = RequestMethod.POST)
2. @PostMapping
3. @RequestBody
   - body의 데이터를 객체에 매핑
   - 매칭된 데이터가 없는 필드는 기본 값으로 초기화
   - Map으로 모든 데이터 쌍을 가져올 수 있음

<br>

## SWAGGER

1. 스프링 부트와의 호환성 문제로 스프링 부트 버전을 2.5.6으로 다운
2. @ApiOperation
3. @ApiParam
   - required 속성으로 해당 파라미터가 필수인지 아닌지 지정 가능

<br>

___

**[참고]**  
장정우, 스프링 부트 핵심 가이드(스프링 부트를 활용한 애플리케이션 개발 실무)  
깃허브 주소 : [https://github.com/chocolaggibbiddori/springboot-wikibooks](https://github.com/chocolaggibbiddori/springboot-wikibooks)

[<== 부트캠프 42일차](/bootcamp-day42) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 48일차 ==>](/bootcamp-day48)