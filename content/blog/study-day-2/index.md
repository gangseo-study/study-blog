---
title: Study Day 2 Database
date: "2023-02-04"
description: "Database 기초 공부"
---

# 스터디 Day 2 데이터베이스

![](./img/erd.png)

### **\[ 인덱스란 ? \]**

추가적인 쓰기 작업과 저장 공간을 활용하여 데이터베이스 테이블의 검색 속도를 향상시키기 위한 자료구조

색인이라는 뜻으로 책 내용을 쉽게 찾는 뜻의 예도 있다.

검색 성능의 향상을 위해 테이블 열에 사용하는 객체를 의미한다.

특정 행 데이터의 주소, 위치 정보를 목록으로 만들어 놓은 것이다.

인덱스 사용 여부에 따라 데이터 검색 방식을 Table Full Scan, Index Scan으로 구분한다.

[인덱스 스캔 정리](https://myjamong.tistory.com/237)

![](./img/index-structure.png)

#### 장점

```markdown
1. 테이블을 조회하는 속도와 그에 따른 성능을 향상시킬 수 있다.

2. 전반적인 시스템의 부하를 줄일 수 있다.
```

#### 단점

```markdown
1. 인덱스를 관리하기 위해 DB의 약 10%에 해당하는 저장공간이 필요하다.

2. 인덱스를 관리하기 위해 추가 작업이 필요하다.

3. 인덱스를 잘못 사용할 경우 오히려 성능이 저하되는 역효과가 발생할 수 있다.
```

만약 CREATE, DELETE, UPDATE가 빈번한 속성에 인덱스를 걸게 되면 인덱스의 크기가 비대해져서 성능이 오히려 저하되는 역효과가 발생할 수 있다.  
그러한 이유 중 하나는 DELETE와 UPDATE 연산 때문이다.  
앞에서 설명한대로, UPDATE와 DELETE는 기존의 인덱스를 삭제하지 않고 '사용하지 않음' 처리를 해준다고 하였다.  
만약 어떤 테이블에 UPDATE와 DELETE가 빈번하게 발생된다면 실제 데이터는 10만건이지만 인덱스는 100만 건이 넘어가게 되어,  
SQL문 처리 시 비대해진 인덱스에 의해 오히려 성능이 떨어지게 될 것이다.

### **\[ 인덱스(index)를 사용하면 좋은 경우 \]**

```markdown
1. 규모가 작지 않은 테이블
2. INSERT, UPDATE, DELETE가 자주 발생하지 않는 컬럼
3. JOIN이나 WHERE 또는 ORDER BY에 자주 사용되는 컬럼
4. 데이터의 중복도가 낮은 컬럼

기타 등등
```

인덱스를 사용하는 것 만큼이나 생성된 인덱스를 관리해주는 것도 중요하다.

그러므로 사용되지 않는 인덱스는 바로 제거를 해주어야 한다.

[인덱스 사용 규칙](https://jmkim.tistory.com/64)

### **\[ 시퀀스란 ? \]**

오라클 데이터베이스에서 특정 규칙에 맞는 연속 숫자를 생성하는 객체이다.

시퀀스 생성

```markdown
CREATE SEQUENCE 시퀀스 이름

INCREMENT BY n

START WITH n

MAXVALUE n | NOMAXVALUE

MINVALUE n | NOMINVALUE

CYCLE | NO CYCLE

CACHE n | NOCACHE

INCREMENT : 시퀀스에서 생성할 번호의 증가 값

START WITH : 시퀀스에서 생성할 번호의 시작 값

MAXVALUE : 시퀀스에서 생성할 번호의 최댓값 지정

MINVALUE : 시퀀스에서 생성할 번호의 최솟값 지정

CYCLE : 시퀀스에서 생성한 번호가 최댓값에 도달 했을 경우
CYCLE이면 시작값에서 다시 시작

CACHE : 시퀀스가 생성할 번호를 메모리에 미리 할당해 놓은 수를 지정
```

[데이터 정규화 비정규화(반정규화)](https://owlyr.tistory.com/20)

[데이터베이스 정규화 정리](https://3months.tistory.com/193)

1차 정규화 쉼표 구분 관련 함수는 오라클은 Listagg, mysql은 group_concat 등이 있다.

보통 프로젝트에서는 일관된 쿼리를 위해 함수를 직접 만들어 사용하는 경우가 많다.

[오라클 Row to Column](https://engineering-skcc.github.io/sql/SQL_RowColunm%EB%B3%80%ED%99%98/)
