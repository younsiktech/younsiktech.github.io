---
layout: post
title:  "This is Just Another Post"
date:   2017-03-25 01:30:13 +0800
categories: default
tags: test
---

Header
=
sub header
-


I have some text.

I want some _italics_. *italic*.

I want some **bold**. __bold__.

# this is heading 1

## this is heading 2

### this is heading 3

#### this is heading 4

##### this is heading 5

###### this is heading 6

A [링크](http://kramdown.gettalong.org)

A [링크](http://kramdown.gettalong.org "hp")

A [링크][kramdown hp] to the homepage.

[kramdown hp]: http://kramdown.gettalong.org "hp"

A link to the [kramdown hp].

[kramdown hp]: http://kramdown.gettalong.org "hp"

이미지 : ![test](http://gjchoi.github.io/favicon.png)

각주1 선언부분 a footnote[^1].  
각주2 선언부분 a footnote[^2].

[^1]: 각주에 대한 설명 내용 부분 (문서 최하단)

문장내의 `###` 사용을 무시함

문장내의 `` `code` `` 사용을 무시함

같은 라인 문단 1,
같은 라인 문단 2

같은 문단 다른라인 1  ^[space]^[space]  
같은 문단 다른라인 2\\
같은 문단 다른라인 3

> blockquote 첫줄
>
> > 서브 blockquote1
> > 서브 blockquote2
>
> ## blockquote내의 헤더
> 헤더뒤 기본글

  블럭내에 첫째줄
    블럭내의 둘째줄

    블럭내의 넷째줄
    #문자escape
    ~~~~

~~~~~
블럭내에 첫째줄
블럭내의 둘째줄

블럭내의 넷째줄
#문자escape
~~~~
~~~~~

~~~ ruby
def what?
 42
end
~~~

~~~ java
public static void main(String[] args){
}
~~~

가로선
* * *

순서 리스트1

1. 리스트 Item 1
2. 리스트 Item 2
2. 2번째인듯한 리스트 Item 3
   ,다음줄인 듯한 3번째 뒷부분

  _  _  _  _
  
순서 리스트2

1.  리스트 Item 1

    > blockquote와 함께

    # 헤더와 함께
{:.no_toc}    

2.  리스트 Item 2

---------------

순서 리스트3

1. 리스트 Item 1
   1. 서브 리스트 Item 1
   2. 서브 리스트 Item 2
   3. 서브 리스트 Item 3
2. 리스트 Item 2

---

순서없는 리스트1

* Item1
,Item1의 뒷부분

순서없는 리스트2

* Item 1
+ Item 2
- Item 3


용어1
: 정의1
: 정의2

용어2
용어3(2번 같은 뜻)
: 용어2,3번의 정의

용어4

: 정의 문단 1
: 정의 문단 2


| 헤더1 | 헤더2 | 헤더3 |
|:--------|:-------:|--------:|
| 컬럼1   | 컬럼2   | 컬럼3   |
| 컬럼4   | 컬럼5   | 컬럼6   |
|----
| 컬럼1   | 컬럼2   | 컬럼3   |
| 컬럼4   | 컬럼5   | 컬럼6   |
|=====
| Foot1   | Foot2   | Foot3
{: rules="groups"}

> 블럭문구
{: title="Blockquote title"}

> 블럭문구
{: .class1 .class2}

> 블럭문구
{: #custom_id}

{:refdef: .c1 #id .c2 title="title"}
둘러쌓아진 문단
다음줄
{: refdef}

### Table of Content (TOC) 기능
{:toc}

### 제외하고 싶은 헤더
{:.no_toc}

{::options auto_ids="false" /}

### code block 
Ruby:
{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Python with line numbers:
{% highlight python linenos %}
def print_hi(name):
    print("Hi, {}".format(name))

print_hi('Tom')
# prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

C with line numbers:
{% highlight c linenos %}
void print_hi(string name) {
  printf("Hi, %s", name);
}
print_hi("Tom");
/* prints 'Hi, Tom' to STDOUT. */
{% endhighlight %}