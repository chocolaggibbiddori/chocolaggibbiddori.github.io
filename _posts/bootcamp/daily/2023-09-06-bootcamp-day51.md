---
layout: single
title: "부트캠프 51일차"
tag: [bootcamp, spring framework, spring boot, jpa]
---

# Spring Boot

## JPA 연관관계 매핑

&nbsp;&nbsp; 객체와 테이블간의 패러다임 불일치를 해결하기 위해서 JPA가 제공하는 연관관계 매핑 방법에 대해 다뤘다.

### 일대일 매핑

1. @OneToOne
   - 양방향 매핑 시 mappedBy 속성을 부여한 쪽이 주인이 됨. 값은 반대 엔티티의 필드명

2. @JoinColumn
   - 외래키 지정 어노테이션. name 속성으로 외래키 컬럼의 이름을 정함

> ***주의!***  
> &nbsp;&nbsp; 양방향 매핑 시 toString() 메서드에서 순환 참조 오류가 발생할 수 있다. 해결 방법은 @ToString.Exclude 어노테이션을 이용해서 한 쪽을 막아주는 것

### 다대일 매핑

1. @ManyToOne
   - 다대일 관계에서 '다' 쪽 엔티티에 달아준다. 이쪽이 연관관계 주인이 됨

2. @OneToMany
   - 다대일 양방향 관계에서 '일' 쪽 엔티티에 달아준다. mappedBy 속성 필요
   - 이 어노테이션이 붙은 필드는 주로 List 인터페이스 타입으로 만듬

> ***주의!***  
> &nbsp;&nbsp; @OntToMany 어노테이션이 붙은 엔티티는 연관관계 주인이 아니므로 List 인터페이스의 `add()` 메서드로 인스턴스를 추가하더라도 데이터베이스에는 반영되지 않는다.

### 일대다 매핑

1. @OneToMany
2. @JoinColumn
   - 상대 엔티티에 외래키가 생김

### 다대다 매핑

&nbsp;&nbsp; 일반적으로 사용하지 않는다. 다대다 연관관계인 경우 중간 테이블이 생성되어 불편하므로 일대다-다대일로 매핑할 수 있도록 중간 엔티티를 만드는 것이 좋다.

<br>

## 영속성 전이

&nbsp;&nbsp; 영속성 전이(cascade)란 특정 엔티티의 영속성 상태를 변경할 때 그 엔티티와 연관된 엔티티의 영속성에도 영향을 미쳐 영속성 상태를 변경하는 것을 의미한다.
@OneToOne, @OneToMany, @ManyToOne, @ManyToMany 어노테이션의 cascade() 속성을 통해 정의한다.

### CascadeType

| 종류      | 설명                               |
|---------|----------------------------------|
| ALL     | 모든 영속 상태 변경에 대해 영속성 전이를 적용       |
| PERSIST | 엔티티가 영속화될 때 연관된 엔티티도 함께 영속화      |
| MERGE   | 엔티티를 영속성 컨텍스트에 병합할 때 연관된 엔티티도 병합 |
| REMOVE  | 엔티티를 제거할 때 연관된 엔티티도 제거           |
| REFRESH | 엔티티를 새로고침할 때 연관된 엔티티도 새로고침       |
| DETACH  | 엔티티를 영속성 컨텍스트에서 제외하면 연관된 엔티티도 제외 |

<br>

## 고아 객체

&nbsp;&nbsp; 부모 엔티티와 연관관계가 끊어진 엔티티를 고아 객체라고 한다. JPA는 고아 객체를 자동으로 제거해주는 기능을 제공한다.

- orphanRemoval
   - true로 설정하면 고아 객체를 자동으로 제거
   - 주로 컬렉션 필드에서 사용
   - cascade 속성에 REMOVE가 포함되어 있어야 함

<br>

___

**[참고]**  
장정우, 스프링 부트 핵심 가이드(스프링 부트를 활용한 애플리케이션 개발 실무)  
깃허브 주소 : [https://github.com/chocolaggibbiddori/springboot-wikibooks](https://github.com/chocolaggibbiddori/springboot-wikibooks)

[<== 부트캠프 50일차](/bootcamp-day50) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [부트캠프 53일차 ==>](/bootcamp-day53)