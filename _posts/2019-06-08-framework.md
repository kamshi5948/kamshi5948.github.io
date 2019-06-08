---
layout: post
title: "Framework"
tags: [Framework]
comments: true
---

##### S/W아키텍처와 Framework
 * 소프트웨어 구조와 골격 제공
 * 라이브러리, 구성요소들을 엮어 주는 역할
 * 도구와 생산성 향상
 * 디자인패턴 : 재사용이 용이하도록 일반화한 명세


## pom.xml
 * compile : 컴파일 시점에 필요하고 배포할대도 포함이 되어진다.
 * provided : 컴파일 시점에는 필요하지만 배포시점에는 불필요한 라이브러리로 JDK, servlet API, Java EE API 등이 해당된다.
 * runtime : 컴파일 시점에는 필요없지만 runtime 시점에 필요한 라이브러리 system Repository 에서 검색을 하지 않고 명시된 위치에서 Jar를 이용한다.
 * test : 테스트 컴파일과 실행 시점에만 사용됨
 * import : 다른 POM 설정 파일에 정의되어 있는 의존 관계 설정을 현재 프로젝트로 가져옴

## JSESSIONID
 * 세션을 유지하기 위해서 발급하는 키
   http프로토콜은 stateless 하기 때문에 요청시 마다 새로운 연결이 생성되고 응답 후 연결이 끊김
   따라서 상태를 저장하기 위해서 JSESSIONID 쿠키를 클라이언트에 발급해 주고 이 값을 통해 세션을 유지함
   HttpOnly 설정(document.cookie JSESSIONID 값 참조 막을 수 있음)
 * 중요정보는 session에 저장

 ## Transctional
  * propagation : REQUIRED, REQUIRED_NEW, NESTED
  * readOnly
  * rollbackFor
  * timeout

## JPA  
  Java Persistent API, ORM(Oject Relationship Management), hibernate, RDB 테이블을 객체지향적으로 사용하기 위함

## Querydsl
  type-safe한 쿼리를 위한 Domain Specific Language
  SQL query는 문자로 type-check가 불가능하고 실행해 보기 전까지 작동여부 확인이 어렵다.
  java처럼 type-safe하게 개발 할 수 있게 해주는 프레임워크   

## flyway
  DB 마이그레이션 tool, 자동으로 개발DB와 운영DB를 맞춰줌
  
