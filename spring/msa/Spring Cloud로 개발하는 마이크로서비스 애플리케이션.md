# Microservice와 Spring Cloud 소개

## 소프트웨어 아키텍처
* IT System은 아래와 같이 변화
    - 1960~1980: Fragile(깨지기 쉬운), CowBoys
    - 1990~2000: Robust, Distributed
    - 2010~: Resilient(탄력적인)/Anti-Fragile, Cloud Native

* Antifragile의 핵심
    - Auto scaling
    - Microservices
    - Chaos engineering
    - Continuous deployments

## Cloud Native Architecture
* 확장 가능
* 탄력적
* 장애 격리

### CI/CD
* 지속적인 통합, CI(Continuous Integration)
* 지속적 배포
    - Continuous Delivery
    - Contunuous Deployment
    - Pipe line
* 카나리 배포, 블루그린 배포
    - 100 중에 5는 새 버전 서비스, 95는 이전 버전 서비스를 이용하게 함
    - 점진적으로 새 버전 서비스로 트래픽을 이동

 ## Factors
 * BASE CODE - 코드를 한 곳에서 통일적인 관리 필요
 * DEPENDENCY ISOLATION - 각 마이크로 서비스는 자체 종속성을 가지고 패키지 됨. 전체 시스템에 영향을 주지 않고 변경 가능해야 됨
 * CONFIGURATIONS - 시스템 외부에서 마이크로서비스에 필요한 작업들을 제어
 * LINKABLE BACKING SERVICES - 보조서비스(DB, Redis, MQ) 같은걸 이용해서 MSA가 가져야할 기능을 추가로 지원할 수 있는 것
* STAGES OF CREATION - 빌드와 릴리즈와 실행 환경을 각각 분리
* STATELESS PROCESSES - 각 미이크로 서비스는 독립되어 실행될 수 있는 구조여야 함
* PORT BINDING - 각 마이크로서비스는 자체 포트에서 인터페이스와 함께 포함되어있는 기능이 있어야 한다
* CONCURRENCY - 동시성 제어가 가능해야 함
* DISPOSABILITY - 서비스 인스턴스 자체가 삭제 가능해야함
* 개발 단계와 프로덕션 단계 구분
* LOGS - 마이크로서비스에 의해서 발생된 로그를 이벤트 스트리밍으로 처리
* ADMIN PROCESSES FOR EVENTUAL PROCESSES - 시스템의 상태 파악을 위한 관리도구 필요

## Monolitic vs Microservice
* Monolitic
    - 하나의 어플리케이션 안에 모든 기능이 포함
    - 하나의 커다란 건축물과 같음
* Microservice
    - 각각의 구성요소 및 서비스의 내용을 분리해서 개발 및 운영하는 방식

## Microservice Architecture
### Microservice의 특징
* Challenges
* Samll Well Chosen Deployable Units
* Bounded Context
* RESTful
* Configuration Management
* Cloud Enabled
* Dynamic Scale Up And Scale Down
* CI/CD
* Visibility

### SOA와 MSA의 차이점
* SOA(Service Oriented Architectur)
    - 재사용을 통한 비용 절감
    - 공통의 서비스를 ESB에 모아 공통 서비스 형식으로 서비스 제공

* MSA
    - 서비스 간의 결합도를 낮추어 변화에 능동적으로 대응
    - 각 독립된 서비스가 노출된 REST API를 사용

