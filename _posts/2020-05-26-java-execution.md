---
layout: post
title:  "Java 실행 과정"
date:   2020-05-26 11:04:43
categories: java
tags: java jvm
---

## Java 실행 과정

1. 자바 파일을 자바 컴파일러가 바이트 코드 형태인 클래스 파일로 변환을 한다.
2. 클래스 로더가 실행될 클래스 파일을 메모리에 로드 후 초기화 작업을 수행한다.
3. 메소드와 클래스 변수들을 해당 메모리 영역에 배치한다.
4. 클래스 로드가 끝난 후 JVM은 main 메소드를 찾아 지역변수, 객체변수, 참조변수를 스택에 쌓는다.
5. 다음 라인을 진행하면서 상황에 맞는 작업(함수 호출, 객체 할당 등)을 수행한다.

![001](/images/20200526/java-execution.jpg)

### 용어

- Class Loader
  - JVM 내로 클래스를 로드하고 링크를 통해 배치하는 작업을 수행하는 모듈
  - 런타임시 동적으로 클래스를 로드
- Execution Engine
  - Class Loader를 통해 JVM 내의 Runtime Data Area에 배치된 바이트 코드를 실행
  - Java 바이트 코드를 명령어 단위로 읽어서 실행
- Garbage Collector
  - 애플리케이션이 생성한 객체의 생존 여부를 판단하여 더 이상 사용되지 않는 객체를 해제하는 방식으로 메모리를 자동 관리
- Runtime Data Area
  - JVM이 운영체제 위에서 실행되면서 할당받는 메모리 영역
  - Class Loader에서 준비한 데이터들을 보관
