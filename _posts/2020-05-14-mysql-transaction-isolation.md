---
layout: post
title:  "MySQL - Transaction Isolation"
date:   2020-05-14 17:22:40
categories: mysql
tags: mysql transaction
---

## Transaction Isolation Level의 종류와 특성

### Transaction Isolation Level

#### READ-UNCOMMITTED

1) COMMIT 되지 않은 데이터에 다른 트랜잭션에서 접근할수 있다. 즉, 어떤 트랜잭션의 변경 내용이 commit이나 rollback과 상관없이 다른 트랜잭션에서 보여진다.
2) INSERT, UPDATE, DELETE 후 COMMIT 이나 ROLLBACK에 상관없이 현재의 데이터를 읽어온다.
3) ROLLBACK이 될 데이터도 읽어올 수 있으므로 주의가 필요하다.
4) LOCK이 발생하지 않는다.
5) 데이터의 정합성에 문제가 많으므로 RDBMS 표준에서 격리수준으로 인정하지 않는다.

~~~
1) A 트랜잭션에서 1번 row의 number를 10->20 변경
2) 아직 커밋하지 않음
3) B 트랜잭션에서 1번 row의 number를 조회함
4) 20이 조회됨 (dirty read 발생)
5) A 트랜잭션에서 문제가 발생해 rollback
6) B 트랜잭션은 1번 row가 여전히 20으로 생각하고 로직을 수행
~~~

#### READ-COMMIITED

1) COMMIT 된 데이터에 다른 트랜잭션에서 접근할 수 있다. 즉, 어떤 트랜잭션의 변경이 commit되어야만 다른 트랜잭션에서 조회할 수 있다.
2) 구현 방식이 차이 때문에 Query를 수행한 시점의 데이터와 정확하게 일치하지 않을 수 있다.
3) LOCK이 발생하지 않는다.
4) MySQL에서 많은 양의 데이터를 복제하거나 이동할 때 이 LEVEL을 추천한다.

~~~
1) A 트랜잭션에서 1번 row의 number를 10->20 변경
2) 아직 커밋하지 않음
3) B 트랜잭션에서 1번 row의 number를 조회함
4) 10이 조회됨
5) A 트랜잭션에서 commit
6) B 트랜잭션이 1번 row 다시 조회
7) 20이 조회됨 (non-repeatable read 발생)
~~~

#### REPEATABLE-READ (MySQL 기본값)

1) Default LEVEL이다.
2) SELECT시 현재 시점의 스냅샷을 만들고 스냅샷을 조회한다. 즉, 트랜잭션이 시작되기 전에 커밋된 내용에 대해서만 조회할 수 있다.
3) 동일 트랜잭션 내에서 일관성을 보장한다. 자신의 트랜잭션 번호보다 낮은 트랜잭션 번호에서 변경된(+커밋된) 내용만 본다.
4) record lock과 gap lock이 발생한다.
5) CREATE SELECT, INSERT SELECT시 lock이 발생한다.

~~~
1) A 트랜잭션에서 1번 row의 number를 조회
2) 10이 조회됨
3) B 트랜잭션에서 1번 row의 number를 10 -> 20 변경
4) B 트랜잭션이 커밋함
5) A 트랜잭션이 1번 row 다시 조회
7) 10이 조회됨 (undo 영역에 백업된 데이터 반환)
~~~

#### SERIALIZE

1) 가장 강력한 LEVEL이다.
2) SELECT 문에 사용하는 모든 테이블에 shared lock이 발생한다.

### Transaction Isolation 특성

| Transaction Level | dirty read | non-repeatable read | phantom read |
|:------------------|:----------:|:-------------------:|-------------:|
| READ-UNCOMMITED   | O          | O                   | O            |
| READ-COMMITED     | X          | O                   | O            |
| REPEATABLE-READ   | X          | X                   | X            |
| SERIALIZE         | X          | X                   | X            |

#### dirty read

commit 되지 않은 데이터, 즉 신뢰성 없는 데이터를 읽을 수 있습니다. rollback 해버리면 데이터는 사라집니다.

#### repeatable read

하나의 트랜잭션에서 똑같은 select를 수행하면 항상 같은 결과를 반환해야한다.

#### non-repeatable read

나의 commit 유무에 따라서 데이터가 달라집니다. session 1에서 update 후 commit을 한 다음 session 2에 영향을 끼치게되면 non-repeatable read가 발생한다고 합니다. 즉, repeatable read 정합성이 어긋나는 경우를 의미한다.

#### phantom read

유령 읽기로 보면 되는데 업던 데이터가 갑자기 나타나거나 있던 데이터가 사라지는 경우를 의미합니다. 이것은 session 1이 commit을 한 이후 데이터를 변경하는데 session 2가 그 사이에 작업을 하면서 session 1이 작업을 하는 범위에 영향을 끼치는 경우입니다.

### REPEATABLE-READ에서 발생할 수 있는 부정합

#### update 부정합

~~~ sql {.line-numbers}
    START TRANSACTION; -- transaction id : 1
    SELECT * FROM User WHERE name='younsik1';

        START TRANSACTION; -- transaction id : 2
        SELECT * FROM User WHERE name = 'younsik1';
        UPDATE User SET name = 'younsik2' WHERE name = 'younsik1';
        COMMIT;

    UPDATE User SET name = 'younsik3' WHERE name = 'younsik1'; -- 0 row(s) affected
    COMMIT;
~~~

최종 결과 name=younsik2가 된다.
REPETABLE READ이기 때문에, 2번 트랜잭션에서 name = younsik2로 변경하고 COMMIT을 하면 name = younsik의 내용을 언두로그에 남겨놔야 한다.
그래야 1번 트랜잭션에서 일관되게 데이터를 보는 것을 보장해줄 수 있기 때문이다.

이렇게 되면 아래 구문에서 UPDATE 문을 실행하게 되는데, UPDATE의 경우 변경을 수행할 로우에 대해 잠금이 필요하다.
하지만 현재 1번 트랜잭션이 바라보고 있는 name = younsik 의 경우 레코드 데이터가 아닌 언두영역의 데이터이고,
언두영역에 있는 데이터에 대해서는 쓰기 잠금을 걸 수가 없다.