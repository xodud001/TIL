# Mockito
* Mock : 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 객체의 행동을 관리하는 객체
* Mockito: Mock 객체를 쉽게 만들고 관리하고 검증할 수 있는 방법을 제공

## 시작하기
* Spring Boot 2.2에서 자동으로 추가

## Mock 만들기
* `Mockito.mock()`을 이용해서 생성
``` java
MemberService memberService = Mockito.mock(MemberService.class);
StudyRepository studyRepository = Mockito.mock(StudyRepository.class);
```
* `@Mock` 어노테이션을 이용해서 생성
```java
// 클래스에 DI 받기
@ExtendWith(MockitoExtension.class) // @Mock 어노테이션이 붙은 필드에 DI 처리 등을 위해 필요
class StudyServiceTest {

    @Mock
    MemberService memberService;

    @Mock
    StudyRepository studyRepository;
}
```

```java
// 매개변수로 받기
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {
    
    @Test
    void test1(@Mock MemberService memberService,
            @Mock StudyRepository studyRepository){
        StudyService studyService = new StudyService(memberService, studyRepository);
    }
}
```

## Mock 객체 Stubbing
모든 Mock 객체의 행동
* Null을 리턴
* Primitive 타입은 기본 Primitive 값
* 컬렉션은 비어있는 컬렉션
* void 메소드는 예외를 던지지 않고 아무일도 일어나지 않음

Mock 객체를 조작해서 아래와 같이 할 수 있다
* 특정한 매개변수를 받은 경우 특정한 값을 리턴하거나 예외를 던지도록 할 수 있음
* void 메소드 특정 매개변수를 받거나 호출된 경우 예외를 발생시킬 수 있다
* 메소드가 동일한 매개변수로 여러번 호출될 때 각기 다르게 행동하도록 조작할 수 있다

```java
Member member = new Member();
member.setId(1L);
member.setEmail("kty@naver.com");

when(memberService.findById(1L)).thenReturn(Optional.of(member));
```

## Mockito BDD 스타일 API
BDD : 애플맄이션이 어떻게 '행동'해야 하는지에 대한 공통된 이해를 구성하는 방법으로, TDD에서 창안

행동에 대한 스펙
* Title
* Narrative
    + As a
    + I want
    + so that
* Acceptance criteria
    + Given
    + When
    + Then

Mockito는 BddMockito라는 클래스를 통해 BDD 스타일의 API를 제공

```java

// Normal
when(memberService.findById(1L)).thenReturn(Optional.of(member));

// BDD
given(memberService.findById(1L)).willReturn(Optional.of(member));
```