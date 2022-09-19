# Docker

# Section 3 데이터 관리 및 볼륨으로 작업하기

## Bind Mount
* Docker Container에 Volume을 지정할 때 Anonymous or Named Volume을 지정하면 관리가 어렵다.
* Bind Mount를 이용해서 호스트의 특정 폴더를 Container에 Bind 시킬 수 있다.
* Bind Mount를 사용할 때는 Docker가 해당 경로에 접근이 가능한지 확인이 필요함

## Read Only Volume
* 기본적으로 마운트되는 볼륨은 read-write인데 docker 컨테이너에서 volume을 수정할 수 없도록 read-only로 설정 할 수 있음
    * 마운트 할 경로 뒤에 `:ro` 옵션을 추가해주면 됨
    * `[path]:[path]:[option]`
    * 여러 볼륨을 동시에 마운트 할 때 상위의 디렉토리의 옵션을 하위 디렉토리가 상속하지 않음. /etc/conf일 경우 /etc가 `ro`로 설정되어도 /etc/conf는 `rw`로 설정됨

## Docker 볼륨 관리하기
* `docker volume ls` : 현재 활성화 된 volume을 볼 수 있음
* 바인드 마운트는 volume이 아니라 호스트 머신의 디렉토리를 마운트 한 것이기 때문에 Docker에서 관리하지 않음
* `docker volume create [NAME]` : 볼륨 생성
* `docker volume inspect [NAME]` : volume 정보 확인
* `docker volume remove [NAME]` : volume 삭제
* `docker volume prune` : 사용하지 않는 volume 모두 삭제


## .dockerignore
* 이미지를 빌드할 때 Dockerfile에서 COPY 명령에 의해 복사하지 않아도 되는 파일을 지정할 수 있음

## ARGuments & ENVironment Variables
* 도커는 빌드 타임 인수와 런타임 환경 변수를 지원
* 인수(ARG)를 사용하면 특정 Dockerfile에 인수를 줄 수 있음
    * `docker build`에서 `--build-arg` 옵션으로 사용
    * Dockerfile에서 ARG로 인수 설정
        * `ARG [NAME]=[DEFAULT-VALUE]`
    * Dockerfile에서 해당 ARG를 다시 참조하려면 `$[NAME]` 문법을 사용해서 참조
* 환경변수(ENV)는 인수처럼 Dockerfile에 넘길 수 있고 런타임에 해당 값을 가져다 쓸 수 있음
    * `docker run`에서 --env 옵션으로 설정
    * Dockerfile에서 EVN 옵션으로 환경변수 이름과 기본값을 지정할 수 있다
        * `ENV [NAME] [DEFAULT-VALUE]`
    * Dockerfile 내부에서 환경 변수를 변수처럼 사용하려면 `$[NAME]` 문법을 사용해서 참조
        * `EXPOSE $PORT`

# 네트워킹 : 컨테이너 통신
* 기본적으로 도커는 localhost라는 url이 주어졌을 때 컨테이너가 실행되고 있는 호스트 머신을 가르키지 못함
* host.docker.internal이라는 url을 사용하면 호스트 머신의 IP로 변환됨

* `docker run` 명령어와 함께 `--network my_network` 옵션을 주면 네트워크를 지정할 수 있다.
* `docker network create [name]` 명령으로 네트워크 그룹을 생성할 수 있다.
* 같은 네트워크로 묶인 컨테이너들은 url을 각 컨테이너의 이름으로 대신할 수 있다. docker가 해당 컨테이너의 이름으로 IP 주소를 변환해 준다

# Docker Compose
* docker run과 docker build 명령을 대체할 수 있는 도구
* 다수의 docker run과 docker build 명령을 단 하나의 구성 파일로 가짐
* 모든 서비스나 컨테이너를 동시에 바로 시작하면서 필요하면 모든 이미지를 빌드하는 오케스트레이션 명령 set이다
* 하나의 명령을 이용해서 모든 것을 시작하거나 중지할 수 있음
* Service는 실제로 컨테이너를 가리킨다

## Compose 파일 만들기
* compose 내의 서비스들은 같은 네트워크로 구성됨
* 프로젝트 폴더에 `docker-compose.yaml` 파일 생성

```docker-compose
version: 'version'
services:
    service1:
        image: 'image'
        container_name: name
        volumes:
            - v1:/data
        environment:
            key: value
            or
            - name=value
        env_file:
            - path
        network:
            - name
    service2:
        build: path
        ports:
            - 80:80
        depends_on:
            - service1
    service3:
        build:
            context: path
            dockerfile: filename
            args:
                some-arg: value
        ports:
            - 3306:3306
        # 아래 2개는 -it와 같은 기능
        stdin_open: true
        tty: true

volumes:
    v1:
```

## compose 실행
* docker-compose up -d 명령으로 detached 모드로 실행
* docker-compose down 하면 다 종료되면서 삭제
* docker-compose down -v 불륨까지 삭제

# 컨테이너 배포
* 로컬에서 개발하던것을 프로덕션으로 옮겨도 그대로 실행 됨
## 주의점
* Bind Mounts을 production에서 사용 하면 안됨
* 개발 환경과는 다른 설정이 필요함
* 여러 컨테이너 프로젝트는 여러 호스트 or 리모트 머신에 나눠져야 한다

## 간단한 배포 순서
1. Local에서 image build
2. build된 이미지를 저장소에 push
3. Remote에서 저장소에 저장된 이미지를 pull and run

## ECS(Elastic Container Service)
* AWS에서 제공하는 컨테이너 관리를 도와주는 서비스