# JUnit5
자바 개발자가 가장 많이 사용하는 테스팅 프레임워크
* 단위 테스트를 작성하는 자바 개발자 93%가 JUnit을 사용
* 자바 8 이상 필요

ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ
| Jupiter | Vintage |
ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ
|   JUnit Platform  |
ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ

* Platform : 테스트를 실행해주는 런처 제공. TestEngine API 제공
* Jupiter : TestEngine API 구현체로 JUnit 5 제공
* Vintage : JUnit4와 3를 지원하는 TestEngine 구현체

## 시작하기
* JUnit 의존성을 추가하고 test 클래스를 만들어서 사용
* Spring Boot 2.2 버전 부터 기본적으로 JUnit5를 기본으로 제공
* JUnit5 부터 클래스와 메소드에 public 키워드를 붙일 필요 없음
* 테스트를 하기 위한 메소드에 @Test 어노테이션을 달면 테스트 가능
* @BeforeAll, @AfterAll, @BeforeEach, @AfterEace 어노테이션을 이용해서 테스트의 시작과 끝에 특정 동작을 수행 할 수 있음
    * @BeforeAll와 @AfterAll는 특정 클래스 내의 @Test 어노테이션 붙은 모든 메소드들의 실행 전후 한번만 실행된다.
        * 메소드 시그니처에 static 필요
    * @BeforeEach와 @AfterEace는 @Test 어노테이션 붙은 각각의 메소드들의 실행 전후에 실행된다.
* @Disabled 어노테이션으로 실행하고 싶지 않는 테스트에 적용

## 테스트 이름 표시하기
기본적으로 테스트를 실행하면 report에 각 테스트 이름이 표시됨

* @DisplayNameGeneration
    * 기본적으로 출력되는 Class와 Method의 이름의 생성 전략을 지정 할 수 있음
* @DisplayName
    * Method 별로 테스트 이름을 자유롭게 지정 할 수 있다

## 테스트 검증하기
* 실제 값이 기대한 값과 같은지 확인
    + `assertEquals(expected, actual)`
* 값이 null이 아닌지 확인
    + `assertNonNull(actual)`
* 다음 조건이 참(true)인지 확인
    + `assertTrue(boolean)`
* 모든 확인 구문 확인
    + `assertAll(executables..)`
* 예외 발생 확인
    + `assertThrows(expectedType, executable)`
* 특정 시간 안에 실행이 완료되는지 확인
    + `assertTimeout(duration, executable)`

## 조건에 따라 테스트 실행하기
* `assumeTrue(boolean)` 메소드를 이용해서 해당 테스트 조건 설정
* `assumingThat(boolean, executable)` 메소드를 이용해서 특정 테스트 내에서 세심하게 조건 설정 가능
* `@EnabledOnOs` 어노테이션으로 OS 별로 테스트 실행 조건 설정
* `@EnabledOnJre` 어노테이션으로 특정 자바 버전 별로 테스트 실행 조건 설정
* `@EnabledIfEnvironmentVariable` 어노테이션으로 환경변수 값 조건에 따라 설정

## 테스트 태깅과 필터링
테스트를 그룹화 할 수 있음. 모듈화, 단위 테스트인지, 통합 테스트 인지 등등
* `@Tag` 어노테이션으로 태그 설정
    + Gradle에서는 Task 별로 테스트 할 Tag 지정 가능

## 커스텀 태그
JUnit5 어노테이션을 조합하여 커스텀 태그를 만들 수 있음

``` java
@Retention(RUNTIME)
@Target(METHOD)
@Test
@Tag("fast")
public @interface FastTest{
}

@Retention(RUNTIME)
@Target(METHOD)
@Test
@Tag("slow")
public @interface SlowTest{
}

class Test{

    @FastTest
    void fast(){}

    @SlowTest
    void slow(){}
}
```

## 테스트 반복하기
* 테스트를 반복해서 검증해야 하는 경우 `@RepeatedTest(count)` 어노테이션을 이용해서 설정

```java
@RepeatedTest(10)
void repeat(RepeatationInfo info){ // 현재 반복 정보를 받을 수 있음
    // do something...
}
```

* `@ParameterizedTest` 어노테이션을 이용해서 여러 파라미터 값을 이용해서 테스트 가능

``` java
@ParameterizedTest
@ValueSorce(strings= {"반복적으로", "테스트를", "실행하는", "방법"})
void parameter(String value){
    // do something...
}
```

* `@ValueSorce`에는 `String` 외에도 다른 타입을 이용해서 할 수 있음

[@ParameterizedTest 참조 링크](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests)

## 테스트 인스턴스
* 각 테스트의 독립성을 위해서 각 `@Test` 마다 해당 클래스를 새로 만듬
    * 테스트는 순서를 보장할 수 없기 때문에 각 테스트 간에는 의존성이 없어야 됨
* `@TestInstance` 어노테이션으로 테스트 인스턴스 생성 전략을 변경 할 수 있음

## 테스트 순서
* 기본적으로 단위 테스트는 독립적이어야 한다.
* 하지만 통합 테스트 같은 경우, 순서가 필요할 수가 있는데 이때, `@TestMethodOrder` 어노테이션을 이용해서 순서를 지정 할 수 있다.
    * MethodOder의 구현체
        + Alphanumeric
        + OrderAnnotation. 메소드의 실행 순서를 `@Order` 어노테이션으로 지정
        + Random

## 확장 모델
* JUnit 4의 확장 모델 : @RunWith(Runner), TestRule, MethodRule
* JUnit 5의 확장 모델 : Extension

### 확장팩 등록 방법
* 선언적인 등록
* 프로그래밍 등록
* 자동 등록, 자바 ServiceLoader 이용

### 확장팩 만드는 방법
* 테스트 실행 조건
* 테스트 인스턴스 팩토리
* 테스트 인스턴스 후-처리기
* 테스트 매개변수 리졸버
* 테스트 라이프사이클 콜백
* 예외처리
* ...


