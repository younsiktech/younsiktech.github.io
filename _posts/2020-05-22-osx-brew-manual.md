---
layout: post
title:  "OSX - brew 사용설명서"
date:   2020-05-22 15:09:43
categories: shell
tags: shell bash
---

#### 1. Xcode 커맨드라인 툴 설치

> ``` text
> > xcode-select --install
> ```

#### 2. brew 설치

> ``` text
> ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
> ```

#### 3. brew 설치 확인

> ``` text
> // 만약 다음과 같은 키가 나오면 Return(엔터)키를 누른다.
> > Press RETURN to continue or any other key to abort
>
> // 설치에 성공했다는 문구가 나올 때까지 기다린다
> > Installation successful!
>
> // 나오면 다음 명령어로 설치를 확인한다.
> > brew doctor
>
> // brew 사용 준비가 되었다는 문구
> > Your system is ready to brew.
> ```

#### 4. brew 패키지 설치

> ``` text
>
> // 설치하고자 하는 패키지 찾기
> > brew search {패키지 이름}
>
> // 패키지 설치
> > brew install {패키지1 이름} {패키지2 이름} ...
> ```

#### 5. brew 패키지 업그레이드(버전 up)

> ``` text
> // brew 최신 정보 가져오기 (패키지 이름 없으면 전체 패키지 검색한다.)
> > brew update {패키지 이름}
>
> // 패키지 버전 업 (Update Fomulae에 체크되어 있으면 최신 버전 존재 의미함.)
> > brew upgrade {패키지 이름}
> ```

#### 6. brew 패키지 삭제 1 (간단한 앱들 : brew services list에 없는 것들)

> ``` text
> // 패키지 삭제
> > brew uninstall {패키지 이름}
>
> // uninstall 말고 remove 사용해도 됨.
> > brew uninstall {패키지 이름}
> ```

#### 7. brew 패키지 삭제 2 (데몬, 상시 : brew serives list에 있는 것들)
mysql 삭제 참조