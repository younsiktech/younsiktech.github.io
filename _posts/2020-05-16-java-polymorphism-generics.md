---
layout: post
title:  "Java - 다형성 : 제네릭(generics)"
date:   2020-05-16 10:12:43
categories: java
tags: java generics polymorphism
---

Generics를 설명할 수 있는 가장 쉬운 예로는 Collection 계열 클래스에서 흔히 사용되는 List와 같은 컨테이너 인터페이스가 있다.

``` java
List intList = new ArrayList();
intList.add(new Integer(0));
Integer x = (Integer).intList.iterator().next();
```

1행에서는 intList는 List 인터페이스 타입으로 ArrayList 인스턴스를 생성하고 있다.

2행에서 Integer(0)으로 정수 0을 Integer 클래스로 인스턴스를 생성하여 intList 컨테이너에 들어가게 한다.

3행에서는 Integer 타입으로 wrapping한 intList에서 다시 Iterator로 이 인스턴스(Integer(0))를 가져오고 있다.

이 과정에서, List 인터페이스의 add는 Object형 매개변수를 받고 있으므로 Integer(0)은 upcasting이 되어 intList라는 컨테이너에 들어가고, Iterator에 의해 나올 때에도 Object형으로 리턴된다.

3행에서 리턴된 값을 (Integer)로 형변환하는 것을 보면 downcasting해주지 않으면 Integer x 에 대입할 수가 없다는 것을 알 수 있다.

그런데 여기서 문제가 발생하게 되는데 강제 형변환은 프로그램을 난잡하게 할 뿐만 아니라, 런타임 에러의 가능성을 발생시킨다.

위의 예제에서는 (Integer) 강제 형변환으로 해결하고 있지만, 사실 프로그래머의 의도대로 돌아가기 위해서는 컨테이너 차원에서 "이 컨테이너는 Integer형 인스턴스만을 저장할 수 있다"라고 명시하는게 논리적일 것이다.

여기서 Generics의 핵심 개념이 등장하는데 다룰 객체를 미리 명시함으로 형변환을 하지 않고 특정 타입만 들어갈 수 있도록 강제하는 것이다.

Generics를 이용해서 위의 코드를 바꾼다면 아래와 같을 것이다.

``` java
List<Integer> intList = new ArrayList<Integer>();
intList.add(new Integer(0));
Integer x = intList.iterator().next();
```

이제 강제 형변환 없이 논리적으로 리스트를 규정하고 활용하도록 바뀌었다.

이러한 경우 List는 Integer를 받는 generic 인터페이스라고 한다.

인터페이스와 관련된 형변환이 주로 컨테이너 클래스를 사용할 때 빈번히 일어나기 때문에 다형성과 관련되어 generics에 이런 내용이 있다는 정도만 알자.
