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


## 팩토리 메소드 패턴
* 어떤 객체를 생성하는 책임을 추상정인 인터페이스로 감싸는 것

### 구현 방법
* 생성할 객체의 Interface와 이 객체를 생성을 담당하는 책임의 Interface를 만듬
```java
public interface Product{

}

public interface ProductFactory{
    Product createProduct();
}
```
* 생성을 담당하는 책임의 Interface를 상속받은 Concrete 클래스를 생성 후 해당 클래스에서 생성할 객체의 Concrete 클래스를 생성해서 반환
```java
public class ConcreteProduct implements Product{

}

public class ConcreateProductFactory implements ProductFactory{

    public Product createProduct(){
        return new ConcreteProduct();
    }
}
```
* 

### 장단점
**장점**
* 생성의 책임을 분리해서 커플링을 루즈하게 할 수 있음
* OCP 원칙을 지키는데 사용할 수 있음

**단점**
* 클래스가 늘어남

## 추상 팩토리 패턴
* 서로 관련있는 여러 객체를 만들어주는 인터페이스
* 구체적으로 어떤 클래스의 인스턴스를 사용하는지 감출 수 있음


### 구현 방법
* 자동차의 바퀴은 타이어와 내부의 휠의 조합으로 이루어져 있음
```java
public interface Wheel{

    private Tire tire;
    private InnerWheel innerWheel;
}

public interface Tire{

}

public interface InnerWheel{

}
```

* 바퀴를 만드는 Factory 클래스를 만들고 구체 클래스를 생성
```java
public interface WheelFactory{
    Wheel createWheel();
}

public class NormalWheelFactory{
    public Wheel createWheel(){
        Tire tire = new NormalTire();
        InnerWheel innerWheel = new NormalInnerWheel();
        return new NormalWheel(tire, innerWheel)
    }
}
```

* 위 코드에서 Tire와 InnerWheel을 만드는 Factory가 필요함 -> 추상 팩토리
```java
public interface WheelPartsFactory{

    Tire createTire();
    InnerWheel createInnerWheel();
}

public class NormalWheelPartsFactory implements WheelPartsFactory{
    public Tire createTire(){
        return new NormalTire();
    }

    public InnerWheel createInnerWheel(){
        return new InnerWheel();
    }
}
```

* 이제 `NormalWheelFactory`에서 `WheelPartsFactory` 사용해서 `Wheel`을 만들도록 수정
```java
public class NormalWheelFactory{

    private WheelPartsFactory wheelPartsFactory

    public Wheel createWheel(){
        Tire tire = wheelPartsFactory.createTire();
        InnerWheel innerWheel = wheelPartsFactory.createInnerWheel();
        return new NormalWheel(tire, innerWheel)
    }
}
```
* 이후에 새로운 Parts들이 구현되어야 한다면 WheelPartsFactory를 새로 구현해서 DI만 해주면 됨

### 팩토리 메소드 패턴과의 차이점
* 모양과 효과는 비슷
    - 둘 다 구체적인 객체 생성 과정을 추상화한 인터페이스를 제공

* 관점이 다름
    - 팩토리 메소드 패턴 : 팩토리를 구현하는 방법에 초점
    - 추상 팩토리 패턴 : 팩토리를 사용하는 방법에 초점

* 목적이 다름
    - 팩토리 메소드 패턴: 구체적인 객체 생성 과정을 하위 또는 구체적인 클래스로 옮기는 것이 목적
    - 추상 팩토리 패턴: 관련있는 여러 객체를 구체적인 클래스에 의존하지 않고 만들 수 있게 해주는 것이 목적

## 빌더 패턴
* 객체를 생성하기 위한 생성자가 여러개가 있을 때 유연한 생성을 위해서 상황에 맞게 조합해서 객체를 생성할 수 있게 해줌

### 구현 방법
```java
public class AClass{
    int a;
    int b;

    public AClass(int a, int b){
        this.a = a;
        this.b = b
    }

    public static class AClassBuilder{
        int a;
        int b;

        public AClass build(){
            return new AClass(this.a, thia.b);
        }

        public AClassBuilder a(int a){
            this.a = a;
            return this;
        }

        public AClassBuilder b(int b){
            this.b = b;
            return this;
        }
    }
}
```

### 장단점
**장점**
* 생성 순서가 복잡한 인스턴스를 단순화 할 수 있음
* 불완전한 객체를 사용하지 못하게 안정장치를 만들 수 있다

