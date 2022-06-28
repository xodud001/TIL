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
