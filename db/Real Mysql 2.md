# 11장. 쿼리 작성 및 최적화
* 애플리케이션에서 입력된 데이터를 데이터베이스에 저장하거나 데이터베이스로부터 필요한 데이터를 가져오려면 `SQL`이라는 정형화된 문장을 사용해야 한다
* SQL은 어떠한(What) 데이터를 요청하기 위한 언어이다.

## 11.1 쿼리 작성과 연관된 시스템 변수
* 대소문자 구분, 문자열 표기 방법 등과 같이 SQL 작성 규칙은 MySQL 서버의 시스템 설정에 따라 달라짐

### 11.1.1 SQL 모드
* STRICT_ALL_TABLES & STRICT_TRANS_TABLES
	* 서버에서 INSERT나 UPDATE 문장으로 데이터를 변경하는 경우 칼럼의 타입과 저장되는 값의 타입이 다를 때 자동으로 타입 변경을 수행. 
	* 이때 타입이 적절히 변환되기 어렵거나 칼럼에 저장될 값이 없거나 값의 길이가 칼럼의 최대 길이보다 큰 경우 문장을 계속 실행할지, 에러를 발생시킬지 결정
* ANSI_QUOTES
	* MySQL에서는 문자열 값을 표현하기 위해 홑따옴표와 쌍따옴표를 동시에 사용 가능.
	* 해당 값을 설정하면 홑따옴표만 문자열 값 표기로 사용 가능
	* 쌍따옴표는 칼럼명이나 테이블명과 같은 식별자를 표기하는 데만 사용 가능
* ONLY_FULL_GROUP_BY
	* MySQl의 쿼리에는 GROUP BY 절에 포함되지 않은 칼럼이더라도 집합 함수의 사용 없이 그대로 SELECT 절이나 HAVING 절에 사용 할 수 있다.
* PIPE_AS_CONCAT
	* MySQL에서 `||`는 OR 연산자와 같은 의미로 사용된다. 하지만 해당 값을 설정하면 오라클과 같이 문자열 연산자로 사용할 수 있다.
* PAD_CHAR_TO_FULL_LENGTH
    * 유효 문자열 뒤의 공백 문자를 제거하는 것을 끄는 옵션
* NO_BACKSLASH_ESCAPES
    * 역슬래시 문자를 이스케이프 문자로 사용 못함
* IGNORE_SPACE
    * 함수명과 괄호 사이 공백까지 함수의 이름으로 간주. 해당 옵션은 이 공백을 무시
* REAL_AS_FLOAT
    * MySQL에서 부동 소수점 타입은 FLOAT과 DOUBLE 타입이 지원됨. REAL 타입은 DOUBLE 타입의 동의어로 사용. 해당 옵션은 REAL 타입을 FLOAT 타입의 동의어로 바꿈
* NO_ZERO_IN_DATE & NO_ZERO_DATE
    * 해당 옵션이 활성화 되면 DATE 또는 DATETIME 타입의 칼럼에 '2020-00-00' 또는 '0000-00-00'과 같은 잘못된 날짜를 저장하는 것이 불가능해짐
* ANSI
    * SQL 표준에 맞게 동작하게 만들어 줌
* TRADITIONAL
    * 조금 더 엄격한 방법으로 SQL의 작동을 제어. 해당 모드가 아닐 때 경고로 처리되던 상황이 모두 에러로 바뀌고 SQL 문장은 실패함

### 11.1.2 영문 대소문자 구분
* MySQL 서버는 설치된 운영체제에 따라 테이블명의 대소문자를 구분. MySQL의 DB나 테이블이 디스크의 디렉터리나 파일로 매핑되기 때문

