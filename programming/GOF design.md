# 객체 생성 관련 디자인 패턴

## 싱글톤 패턴
* 인스턴스를 오직 한개만 제공하는 클래스
* 시스템 런타임, 환경설정 정보, 인스턴스가 여러개 일 때 문제가 생길 수 있는 경우가 있어서 필요

### 기본적인 구현 방법
* 기본적으로 외부에서 생성자를 생성 하지 못하게 private으로 막아야 됨
* static으로 자기 자신을 필드로 가지고 static method로 해당 필드만 반환하도록 생성
```
public class AClass {

    private static AClass instance;

    private AClass(){

    }

    public static AClass getInstance(){
        if(instance == null){
            instance = new AClass();
        }
        return instance;
    }
}
```
* 해당 멀티쓰레드 환경에서 문제가 생길 수 있음. thread-safe 하지 않은 방법
    - A와 B 쓰레드가 동시에 `instance == null` 조건에서 `true`를 반환해 서로 다른 인스턴스를 생성할 수 있는 위험이 있음


### Thread-Safe하게 만들기
* 메소드에 `synchronized` 키워드 사용. 
    - `synchrozied` 때문에 락에 의해 성능이 안좋아질 수 있다는 단점이 있음
```
public synchronized static AClass getInstance(){
    if(instance == null){
        instance = new AClass();
    }
    return instance;
}
```

* `eager initialization` 사용하기. 
    - 미리 생성한다는것 자체가 단점이 될 수 있음
```
private static final Setting INSTANCE = new Setting();
```

* `double checked locking` 사용하기. 
    - 2개의 쓰레드가 동시에 들어오더라도 내부의 `synchronized` block에 의해 `thread-safe`하게 처리됨
    - 필드에 `volatile` 키워드를 써줘야 함
```
private static volatile Setting instance;

public static AClass getInstance(){
    if(instance == null){
        synchronized(AClass.class){
            if(instance == null){
                instance = new AClass();
            }
        }
    }
    return instance;
}
```

* `staic inner` 클래스 사용하기
    - thread-safe 해지면서 안전하게 lazy 로딩까지 사용할 수 있는 방법

```
public class Setting {
    private Setting(){

    }

    private static class SettingHolder {
        private static final Setting INSTANCE = new Setting();
    }

    public static Setting getInstance(){
        return SettingHolder.INSTANCE;
    }
}
```

### 안전하고 단순하게 구현하는 방법
* enum 사용하기
    - 리플렉션에도 안전해짐
```
public enum App{

    INSTANCE;

}
```
