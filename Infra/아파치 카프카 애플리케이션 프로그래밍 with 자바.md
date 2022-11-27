# Chapter 1. 들어가며

## 1.1. 카프카의 탄생
* 링크드인 팀에서 파편화된 데이터를 저장하는 기능이 필요
* 기존에 나와있던 방식으로는 데이터 파이프라인 복잡도를 낮춰주지는 못함
* 결국 신규 시스템을 만들었는데 그 결과물이 아파치 카프카

## 1.2. 빅데이터 파이프라인에서 카프카의 역할
* 데이터 레이크
    - 빅데이터를 저장하고 활용하기 위해서는 생성되는 데이터를 모두 모으는 것이 중요
    - 데이터 레이크는 데이터가 모이는 저장 공간을 뜻함
    - 필터링이나 패키지화되지 않은 데이터가 저장된다는 점이 특징
* 서비스에서 발생하는 데이터를 데이터 레이크로 모으려면 단순히 엔드 투 엔드 방식으로 넣을 수 있다
* 하지만 트래픽이 많아지면 복잡도가 올라가는 문제가 발생
* 이를 해결하기 위해서 데이터를 추출하고 변경, 적재하는 과정을 묶은 데이터 파이프라인을 구축
* 엔드 투 엔드 방식의 데이터 수집 및 적재를 개선하고 안정성을 추구하며, 유연하면서도 확장 가능하게 자동화한 것을 데이터 파이프라인이라고 부름

### 아파치 카프카가 데이터 파이프라인으로 적합한 이유
* 높은 처리량
    - 브로커가 프로듀서에서 받을때나, 컨슈머로 보낼 때 모두 데이터를 묶어서 전송
    - 동일한 양의 데이터를 보낼 때 네트워크 통신 횟수를 최소한으로 함
* 확장성
    - 카프카는 가변적인 환경에서 안정적으로 확장 가능하도록 설계
    - 데이터가 많아지면 브로커 개수를 자연스럽게 스케일 아웃 할 수 있다
    - 스케일 인, 아웃 과정을 무중단으로 지원
* 영속성
    - 전송받은 데이터를 메모리에 저장하지 않고 파일 시스템에 저장
    - 성능을 위해 페이지 캐시 영역을 메모리에 따로 생성하여 사용
    - 장애 발생으로 급작스럽게 종료되더라도 프로세스를 재시작하여 안전하게 데이터를 다시 처리
* 고가용성
    - 일부 서버에 장애가 발생하더라도 무중단으로 안전하고 지속적으로 데이터를 처리할 수 있다
    - 프로듀서로 전송바은 데이터를 여러 브로커 저장

## 1.3 데이터 레이크 아키텍처와 카프카의 미래
* 데이터 레이크 아키텍처의 2가지
    - 람다 아키텍처: 레거시 데이터 수집 플랫폼을 개선하기 위해 구성한 아키텍처
    - 카파 아키텍처: 람다와 비슷하지만 배치 레이어를 제거하고 모든 데이터를 스피드 레이어에 넣어서 처리

| 배치 데이터는 초, 분, 시간, 일 등으로 한정된 기간 단위의 데이터
| 스트림 데이터는 한정되지 않은 데이터로 시작 데이터와 끝 데이터가 명확히 정해지지 않은 데이터


## 카프카 설치
* 카프카를 활용하기 위해서는 주키퍼와 카프카 브로커가 필요
* 주키퍼는 분산 코디네이션 서비스를 제공하는 오픈소스
* 분산 코디네이션은 분산 시스템 내부에 상태 정보를 저장하고 데이터를 key/value 저장소로 저장 및 제공하는 서비스를 지칭
* 주키퍼와 카프카 브로커는 JVM 위에서 돌아가는 애플리케이션으로 heap memory를 지정해야 함

### JVM 설치
```shell
sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```
### 바이너리 다운로드
```shell
wget https://archive.apache.org/dist/kafka/2.5.0/kafka_2.12-2.5.0.tgz
tar xvf kafka_2.12-2.5.0.tgz
```

### 카프카 브로커 힙 메모리 설정
```shell
export KAFKA_HEAP_OPTS="-Xmx300m -Xms300m"
echo $KAFKA_HEAP_OPTS

==================================
-Xmx300m -Xms300m
```

### 주키퍼 실행
```shell
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
```

## 카프카 설정파일 수정
```text 
# config/server.properties
adverties.listener=PALINTEXT://host.name:9092
```

### 카프카 실행
```shell
bin/kafka-server-start.sh -daemon config/server.properties
tail -f logs/server.log
```

## 연결 확인
* 원격의 브로커 서버의 정보를 받아오는 테스트
```shell
bin/kafka-broker-api-versions.sh --bootstrap-server host.name:9092
```

## 커맨드 라인 툴 - kafka-topics.sh
* 토픽과 관련된 명령을 실행할 수 있음

