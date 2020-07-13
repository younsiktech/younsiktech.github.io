---
layout: post
title:  "Java - 다형성 : 인터페이스(interface)"
date:   2020-05-16 10:12:43
categories: java
tags: java interface polymorphism
---

Java에서 다형성(polymorphism)을 구현하기 위해 지원하는 기능.

개인적으로 보통 다형성은 '확장성'에 집중하고 있는것 같다.

인터페이스를 구현한 클래스들에게 필수 메서드를 선언하게 하는 최소한의 규약과 이 클래스들에게 외부의 소통 방식을 알려서 개발자는 인터페이스의 내용을 구현하고 사용자는 인터페이스의 내용만 알면 인터페이스를 구현한 클래스를 세부적으로 알지 않아도 사용할 수 있다.

이러한 점은 은닉화(encapsulation)과 관련되어 있기도 하다.

흔히 상속이나 인터페이스와 관련해서 다형성을 설명하면 '상위 클래스 타입의 객체 참조 변수에 하위 클래스의 인스턴스를 연결할 수 있다'라는 말로 축약된다.

#### 예제

``` java
public interface Book{
    private String author;
    private String title;
    public void publish(){
        //출판
    }
}

public class Cartoon implements Book{
    //author와 tittle에 접근 할 수 있음
    public void publish(){
        // 꼭 구현해야함
    }
}

public class Novel implements Book{
    //author와 tittle에 접근 할 수 있음
    public void publish(){
        // 꼭 구현해야함
    }
}

public static void main() {
    // 일반적인 인스턴스 생성
    Cartoon c = new Cartoon();
    Novel n = new Novel();

    // 인터페이스 사용한 인스턴스 생성
    Book c = new Cartoon();
    c.publish();//만화책 출판
    Book n = new Novel();
    n.publish();//소설책 출판
}
```

위 예제의 인터페이스 사용처럼 인터페이스의 메소드를 호출하게 되면 실제는 생성된 클래스의 구현 메소드가 호출된다.

인터페이스라는 것을 통해서 클래스의 기본 틀을 얻을수 있으며 구현 클래스들에 접근할 수 있다는 것을 알 수 있다.

이러한 경우 장점은 구현 클래스에 의존하지 않은 인터페이스에 의존하는 프로그램을 작성할 수 있다는 것이다.

따라서 인터페이스에 의존한다는 것은 쉽게 말해서 고정된 틀에 얽매이지 않아도 되는 것을 말한다.

물론 인터페이스의 틀에는 얽매이겠지만 적어도 공통된 틀이기 때문에 어느 클래스에도 접근할 수 있다는 것을 의미한다.

그러면 다른 클래스에서 해당 인터페이스를 사용할 수 있을까?

#### 예제

``` java

public interface Eproduct {
    void disp();
}

public class TV implements Eproduct {
    int channel = 11;
    public void disp() {
       //저장된 채널값을 바탕으로 해당 채널을 TV 화면에 보여주는 내용
    }
    public int setChannel(int ch) {
       this.channel = ch;
    }
}

public class CDP implements Eproduct {
    int cdNumber = 1;
    public void disp() {
       // 오디오 CD의 현재 곡 번호를 액정에 표시하는 내용
    }
    public int setCdNumber(int cdNum) {
        this.cdNumber = cdNum;
    }
}

public void remocon(Eproduct thing) {
    thing.disp(7);
}

```

위 예제처럼 disp() 메서드를 사용하는 리모콘 메소드가 있다.

remocon 메서드에 각각 TV 클래스의 인스턴스와 CDP 클래스의 인스턴스가 인자로 들어가는 경우를 생각해보면 다음과 같다.

``` java
// 일반적인 인스턴스 생성한 객체변수
TV tv = new TV();
CDP cdp = new CDP();
```

위처럼 직접 일반적인 인스턴스 생성을 사용한 자손형 객체변수를 인터페이스형 파라미터 공간에 대입하게 되면, 임시 upcasting이 일어난다.

즉, 매개변수 thing은 Eproduct형이지만, 결론적으로는 자손형 인스턴스를 참조할 수 있다.

``` java
// 인터페이스를 사용한 인스턴스 생성한 객체변수
Eproduct tv = new TV();
Eporduct cdp = new CDP();
```

인터페이스를 사용한 인스턴스 생성은 생성 부분에서 임시로 형변환이 이루어지는 과정을 표현하고 있다.

즉, Eproduct 타입인 tv 객체 참조 변수로 TV 타입 인스턴스를 참조하고 있는 것이다. 

 경우 tv 변수로 TV 인스턴스의 setter 메서드인 setChannel()에 접근할 수 없다.

하지만 일반적인 인스턴스 생성한 객체변수와 같은 경우에도 remocon 메서드 안에서 thing 지역 변수는 절대로 setChannel()이나 setCdNumber()에 접근할 수 없다.

``` java
Eproduct tv = new TV();
TV realTV = (TV)tv;
realTV.setChannel(9);
```

위의 예제에서 realTV 변수는 tv가 가리키는 인스턴스의 모든 것을 다시금 접근할 수 있게 된다.

v는 단지 Eproduct형 객체 참조 변수이기 때문에 TV 인스턴스의 고유 메서드에 접근할 수 없을 뿐이지, 인스턴스 자체에 손실이 있는 것은 아니다.

따라서 TV형 참조 변수를 이 인스턴스에 연결하는 것도 합법이며, RealTV는 아무런 손실 없이 tv가 가리키고 있던 TV형 인스턴스의 고유 메서드를 모두 사용할 수 있게 된다.

위와 같은 예를 downcasting이라고 하는데, 인터페이스를 사용한 인스턴스 생성한 객체변수와는 달리 강제로 형변환하지 않으면 대입 자체가 불가능해진다.

확장성과 연관지어 생각해보자면, 위의 리모콘 메서드는 가전제품 인터페이스를 구현하는 두 종류의 인스턴스를 매개변수로 받고 있다.

가전제품 인터페이스를 구현하는 다른 클래스, 이를테면 MicowaveOven이라던가 AirConditioner와 같은 새로운 클래스를 제작하게 되더라도 리모콘 메서드에 변화를 주지 않고 Eproduct형으로 인스턴스를 받을 수 있을 것이다.

가전제품 인터페이스를 활용하는 개발자 입장에서는 전자렌지나 에어컨이 구현하는 인터페이스의 내용 disp()의 세부 구현 사항을 알 필요가 없다.

그저 이들의 disp()를 호출했을 때 '액정이나 화면에 핵심 정보를 출력하는 기능'을 수행한다는 인터페이스 명세서를 가지고 있으면 된다.
