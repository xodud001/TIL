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

## 1. 카프카 브로커•클러스터•주키퍼
- 카프카 브로커
    - 카프카 클라이언트와 데이터를 주고받기 위해 사용하는 주체
    - 데이터를 분산 저장하여 장애가 발생하더라도 안전하게 사용할 수 있도록 도와주는 애플리케이션
- 카프카 브로커
    - 데이터를 안전하게 보관하고 처리하기 위해 3대 이상의 브로커 서버를 1개의 클러스터로 묶어서 운영
    - 프로듀서가 보낸 데이터를 안전하게 분산 저장하고 복제하는 역할 수행
- 주키퍼 앙상블
    - 카프카의 메타데이터를 관리하는데 사용
    - 카프카 클러스터와 연동

### 1.1. 데이터 저장, 전송
- 프로듀서로부터 데이터를 전달받으면 카프카 브로커는 프로듀서가 요청한 토픽의 파티션에 데이터를 저장하고 컨슈머가 데이터를 요청하면 파티션에 저장된 데이터를 전달한다
- 전달된 데이터는 파일 시스템에 저장
    - `config/server.properties`의 `log.dir` 옵션에 정의한 디렉토리에 데이터를 저장
    - 토픽 이름과 파티션 번호의 조합으로 하위 디렉토리를 생성하여 데이터를 저장
    - 페이지 캐시를 사용하여 디스크 입출력 속도를 높임
    - 카프카 브로커를 실행하는데 힙 메모리 사이즈를 크게 설정할 필요가 없다

<aside>
💡 페이지 캐시란 OS에서 파일 입출력의 성능 향상을 위해 만들어 놓은 메모리 영역을 뜻한다. 한번 읽은 파일의 내용은 메모리의 페이지 캐시 영역에 저장된다.

</aside>

### 1.2. 데이터 복제, 싱크
### 데이터 복제
- 데이터 복제(replication)는카프카를 장애 허용 시스템(fault tolerant system)으로 동작하도록 하는 기술
- 클러스터로 묶인 브로커 중 일부에 장애가 발생하더라도 데이터를 유실하지 않고 안전하게 사용하기 위함
- 파티션 단위로 복제
- 복제된 파티션은 리더와 팔로워로 구성
    - 리더 : 프로듀서 또는 컨슈머와 직접 통신하는 파티션
    - 팔로워 : 복제 데이터를 가지고 있는 파티션
- 팔로워 파티션들은 리더 파티션의 오프셋을 확인하여 현재 자신이 가지고 있는 오프셋과 차이가 나는 경우 리더 파티션으로부터 데이터를 가져와 자신의 파티션에 저장. 이 과정을 복제라고 함

### 컨트롤러(controller)
- 다른 브로커들의 상태를 체크하고 브로커가 클러스터에서 빠지는 경우 해당 브로커에 존재하는 리더 파티션을 재분배
- 클러스터의 다수 브로커 중 한 대가 컨트롤러 역할을 함

### 데이터 삭제
- 컨슈머가 데이터를 가져가더라도 토픽의 데이터는 삭제되지 않는다
- 오직 브로커만이 데이터를 삭제할 수 있다
- 데이터 삭제는 파일 단위로 이루어지는데 이 단위를 `로그 세그먼트`라고 부름
- 카프카 브로커에 log.segment.bytes 또는 log.segments.ms 옵션에 값이 설정되면 세그만트 파일이 닫힘
- 세그먼트 파일이 닫히는 기본값은 1GB

### 컨슈머 오프셋 저장
- 컨슈머 그룹은 파티션의 어느 레코드까지 가져갔는지 확인하기 위해 오프셋을 커밋

### 코디네이터
- 컨슈머 그룹의 상태를 체크하고 파티션을 컨슈머와 매칭되도록 분배하는 역할을 한다
- 컨슈머가 그룹에서 빠지면 매칭되지 않은 파티션을 정상 동작하는 컨슈머로 할당하여 끊임없이 데이터가 처리되도록 도와준다. 이 과정을 리밸런스(rebalance)라고 부른다

# 2. 토픽과 파티션
### 토픽
- 토픽은 카프카에서 데이터를 구분하기 위해 사용하는 단위
- 토픽은 1개 이상의 파티션을 소유
- 파티션에는 프로듀서가 보낸 데이터가 저장되는데 이 데이터를 ‘레코드’라 함

### 파티션
- 그룹으로 묶인 컨슈머들이 레코드를 병렬로 처리 할 수 있도록 매칭
- 큐와 비슷한 구조. 데이터를 삭제하지는 않음

