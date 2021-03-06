---
title: HTTP 완벽가이드 4장
date: 2019-08-25 15:54:07
tags: [HTTP, 스터디]
categories: [스터디]
toc: true
---
# 4장 커넥션 관리

## 4.1 TCP 커넥션

- 모든 HTTP통신은 네트워크 프로토콜의 집합인 TCP/IP를 통해 이루어진다.
- 커넥션이 맺어지면, 메시지가 손실, 손상되거나 순서가 바뀌지 않고 안전하게 전달된다.

***커넥션 과정***

1) 브라우저가 [`https://bebiangel.github.io`](https://bebiangel.github.io/) 라는 호스트 명을 추출한다.

2) 브라우저가 호스트 명에 대한 IP 주소를 찾는다.

3) 브라우저가 포트 번호(80)를 얻는다.

4) 브라우저가 IP 주소의 80포트로 TCP 커넥션을 생성한다.

5) 브라우저가 서버로 HTTP GET 요청 메시지를 보낸다.

6) 브라우저가 서버에서 온 HTTP 응답 메시지를 읽는다.

7) 브라우저가 커넥션을 끊는다.

### 4.1.1 신뢰할 수 있는 데이터 전송 통로인 TCP

TCP는 HTTP에게 신뢰할만한 통신 방식을 제공한다.

### 4.1.2 TCP 스트림은 세그먼트로 나뉘어 IP 패킷을 통해 전송된다

TCP는 `IP 패킷` 이라는 작은 조각을 통해 데이터를 전송한다.

### 4.1.3 TCP 커넥션 유지하기

TCP커넥션은 여러 개를 가지고 있고, 포트 번호를 통해서 여러개의 커넥션을 유지한다.

`<발신지 IP 주소, 발신지 포트, 수신지 IP주소, 수신지 포트>` 네 가지 항목으로 커넥션을 생성하고, 서로 다른 TCP 커넥션은 일부는 같을 수 있으나 모든 항목이 같을 수는 없다.

## 4.2 TCP의 성능에 대한 고려

- HTTP는 TCP 바로 위에 있는 계층이기 때문에 HTTP 트랜잭션의 성능은 TCP 성능에 영향을 받는다.
- HTTP의 커넥션 최적화 요소들을 통해서 더 좋은 성능의 HTTP 애플리케이션을 설계하고 구현한다.

### 4.2.1 HTTP 트랜잭션 지연

- 트랜잭션을 처리하는 시간은 TCP 커넥션을 설정하고, 요청을 전송하고, 응답 메시지를 보내는것에 비하면 상당히 짧다

***트랜잭션을 지연시키는 원인***

1) 클라이언트가 URI에 기술된 호스트에 방문한적이 없다면, DNS 이름 분석을 사용하여 호스트명을 IP 주소로 변환하는데 수십 초의 시간이 걸린다.

2) 클라이언트는 TCP 커넥션 요청을 서버에게 보내고 서버가 커넥션 허용 응답을 회신하기를 기다린다. 서버에서 수백개의 HTTP 트랜잭션이 만들어진다면 많은 소요시간이 걸린다.

3) 커넥션이 맺어지고 클라이언트는 HTTP 요청을 전송한다. 서버에서는 요청 메시지를 읽고 처리하는데 시간이 소요된다.

4) 서버에서 HTTP 응답을 보내는데 시간이 걸린다.

### 4.2.2 성능 관련 중요 요소

고성능의 HTTP 소프트웨어를 개발하기 위해서 알아야 할 요소들

- TCP 커넥션의 핸드셰이크 설정
- 인터넷의 혼잡을 제어하기 위한 TCP의 느린 시작(slow-start)
- 데이터를 한데 모아 한 번에 전송하기 위한 네이글(nagle) 알고리즘
- TCP의 편승 확인응답을 위한 확인응답 지연 알고리즘
- TIME_WAIT 지연과 포트 고갈

### 4.2.3 TCP 커넥션 핸드셰이크 지연

- TCP 커넥션을 열때, 커넥션을 맺기 위한 조건을 맞추기 위해 연속적으로 IP 패킷을 교환한다.
- 작은 크기의 데이터 전송시에 커넥션을 사용한다면, HTTP 성능을 저하시킬 수 있다.

***핸드셰이크 순서***

1) 커넥션 생성시에 작은 TCP 패킷을 서버에게 보내는데, 커넥션 생성 요청이라는 의미를 가진`SYN` 플래그를 가진다.

2) 서버가 커넥션을 받으면 커넥션 매개변수를 산출하고, 커넥션 요청이 받아들여졌음을 의미하는 `SYN` 과 `ACK` 플래그를 포함한 TCP 패킷을 클라이언트에게 보낸다.

3) 클라이언트는 커넥션이 잘 맺어졌음을 서버에게 다시 확인응답 신호를 보낸다. 클라이언트는 확인응답 패킷과 데이터를 함께 보낼수 있다.

## 4.3 HTTP 커넥션 관리

### 4.3.1 흔히 잘못 이해하는 Connection 헤더

- HTTP Connection 헤더 필드는 토큰을 쉼표로 구분하여 가지고 있으며, 다른 커넥션에 전달되지 않는다.
- Connection 헤더에 있는 모든 헤더 필드는 메시지를 다른 곳으로 전달하는 시점에 삭제되어야한다.

### 4.3.2 순차적인 트랜잭션 처리에 의한 지연