**단점**
* 클래스의 양이 많아진다
* 구조가 복잡해진다

## 프로토타입 패턴
* 기존 인스턴스를 복제하여 새로운 인스턴스를 만드는 방법

### 구현 방법
* 자바에서는 `Cloneable` 인터페이스를 구현하고 `Object`의 `clone()` 메소드를 재정의하면 된다.
```java
public class AClass implements Cloneable {

    private int a;
    private int b;

    public AClass(int a, int b){
        this.a = a;
        this.b = b;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return new AClass(this.a ,this.b);
    }
}
```

### 장단점
**장점**
* 복잡한 객체를 만드는 과정을 숨길 수 있음
* 기존 객체를 복제하는 과정이 새 인스턴스를 만드는 것보다 비용적인 면에서 효율적일 수 있음
* 추상적인 타입을 리턴할 수 있다

**단점**
* 복제한 객체를 만드는 과정 자체가 복잡할 수 있다 

# 구조 관련 디자인 패턴

## 어댑터 패턴
* 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴
* 클라이언트가 사용하는 인터페이스를 따르지 않는 기존 코드를 재사용할 수 있게 해준다


### 구현 방법
* 인터페이스 A가 있고 B 클래스가 있다고 가정
```java
public interface AInterface(){

    String getSomething();
}

public class BClass implements AInterface {
    public String getValue(){
        return "value"
    }
}
```

* 이때 클라이언트에서 A 인터페이스를 사용하는데 B를 구현체로 쓰고 싶을때 어댑터를 구현
```java
public class Adaptor implements AInterface{

    private final BClass b;

    public Adaptor(BClass b){
        this.b = b;
    }

    String getSomething(){
        return b.getValue();
    }
}
```

### 장단점
**장점**
* 어댑티를 변경하지 않고 확장이 가능하다
* 기존 코드의 책임과 변환의 책임을 분리해서 관리할 수 있다

**단점**
* 어댑터 코드 때문에 클래스가 많아지고 구조가 복잡해짐

## 브릿지 패턴
* 추상적인 것과 구체적인 것을 분리하여 연결하는 패턴

### 구현 방법
* 추상클래스와 인터페이스가 있고 추상클래스가 인터페이스를 가짐
```java
public abstract class AbstractClass {

    private NormalInterface interface;

    public AbstractClass(NormalInterface interface){
        this.interface = interface;
    }

    public void do(){
        this.interface.getValue();
    }
}

public interface NormalInterface {

    String getValue();

}
```

* 위와 같은 구조를 만들어 놓고 각 구현체들을 만들면 됨
```java
public class ConcreteClass extends AbstractClass {

    public ConcreteClass(NormalInterface interface){
        super(interface);
    }

}

public class NormalClass implements NormalInterface {

    public String getValue(){
        return "Normal";
    }
}
```

### 장단점
**장점**
* 추상적인 코드(행동, 메시지) 만으로 구조를 만들고 코드 변경 없이도 독립적으로 확장할 수 있다
* 추상적인 코드와 구체적인 코드를 분리할 수 있다

**단점**
* 계층 구조가 늘어나 복잡도가 증가

## 컴포짓 패턴
* 구조가 트리구조일 때 구현 가능
* 그룹 전체와 개별 객체를 동일하게 처리할 수 있는 패턴
* 클라이언트 입장에서 전체 or 부분을 동일한 컴포넌트로 인식할 수 있는 계층 구조를 만듬

### 구현 방법
* 클라이언트 입장에서 보는 Component가 필요
```java
public interface Component {

    int getPrice();
}
```

* 해당 컴포넌트를 구현한 구현체들이 필요
```java
public class Item implements Component{

    private int price;

    @Override
    public int getPrice(){
        return price;
    }
}

public class Bag implements Component{

    private List<Component> items; // Bag에서 Item을 가지는게 아니라 Component를 가짐

    @Override
    public int getPrice(){
        return items.stream().mapToInt(Component::getPrice).sum();
    }
}
```

### 장단점
**장점**
* 복잡한 트리 구조를 편하게 사용
* 다형성과 재귀 활용
* 클라이언트 코드를 변경하지 않고 새로운 타입을 추가할 수 있다

**단점**
* 트리를 만들어야 하기 때문에 지나친 일반화가 될 수 있음