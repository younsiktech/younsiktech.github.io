---
layout: post
title:  "웹 - Session vs Persistent Cookie"
date:   2020-05-07 10:39:24
categories: web
tags: web session cookie
---

### Session Cookie

Temporary Cookie 라고도 합니다.

말 그대로 임시쿠키 입니다.

브라우저가 살아 있는 동안만 유지되는 쿠키로 expire date 이 없기 때문에 브라우저를 닫으면 바로 쿠키는 사라집니다.

### Persistent Cookie

세션쿠키와 다르게 브라우저가 닫혀도 쿠키가 유지 되는 쿠키 입니다.

즉, expire date 을 설정해 주기 때문에 그 기간 동안 쿠키는 유지 되며, 임의 삭제를 하지 않는 이상 유지됩니다.

이 쿠키는 expire 되기 전까지 쿠키 file 에 쓰여져서 저장됩니다.
