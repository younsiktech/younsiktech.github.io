---
layout: post
title:  "스프링 IoC(Inversion of Control)"
date:   2020-05-06 10:39:24
categories: oop
tags: oop 
---

스프링에서의 제어의 역전(Inversion of Control : IoC)는 기존에 개발자가 제어하던 프로그램의 흐름을 프레임워크가 주도하여 객체의 생성부터 생명 주기 관리를 컨테이너가 하게된다.

이로써 제어권이 컨테이너로 넘어가게 되어 의존성 주입(DI), 관점 지향 프로그래밍(AOP)이 가능해진다.

다시 말해 프로그래머가 new 연산(의존 관계)을 사용해서 object를 만들던 것을 스프링 프레임워크의 container로부터 객체를 받아 사용하게 되는 상황을 말한다.

구현은 의존성 주입(DI) 또는 의존성 검색(DL)으로 구현한다.

#### 프로그램 제어 흐름 구조 비교

- 기존
  - main()과 같은 프로그램이 시작되는 entry point에서 object를 결정/생성하고 object의 method를 호출하는 작업을 반복한다.
  - 각 object는 프로그램 흐름을 결정하거나 사용할 object를 구성하는 작업에 능동적으로 참여한다.
  - 모든 종류의 작업을 사용하는 쪽에서 제어하는 구조
- IoC
  - 자신이 사용할 obejct를 생성하거나 선택하지 않고 어떻게 만들어지고 어디서 사용되는지 알 수 없다.
  - 모든 제어 권한을 자신이 아닌 다른 대상에게 위임한다.
  - main() 같은 entry point를 제외하면 모든 object는 위임받은 제어 권한을 갖는 특별한 object에 의해 결정되고 만들어진다.
  - 즉, container에게 위임하고 container는 bean 설정 정보를 바탕으로 bean을 만들어 DI(의존성 주입)한다.
  - 이러한 과정을 통해 IoC가 이루어진다.

#### 클래스 호출 방식 비교

#### 용어

- 빈 (bean)
  - 스프링에서 제어권을 가지고 직접 만들고 관계를 부여하는 object
  - object 단위의 application component를 의미함.
  - but, 스프링을 사용하는 application에서 만들어지는 모든 object가 bean은 아님.
  - 스프링 bean은 스프링 container가 생성, 관계 설정, 사용 등을 제어해주는 object를 의미함.
  - 특정 인터페이스 또는 어노테이션 등으로 빈 등록이 된다.
    - Component Scanning (빈 등록 및 사용) : SpringApplication 하위 클래스 탐색
      - 빈 등록 
        - @Component 애너테이션 사용
          - @Controller
          - @Service
          - @Repository
        - 일일이 XML이나 자바 설정 파일에 등록
      - 빈 사용
        - @Autowired
          - 생성자 : 스프링 4.3부터 어떤 class의 생성자가 하나뿐이고 생성자의 레퍼런스 인자가 bean 등록이 되어 있으면 생략할 수 있다.
          - 필드
          - setter
        - @Inject
        - ApplicationContext에서 직접 getBean() 메소드 호출
- 빈 팩토리 (bean factory)
  - 스프링 IoC를 담당하는 핵심 container
  - bean을 등록, 생성, 조회, 반환, 관리(필요한 곳에 의존성 주입 등)한다.
  - 보통은 bean factory가 아닌 이를 확장한 application context를 사용한다.
- 어플리케이션 콘텍스트 (application context)
  - bean factory를 확장한 IoC 용도의 container
  - bean factory의 기능에 스프링의 각종 부가 서비스를 추가로 제공한다.
  - ApplicationContext는 application context가 구현해야하는 interface이며 BeanFactory를 상속한다.
- 컨테이너 (container)
  - IoC 방식으로 bean을 관리한다는 의미에서 bean factory나 application context를 가리킨다.
  - application context는 그 자체로 ApplicationContext interface를 구현한 object를 가리키기도 하는데 하나의 application에 여러 개의 ApplicationContext object가 만들어진다.
  - 간단하게 object를 관리하는 container로 container에 object를 담아두고 필요할 때 container로부터 object를 가져와 사용할 수 있도록 하고 있다.
  - 이를 스프링 container라 한다.
- 설정 정보 / 설정 메타 정보
  - bean factory 또는 application context가 IoC를 적용하기 위해 사용하는 메타 정보
  - container의 기능을 세팅하거나 조정할 수 있다.
  - 주로 bean을 생성/구성하는 용도로 사용한다.

#### 요약

- 작업을 수행하는 쪽에서 object를 생성하는 제어 흐름의 개념을 거꾸로 뒤집는다.
- IoC에서는 obejct가 자신이 사용할 object를 생성하거나 선택하지 않는다.
- object는 자신이 어떻게 생성되고 어떻게 사용되는지 알 수 없다.
- 모든 object는 제어 권한을 위임받는 특별한 object에 의해서 만들어지고 사용된다.