### 토픽 생성
* 기본적인 방법
```shell
bin/kafka-topics.sh \
--create \ # 토픽 생성 명령을 명시
--bootstrap-server host:9092 \ # 브로커 지정
--topic hello.kafka # 토픽 이름 지정
```
* 다양한 옵션을 이용해서 생성
```shell
bin/kafka-topics.sh \
--create \
--bootstrap-server host:9092 \
—-partitions 3 \
—-replication-factor 1 \
—-config retention.ms=172800000 \
--topic hello.kafka 
```

### 토픽 리스트 조회
```shell
bin/kafka-topics.sh --bootstrap-server my-kafka:9092 --list
```

### 토픽 상세 조회
```shell
bin/kafka-topics.sh --bootstrap-server my-kafka:9092 \
--describe --topic hello.kafka
```

### 토픽 옵션 수정
* 파티션 개수 변경
```shell
bin/kafka-topics.sh --bootstrap-server my-kafka:9092 \
--topic hello.kafka \
--alter \
--partitions 4
```

* 토픽 삭제 리텐션 기간 변경
```shell
bin/kafka-configs.sh --bootstrap-server my-kafka:9092 \
--entity-type topics \
--entity-name hello.kafka \
--alter --add-config retention.ms=86400000
```

## kafka-console-producer.sh
- 토픽에 데이터를 넣을 수 있음
- 토픽에 넣는 데이터는 레코드라고 부르며 메시지 key와 메시지 value로 이루어져 있음
- 키는 없이 전송될 수 있고 자바의 null로 기본 설정되어 브로커로 전송된다
- 레코드 값은 UTF-8을 기반으로 Byte로 변환되고 ByteArraySerializer로만 직렬화됨
```bash
bin/kafka-console-producer.sh --bootstrap-server my-kafka:9092 \
--topic hello.kafka
>hello
>kafka
>0
>1
>2
>3
```

- 메시지 키를 가지는 레코드를 전송하기 위해서 몇 가지 추가 옵션을 작성해야 됨
```bash
bin/kafka-console-producer.sh --bootstrap-server my-kafka:9092 \
--topic hello.kafka \
--property "parse.key=true" \ # 메시지 키를 추가하겠다 명시
--property "key.separator=:" # 키와 메시지를 구분하는 구분자를 선언. 기본은 Tab
>key1:no1
>key2:no2
>key3:no3
```
```bash
bin/kafka-console-producer.sh --bootstrap-server my-kafka:9092 \
--topic hello.kafka \
--property parse.key=true \
--property key.separator=":" 
```

- 메시지 키가 null인 경우에는 프로듀서가 파티션으로 전송할 때 레코드 배치 단위로 라운드 로빈 전송
- 메시지 키가 존재하는 경우에는 키의 해시값을 작성하여 존재하는 파티션 중 한개에 할당. 때문에 메시지 키가 동일한 경우에는 동일한 파티션으로 전송

## kafka-console-consumer.sh
- 브로커로부터 데이터를 받아올 때 사용

```bash
bin/kafka-console-consumer.sh --bootstrap-server my-kafka:9092 \
--topic hello.kafka \
--from-beginning # 토픽에 저장된 가장 처음 데이터부터 추출
```

- `--property` 옵션을 사용해서 메시지 키와 메시지 값을 확인

```bash
bin/kafka-console-consumer.sh --bootstrap-server my-kafka:9092 \
--topic hello.kafka \
--property print.key=true \
--property key.separator="-" \
--group hello-group
--from-beginning
```

- consumer에서 데이터를 받아올 때 producer에서 생성한 순서대로 받고 싶을 때 가장 좋은 방법은 파티션 1개로 구성된 토픽을 만드는 것

## kafka-consumer-groups.sh

- 컨슈머 그룹은 컨슈머를 동작할 때 컨슈머 그룹 이름을 지정하면 생성
- 생성된 컨슈머 그룹의 리스트 확인

```bash
bin/kafka-consumer-groups.sh --bootstrap-server my-kafka:9092 --list
```

- 컨슈머 그룹 이름을 토대로 컨슈머 그룹이 어떤 토픽의 데이터를 가져가는지 확인

```bash
bin/kafka-consumer-groups.sh --bootstrap-server my-kafka:9092 \
--group hello-group \
--describe
```

## kafka-verifiable-producer, consumer.sh

- String 타입 메시지 값을 코드 없이 주고받을 수 있다
- 간단한 네트워크 통신 테스트를 할 때 유용하다

```bash
bin/kafka-verifiable-producer.sh --bootstrap-server my-kafka:9092 \
--max-messages 10 \ # 전송할 데이터 개수 지정, -1로 하면 계속 데이터 전송
--topic verify-test
```

```bash
bin/kafka-verifiable-consumer.sh --bootstrap-server my-kafka:9092 \
--topic verify-test \
--group-id test-group
```

## kafka-delete-records.sh

- 적재된 토픽의 데이터를 지우는 방법
- 가장 오래된 데이터부터 특정 시점의 오프셋까지 삭제할 수 있다

```json
// 쿼리할때 사용할 json 파일 생성
{"partitions": 
	[{"topic":"test", "partition":0, "offset":50}], 
	"version":1
}
```

```bash
bin/kafka-delete-records.sh --bootstrap-server my-kafka:9092 \
--offset-json-file delete-topic.json # 파일 지정
```