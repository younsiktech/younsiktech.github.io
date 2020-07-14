---
layout: post
title:  "스프링 - Core - Autowired"
date:   2020-06-04 11:08:24
categories: spring
tags: spring core bean
---

## Autowired

필요한 의존 객체의 "타입"에 해당하는 빈을 찾아서 주입한다.

### 생성자를 통한 주입

~~~ java

    // SampleRepository 아직 빈 등록 안함.
    public class SampleRepository {
        // nothing
    }

    @Service
    public class SampleService {

        SampleRepository sampleRepository;

        // 실행하면 에러 발생 : SampleRepsitory 빈을 찾을 수 없으니 생성해달라는 에러 발생
        // SampleRepsiotry에 @Repository 붙이면 됨.
        @Autowired
        public SampleService(SampleRepository sampleRepository) {
            this.sampleRepository = sampleRepository;
        }

    }

~~~

### Setter를 통한 주입

~~~ java

    // SampleRepository 아직 빈 등록 안함.
    public class SampleRepository {
        // nothing
    }

    @Service
    public class SampleService {

        SampleRepository sampleRepository;

        // Setter를 통한 주입이 생성자를 통한 주입과 다르다.
        // 생성자를 통한 주입은 빈을 생성하다가 실패함.
        // Setter를 통한 주입은 SampleService 빈은 생성을 하였으나 의존성 관계 주입을 시도하다가 실패함.
        @Autowired
        // 만약 빈은 형성하고 싶으면 @Autowired의 required=false(기본값:true)로 하면 의존성 주입이 실패해도 애플리케이션 실행은 된다.
        // 다만 정상 작동은 보장 못함.
        public void setSampleRepository(SampleRepository sampleRepository) {
            this.sampleRepository = sampleRepository;
        }

    }

~~~

### 어노테이션만 사용한 주입

~~~ java

    // SampleRepository 아직 빈 등록 안함.
    public class SampleRepository {
        // nothing
    }

    @Service
    public class SampleService {

        @Autowired
        // 그냥 필드에도 붙일 수 있다. Applciation 실행하려면 @Autowired(required=false)로 한다.
        SampleRepository sampleRepository;

    }

~~~

#### 각각의 차이

필드에 붙인 @Autowired와 생성자를 의용한 의존성 주입을 비교하면 다르다.

생성자를 통한 의존성 주입은 빈을 만들때에도 개입을 한다.

생성자를 통한 의존성 주입은 생성자에 전달받야아 하는 빈이 없으면 인스턴스를 만들지 못한다. 따라서 빈도 생성되지 않는다.

필드에 붙이면 해당 의존성이 없이도 인스턴스가 생성되어 빈은 생성할 수 있다.

### 빈이 여러 개인 경우


~~~ java

    // SampleRepository 아직 빈 등록 안함.
    public interface SampleRepository {
        // nothing
    }

    @Repository
    public class YounsikRepository implements SampleRepository{

    }

    @Repository
    public class TechRepository implements SampleRepository {

    }

    @Service
    public class SampleService {

        @Autowired
        // 스프링에서 Younsik과 Tech 중 어느 repository를 주입할까?
        // 스프링은 이런 경우 주입을 못한다. 주입해야하는 빈이 여러개 발견되어서 모른다고 에러가 나온다.
        SampleRepository sampleRepository;

    }

~~~

#### 해결 1 : @Primary 사용해서 우선 적용 (추천)

~~~ java

    @Repository
    @Primary
    public class YounsikRepository implements SampleRepository{

    }

~~~

#### 해결 2 : @Qualifier를 사용해서 원하는 빈 설정

~~~ java

    @Service
    public class SampleService {

        @Autowired
        @Qualifier("younsikRepository")
        SampleRepository sampleRepository;

    }

~~~

#### 해결 3 : 모든 빈을 받도록 설정

~~~ java

    @Service
    public class SampleService {

        @Autowired
        List<SampleRepository> sampleRepositories;

    }

~~~

#### 해결 4 : 받고자하는 빈과 동일하게 필드명을 설정 (추천하지 않음)

~~~ java

    @Service
    public class SampleService {

        @Autowired
        SampleRepository younsikRepository;

    }

~~~

### @Autowired 동작 원리

빈 라이프 사이클 인터페이스 BeanPostProcessor를 구현한 AutowiredAnnotationBeanPostProcessor에 의해 동작한다.

- BeanPostProcessor
  - 라이프 사이클 상 빈을 만들고 빈의 인스턴스를 만든 후에 동작한다.
  - 빈의 초기화(빈이 만들어지 이후에 할 일) 이후에 부가적인 작업을 할수 있는 콜백
    - postProcessBeforeInitialization()
    - postProcessAfterInitialization()
  - InitializingBean Interface를 상속
    - afterPropertiesSet() 오버라이드
  - PostConstruct 어노테이션 사용

AutowiredAnnotationBeanPostProcessor가 Autowired 어노테이션에 해당하는 빈을 찾아서 빈의 초기화 이전에 주입해준다.

~~~ java
   @Service
    public class SampleService {

        @Autowired
        SampleRepository younsikRepository;

        @PostConstruct
        // PostConstruct 콜백은 애플리케이션 구동 중에 실행되어서  main 이전에 찍힘.
        // ApplicationRunner들은 어플리케이션이 다 구동되고 나서 일을 하기때문에 main 뒤에 찍힌다.
        public void setup() {
            System.out.println(younsikRepository.getClass());
        }

    }
~~~
