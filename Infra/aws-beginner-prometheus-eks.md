# Prometheus Operator with EKS

## Monitoring
* 정의
    - 시스템에 관련된 수치를 수집, 처리 집ㅖ
    - 서비스 관점 : 지연시간, 트래픽, 에러, 포화도
    - 시스템 관점 : 사용률, 포화도 에러
* 구성요소
    - Metrics
    - Logging
    - Tracing : 사용기록 추적
    - Alerting
    - Visualization

## EKS
* AWS에서 제공하는 서비스
    - control plane 역할에 한해서 대신 해줌
* 무중단 보장
* ECR, ELB, IAM, VPC 와의 유연한 연동

## Helm
* 쿠버네티스를 위한 패키지 매니저
* easy updates
* simple sharing
* rollbacks

## Prometheus Operator
* Kubernates에서 Prometheus의 관리 제공
* 간편한 스택 구성과 자동화 제공
