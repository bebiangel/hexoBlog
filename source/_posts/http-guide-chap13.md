---
title: HTTP 완벽가이드 13장
date: 2019-11-24 18:49:28
tags: [HTTP, 스터디]
categories: [스터디]
---
# 13장 다이제스트 인증

> 목표: 기본 인증과 호환되는 더 안전한 대체재로서의 다이제스트 인증을 알아본다.

## 1. 다이제스트 인증의 개선점

**특징**

- 비밀번호를 절대로 네트워크를 통해 평문으로 전송하지 않는다.
- 인증 체결을 가로채서 재현하려는 악의적인 사람들을 처단한다.
- 구현하기에 따라서, 메시지 내용 위조를 막는것이 가능하다.
- 그 외의 잘 알려진 형태의 공격을 막는다.

### 1.1 비밀번호를 안전하게 지키기 위해 요약 사용하기

- 다이제스트 인증을 요약하면 "절대로 비밀번호를 네트워크를 통해 보내지 않는다" 이다.
- 클라이언트는 비밀번호를 비가역적으로 뒤섞은 `지문(fingerprint)` 혹은 `요약(digest)`를 보낸다.

### 1.2 단방향 요약

- `요약`은 '정보 본문의 압축'이다.
- 요약은 단방향 함수로 동작하고, 무한 가지의 모든 입력 값들을 유한한 범위의 압축으로 변환한다.
- 만약 비밀번호를 모른다면 서버에게 보내줄 알맞은 요약을 추측하기 위해 많은 시간을 소모하게 된다.
- 요약은 비밀번호를 그대로 전송해야 할 필요성에서 해방시켜준다.

### 1.3 재전송 방지를 위한 난스(nonce) 사용

- `요약`은 비밀번호 자체와 다름 없으므로 요약을 가로챈다면 요약을 서버로 재전송할 수 있다.
- 재전송을 막기 위해서 서버는 클라이언트에게 `난스`라고 불리는 특별한 증표를 넘겨준다.
- 난스는 약 1밀리초마다, 인증할때마다 바뀌게 된다.

### 1.4 다이제스트 인증 핸드 셰이크

![](/images/http-guide-chap13.png)

1. 서버는 난스 값을 계산한다.
2. 서버는 난스를 WWW-Authenticate 인증요구 메시지에 담아, 알고리즘 목록과 함께 클라이언트에 보낸다.
3. 클라이언트는 알고리즘을 선택하고 비밀번호와 그 외 데이터에 대한 요약을 계산한다.
4. 클라이언트는 Authorization 메시지에 요약을 담아 서버에게 돌려준다.
5. 서버는 요약, 선택한 알고리즘, 그 외 보조 데이터들을 받고, 클라이언트가 했던 그대로 요약을 계산한다. 서버는 자신이 계산한 요약과 네트워크로 전송되어 온 요약이 서로 같은지 확인한다.

## 2. 보호 수준(Quality of Protection) 향상

- qop 필드는 클라이언트와 서버가 어떤 보호 기법을 어느 정도 수준으로 사용할 것인지 협상할 수 있게 해준다.

### 2.1 메시지 무결성 보호

- 무결성 보호가 적용되었을 때 계산되는 엔터티 본문은, 메시지 본문의 해시가 아닌 엔터티 본문의 해시이다.
- 송신자에 의해 어떠한 전송 인코딩이 적용되기도 전에 먼저 계산되고 그 후 수신자에 의해 제거된다.

### 2.2 다이제스트 인증 헤더

- 기본, 다이제스트 인증은 WWW-Authentication 헤더에 담겨 전달되는 인증요구와, Authentication 헤더에 담겨 전달되는 인기 응답을 포함한다.
- 다이제스트는 Authentication-Info 헤더를 추가했다.
- 3단계 핸드셰이크를 완성하고 다음번 사용할 난스를 전달하기 위해 인증 성공 후에 전송된다.