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
