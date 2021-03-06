---
layout: post
description: >
  왜 Spring Batch를 사용해야하는가
author: cgkim449
---

# [Spring Batch] 스프링 배치를 사용하는 이유
### 배치 프로그램이란
- **정해진 시간**에 **일괄적**으로 작업을 처리하는 프로그램 (대체로 대용량 데이터)  
- 배치 프로그램은 보이진 않지만 늘 존재한다.
  - 서비스를 운영하는 관점에서 주기적으로 작업을 처리하려면 배치 프로그램을 사용해야 한다.

## 1. 현업에서는 배치 프로그램을 언제 사용하는가
### 배치 프로그램이 필요한 상황
1. 필요한 데이터를 모아서 처리해야 할 때  
(ex. 월별 거래 명세서 생성)
2. 일부러 지연시켜 처리할 때  
(ex. 주문한 상품을 바로 배송 처리 하지 않고, 일정 시간 뒤 처리(주문을 하고나서 취소하는 경우가 많기 때문))
3. 자원을 효율적으로 활용하기 위해  
(ex. 트래픽이 적은 시간 대에 서버 리소스를 활용)  
  - 낮에는 트래픽이 많다. 그런데 배치 프로그램은 꼭 유저가 요청했을때 실행할 필요가 없다.

### 현업에서 배치 프로그램을 사용하는 2가지 관점
#### 1) 데이터를 처리하는 관점
- 각 서비스의 데이터를 데이터 웨어하우스에 저장할 때 = ETL(Extract-Transform-Load)
- 아마존에서 연관 상품을 추천하는 데이터 모델을 만들 때
- 유저의 리텐션 등 마케팅에 참고할 데이터 지표를 집계할때

#### 2) 서비스 배치 프로그램에 대한 관점
- 메시지, 이메일, 푸시 등을 발송할 때
- 데이터를 마이그레이션 할 때(스키마가 변경돼었을때)
- 실패한 트랜잭션을 재처리할 때
- 쿠폰, 포인트 등이 만료되었을 때 소진시키는 처리를 할 때
- 월말 또는 월초에 특정 데이터를 생성할 때 - 월별 거래 명세서

## 2. 그렇다면 왜 `Spring` Batch를 써야하는가
Spring Batch의 정의 :  
```
A lightweight, comprehensive batch framework  
designed to enable the development of robust batch applications  
vital for the daily operations of enterprise systems.  
```
`(Spring 기반) 가볍고 다양한 기능을 가진 배치 프레임 워크`  
-> 스프링 배치를 사용하면 우리가 기능을 잘 조합해서 원하는 배치 프로그램을 쉽게 구현할 수 있다  
`견고한 배치 어플리케이션 개발이 가능하도록 디자인 되어있다.`  
-> 보다 안정적으로 개발할 수 있다  
`기업 시스템의 매일 운영에 필수적인 수준`  
-> 수많은 데이터를 처리하는 기업시스템에서 매일매일 처리를 해도 불편함이 없을 수준  

**`Spring` 기반이라는게 장점이다!**  
- 스프링으로 작성된 코드를 재활용할 수 있다. 기존 Spring 프로젝트의
코드를 활용하거나 모듈을 이용할 수 있다.
  - 예: 우리가 만든 ProfileDao
- 만약 배치용 코드를 새로 작성한다면 Python, Shell script, Go 등 다른
언어로 비슷한 처리를 새로 구현해야 한다.
- 배치 처리를 위한 로직을 새로 만드는 것보다 **스프링 배치에서 제공하는
기본적인 기능을 이용**하는게 생산성 있는 방법이다.  
  - 스프링 배치가 견고하고 가볍고 다양한 기능을 제공하기 때문에 사용하는게 아주 어렵지 않다.
  - 스프링 배치는 다양한 기능을 제공한다
    - 로깅/추적, 트랜잭션 관리, 작업 처리 통계, 재시작, 건너뛰기 등 **대용량 처리**에 필수적인 기능들을 제공
    - 멀티 코어 또는 멀티 서버에서 처리를 분산하는 기능을 제공한다.  
  
> 즉, 프레임워크가 제공하는 기능들을 이해하고 잘 사용하면 된다!  

## 참고
fast campus 스프링 배치 강의를 듣고 정리하였습니다.