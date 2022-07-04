# 함수형 인터페이스
* 함수형 인터페이스는 추상 메소드를 하나만 가지고 있는 인터페이스이다.
* 추상 메소드가 2개 이상 있으면 안됨!
* 함수형 인터페이스를 만들기 위해서는 @FunctionInterface 어노테이션을 인터페이스 달아주면 된다. 자바에서 지원되는 기능이라 import 없음
    * 해당 어노테이션을 달고 추상 메소드를 2개 이상 만들면 컴파일 에러 발생

interface에서는 abstract 생략가능. `void run();`이라고 선언된 메소드는 앞에 `abstract`를 포함하고 있음.

자바 8에서 interface에 static, default 키워드가 붙은 메소드가 있어도 **추상 메소드만 1개이면 함수형 인터페이스이다.**

```java
@FunctionalInterface
public interface RunSomething {

    void doIt();
}
```


## 익명클래스로 사용
위의 함수형 인터페이스를 아래와 같이 익명클래스를 생성해서 사용할 수 있다.
```java
RunSomething runSomething = new RunSomething() {
    @Override
    public void doIt() {
        System.out.println("test");
    }
};

runSomething.doIt();
```

## 람다로 사용
위의 함수형 인터페이스를 아래와 같이 람다를 이용해서 구현할 수 있다.
```java
RunSomething runSomething = () -> System.out.println("test");

runSomething.doIt();
```
람다는 함수같이 보이지만 java에서는 이 또한 객체이다.

# 람다
* 함수형 인터페이스를 익명 클래스로 사용 할 때 코드를 줄여서 표현 할 수 있는 식
* `(...인자) -> { 바디 }` 로 표현되는 식
* 바디가 1줄이면 `{}` 생략 가능
* 인자에 타입은 타입추론을 이용해서 생략 가능
* 람다에서 외부 변수를 참조 할 때는 final or effectively final 만 참조 가능
    * effectively final : final 키워드가 없는 외부에 선언된 변수가 람다에서 참조될 때 final로 바뀌는 것.
* 람다는 변수의 scope이 감싸고 잇는 메소드와 같음
    + 외부에 i라는 변수가 있고 파라미터나 내부에서 i라는 변수가 선언되서 사용 될 때 외부의 i라는 변수에 shadowing이 안됨
        + shadowing : 내부의 변수가 외부의 변수를 가리는 것

# 메소드 레퍼런스
람다가 하는 일이 기존 메소드 또는 생성자를 호출하는 용도라면, 메소드 레퍼런스를 사용해서 매우 간결하게 표현할 수 있음

## 메소드 참조 방법
* 스태틱 메소드 참조 = `타입::스태틱 메소드`
* 특정 객체의 인스턴스 메소드 참조 = `객체 레퍼런스::인스턴스 메소드`
* 임의 객체의 인스턴스 메소드 참조 = `타입::인스턴스 메소드`
* 생성자 참조 = `타입::new`

# 인터페이스 기본 메소드와 스태틱 메소드
* 기본 메소드
    + 인터페이스에 메소드 선언이 아니라 구현체를 제공하는 방법
    + 해당 인터페이스의 구현체들에 컴파일 에러를 내지않고 새 기능 추가 할 수 있음
    + 구현체는 해당 기능을 모르기 때문에 주의
    + Object가 제공하는 기능은 기본 메소드로 제공 불가
* 스태틱 메소드
    + 인터페이스에 스태틱 메소드를 제공할 수 있음


# 스트림 API
* 연속된 데이터를 처리하는 operation들의 모음
* 데이터를 저장하고 있는 저장소(컬렌션)가 아님
* 스트림이 처리하는 데이터 소스를 변경하지 않음
* 중개 오펴레이션들은 근본적으로 Lazy 함
    * 종료 오퍼레이션이 실행되기 전까지 중개 오퍼레이션들은 실행되지 않음


## 중개 오퍼레이션
* Stream을 리턴
* Stateful / Stateless로 더 상세하게 구분 가능(distinct나 sorted 같이 이전 데이터를 참조해야 하는 오퍼레이션은 Stateful 오퍼레이션)
* filter, map, limit, skip, sorted...

## 종료 오퍼레이션
* Stream을 리턴하지 않음
* collect, allMatch, count, forEach, min, max...


# 스트림 API 2
* 걸러내기 : `filter(Predict)`
    * 특정 조건에 만족하는 데이터만 다음 스트림으로 이동
* 변경하기 : `map(Function) or flatMap(Function)`
    * AClass를 BClass로 변경해서 스트림을 이어갈 수 있음
* 생성하기 : `generate(Supplier) or Iterate(T seed, UnaryOperator)`
    * 10부터 1씩 증가하는 무제한 숫자 스트림
    * 랜덤 int 무제한 스트림
* 제한하기 : `limit(long) or skip(long)`
    * 최대 5개의 요소가 담긴 스트림을 리턴한다
    * 앞에서 3개를 뺀 나머지 스트림을 리턴한다
* 스트림에 있는 데이터가 특정 조건을 만족하는지 확인 : `anyMatch(), allMatch(), nonMatch()`
    * 특정 조건에 맞는 데이터가 있는지 확인
    * 특정 조건에 모든 데이터가 맞는지 확인
* 개수 세기 : `count()`
    * 마지막 스트림의 갯수 세기
* 스트림을 데이터 하나로 뭉치기 : `reduce(identity, BiFunction), collect(), sum(), max()`
    * 모든 문자열 이어 붙이기
    * 최대값, 통계 구하기
    * 모든 데이터를 하나의 Collection에 옮겨 담기
