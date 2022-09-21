# AWS VPC(Virtual Private Cloud)

## AWS Network service
* AWS 리전(27개)과 가용영역(87개), 접속 지점(410개 이상)
* AWS VPC, Subnet, Routing Table
* NACL, SecurityGroup, GateWay
* AWS의 리전은 가용역역의 클러스터링의 집합

### VPC
* AWS의 사설망

### Subnet
* AWS 부분망

### Routin Table
* 트래픽의 경로 지정

### NACL
* Access Control : 서브넷 트래픽 제어. 허용 거부 가능

### SecurityGroup
* 보안그룹 : 서비스 트래픽 제어. 허용만 가능

### Internet & NAT Gateway
* 입구/출구
* Internet Gateway: VPC와 인터넷 간에 통신 할 수 있게 해줌
* NAT Gateway : Private 서브넷과 인터넷 간 아웃바운드 통신을 할 수 있게 해줌

