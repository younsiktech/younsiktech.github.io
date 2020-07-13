---
layout: post
title:  "Mac에서 brew로 설치한 서비스(데몬 etc) 제거하기"
date:   2020-05-22 15:09:43
categories: brew
tags: brew uninstall mac
---

기존에 로컬 환경에 설치한 mysql을 docker로 전환하기 위해서 삭제하기로 하였다.

brew services list에 존재하는 패키지는 거의 비슷하게 삭제하는 것 같다.

mysql을 삭제하기로 했으므로 해당 서비스를 삭제하는 것으로 예를 들기로 하였다.

#### 1. 삭제할 서비스 확인

> ``` text
> > brew services list
> ```

|    Name   | Status  |  User       | Plist                                                                 |
|:----------|:--------|:------------|:----------------------------------------------------------------------|
| mysql@5.7 | started | {user name} | /Users/{user name}/Library/LaunchAgents/homebrew.mxcl.mysql@5.7.plist |

삭제할 서비스의 이름과 버전을 확인한다.

#### 2. 서비스 중지

> ``` text
> // 삭제할 프로세스 pid 확인한다.
> > ps -ax | grep mysql
>
> // 위에서 찾은 pid로 삭제한다.
> > kill -9 {mysql pid}
>
> > brew services list
> ```

|    Name   |Status |  User       | Plist                                                                 |
|:----------|:------|:------------|:----------------------------------------------------------------------|
| mysql@5.7 | error | {user name} | /Users/{user name}/Library/LaunchAgents/homebrew.mxcl.mysql@5.7.plist |

삭제할 서비스가 error로 표시되는 것을 확인할 수 있다.

#### 3.서비스 삭제

> ``` text
> // mysql은 로컬 폴더 삭제가 필요해서 삭제한다. (다른 패키지들은 로컬 폴더 삭제가 불필요할 수 있다.)
> > rm -rf /usr/local/var/mysql
>
> > brew uninstall mysql@5.7
>
> > brew cleanup
>
> // ~/Library/LaunchAgents에서 파일 존재하는지 확인해보고 남아있으면 삭제한다.
> > rm -rf ~/Library/LaunchAgents/homebrew.mxcl.mysql@5.7.plist
> ```

로컬에서 brew로 설치했던 mysql이 깔끔하게 삭제되었다.

다른 패키지도 비슷하게 삭제하면 된다.