- 여러개의 리소스를 받아와야 하는 화면이 있다면, 각각의 리소스의 HTTP 트랜잭션을 만들어야한다.
- 특정 브라우저의 경우 객체를 화면에 배치하려면 객체의 크기를 알아야 하기 때문에, 모든 객체를 내려받기 전까지는 빈 화면을 보여준다.

***HTTP 커넥션의 성능을 향상시키는 기술***

**병렬(parallel) 커넥션**

여러 개의 TCP 커넥션을 통한 동시 HTTP 요청

**지속(persistent) 커넥션**

커넥션을 맺고 끊는 데서 발생하는 지연을 제거하기 위한 TCP 커넥션의 재활용

파이프라인(piplined) 커넥션

공유 TCP 커넥션을 통한 병렬 HTTP 요청

**다중(multiplexed) 커넥션**

요청과 응답들에 대한 중재

## 4.4. 병렬 커넥션

- 브라우저가 HTML 페이지를 보여주는데 필요한 리소스를 순서대로 받는다면 너무 느리다.
- HTTP는 클라이언트가 여러 개의 커넥션을 맺음으로써 여러 개의 HTTP 트랜잭션을 병렬로 처리할 수 있게 한다.

### 4.4.1 병렬 커넥션은 페이지를 더 빠르게 내려받는다

- 커넥션의 대역폭 제한과 커넥션이 동작하지 않고 있는 시간을 활용하면, 객체가 여러개 있는 웹페이지를 더 빠르게 내려받을수 있을 것이다.
- 각 커넥션의 지연 시간을 겹치게 하면 총 지연 시간을 줄일 수 있다.
- 인터넷 대역폭을 한 개의 커넥션이 다 써버리지 않고, 나머지 객체를 내려받는 데에 남은 대역폭을 사용한다.

### 4.4.2 병렬 커넥션이 항상 더 빠르지는 않다

- 클라이언트의 네트워크 대역폭이 좁을 때는 대부분 시간을 데이터 전송하는 데만 쓸 것이다.
- 제한된 대역폭 내에서 각 객체를 전송받는 것은 느리기 때문에 성능상의 장점이 사라진다.
- 서버는 다른 여러 사용자의 요청도 함께 처리해야 하기 때문에 수백 개의 커넥션을 허용하는 경우는 드물다.

## 4.5 지속 커넥션

- 클라이언트가 같은 사이트에 여러 개의 커넥션을 맺는 속성을 `지역성(site locality)`라 한다.
- HTTP/1.1을 지원하는 기기는 처리가 완료된 후에도 TCP 커넥션을 유지하여 앞으로 있을 HTTP 요청에 재사용이 가능하다.
- 처리가 완료된 후에도 계속 연결된 상태로 있는 TCP 커넥션을 `지속 커넥션`이라고 부른다.

### 4.5.1 지속 커넥션 vs 병렬 커넥션

***병렬 커넥션 단점***

- 각 트랜잭션마다 새로운 커넥션을 맺고 끊기 때문에 시간과 대역폭이 소요된다.
- 각각의 새로운 커넥션은 TCP 느린 시작 때문에 성능이 떨어진다.
- 실제로 연결할 수 잇는 병렬 커넥션의 수에는 제한이 있다.

***지속 커넥션 장점***

- 커넥션을 맺기 위한 사전 작업과 지연을 줄여주고, 커넥션의 수를 줄여준다.

***지속 커넥션 단점***

- 지속 커넥션을 잘못 관리할 경우, 계속 연결된 상태로 잇는 수많은 커넥션이 쌓인다.
- 로컬의 리소스 그리고 원격의 클라이언트와 서버의 리소스에 불필요한 소모를 발생한다.

> **지속 커넥션은 병렬 커넥션과 함께 사용될 때에 가장 효과적이다.**

### 4.5.2 HTTP/1.0+의 Keep-Alive 커넥션

- 커넥션을 맺고 끊는 데 필요한 작업이 없어서 시간이 단축된다.
- keep-alive가 HTTP/1.1 명세에서는 빠졌지만, 아직 널리 사용되고 있어서 HTTP 애플리케이션은 그것을 처리할 수 있게 개발해야 한다.
- keep-alive 헤더 사용은 선택 사항이지만, Connection:Keep-Alive 헤더가 있을 때만 사용 할 수 있다.

## 4.6 파이프라인 커넥션

- 지속 커넥션을 통한 파이프라이닝은 keep-alive 커넥션의 성능을 높여준다.

***파이프라인의 제약조건***

- HTTP 클라이언트는 커넥션이 지속 커넥션인지 확인하기 전까지는 파이프라인을 이어서는 안된다.
- HTTP 응답은 요청 순서와 같게 와야한다.
- HTTP 클라이언트는 커넥션이 언제 끊어지더라도, 완료되지 않은 요청이 파이프라인에 있으면 언제든 다시 요청을 보낼 준비가 되어 있어야 한다.
- HTTP 클라이언트는 POST 요청같이 반복해서 보낼 경우 문제가 생기는 요청은 파이프라인으로 보내면 안된다.

## 4.7 커넥션 끊기에 대한 미스터리

- 커넥션 관리(언제 어떻게 끊기는지)는 명확한 기준이 없다.
- HTTP 클라이언트, 서버 혹은 프락시든 언제든지 TCP 전송 커넥션을 끊을 수 있다.
- 커넥션이 에러가 없더라도 언제든 끊을 수 있다.