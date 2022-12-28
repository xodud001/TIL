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

## 데코레이터 패턴
* 기존 코드를 변경하지 않고 부가 기능을 추가하는 패턴
* 상속이 아닌 위임을 사용해서 보다 유연하게 부가 기능을 추가하는 것도 가능

### 구현 방법
* 기준이 되는 interface와 기본 concrete class가 필요
```java
public interface Printer{

    void print(String value);
}

public class  v implements Printer {

    @Override
    public void print(String value){
        System.out.println(value);
    }
}
```
* 기능을 확장 할 때 상속을 사용하면 1개 밖에 상속이 안되기 때문에 유연하게 만들지 못함. 
* 그래서 위임을 이용해서 interface를 멤버로 가짐
```java
public class DoubleWritePrinter implements Printer {

    public final Printer printer;

    public DoubleWritePrinter(Printer printer){
        this.printer = printer;
    }

    @Override
    public void print(String value){
        printer.print();
        printer.print();
    }
}
```

* 클라이언트 측에서는 Printer 인터페이스만 바라보고 구체 클래스를 주입할 때 아래와 같이 조합해서 생성
```java
public class App {

    public static void main(String[] args){
        Printer printer = new DoubleWritePrinter(new DefaultPrinter());

        printer.print();
    }
}
```

### 장단점
**장점**
* 단일 책임 원칙을 지키는데 도움을 줌
* 기능의 조합이 필요할 때 새로운 클래스를 만들지 않고 기능을 조합할 수 있다
* 컴파일 타임이 아닌 런타임에 동적으로 기능을 변경할 수 있다

**단점**
* 데코레이터를 생성할 때 조합하는 코드가 복잡할 수 있다

## 퍼사드 패턴
* 복잡한 서브 시스템의 의존성을 최소화하는 방법
* 클라이언트가 사용해야 하는 복잡한 서브 시스템 의존성을 간단한 인터페이스로 추상화

| **퍼사드**는 건물의 출입구로 이용되는 정면 외벽 부분을 가리키는 말

* 구현 방식은 일반적으로 커플링이 강한 코드를 기능별로 분리하고 인터페이스로 추상화 해서 뒤로 숨기는 것

### 장단점
**장점**
* 구체적인 의존성이 서브시스템 코드에 남아 있지만 사용하는 쪽에서는 단순하고 읽기 쉬움


**단점**
* 구체적인 의존성이 서브시스템 코드에 남아 있음

## flyweight pattern
* 객체를 가볍게 만들어 메모리 사용을 줄이는 패턴
* 자주 변하는 속성과 변하지 않는 속성을 분리하고 재사용하여 메모리 사용량을 줄임

### 구현 방법
* 다양한 속성을 가진 Character 클래스가 있음
```java
public class Character {

    private char value;
    private String color;
    private String fontFamily;
    private int fontSize;

    public Character(char value, String color, String fontFamily, int fontSize) {
        this.value = value;
        this.color = color;
        this.fontFamily = fontFamily;
        this.fontSize = fontSize;
    }
}
```
* 해당 클래스의 속성 중에서 fontFamily와 fontSize를 Font라는 클래스로 분리
* 여러 곳에서 공통적으로 사용되기 때문에 immutable하게 생성
```java
public final class Font {

    private final String fontFamily;
    private final int size;

    public Font(String fontFamily, int size) {
        this.fontFamily = fontFamily;
        this.size = size;
    }

    public String getFontFamily() {
        return fontFamily;
    }

    public int getSize() {
        return size;
    }
}
```

* 해당 클래스를 생성하는 Factory를 생성
```java
public class FontFactory {

    private Map<String, Font> cache = new HashMap<>();

    public Font getFont(String font){
        if (cache.containsKey(font)){
            return cache.get(font);
        } else {
            String[] token = font.split(":");
            Font newFont = new Font(token[0], Integer.parseInt(token[1]));
            cache.put(font, newFont);
            return newFont;
        }
    }
}
```