### 11.1.3 MySQL 예약어
* 생성하는 데이터베이스나 테이블, 칼럼의 이름을 예약어로 생성하면, SQL에서 사용하기 위해 항상 역따옴표(`)나 쌍따옴표로 감싸야 한다.
* 테이블 생성할 때부터 테이블명이나 칼럼명을 감싸지 않고 생성하면 에러가 나니까 거를 수 있음

## 11.3 MySQL 연산자와 내장 함수
* MySQL에서만 사용되는 연산자나 표기법이 있음
* 가능하면 SQL의 가독성을 높이기 위해 ANSI 표준 형태의 연산자를 사용하길 권장

### 11.3.1 리터럴 표기법 문자열

#### 11.3.1.1 문자열
* SQL 표준에서 문자열은 항상 홑따옴표를 사용해서 표시
* MySQL에서는 쌍따옴표를 사용해 문자열을 표기할 수도 있음

#### 11.3.1.2 숫자
* 숫자 값을 상수로 SQL에 사용할 때는 따옴표 없이 숫자값을 입력하면 됨

#### 11.3.1.3 날짜
* 다른 DBMS에서는 날짜 타입을 비교하거나 INSERT 하려면 문자열을 DATE 타입으로 변환하는 코드가 필요
* MySQL에서는 정해진 형태의 날짜 포맷으로 표기하면 MySQL 서버가 자동으로 DATE나 DATETIME 값으로 변환

#### 11.3.1.4 불리언
* BOOL이나 BOOLEAN이라는 타입이 있지만, 이것은 TINYINT 타입에 대한 동의어일 뿐
* 하지만 쿼리에 TRUE 또는 FALSE 형태로 비교하거나 저자알 수 있음

### 11.3.2 MySQL 연산자

#### 11.3.2.1 동등(Equal) 비교(=, <=>)
* 동등 비교는 '=' 기호를 사용해서 수행
* '<=>' 연산자도 제공하는데 '=' 연산자와 같지만, NULL 값에 대한 비교까지 수행. MySQL에서는 이 연산자를 NULL-Safe 비교 연산자라 표시

#### 11.3.2.2 부정(Not-Equal) 비교(<>, !=)
* '같지 않다' 비교를 위한 연산자는 '<>'를 일반적으로 많이 사용
* '!='도 사용 가능

#### 11.3.2.3 NOT 연산자(!)
* 연선의 결과를 반대로 만드는 연산자로 'NOT'을 사용
* '!'도 사용 가능

#### 11.3.2.4 AND(&&)와 OR(||) 연산자
* 일반적으로 불리언 표현식의 결과를 결합하기 위해 AND나 OR를 사용
* MySQL에서는 '&&'와 '||'도 허용

#### 11.3.2.5 나누기(/, DIV)와 나머지(%, MOD) 연산자
* 나누기는 일반적으로 '/'를 사용
* 나눈 목의 정수 부분만 가져오려면 DIV 연산자를 사용
* 나머지를 가져오는 연산자로는 '%' 또는 MOD 연산자 사용

#### 11.3.2.6 REGEXP 연산자
* 문자열 값이 어떤 패턴을 만족하는지 확인하는 연산자
* RLIKE는 REGEXP와 똑같은 비교를 수행하는 연산자

#### 11.3.2.7 LIKE 연산자
* 단순 문자열 패턴 비교 연산자
* `%`와 `_` 사용
    * `%` : 0 또는 1개 이상의 모든 문자에 일치
    * `_` : 정확히 1개의 문자에 일치

#### 11.3.2.8 BETWEEN 연산자
* BETWEEN 연산자는 '크거나 같다'와 '작거나 같다'라는 두 개의 연산을 하나로 합친 연산자

#### 11.3.2.9 IN 연산자
* IN은 여러 개의 값에 대해 동등 비교 연산을 수행하는 연산자
* 범위로 검색하는 것이 아니라 여러 번의 동등 비교로 실행하기 때문에 일반적으로 빠르게 처리됨

### 11.3.3 MYSQL 내장 함수
* DBMS 종류와 관계없이 기본적인 기능의 SQL 함수는 대부분 동일하게 제공, 하지만 함수의 이름이나 사용법은 표준이 없으므로 DBMS별로 거의 호환되지 않음
* MySQL은 기본으로 제공하는 내장 함수와 사용자가 직접 작성해서 추가할 수 있는 사용자 정의 함수로 구분

#### 11.3.3.1 NULL 값 비교 및 대체
* `IFNULL([칼럼|표현식], 대체값)`은 칼럼이나 표현식의 값이 NULL인지 비교하고, NULL이면 다른 값으로 대체하는 용도로 사용
* `ISNULL([칼럼|표현식])` 함수는 인자로 전달한 표현식이나 칼럼의 값이 NULL인지 아닌지 비교하는 함수

#### 11.3.3.2 현재 시각 조회(NOW, SYSDATE)
* `NOW()`: 현재 시간 반환. 하나의 SQL에서 동일한 값 반환
* `SYSDATE()`: 현재 시간 반환. 하나의 SQL 내에서도 호출되는 시점에 따라 결괏값이 달라짐
    * SYSDATE() 함수의 잠재적인 문제점
        * 해당 함수가 사용된 SQL은 레플리카 서버에서 안정적으로 복제되지 못함
        * 해당 함수와 비교되는 칼럼은 인덱스를 효율적으로 사용하지 못함

#### 11.3.3.3 날짜와 시간의 포맷(DATE_FORMAT, STR_TO_DATE)
* `DATETIME` 타입의 칼럼이나 값을 원하는 형태의 문자열로 변환해야 할 때는 `DATE_FORMAT()` 함수를 이용
* 날짜의 각 부분을 의미하는 지정자
    * %Y : 4자리 연도
    * %m : 2자리 숫자 표시의 월(01~12)
    * %d : 2자리 숫자 표시의 일자(01~31)
    * %H : 2자리 숫자 표시의 시(00~23)
    * %i : 2자리 숫자 표시의 분(00~59)
    * %s : 2자리 숫자 표시의 초(00~59)
* SQL에서 표준 형태(년-월-일 시:분:초)로 입력된 문자열은 자동으로 DATETIME 타입으로 변환되어 처리 할 수 있음
* 명시적으로 문자열을 DATETIME으로 변환해야 하는 경우에는 STR_TO_DATE() 함수를 이용해 문자열을 DATETIME 타입으로 변환할 수 있다. 지정자는 위와 같이 사용

#### 11.3.3.4 날짜와 시간의 연산(DATE_ADD, DATE_SUB)
* 특정 날짜에서 연도나 월일 또는 시간 등을 더하거나 뺄 때는 `DATE_ADD()` 함수나 `DATE_SUB()` 함수를 사용
* 첫 번째 인자는 연산을 수행할 날짜
* 두 번째 인자는 더하거나 빼고자 하는 월의 수나 일자의 수 등을 입력. 두 번째 인자는 `INTERVAL N [YEAR, MONTH, DAY, HOUR, MINUTE, SECOND,...]` 형태로 입력
```
mysql> SELECT DATE_ADD(NOW(), INTERVAL 1 DAY) AS tomorrow;
mysql> SELECT DATE_ADD(NOW(), INTERVAL -1 DAY) AS yesterday;
```
* 대표적인 단위
    * `YEAR` : 연도
    * `MONTH` : 월
    * `DAY` : 일
    * `HOUR` : 시간
    * `MINUTE` : 분
    * `SECOND` : 초
    * `MICROSECOND` : 마이크로 초
    * `QUARTER` : 분기
    * `WEEK` : 주

#### 11.3.3.5 타임스탬프 연산(UNIX_TIMESTAMP, FROM_UNIXTIME)
* UNIX_TIMESTAMP() 함수는 1970-01-01 00:00:00으로부터 경과된 초의 수를 반환
* FROM_UNIXTIME() 함수는 타임스탬프 값을 DATETIME 타입으로 변환하는 함수

#### 11.3.3.6 문자열 처리(RPAD, LPAD / RTRIM, LTRIM, TRIM)
* RPAD()와 LPAD() 함수는 문자열 좌측 또는 우측에 문자를 덧붙여서 지정된 길이의 문자열로 만드는 함수
* RTRIM()과 LTRIM() 함수는 문자열의 우측 또는 좌측에 연속된 공백 문자를 제거하는 함수
* TRIM()은 RTRIM()과 LTRIM()를 동시에 수행

#### 11.3.3.7 문자열 결합(CONCAT)
* 여러 개의 문자열을 연결해서 하나의 문자열로 반환하는 함수

#### 11.3.3.9 값의 비교와 대체(CASE WHEN ... THEN ... END)
* CASE WHEN은 SWITCH 구문과 같은 역할을 함
* CASE로 시작하고 END로 끝남.
* WHEN .. THEN .. 은 필요한 만큼 반복
```sql
SELECT emp_no, first_name,
    CASE gender WHEN 'M' THEN 'Man'
                WHEN 'F' THEN 'Woman'
                ELSE 'Unknown' END AS gender
FROM employees;
```

#### 11.3.3.10 타입의 변환(CAST, CONVERT)
* Prepared Statement를 제외하면 SQL은 텍스트 기반으로 작동하기 때문에 SQL에 포함된 모든 입력값은 문자열처럼 취급된다
* 이때 명시적으로 타입의 변환이 필요하다면 CAST() 함수를 이용
* CAST() 함수를 통해 변환할 수 있는 데이터 타입
    - DATE
    - TIME
    - DATETIME
    - BINARY
    - CHAR
    - DECIMAL
    - SIGNED
    - INTEGER
    - UNSIGNED
    - INTEGER
```sql
SELECT CAST('1234' AS SIGNED INTEGER);
SELECT CAST('2000-01-01' AS DATE);
```
* CONVERT() 함수는 타입을 변환하는 용도와 문자열의 문자 집합을 변환하는 용도
```sql
SELECT CONVERT(1-2, UNSIGNED)
SELECT CONVERT('ABC' USING 'utf8mb4')
```

#### 11.3.3.11 이진값과 16진수 문자열 변환
* HEX() 함수는 이지낪을 16진수 문자열로 변환
* UNHEX() 함수는 16진수의 문자열을 이진값으로 변환

#### 11.3.3.12 암호화 및 해시 함수(MD5, SHA, SHA2)
* MD5(), SHA1(), SHA2() 함수는 각각 128, 160, 224~512비트 해시 값을 반환

#### 11.3.3.13 처리 대기
* SLEEP(second) 함수는 프로그래밍 언어나 셸 스크립트 언어에서 제공하는 sleep 기능을 수행
```sql
SELECT SLEEP(1.5) FROM employees;
```
* SLEEP() 함수는 레코드의 건수만큼 호출.

#### 11.3.3.14 벤치마크
* BENCHMARK() 함수는 디버깅이나 간단한 함수의 성능 테스트용으로 유용
* 두 개의 동일 기능을 상대적으로 비교 분석하는 용도로 사용

## 11.4 SELECT
* SELECT는 여러 개의 테이블로부터 데이터를 조합해서 빠르게 가져와야 하기 때문에 여러 개의 테이블을 어떻게 읽을 것인가에 많은 주의릴 기울여야 함

### 11.4.1 SELECT 절의 처리 순서
* 드라이빙 테이블 기준으로 드리븐 테이블1, 2과 같이 WHERE 적용 및 조인 실행 -> GROUP BY -> DISTINCT -> HAVING 조건 저용 -> ORDER BY - LIMIT
* 인덱스를 이용해 처리할때는 ORDER BY나 GROUP BY 절이 있더라도 불필요하므로 생략
* GROUP BY 없이 ORDER BY만 있는 경우 조인보다 먼저 ORDER BY가 적용됨
* MySQL의 LIMIT은 항상 모든 처리의 결과에 대해 레코드 건수를 제한하는 형태로 사용

### 11.4.2 WHERE 절과 GROUP BY 절, ORDER BY 절의 인덱스 사용
* GROUP BY나 ORDER BY를 이용해 인덱스를 사용하는 방법

#### 11.4.2.1 인덱스를 사용하기 위한 기본 규칙
* WHERE 절이나, ORDER BY 또는 GROUP BY가 인덱스를 사용하려면 기본적으로 인덱스된 칼럼의 값 자체를 변환하지 않고 그대로 사용해야 함
* 인덱스는 칼럼의 값을 아무런 변환 없이 B-Tree에 정렬해서 저장
* WHERE 절에서 사용되는 비교 조건에서 연산자 양쪽의 두 비교 대상 값은 데이터 타입이 일치해야 함

#### 11.4.2.2 WHERE 절의 인덱스 사용
* WHERE 조건이 인덱스를 사용하는 방법
    - 작업 범위 결정 조건
    - 체크 조건

#### 11.4.2.3 GROUP BY 절의 인덱스 사용
* GROUP BY 절에 명시된 칼럼의 순서가 인덱스를 구성하는 칼럼의 순서와 같으면 GROYP BY 절은 인덱스를 이용할 수 있다
* 인덱스를 구성하는 칼럼 중에서 뒤쪽에 있는 칼럼은 GROUP BY 절에 명시되지 않아도 됨
* GROUP BY 절에 명시된 칼럼이 하나라도 인덱스에 없으면 인덱스 이용 못함

#### 11.4.2.4 ORDER BY 절의 인덱스 사용
* 정렬되는 각 칼럼의 오름차순 및 내림차순 옵션이 인덱스와 같거나 정반대인 경우에만 사용할 수 있음

#### 11.4.2.5 WHERE 조건과 ORDER BY 절의 인덱스 사용
* WHERE 조건은 A 인덱스를, ORDER BY는 B 인덱스를 사용하게 쿼리가 실행될 수는 없다
* WHERE 절과 ORDER BY 절이 같이 사용될때 인덱스를 이용하는 방법
    - WHERE 절과 ORDER By 절이 동시에 같은 인덱스를 이용
    - WHERE 절만 인덱스를 이용
    - ORDER BY 절만 인덱스를 이용
* 조건으로 사용된 칼럼의 순서가 인덱스와 왼쪽부터 일치해야 함

#### 11.4.2.6 GROUP BY 절과 ORDER BY 절의 인덱스 사용
* GROUP BY와 ORDER BY 절이 동시에 사용된 쿼리에서 하나의 인덱스를 사용해서 처리되려면, 각각에 명시된 칼럼의 순서와 내용이 모두 같아야 함
* GROUP BY와 ORDER BY가 같이 사용된 쿼리에서는 둘 중 하나라도 인덱스를 이용할 수 없을 때는 둘 다 인덱스를 사용하지 못함

#### 11.4.2.7 WHERE 조건과 ORDER BY 절, GROUP BY 절의 인덱스 사용
* 판단하는 방법
    - WHERE 절이 인덱스를 사용할 수 있는가?
    - GROUP BY 절이 인덱스를 사용할 수 있는가?
    - GROUP BY 절과 ORDER BY 절이 동시에 인덱스를 사용할 수 있는가?

* yes | yes | yes : 모두 인덱스 사용
* yes | no  | no  : WHERE 절만 사용
* no  | yes | yes : GROUP BY 절 ORDER BY 절 인덱스 사용
* no  | no  | no  : 인덱스 전혀 사용 못함

### 11.4.3 WHERE 절의 비교 조건 사용 시 주의사항
* 쿼리가 최적으로 실행되려면 적합한 인덱스와 함께 WHERE 절에 사용되는 비교 조건의 표현식을 적절하게 사용해야 함

#### 11.4.3.1 NULL 비교
* MySQL에서는 NULL 값이 포함된 레코드도 인덱스로 관리됨
* SQL에서 NULL의 정의는 비교할 수 없는 값
* 비교할 때 한쪽이라도 NULL이면 그 결과로 NULL이 나옴
* NULL 비교는 `IS NULL` 사용

#### 11.4.3.2 문자열이나 숫자 비교
* 문자열 칼럼이나 숫자 칼럼을 비교할 때는 반드시 그 타입에 맞는 상숫값을 사용할 것을 권장

#### 11.4.3.3 날짜 비교

##### 11.4.3.3.1 DATE 또는 DATETIME과 문자열 비교
* DATE 또는 DATETIME 타입의 값과 문자열을 비교할 때는 문자열 값을 자동으로 DATETIME 타입의 값으로 변환해서 비교 수행

##### 11.4.3.3.2 DATE와 DATETIME의 비교
* DATETIME 값에서 시간 부분만 떼어 버리고 비교하는 쿼리
```sql
SELECT COUNT(*) FROM employees WHERE hire_date > DATE(NOW());
```
##### 11.4.3.3.3 DATETIME과 TIMESTAMP의 비교
* DATE나 DATETIME 타입의 값과 TIMESTAMP 값을 비교할 때는 TIMESTAMP 값을 비교할 타입으로 변환후 비교

##### 11.4.3.4 Short-Circuit Evaludation
* 여러 개의 표현식이 AND 또는 OR 논리 연산자로 연결된 경우 선행 표현식의 결과에 따라 후행 표현식을 평가할지 말지 결정하는 최적화

### 11.4.4. DISTINCT
* DISTINCT를 남용하는 것은 성능적인 문제도 있지만 쿼리의 결과도 달라질 수 있음
* 9.2.5절 참조

### 11.4.5 LIMIT n
* LIMIT은 쿼리 결과에서 지정된 순서에 위치한 레코드만 가져오고자 할 때 사용
* 순서
    * 조건에 일치하는 레코드를 전부 읽어 옴
    * 읽어온 레코드를 정렬
    * n건만 사용자에게 반환
* 항상 쿼리의 가장 마지막에 실행
* LIMIT에서 필요한 레코드 건수만 준비되면 즉시 쿼리를 종료
* LIMIT의 offset이 커질수록 쿼리 시간도 오래걸림

### 11.4.6 COUNT()
* COUNT에서 *는 레코드 자체를 의미
* InnoDB 스토리지 엔진을 사용하면 COUNT를 할 때 직접 데이터나 인덱스를 읽어야만 레코드 건수를 가져올 수 있기 때문에 큰 테이블에서 COUNT() 함수를 사용하는 작업은 주의
