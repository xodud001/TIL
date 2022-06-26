# Docker


# Section 3 데이터 관리 및 볼륨으로 작업하기

## Bind Mount
* Docker Container에 Volume을 지정할 때 Anonymous or Named Volume을 지정하면 관리가 어렵다.
* Bind Mount를 이용해서 호스트의 특정 폴더를 Container에 Bind 시킬 수 있다.
* Bind Mount를 사용할 때는 Docker가 해당 경로에 접근이 가능한지 확인이 필요함