## 2.1. 토픽 이름 제약 조건
- 빈 문자열 x
- 마침표 하나 또는 둘로 생성될 수 없음
- 이름의 길이는 249자 미만
- 영어 대소문자, 숫자, 마침표, 언더바, 하이픈 조합만 가능
- 예약으로 사용되는 토픽 이름 사용 불가
    - __consumer_offsets
    - __transaction_state
- 마침표와 언더바가 동시에 들어가면 안됨
- to.pic과 to_pic은 공생 할 수 없음

- 

# 3. 레코드
- 레코드는 타임스탬프, 메시지 키, 메시지 값, 오프셋 헤더로 구성
- 한번 적재된 레코드는 수정할 수 없고 로그 리텐션 기간 또는 용량에 따라서만 삭제
- 타임스탬프는 생성된 시점의 유닉스 타임이 설정
- 메시지 키는 메시지 값의 종류를 나타내기 위해 사용. 동일한 메시지 키라면 동일한 파티션에 적재
- 메시지 값에는 실질적으로 처리할 데이터가 들어감
- 메시지 키와 값은 직렬화되어 전송. 역직렬화는 직렬화와 동일한 형태로 수행
- 레코드의 오프셋은 0 이상의 숫자로 구성. 직접 지정 불가
- 헤더는 레코드의 추가적인 정보를 담는 메타데이터 저장소 용도로 사용

## 4.1 프로듀서 API
- 카프카에 필요한 데이터를 선언하고 브로커의 특정 토픽의 파티션에 전송
- 프로듀서는 리더 파티션을 가지고 있는 브로커와 직접 통신
- 직렬화를 통해 자바의 기본형과 참조형뿐만 아니라 동영상, 이미지 같은 바이너리 데이터도 전송 가능

### gradle 설정
```java
implementation 'org.apache.kafka:kafka-clients:2.5.0'
```

### producer sample
```java
public class SimpleProducer {

    private final static Logger log = LoggerFactory.getLogger(SimpleProducer.class);
    private final static String TOPIC_NAME = "test";
    private final static String BOOTSTRAP_SERVERS = "my-kafka:9092";

    public static void main(String[] args) {
        Properties configs = new Properties();
        configs.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS);
        configs.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        configs.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        KafkaProducer<String, String> producer = new KafkaProducer<>(configs);

        String messageValue = "testMessage";
        ProducerRecord<String, String> record = new ProducerRecord<>(TOPIC_NAME, messageValue);
        producer.send(record);
        log.info("{}", record);
        producer.flush();
        producer.close();
    }
}
```

### 프로듀서 중요 개념
- 브로커로 데이터를 전송할 때 내부적으로 파티셔너, 배치 생성 단계를 거침
- ProducerRecord 인스턴스에 파티션 번호를 직접 지정하거나 타임스탬프 설정, 메시지 키를 설정할 수도 있다
- send() 메시지에 의해 파티셔너에서 토픽이 어느 파티션으로 전송될 것인지 정함
- 파티셔너에 의해 구분된 레코드는 전송전 accumulator에 데이터를 버퍼로 쌓음
- 버퍼로 쌓인 데이터는 배치로 묶어서 전송

### 프로듀서 주요 옵션

**필수 옵션**
- `bootstrap.servers`: 데이터를 전송할 대상 카프카 클러스터에 속한 브로커의 `호스트 이름:포트`를 1개 이상 작성
- `key.serializer`: 레코드의 메시지 키를 직렬화하는 클래스를 지정
- `value.serializer`: 레코드의 메시지 값을 직렬화하는 클래스를 지정

**선택 옵션**
- `acks`: 프로듀서가 전송한 데이터가 브로커들에 정상적으로 저장되었는지 확인하는데 사용
    - 0 : 저장 여부와 상관없이 성공으로 판단
    - 1(default) : 리더 파티션에 데이터가 저장되면 성공으로 판단
    - -1 : min.insync.replicas 개수에 해당하는 리더 파티션과 팔로워 파티션에 데이터가 저장되면 성공하는 것으로 판단
- `buffer.memory`: 브로커로 전송할 데이터를 배치로 모으기 위해 설정할 버퍼 메모리양을 지정. 기본값은 32MB
- `retries`: 재전송을 시도하는 횟수. 기본값은 2147483647
- `batch.size`: 배치로 전송할 레코드 최대 용량. 기본값은 16384
- `linger.ms`: 배치를 전송하기 전까지 기다리는 최소 시간. 기본값은 0
- `partitioner.class`: 레코드를 파티션에 전송할 때 적용하는 파티셔너 클래스를 지정. 기본값은 `DefaultPartitioner`
- `enable.idempotence`: 멱등성 프로듀서로 동작할지 여부 설정. 기본값은 false
- `transactional.id`: 프로듀서가 레코드를 전송할 때 레코드를 트랜잭션 단위로 묶을지 여부를 설정