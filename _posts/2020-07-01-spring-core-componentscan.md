---
layout: post
title:  "스프링 - Core - Component Scan"
date:   2020-07-01 15:09:43
categories: spring
tags: spring core bean
---

## Component Scan

### @SpringBootApplication 어노테이션

- @ComponentScan 어노테이션을 지니고 있다.
  - @SpringBootApplication 어노테이션이 붙은 클래스가 시작 지점이 된다. (기본값)

### @ComponentScan

- 속성
  - 스캔 위치 설정
    - basePackage
      - 문자열로 패키지 위치를 명시
    - basePackageClasses
      - 클래스로 패키지 위치를 명시 (Type Safety)
  - @Filter : 어떤 어노테이션을 스캔할 것인지 여부
- 구동 방식
  - ConfigurationClassPostProcessor를 사용한다.
    - BeanFactoryPostProcessor
      - 실행되는 시점이 다른 모든 빈들을 만들기 이전에 해당 구현체들을 적용한다.
      - 즉, 다른 빈들(직접 빈을 등록하거나 @Bean 어노테이션을 사용 등등)을 모두 등록하기 전에 컴퍼넌트 스캔을 해서 빈으로 등록한다.
- 스캔 방식
  - 해당 클래스의 패키지 이하의 경로에 대해 컴포넌트 스캔을 한다.
  - 해당 패키지 외의 상위 혹은 다른 패키지에 대해서는 빈 등록을 할 수 없다.

### @Component

- @Service
- @Repository
- @Controller
- @Configuration

등록해야하는 빈이 많으면 초기 구동 시간이 오래 걸릴 수 있다.
(초기 구동에만 어느정도 시간이 소요되고 그 이후에는 재사용해서 괜찮다.)

만약 이를 초기 구동에도 성능을 올려야 하면 스프링 5부터 제공하는 펑션을 통한 빈 등록을 해야한다.

#### 스프링 어플리케이션 인스턴스를 만들어서 펑션을 이용한 빈 등록

~~~ java
    @SpringBootApplication
    public class SampleApplication {

        // 컴포넌트 스캔 밖의 빈이지만 빈을 등록해서 애플리케이션 실행이 가능해진다.
        @Autowired
        SampleOuterBean sampleOuterBean;

        public static void main(String[] args) {
            // java 10부터 로컬 variable 사용 가능
            var app = new SpringApplication(SampleApplication.Class);

            // 추가 작업이 필요한 경우 Initializer를 사용한다.
            app.addInitializers(new ApplicaitonContextInitializer<GenericApplicationContext>(){
                // 원하는 애플리케이션 컨텍스트 주입을 할 수 있다.
                @Overide
                public void initialize(GenericApplicationContext ctx) {
                    // GenericApplicationContext의 registerBean을 사용해서 빈을 등록한다.
                    // 실행 패키지 밖의 빈 추가
                    ctx.registerBean(SampleOuterBean.Class);

                    ctx.registerBean(ApplicationRunner.class, new Supplier<ApplicationRunner>(){
                        @Overide
                        public ApplicationRunner get(){
                            return new ApplicationRunner() {
                                @Overide
                                public void run(ApplicationArgument args) throws Exception {
                                    System.out.println("Functional Bean Definition!");
                                }
                            };
                        }
                    });
                }
            });

            // 애플리케이션 실행
            app.run(args);
        }
    }

~~~

- 장점
  - 프로그래밍적인 컨트롤이 가능하다.
    - 코드로 로직을 태워 필요한 빈을 등록할 수 있게 된다.
  - 어플리케이션 구동상 성능상의 장점이 있다.
    - 초기에 빈을 등록하지 않으니까
- 주의할 점
  - 펑셔널한 빈 등록 방법이 앱 구동시 성능상 이점이 있다고 컴포넌트 스캔 버리고 직접 일일이 등록하는것은 비추천
    - 불편하고 어마어마한 설정 파일이 등장한다.
  - 직접 빈을 등록하는 경우에만 사용하는 것이 좋다.
