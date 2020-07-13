---
layout: post
title:  "shell - pipe"
date:   2020-05-13 15:09:43
categories: shell
tags: shell bash
---

여러개의 명령어를 실행할 경우 이전 명령어의 결과를 다음 명령어의 입력값으로 사용할 경우 사용한다.

#### 사용법

``` bash
  cat /etc/sample | grep test
```

cat 명령어로 인한 sample 파일이 라인별로 출력되면 그 결과를 grep 명령어로 전달한다.

파이프의 개수는 제한이 없고 awk, cut, grep, more 등과 자주 쓰인다.
