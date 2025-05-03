```
#)인덱스를 사용할 때 인덱스 순으로 컬럼을 만들어야 순서가 빠르다.
그리고 기본키를 만들면 인덱스는 자동으로 만들어진다.

기본키와 인덱스 차이는 기본키는 중복데이터가 안들어가지만 인덱스로 잡은 컬럼은 중복데이터가 들어간다.

> 인덱스 생성방법
CREATE INDEX TBL_TARGETS_TRACE_IX4 ON TBL_TARGETS_TRACE(INPT_DT);

인덱스를 A(Key),B(인덱스),C(인덱스)
C(Key),A(인덱스),B(인덱스)  이렇게 잡으면 쿼리에서 쓸때
where A='' 찾을 때 C,A냐 먼저 인덱스를 찾는다.

주의 할것이 where DATE_FORMAT(datacolumn,'%Y-%m-%d') 이렇게 쓰면 인덱스가 풀린다.

그래서 type이 datetime은 변형이 이뤄져야하기 때문에 인덱스로 잘 안잡는다.

> 굳이 잡는다면 아래처럼 between을 한다.
SELECT TARGET_NO,
TARGET_LATITUDE,
TARGET_ONGITUDE,
TARGET_MMSI,
TARGET_CALLSIGN,
TARGET_NATION,
TARGET_SHIPNAME,
INPT_DT
FROM TBL_TARGETS_TRACE
WHERE INPT_DT BETWEEN DATE_FORMAT('20250314000000','%Y%m%d%H%i%s') AND DATE_FORMAT('20250314235959','%Y%m%d%H%i%s
```
```
하지만 SYSDATE()는 인덱스가 안먹힌다.
그래서 NOW를 쓰고 AND범위는 NOW보다 커야한다!!!(단, 인덱스가 먹힐려면
특정 날짜를 정해야한다. 컬럼으로 지정하는게 아니라.)

EXPLAIN
SELECT * FROM TBL_TARGETS_TRACE 
WHERE TARGET_DATE BETWEEN DATE_FORMAT(NOW(),'%Y-%m-%d %H:%i:%s') AND '2025-03-18 23:59:59'
```
```
#)조인걸었을때 INPT, DateNTime 들다 인덱스가 걸려있어야 빠르다.
ON A.INPT_DT = B.DateNTime

```
```
CREATE INDEX index1 ON 테이블명(컬럼명1, 컬럼명2);

MySQL에서 인덱스를 사용할 때, 쿼리의 WHERE 절에 포함된 조건과 인덱스의 컬럼 순서가 중요합니다. 주어진 인덱스와 쿼리를 분석해보면 다음과 같습니다:

인덱스 1: (name, grade)
인덱스 2: (grade, name, class)
쿼리: WHERE grade = "" AND name = ""

이 경우, MySQL은 두 개의 인덱스 중에서 쿼리의 조건에 가장 적합한 인덱스를 선택합니다.

인덱스 1 (name, grade)는 name이 첫 번째 컬럼이므로, name 조건을 먼저 사용해야 합니다. 따라서 grade 조건을 사용할 수 없기 때문에 이 인덱스는 적합하지 않습니다.

인덱스 2 (grade, name, class)는 grade가 첫 번째 컬럼이므로, grade 조건을 먼저 사용할 수 있습니다. 이후 name 조건도 사용할 수 있으므로 이 인덱스는 쿼리에 적합합니다.

결론적으로, WHERE grade = "" AND name = "" 조건을 사용할 때 MySQL은 인덱스 2인 (grade, name, class)를 선택하여 사용하게 됩니다. 이 인덱스는 grade 조건을 먼저 처리하고, 그 다음에 name 조건을 처리할 수 있기 때문입니다.

```
```
단, 인덱스를 잡을때 useYn같은걸로 잡으면 쿼리에서 찾는게 70%이상값이라면 인덱스를 타지않고
그냥 전체를 타버린다.

```
```
기존 데이터가 들어가있는 상태에서 primary키를 설정하게 되면 에러가 나올 수 있는데
기존 데이터가 null로 들어가있는 값이 있을 경우 primary키로 설정안하고
unique index로 설정하게 하면 된다. 단, 중복된 데이터가 없어야한다.
```
```
mysql설정파일에 /etc/my.cnf에서 slow-query-log란 것은
[mysqld]
...
slow_query_log_file=/data/mysql/mysql-slow.log

위에 처럼 설정하고
mysqldump를 사용하여 데이터베이스를 덤프할 때, MySQL은 내부적으로 SELECT 쿼리를 사용하여 데이터를 가져옵니다. 이 과정에서 slow query log에 기록되는 쿼리는 실제로 실행된 SELECT 쿼리입니다.

slow query log는 MySQL에서 실행 시간이 오래 걸리는 쿼리를 기록하는 로그입니다. 이 로그는 성능 문제를 진단하는 데 유용합니다. 기본적으로, 쿼리가 long_query_time 설정에 지정된 시간보다 오래 걸리면 이 로그에 기록됩니다.

즉, db덤프를 받을때 한 테이블에 3천만건이 들어가있다고 하면 select 해서 가져오는 구조라는 것이다.
이말은 패킷프로그램에서 db에 3초동안 계속 던져주는데 db dump까지 같이해서 select까지 해버리면
lock이 걸릴 수 있고, 지금은 등부표 패킷프로그램이 뻗은 이유가 select에서 오래걸려서 기다리다가 패킷프로그램이 db재접속을 다시 안하기 때문에 db에 데이터가 안들어온것이다.

즉, db덤프를뜰때는 db를 내리고 하는 것이 맞다.
```



   



 
