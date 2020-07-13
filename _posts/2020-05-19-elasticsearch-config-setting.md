---
layout: post
title:  "elasticsearch - config"
date:   2020-05-19 15:09:43
categories: elasticsearch
tags: elasticsearch config
---

## Configuration

### 노드 클러스터링 구성

- 클러스터링할 그룹명
  - 묶고자 하는 서버들에 elasticsearch.yml 파일에서 이름을 동일하게 주면 클러스터링 됨

``` yml
cluster.name: clusterName
```

- 마스터 노드 (인덱스 및 메타 데이터 관리)

``` yml
node.name: masterNode
node.master: true
node.data : false
node.ingest: false
```

- 데이터 노드

``` yml
node.name: dataNode
node.master: false
node.data : true
node.ingest: false
node.attr.box_type: serving
```

- 색인 노드

``` yml
node.name: indexNode
node.master: false
node.data : true
node.ingest: false
node.attr.box_type: indexing
```

- 검색 노드 (Coordinating only node)

``` yml
node.name: searchNode
node.master: false
node.data : false
node.ingest: false
```

- 인제스트 노드 (로그스태시와 같은 전치리 용도)

``` yml
node.name: ingestNode
node.master: false
node.data : false
node.ingest: true
```

### 기타 설정

- 색인 파일(데이터) 사이즈가 작을 경우 수치를 작게 (1), 사이즈가 클 경우 수치를 크게 (기본 5)

- 하나의 색인 파일을 몇 개로 나눠서 저장할 것인지 정의

``` yml
index.number_of_shards: 5
```

- 색인 파일에 대한 복사본 생성 수치 (기본 1)

``` yml
index.number_of_replicas: 1
```

- 설정 후 서버간 클러스터링 되는 과정을 파악하기 어려움

- 두 대의 서버에 cluster.name 을 같게 해서 실행 시켜면 자동으로 clustering 됨

- 서버 한대에서 여러개의 elasticsearch instacne 실행 방법

```
./elasticsearch -p pidfile1 -Des.config=elasticsearch/config/elasticsearch1.yml

./elasticsearch -p pidfile2 -Des.config=elasticsearch/config/elasticsearch2.yml
```

- 기타 옵션

```
> -Xmx1g -Xms1g -Des.max-open-files=true
```
