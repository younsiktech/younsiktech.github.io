---
layout: post
title:  "루씬 - 인덱스 옵션"
date:   2020-05-19 15:09:43
categories: lucene
tags: lucene indexing
---

#### Store 옵션

데이터를 저장 여부에 대한 옵션으로 검색 후 화면 출력 여부에 따라 값을 주면 된다.

> Store.YES : 저장 함
>
> Store.NO : 저장 안함
>
> Store.COMPRESS : 압축 저장 함 (글 내용이 크거나, binary 파일)

#### Index 옵션

검색을 위한 색인 여부에 대한 옵션으로 lucene 4. 부터는 전부 deprecated 되었다.

> Index.NO : 색인을 하지 않음 (검색 field 로 사용하지 않음)
>
> Index.TOKENIZED : 검색 가능 하도록 색인 함, analyzer 에 의한 tokenized 수행을 통해 색인을 함.
>
> Index.UN_TOKENIZED : 검색 가능 하도록 색인 함, 단 analyzer 에 의한 분석을 하지 않기 때문에 색인 속도가 빠름. (숫자나 분석이 필요 없는 경우)
>
> Index.NO_NORMS : 검색 가능 하도록 색임 함, 단 색인 속도가 매우 빨라야 할 경우 사용하며, analyzer 에 의한 분석을 수행 하지 않고, field length normalize 를 수행 하지 않음.
