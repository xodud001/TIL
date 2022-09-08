# kafka features
* Source Application과 Target Application의 coupling을 느슨하게 해줌
* 데이터를 전송할 때 자유로움
* 내부에 토픽이라는 큐가 존재
    * 토픽에 데이터를 넣으면 Producer
    * 토픽에서 데이터를 가져가면 Consumer

## kafka 토픽
* 데이터가 들어가는 공간을 토픽이라 함
* 토픽은 여러개 생성 가능
* 토픽은 데이터베이스의 테이블이나 파일시스템의 폴더와 유사함
* 토픽은 이름을 가질 수 있음
* 토픽은 여러 파티션으로 나뉠 수 있음. 기본은 0번 파티션
* Producer는 데이터를 생성해서 토픽의 파티션에 저장
* Consumer는 데이터를 가져감.
* Consumer가 record를 가져가도 데이터는 삭제되지 않음
* 파티션을 늘리는 것은 가능하지만 줄일 수는 없음
    - 파티션을 늘리면 Consumer를 늘려서 분산 처리에 용이함
* 파티션의 데이터가 삭제되는 타이밍 지정 가능
    - 용량 or 시간

## kafka broker
* 카프카가 설치되어 있는 서버 단위를 말함
* 보통 3개 이상의 브로커로 사용하는 것을 권장

## kafka replication
* 카프카의 토픽의 파티션을 브로커 단위로 복제 가능
* N개의 브로커에 N개의 레플리케이션 설정
* 기준이 되는 파티션을 Leader Partition이라 부름
* 복제본은 Follower Partition이라 부름
* 이를 묶어서 ISR(In Sync Replica)이라 볼 수도 있음
* partition의 고가용성을 위해 사용
* 데이터의 전달은 leader partition에서만 일어남

## kafka partitioner
* 토픽의 어떤 파티션에 데이터를 넣을지 결정
* 프로듀서가 데이터를 보내면 무조건 파티셔너를 통해서 브로커로 데이터가 전송

## kafka lag
* 데이터에는 오프셋이라고 하는 숫자가 붙음
* Producer가 데이터를 넣을 때, Consumer가 데이터를 읽을 때 offset을 기억하는데 이때 두 offset의 차이를 consumer lag이라 함
* lag은 파티션마다 존재
* Kafka Burrow로 모니터링

