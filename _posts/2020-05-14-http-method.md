---
layout: post
title:  "HTTP - 메소드"
date:   2020-05-14 20:05:35
categories: http
tags: http
---

### 설명

![000](/images/20200514/http-method.jpeg)

- GET
  - GET 요청 방식은 URI(URL)가 가진 정보를 검색하기 위해 서버 측에 요청하는 형태
  - 즉, request uri에 의해 전송됨. (query string)
  - application/x-www-form-urlencoded (plain/text가 아님.)
- POST
  - POST 요청 방식은 요청 URI(URL)에 폼 입력을 처리하기 위해 구성한 서버 측 스크립트(ASP, PHP, JSP 등) 혹은 CGI 프로그램으로 구성된다.
  - Form Action과 함께 전송되는데, 이때 헤더 정보에 포함되지 않고 데이터 부분(Body)에 요청 정보가 들어가게 된다.
  - Body 패킷을 분석해야만 내용을 알 수 있다.
- PUT
  - POST와 유사한 전송 구조를 가지기 때문에 헤더 이외에 메시지(데이터)가 함께 전송된다.
  - 원격지 서버에 지정한 콘텐츠를 저장하기 위해 사용되며 홈페이지 변조에 많이 악용된다.
- DELETE
  - 원격지 웹 서버에 파일을 삭제하기 위해 사용되며 PUT과는 반대 개념의 메소드.

### REQUEST/RESPONSE

#### request sample

  ![001](/images/20200514/HTTP_Request_Headers2.png)

| HTTP Request 내부 | 설명 |
|:--------|:-------:|
| method : GET | Http 메소드 |
| path : /index.html | 요청 URI |
| HTTP/1.1 | Http 버젼) |
| user-agent: MSIE 6.0; Window NT 5.0 | 사용자 웹 브라우져 종류 |
| accept: test/html; \*/* | 요청 데이터 타입 (응답의 Content-type과 유사) |
| cookie:name=value | 쿠키(인증 정보) |
| referer: http://google.com | 경유지 URL |
| host: www.google.com | 요청 도메인 |

#### response sample

  ![002](/images/20200514/HTTP_Response_Headers2.png)

| HTTP Response 내부 | 설명 |
|:--------|:-------:|
| HTTP/1.1 200 OK | 프로토콜 버젼 및 응답코드 |
| Server: Apache | 웹 서버 정보 |
| Content-type: text/html; charset=UTF-8 | MIME 타입 |
| Content-length : 1593 | HTTP BODY 사이즈 |
| \<html>\<head>..... | HTTP BODY 컨텐츠 |

### 정리

- POST vs PUT
  - 보통 POST = Insert / PUT = Update 로 인식함.
  - 멱등성으로 인해 POST는 서버 자원에 변화가 생기지만 PUT은 변화가 없음.
  - 클라이언트가 리소스 위치를 지정하지 않는 경우는 POST를 사용한다. => 새로운 자원이 생겨남. (멱등하지 않음)
- PUT vs PATCH
  - PUT = 해당 자원 전체 교체 / PATCH = 일부를 변경
  - 따라서, update 이벤트에서는 PATCH가 의미적으로 적합하다고 평가받음.
  - 또한, PUT과 달리 PATCH는 멱등하지 않아 자원의 일부가 변경되므로 멱등성 보장하지 않는다.
- 멱등성
  - 같은 작업을 계속 반복해도 같은 결과가 나오는 경우를 의미함.
  - 예를 들어, 동일한 자원에 대한 GET 요청이라면 클라이언트에 반환되는 모든 응답은 동일해야 한다.
  - 특정 자원에 대한 DELETE의 경우도 자원은 더이상 이용할수 없어야하고 다시 DELETE를 호출한 경우도 자원은 여전히 사용할 수 없는 상태이어야 한다.

### 참조

- [Mozilla MDN HTTP 링크](https://developer.mozilla.org/ko/docs/Web/HTTP)