* 위의 팩토리를 이용해서 Font를 만들고 Character 생성
```java
FontFactory fontFactory = new FontFactory();

Character c1 = new Character('h', "white", fontFactory.getFont("Nanum:12"));
Character c2 = new Character('e', "white", fontFactory.getFont("Nanum:12"));
Character c3 = new Character('l', "white", fontFactory.getFont("Nanum:12"));
Character c4 = new Character('l', "white", fontFactory.getFont("Nanum:12"));
Character c5 = new Character('o', "white", fontFactory.getFont("Nanum:12"));
```

### 장단점
**장점**
* 애플리케이션에서 사용하는 메모리를 줄일 수 있음

**단점**
* 구조가 복잡해진다

## 프록시 패턴
* 특정 객체에 대한 접근을 제어하거나 기능을 추가할 수 있는 패턴
* 초기화 지연, 접근 제어, 로깅, 캐싱 등 다양하게 사용

### 구현 방법
**상속을 이용한 방법**
```java
public class Service{

    public void call(){
        System.out.println("call");
    }
}

public class ServiceProxy extends Service{

    @Override
    public void call(){
        // do something
        super.call();
    }
}
```
```java
Service service = new ServiceProxy();
service.call();
```

**인터페이스를 이용한 방법**
```java
public interface Service{

    void call();
}

public class DefaultService implements Service {

    @Override
    public void call(){
        System.out.println("call");
    }
}

public class ServiceProxy implements Service {

    private final Service service;

    public ServiceProxy(Service service){
        this.service = service;
    }

    @Override
    public void call(){
        // do something
        service.call();
    }
}
```
```java
Service defaultService = new DefaultService();
Service service = new ServiceProxy();
service.call();
```

### 장단점
**장점**
* 기존 코드를 변경하지 않고 새로운 기능을 추가할 수 있음
* 기존 코드가 해야 하는 일만 유지할 수 있음
* 기능 추가 및 초기화 지연 등으로 다양하게 활용할 수 있다

**단점**
* 코드의 복잡도가 증가

# 행동 관련 디자인 패턴

## 책임 연쇄 패턴
* 요청을 보내는 쪽과 요청을 처리하는 쪽을 분리하는 패턴
* 핸들러 체인을 사용해서 요청을 처리

### 구현 방법
* 핸들러에 해당하는 추상 클래스를 생성
```java
public abstract class Handler {

    private Handler handler;

    public Handler(Handler handler){
        this.handler = handler;
    }

    public void handle(Request request){
        if(handler != null){
            this.handler.handle();
        })
    }
}
```

* 위의 추상클래스 기반의 구체 클레스 구현
```java
public class AHandler extends Handler {

    public AHandler(Handler handler){
        super(handler);
    }

    @Override
    public void handle(Request request){
        System.out.println(request);
        super.handle(request)
    }
}

public class BHandler extends Handler {

    public AHandler(Handler handler){
        super(handler);
    }

    @Override
    public void handle(Request request){
        System.out.println(request);
        super.handle(request)
    }
}
```

* A와 B를 연쇄적으로 호출하도록 구성해서 구현
```java
Handler aHandler = new AHandler(null);
Handler bHandler = new BHandler(aHandler);

bHandler.handle(new Request());
```

### 장단점
**장점**
* 클라이언트 코드를 변경하지 않고 새로운 핸들러를 체인에 추가할 수 있음
* 각각의 핸들러가 단일 책임을 유지할 수 있다
* 순서를 강제할 수 있다

**단점**
* 디버깅시에 코드의 흐름을 파악하기 어렵다

## 커맨드 패턴
* 요청을 캡슐화 하여 호출자(invoker)와 수신자(receiver)를 분리하는 패턴
* 요청을 처리하는 방법이 바뀌더라도, 호출자의 코드는 변경되지 않음

### 구현 방법
* 호출하는 쪽(invoker)과 호출 되는 쪽(receiver)의 코드가 있음
```java
public class Invoker {
    private final Receiver receiver;

    public Invoker(Receiver receiver){
        this.receiver = receiver;
    }

    public void invoke(){
        this.receiver.do();
    }
}

public class Receiver {

    public void do(){
        System.out.println("call");
    }
}
```

