---
layout: post
title:  "DesignPattern - 전체 정리"
date:   2020-05-14 20:09:43
categories: designpattern
tags: designpattern gof
---

1. prototype 패턴
이미 생성된 객체를 복제해서 새로운 객체를 생성하는 방법
- 객체 생성 방식이나 구성 형태, 표현 방식 등과 무관하게 객체를 생성 하고 싶을때 유용
- 생성할 객체가 run-time 시에 결정 되어 질때 유용

2. singleton 패턴
객체가 생성되는 개수를 제한 하는 형태의 설계가 singleton 패턴 이라 하고 극단적으로 제한되는 객체의 개수가 1개일 때를 감안한 패턴 (최대 N개 까지만 객체를 생성 하도록 제한)
- 패턴 구현시 모든 생성자는 protected 영역에 정의 되어야 함.
- 상속 관계에 놓인 클래스들에 대해 전체적으로 생성되는 객체의 최대 개수를 제한 하고자 할때 유용

3. Observer 패턴
class 또는 object 의 어떤 상태의 변화를 자동으로 알려주도록 설계 하는 방법
분산 시스템이나 이벤트 시스템에 사용