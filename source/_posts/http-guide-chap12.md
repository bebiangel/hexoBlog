---
title: HTTP 완벽가이드 12장
date: 2019-11-24 18:49:17
tags: [HTTP, 스터디]
categories: [스터디]
toc: true
---
# 12장 기본인증

> 목표 : HTTP 인증과 그것의 기본이 되는 기본 인증을 알아본다.

## 1. 인증

### 1.1 HTTP의 인증요구/응답 프레임워크

![](/images/http-guide-chap12.png)

### 1.2 인증 프로토콜과 헤더

- HTTP에는 기본 인증과 다이제트스트 인증이 존재한다.

|  단계  |    헤더        |  설명 | 메서드/상태 |
|----------|:-------------|------|------|
| 요청 |  | 첫 번째 요청에는 인증 정보가 없다 |GET|
| 인증요구 | WWW-Authenticate  GET    |서버는 사용자에게 사용자 이름과 비밀번호를 제공하라는 의미로 401 상태 정보와 함께 요청을 반려한다.|401 Unauthorized|
| 인증 | Authorization |    클라이언트는 인증 알고리즘과 사용자 이름,비밀번호를 기술한 Authorization 헤더를 보낸다. |GET|
| 성공 | Authentication-Info |    인증 정보가 정확하면, 서버는 문서와 함께 응답한다. |200 OK|


## 2. 기본 인증

- 기본 인증은 `HTTP/1.0`에 기술되어 있었지만, `RFC 2617`로 옮겨졌다.

### 2.1 기본 인증의 예

1. 사용자가 리소스를 요청한다.
2. 서버가 `WWW-Authenticate` 헤더와 함께 리소스를 접근하는데 필요한 비밀번호를 요구하는 `401 Authorization Required` 응답을 반환한다.
3. 브라우저에서 401 응답을 받고 사용자 이름과 비밀번호를 입력하는 화면을 띄운다. 브라우저는 정보들을 콜론으로 이어 붙이고, `base-64`방식으로 인코딩하고, Authorization 헤더에 그 값을 담아 서버로 다시 보낸다.
4. 서버는 사용자 이름과 비밀번호를 디코딩하고, 그 값이 정확한지 확인하고, 문제 없으면 HTTP 200 OK 메시지와 함께 요청받았던 문서를 보낸다.

### 2.2 Base-64 사용자 이름/비밀번호 인코딩

- HTTP 기본 인증은 사용자 이름과 비밀번호를 클론으로 이어서 합치고, base-64인 코딩 메서드를 사용해 인코딩 한다.
- base-64 인코딩은 8비트 바이트로 이루어져 있는 시퀀스를 6비트 덩어리의 시퀀스로 변환한다.
- base-64 인코딩은 국제 문제나 HTTP 헤더에서 사용할 수 없는 문자를 보내야 할 때 유용하다.

### 2.3 프락시 인증

- 중개 프락시 서버를 통해 인증 할 수 있다.
- 프락시 서버에서 접근 정책을 중앙 관리 할 수 있기 때문에, 회사 리소스 전체에 대해 통합적인 접근 제어를 하기 위해서 프락시 서버를 사용하면 좋다.

## 3. 기본 인증의 보안 결함

- 기본 인증은 사용자 이름과 비밀번호를 쉽게 디코딩 할 수 있는 형식이므로 노출되어서는 안된다.
- 메시지의 인증 헤더를 건드리지 않고, 그 외 다른 부분을 수정해서 트랜잭션의 본래 의도를 바꿔버리는 프락시나 중개자가 중간에 개입하는 경우, 기본 인증은 정상적인 동작을 보장하지 않는다.