* Invoker에서는 Command만 바라보고 Command를 구현한 클래스에서 Receiver를 호출하도록 함
```java
public interface Command {

    void execute();
}

public class Invoker {
    private final Command command;

    public Invoker(Command command){
        this.command = command;
    }

    public void invoke(){
        this.command.execute();
    }
}

public class ReceiverCommand implements Command {

    private final Receiver receiver;

    public ReceiverCommand(Receiver receiver){
        this.receiver = receiver;
    }

    @Override
    public void execute(){
        this.receiver.do();
    }
}
```

* Invoker에게 ReceiverCommand를 구현체로 넘겨주고 실행한다
```java
Invoker invoker = new Invoker(new ReceiverCommand(new Receiver));
invoker.invoke();
```

### 장단점
**장점**
* 기존 코드를 변경하지 않고 새로운 커맨드 추가 가능
* 수신차의 코드가 변경되어도 호출자의 코드는 변경되지 않음
* 커맨드 객체를 다양한 방법으로 활용할 수 있다

**단점**
* 복잡하고 클래스가 많아짐

## 인터프리터 패턴
* 자주 등장하는 문제를 간단한 언어로 정의하고 재사용하는 패턴
* 반복되는 문제 패턴을 언어 또는 문법으로 정의하고 확장할 수 있음

### 구현 방법
* 대상이 될 Expression과 대상을 파싱할 Parser가 필요함
* Expression은 TerminalExpression과 NonTerminalExpression로 구분
    - TerminalExpression는 재귀 호출이 없음
    - NonTerminalExpression는 같은 타입의 필드를 가져 Abstract Syntax Tree 구조를 만들어나감
* Parser는 Abstract Syntax Tree 구조의 Expression을 받아서 파싱하고 결과를 반환
* 반환된 Expression에 Context를 전달하고 실행

### 장단점
**장점**
* 자주 등장하는 문제 패턴을 언어와 문법으로 정의
* 기존 코드를 변경하지 않고 새로운 Expression을 추가할 수 있다

**단점**
* 복잡한 문법을 표현하려면 Expression과 Parser가 복잡해진다

## 이터레이터 패턴
* 집합 객체 내부 구조를 노출시키지 않고 순회 하는 방법을 제공하는 패턴
* 집합 객체를 순회하는 클라이언트 코드를 변경하지 않고 다양한 순회 방법을 제공

### 구현 방법
* 기본적으로 자바에는 Iterator라는 인터페이스가 있어서 Collection들을 Iterator로 변환해서 사용하면 된다
```java
List<Object> objs = new ArrayList();
Iterator<Object> itr = objs.iterator();

while(itr.hasNext()){
    Object obj = itr.next();
}
```
* 원본의 Collection에 추가로 정렬이나 필터링 같은 추가 기능이 들어가야 한다면 새로운 ConcreteIterator 클래스를 만들고 Iterator를 상속하고, 생성자로 받은 Iterator에 추가 기능들을 처리해서 처리한다.
```java
public class ConcreteIterator implements Iterator {

    private final Iterator<Object> internalIterator;

    public ConcreteIterator(Iterator iterator){
        //do something to iterator
        this.internalIterator = iterator;
    }

    @Override
    public boolean hasNext(){
        return this.internalIterator.hasNext();
    }

    @Override
    public Object next(){
        return this.internalIterator.next();
    }
}
```

### 장단점
**장점**
* 집합 객체가 가지고 있는 객체들에 손쉽게 접근이 가능하다
* 일관된 인터페이스를 사용해 여러 형태의 집합 구조를 순회할 수 있다

**단점**
* 클래스가 늘어나고 복잡도가 증가한다

## 중재자 패턴
* 여러 객체들이 소통하는 방법을 캡슐화하는 패턴
* 여러 컴포넌트간의 결합도를 중재라를 통해 낮출 수 있다

### 구현 방법
* Client가 있고 해당 클라이언트가 AService와 BService를 보고있다 가정

