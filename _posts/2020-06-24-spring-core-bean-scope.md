---
layout: post
title:  "shell - pipe"
date:   2020-05-13 15:09:43
categories: shell
tags: shell bash
---

## Bean Scope

### singleton (default)

- 스프링 빈의 기본 스코프는 싱글톤 스코프
- 애플리케이션 전반에 걸쳐서 해당 빈의 인스턴스가 오로지 하나뿐이다.
  - 즉, 하나의 인스턴스가 생성되고 이후에는 해당 인스턴스를 공유한다.
- 대부분의 경우는 싱글톤으로 사용한다.
- 싱글턴 이외의 타입은 프로토타입과 비슷한 특성을 지닌다.

### prototype

- 어플리케이션에서 요청시마다 스프링이 매번 새로운 인스턴스를 만들어서 사용한다.
  - 즉, DI에 Bean에 대한 요청이 들어올때마다 Bean을 생성한다.
- 멀티스레드 환경에서 사용되는 Bean에 많이 사용된다.

### etc

#### session

- Spring MVC web application에서 사용
- HTTP 세션별로 인스턴스화되며 세션이 끝나면 소멸

#### Global Session

- 글로벌 HTTP 세션이 생성될때 bean을 생성한다
- 포틀릿 웹 용도
- 전역 세션 스코프의 bean은 같은 포탈 어플리케이션 내의 모든 포틀릿 사이에서 공유할 수 있다

#### request

- Spring MVC web application에서 사용
- HTTP 요청별로 인스턴스화되며 요청이 끝나면 소멸

#### Custom

- 스코프의 이름, 규칙을 개발자가 직접 정의할 수 있다.
  - org.springframework.beans.factory.config.Scope를 구현하고 스프링 설정에 등록하여 사용
- Thread (3.x에서만 사용, 이후는 Custom에서 SingleThreadScope 구현 필요)
  - 새 스레드에서 요청하면 새로운 bean 인스턴스 생성
  - 같은 스레드에 대해서는 같은 bean 인스턴스가 반환
- application (4.x 부터 등장)
  - Spring MVC web application에서 사용
  - 서블릿 컨텍스트가 만들어질 때 새로운 bean을 생성한다.
- websocket (5.x 부터 등장)

### 싱글톤과 프로토타입 차이

#### 싱글톤

~~~ java
    @Component
    public class Single {
        @Autowired
        private Proto proto;

        public Proto getProto() {
            return proto;
        }
    }

    @Component
    public class Proto {
        // nothing
    }

    @Component
    public class SampleRunner implements ApplicationRunner {
        @Autowired
        Single single;

        @Autowired
        Proto proto;

        @Override
        public void run(ApplicaitonArguments args) throws Exception {
            // 별도의 설정이 없으면 proto는 싱글톤이라 두 인스턴스가 동일하다.
            System.out.println(proto);
            System.out.println(single.getProto());
        }
    }
~~~

#### 프로토타입

~~~ java
    @Component
    public class Single {
        @Autowired
        private Proto proto;

        public Proto getProto() {
            return proto;
        }
    }

    @Component
    @Scope("prototype")
    public class Proto {
        // nothing
    }

    @Component
    public class SampleRunner implements ApplicationRunner {
        @Autowired
        ApplicationContext ctx;

        @Override
        public void run(ApplicaitonArguments args) throws Exception {
            // 프로토타입 (객체가 매번 다르다)
            System.out.println(ctx.getBean(Proto.Class));
            System.out.println(ctx.getBean(Proto.Class));
            System.out.println(ctx.getBean(Proto.Class));
            // 싱글톤 (객체가 동일하다)
            System.out.println(ctx.getBean(Single.Class));
            System.out.println(ctx.getBean(Single.Class));
            System.out.println(ctx.getBean(Single.Class));
        }
    }
~~~

## 주의할 점 : 스코프가 서로 섞일 경우

- 프로토타입 빈이 싱글톤 빈을 사용(참조)하는 경우는 문제가 없음.
  - 프로토타입의 빈은 매번 새롭게 생성되지만 참조하는 싱글톤 빈은 동일하다.
    - 새롭게 빈을 생성(프로토타입)해도 참조하는 값(싱글톤)은 변하지 않는 의도에 맞다.

- 싱글톤 빈이 프로토타입 빈을 사용(참조)하는 경우는 문제가 발생한다.
  - 싱글톤 빈을 사용할때마다 프로토타입 빈이 바뀌지 않는다.
    - 기존 생성된 빈을 재사용(싱글톤)해도 참조하는 값(프로토타입)은 변하는 의도와 맞지 않는다.

### 이유

~~~
싱글톤 빈이 생성될 때 한번만 만들어진다. 이때 사용할 프로토타입 빈이 설정된다.
싱글톤 빈을 재사용할때마다 설정된 프로토타입 빈이 사용되므로 참조하는 값이 변하지 않는다.
~~~

### 해결방법

#### 1. 프로토타입 빈 스코프에 프록시모드 설정 (추천)

사용하기는 쉽지만 이해하기는 어렵다.

~~~ java
    @Component
    @Scope(value="prototype", proxyMode=ScopedProxyMode.TARGET_CLASS)
    public class Proto {
        // nothing
    }
~~~

- @Scope 어노테이션에 proxyMode 값 설정
  - ScopedProxyMode.DEFAULT (기본값)
    - 프록시 사용하지 않는다.
  - ScopedProxyMode.TARGET_CLASS
    - 클래스가 타깃일 경우 사용한다.
    - 클래스 기반의 프록시로 감싸라는 의미
      - 싱글톤 빈이 프로토타입 빈을 직접 참조하면 안되기 때문에 사용한다.
      - 직접 사용하면 매번 바꿔줄 수 없다.
    - 별도의 써드파티 라이브러리를 사용한다.
      - JDK의 프록시 기능을 제공하는 다이너믹 프록시는 인터페이스에만 프록시를 제공한다.
  - ScopedProxyMode.INTERFACES
    - 인터페이스가 타깃일 경우 사용한다.
    - JDK 기반의 인터페이스 프록시를 사용한다.
- 프로토타입 인스턴스를 프록시로 감싼 빈을 만들어서 등록한다.
  - 타입은 프로토타입
  - 프로토타입을 주입할때 프록시로 감싼 프로토타입을 주입한다.

#### 2. 싱글톤 빈의 코드를 변경

~~~ java
    @Component
    public class Single {
        @Autowired
        private ObjectProvider<Proto> proto;

        public Proto getProto() {
            return proto.etIfAvaliable();
        }
    }
~~~

클래스에 스프링 코드가 들어가서 POJO스럽지 않아서 추천하지는 않는 방법

## 싱글톤 사용시에 주의할 점

- 싱글톤 객체 사용시엔 프로퍼티가 공유된다.
  - 즉, 멀티쓰레드 환경에서 싱글톤을 사용할 경우에는 쓰레드 세이프한 방법으로 코딩을 해야한다.
- 싱글톤 스코프의 빈들은 기본값이 ApplicationContext를 만들때 만들어진다.
  - 즉, ApplicationContext 초기 구동시에 인스턴스를 생성하여 애플리케이션 구동에 시간이 걸린다.
