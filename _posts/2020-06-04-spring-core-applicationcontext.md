---
layout: post
title:  "스프링 - Core - ApplicationContext"
date:   2020-06-04 10:08:24
categories: spring
tags: spring core bean
---

## 빈 설정

- 빈 명세서라고 불리며 빈에 대한 정의를 담고 있다.
  - 이름
  - 클래스
  - 스코프
  - 생성자 (constructor)
  - 프로퍼트 (setter) 등등

## ApplciationContext

### ApplicationContext를 이용한 Bean 등록

#### Bean 설정

- xml 설정

~~~ xml
    <bean id="sampleBean1"
          class="kr.com.younsiktech.springapplicationcontext.SampleBean1">
          // 의존성 주입 (name은 setter에서 설정, ref는 다른 빈 id 참조)
          <property name="sampleBean2" ref="sampleBean2" />
    </bean>

    <bean id="sampleBean2"
          class="kr.com.younsiktech.springapplicationcontext.SampleBean2"
          scope = "설정 안하면 기본이 싱글톤" />
~~~

- application context 설정

~~~ java
    // applicationContext 생성
    ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
    // 등록된 bean 이름 가져오기
    String[] beanDefinitionNames = context.getBeanDefinitionNames();
    // bean 이름으로 bean 꺼내기
    SampleBean1 sampleBean1 = (SampleBean1) context.getBean("sampleBean1");
~~~

- 단점 : 일일이 xml을 설정해야함

### Component Scan을 이용한 Bean 등록

#### Bean 설정

~~~ xml
    // kr.com.younsiktech.springapplicationcontext 패키지 이하의 빈을 찾아서 등록한다
    <context:component-scan base-package="kr.com.younsiktech.springapplicationcontext" />
~~~

- 기본적으로 4 종류의 애너테이션을 사용한 빈을 탐색한다.
  - @Component
    - @Service
    - @Repository
    - @Controller

### Java 설정 파일을 이용한 Bean 등록

#### Bean 설정

~~~ java
    // ApplicationConfig를 bean 설정으로 사용
    @Configuration
    public class ApplicationConfig {
        // bean 등록
        @Bean
        public SampleBean2 sampleBean2() {
            return new SampleBean2();
        }

        // 의존성 주입 방법 1 : 직접 setter 사용
        @Bean
        public SampleBean1 sampleBean1() {
            SampleBean1 sampleBean1 = new SampleBean1();
            sampleBean1.setSampleBean2(sampleBean2());
            return sampleBean1;
        }

        // 의존성 주입 방법 2 : 생성자를 사용
        @Bean
        public SampleBean1 sampleBean1(SampleBean2 sampleBean2) {
            SampleBean1 sampleBean1 = new SampleBean1();
            sampleBean1.setSampleBean2(sampleBean2);
            return sampleBean1;
        }
    }
~~~

~~~ java
    // applicationContext 생성
    ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
    // 등록된 bean 이름 가져오기
    String[] beanDefinitionNames = context.getBeanDefinitionNames();
    // bean 이름으로 bean 꺼내기
    SampleBean1 sampleBean1 = (SampleBean1) context.getBean("sampleBean1");
~~~

- 단점 : ApplicationConfig에 일일이 Java로 설정하기가 힘들다

~~~ java
    // ApplicationConfig를 bean 설정으로 사용
    @Configuration
    // ComponentScan 방식에는 basePackage와 basePackageClasses가 존재함.
    // basePackage는 문자열로 패키지 위치를 명시함. type safety한데 뭔가 조금 부족.
    // basePackageClasses는 basePackage보다 더 type safety하다.
    // SpringApplication.Class가 위치한 곳부터 애너테이션 붙은 것들을 스캐닝 할 것을 명시함.
    @ComponentScan(basePackageClasses = SpringApplication.Class)
    public class ApplicationConfig {
        // bean 등록
        @Bean
        public SampleBean2 sampleBean2() {
            return new SampleBean2();
        }

        // 의존성 주입 방법 1 : 직접 setter 사용
        @Bean
        public SampleBean1 sampleBean1() {
            SampleBean1 sampleBean1 = new SampleBean1();
            sampleBean1.setSampleBean2(sampleBean2());
            return sampleBean1;
        }

        // 의존성 주입 방법 2 : 생성자를 사용
        @Bean
        public SampleBean1 sampleBean1(SampleBean2 sampleBean2) {
            SampleBean1 sampleBean1 = new SampleBean1();
            sampleBean1.setSampleBean2(sampleBean2);
            return sampleBean1;
        }
    }
~~~

### @SrpingBootApplicatoin을 이용한 Bean 등록

- 자기가 알아서 찾아서 등록한다.
- 어노테이션을 쓰는 경우 빈 이름은 해당 클래스의 small case한 빈 이름과 동일하다.