```java
public class Client {

    private final AService a;
    private final BService b;

    public void callA(){
        a.service(this);
    }

    public void callB(){
        b.service(this);
    }
}

public class AService{

    public void service(Client client){

    }
}

public class BService {

    public void service(Client client){

    }
}
```

* 이때 중간에 중재자를 만들어서 Client에서는 중재자를 통해서만 각각의 서비스에 접근하도록 해서 의존성을 낮춤
```java
public class Mediator {
    public final AService a;
    public final BService b;

    public void aService(Client client){
        a.service(client)
    }

    public void bService(Client client){
        b.service(client)
    }
}
```

* 중재자가 생성된 후 수정된 코드
```java
public class Client {

    private final Mediator mediator;

    public void callA(){
        mediator.aService(this);
    }

    public void callB(){
        mediator.bService(this);
    }
}
```

### 장단점
**장점**
* 컴포넌트 코드를 변경하지 않고 새로운 중재자를 만들어 사용할 수 있다
* 각각의 컴포넌트 코드를 보다 간결하게 유지할 수 있다

**단점**
* 중재자 역할을 하는 클래스의 복잡도와 결합도가 증가

## 메멘토 패턴
* 캡슐화를 유지하면서 객체 내부 상태를 외부에 저장하는 방법
* 객체 상태를 외부에 저장했다가 해당 상태로 다시 복구할 수 있음

### 구현 방법
* 상태를 가진 AClass가 있다
```java
public Class AClass {

    private int stateA;
    private int stateB;

}
```

* AClass의 상태를 저장하는 메멘토를 생성
* 상태가 변경되면 안되기 때문에 불편하게 작성
```java
public class AMemento{

    private final int stateA;
    private final int stateB;

    public AMemento(int stateA, int stateB){
        this.stateA = stateA;
        this.stateB = stateB;
    }
}
```

* 이후 AClass에서 메멘토를 이용해서 상태를 저장하고 복원하는 기능 생성
```java
public class AClass{
    private int stateA;
    private int stateB;

    public AMemento save(){
        return new AMemento(this.stateA, this.stateB);
    }

    public void restore(AMemento save){
        this.stateA = save.stateA;
        this.stateB = save.stateB;
    }
}
```

### 장단점
**장점**
* 객체 내부의 상태를 외부에 노출하지 않고도 객체 상태의 스냅샷을 만들 수 있음

## 옵저버 패턴
* 다수의 객체가 특정 객체 상태 변화를 감지하고 알림을 받는 패턴
* 발행(Pub)-구독(Sub) 패턴을 구현할 수 있다

### 구현 방법
- 호출 대상이 되는 Observer 인터페이스 생성
- 실제 동작하는 ConcreteObserver 구현 클래스 생성
```java
public interface Observer{

    void handle(String value);
}

public class ConcreteObserver {

    public void handle(String value){
        // do something...
    }
}
```

- 외부 동작에 의해 상태 변경이 일어나 Observer를 호출할 주체가 필요
- 해당 주체(Subject)는 Observer의 List를 가지고 등록, 삭제, 알림 하는 기능이 필요
```java
public class Subject{

    private final List<Observer> observers = new ArrayList<>();

    // Observer 등록
    public void subscribe(Observer observer){
        this.observers.add(observer);
    }

    // Observer 삭제
    public void unsubscribe(Observer observer){
        this.observer.remove(observer);
    }

    // Observer에 메시지 전달
    public void notify(String value){
        this.observers.forEach(o -> o.handle(value));
    }
}
```

### 장단점
**장점**
- 상태를 변경하는 객체와 변경을 감지하는 객체의 관계를 느슨하게 유지
- Subject의 상태 변경을 주기적으로 조회하지 않고 자동으로 감지할 수 있다
- 란타임에 옵저버를 추가하거나 제거할 수 있다

**단점**
- 복잡도 증가
- 다수의 Observer 객체를 등록 이후 해지하지 않는다면 memory leak이 발생할 수도 있다




