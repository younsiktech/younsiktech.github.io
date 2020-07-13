---
layout: post
title:  "스프링 - 코어 - 빈 스코프"
date:   2020-06-24 15:09:43
categories: spring
tags: spring core bean scope
---

#### 스프링 빈 스코프

- singleton (default)
  - 기본 싱글톤 스코프
  - 하나의 인스턴스가 생성되고 이후에는 해당 인스턴스를 공유한다
- prototype
  - 어플리케이션에서 요청시마다 스프링이 새 인스턴스를 생성
  - DI에 Bean에 대한 요청이 들어올때마다 Bean을 생성한다.
  - 멀티스레드 환경에서 사용되는 Bean에 많이 사용된다.
- session
  - Spring MVC web application에서 사용
  - HTTP 세션별로 인스턴스화되며 세션이 끝나면 소멸
- Global Session
  - 글로벌 HTTP 세션이 생성될때 bean을 생성한다
  - 포틀릿 웹 용도
  - 전역 세션 스코프의 bean은 같은 포탈 어플리케이션 내의 모든 포틀릿 사이에서 공유할 수 있다
- request
  - Spring MVC web application에서 사용
  - HTTP 요청별로 인스턴스화되며 요청이 끝나면 소멸
- Custom
  - 스코프의 이름, 규칙을 개발자가 직접 정의할 수 있다.
  - org.springframework.beans.factory.config.Scope를 구현하고 스프링 설정에 등록하여 사용
- Thread (3.x에서만 사용, 이후는 Custom에서 SingleThreadScope 구현 필요)
  - 새 스레드에서 요청하면 새로운 bean 인스턴스 생성
  - 같은 스레드에 대해서는 같은 bean 인스턴스가 반환
- application (4.x 부터 등장)
  - Spring MVC web application에서 사용
  - 서블릿 컨텍스트가 만들어질 때 새로운 bean을 생성한다.
- websocket (5.x 부터 등장)
