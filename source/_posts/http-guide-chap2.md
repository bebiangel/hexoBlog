---
title: http-guide-chap2
date: 2019-08-11 13:48:16
tags: [HTTP, 스터디]
categories: ["스터디"]
---
# 2장 URL과 리소스

## 2.1 인터넷의 리소스 탐색하기

URL은 브라우저가 정보를 찾는데 필요한 리소스의 위치를 가리킨다.

ex) [http://www.joes-hardware.com/seasonal/index-fall.html](http://www.joes-hardware.com/seasonal/index-fall.html) 이라는 URL 을 불러온다고 하자.

- http는 URL의 스킴으로 웹 클라이언트가 리소스에 어떻게 접근하는지 알려준다.
- www.joes-hardware.com은 서버 위치로 웹 클라이언트가 리소스가 어디에 호스팅 되어 있는지 알려준다.
- /seasonal/index-fall.html은 리소스의 경로로 서버에 존재하는 로컬 리소스들 중에서 요청받은 리소스가 무엇인지 알려준다.

URL을 사용하면 리소스를 일관된 방식으로 지칭할 수 있으며, 대부분의 URL은 "**스킴://서버위치/경로**" 구조로 이루어져있다.

## 2.2 URL 문법

대부분의 URL은 일반 URL 문법을 따르며, 서로 다른 URL 스킴도 형태와 문법 면에서 매우 유사하다.

스킴의 문법은 일반적으로 9개로 나뉜다.

***`<스킴>://<사용자 이름>:<비밀번호>@<호스트>:<포트>/<경로>;<파라미터>?<질의>#<프래그먼트>`***

### 2.2.1 스킴: 사용할 프로토콜

스킴은 주어진 리소스에 어떻게 접근하는지 알려주는 중요한 정보로, 어떤 프로토콜을 사용하여 리소스를 요청해야하는지 알려준다. 

### 2.2.2 호스트와 포트

URL의 호스트와 포트 컴포넌트가 존재한다.

- 호스트 컴포넌트: 접근하려고 하는 리소스를 가지고 있는 인터넷상의 호스트 장비를 가리킨다.
- 포트 컴포넌트: 서버가 열어놓은 네트워크 포트를 가리킨다.

### 2.2.3 사용자 이름과 비밀번호

많은 서버가 자신이 가지고 있는 데이터를 제공하기 전에 사용자 이름과 비밀번호를 요구한다.

***`ftp://ftp.prep.ai.mit.edu/pub/gnu`***

→ 표준 스킴, 호스트, 경로만 존재한다.

***`ftp://anonymous@ftp.prep.ai.mit.edu/pub/gnu`***

→ 기본 사용자 이름 값은 'anonymous', 비밀번호는 브라우저마다 가지고 있는 기본값을 사용한다.

***`ftp://anonymous:my_passwd@ftp.prep.ai.mit.edu/pub/gnu`***

→ 사용자 이름 'anonymous' 비밀본호 'my_passwd'를 ':'문자로 구분하여 사용한다.

'@'는 URL에서 사용자 이름과 비밀번호 컴포넌트를 분리한다.

### 2.2.4 경로

URL의 경로 컴포넌트는 리소스가 서버의 어디에 있는지 알려준다. 

***`http://www.joes-hardware.com:80/seasonal/index-fall.html`***

'**/**' 문자를 기준으로 경로조각으로 나뉘며, 각각의 경로조각은 자체만의 파라미터 컴포넌트를 가질 수 있다.

### 2.2.5 파라미터

애플리케이션이 서버에 정확한 요청을 하기 위해 필요한 입력 파라미터를 받는데 사용한다.

URL의 파라미터 컴포넌트는 **이름=값** 쌍의 리스트로 URL 나머지로 부터 ';' 문자로 구분하여 URL에 기술한다.

리소스에 접근하는데 필요한 어떤 추가 정보든 전달할 수 있다.

***`ftp://prep.ai.mit.edu/pub/gnu;type=d`***

이름은 'type'이고, 값은 'd'인 type=d라는 단 한 개의 파라미터를 전달한다.

### 2.2.6 질의 문자열

데이터베이스 같은 서비스들은 요청받을 리소스 형식의 범위를 좁히기 위해서 질문이나 질의를 받을 수 있다.

***`http://www.joes-hardware.com/inventory-check.cgi?item=12731`***

위 URL은 아이템 번호가 '12731'의 재고가 있는지 확인하기 위해서 사용된다.

'?' 문자에 이어서 오는 값을 질의 컴포넌트라고 한다.

'&'문자로 나뉜 '**이름=값**' 쌍 형식의 질의 문자열을 많이 사용한다.

### 2.2.7 프래그먼트

리소스의 특정 부분을 가리킬 수 있도록, URL은 리소스 내의 조각을 가리킬 수 있는 프래그먼트 컴포넌트를 제공한다. 프래그먼트는 URL의 오른쪽에 **#** 문자에 이어서 온다.

***`http://www.joes-hardware.com/tools.html#drills`***

drills라는 프래그먼트는 /tools.html 웹페이지의 일부를 가리킨다.

HTTP서버는 객체 일부가 아닌 전체만 다루기 때문에, 클라이언트는 서버에 프래그먼트를 보내지 않는다.

**브라우저가 서버로부터 리소스를 내려받은 후에 프래그먼트를 사용해서 보고자 하는 리소스의 화면을 보여준다.**

## 2.3 단축 URL

웹 클라이언트는 몇몇 단축 URL을 인식하고 사용한다. 

상대 URL은 리소스 안에 있는 리소스를 간결하게 기술하는데 사용할 수 있다.

### 2.3.1. 상대 URL

URL은 상대 URL과 절대 URL 두가지로 나뉜다. 

**상대 URL**

- 리소스에 접근하는데 필요한 모든 정보를 가지고 있지 않다.
- URL을 짥게 표시하는 방식이다.
- HTML 작성자는 URL에 스킴과 호스트 그리고 다른 컴포넌트들을 모두 입력하지 않아도 된다.

**절대 URL**

- 리소스에 접근하는데 필요한 모든 정보를 가지고 있다.

    [](https://www.notion.so/a1668f9c233549789f4f4623977ba522#e37e3cfe7a9a4a369a5fab5020d45117)

### URL 확장

사용자가 URL을 입력한 다음이나 입력하고 있는 동안에 자동으로 URL을 확장한다.

- **호스트 명 확장**

    'yahoo'를 주소창에 입력한다면, 브라우저는 호스트 명에 자동으로 'www.'과 '.com'을 붙여서 'www.yahoo.com'을 만든다.

- **히스토리 확장**

    URL 입력하는 시간을 줄이고자, 과거에 사용자가 방문했던 URL의 기록을 저장해 놓는 것이다.

    URL의 시작부분을 입력하면 자동으로 전체 URL을 보여준다.

## 2.4 안전하지 않은 문자

## 2.4 안전하지 않은 문자

- 프로토콜이 데이터를 전송하기 위해서 서로 다른 장치를 가지고 있기 때문에, 어떤 인터넷 프로토콜을 통해서든 안전하게 전송될 수 있도록 URL을 설계하는 것이 중요하다.
- 안전한 전송이란, 정보가 유실될 위험 없이 URL을 전송할 수 있다는 것을 의미한다.
- **이스케이프**라는 기능을 추가하여, URL에 있는 안전하지 않는 문자들을 표현할 수 있도록 인코딩 방식을 사용하여 이동성과 완성도를 높였다.