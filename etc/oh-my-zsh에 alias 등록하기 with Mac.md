# 방법
맥북에서 oh-my-zsh 터미널을 사용중일 경우 alias를 지정하기 위해서는 홈 경로에 `.zshrc` 파일에 추가하면 된다.

예를 들어, TIL을 작성하기 위해 VSC로 해당 폴더를 여는 alias를 등록하려면 아래와 같이 할 수 있다

## 1. 파일 열기
vi 명령을 이용해서 홈 디렉토리에 있는 `.zshrc` 파일을 열어준다
```
> vi ~/.zshrc
```

## 2. alias 추가
```
# 경로는 본인의 TIL 폴더에 맞게 설정
alias til='code /Users/user/Document/study/til'
```

## 3. 실행
위 라인을 파일에 추가후 터미널을 닫고 다시 열면 이제 alias를 사용할 수 있다.

터미널에 위에서 설정한 alias `til`을 입력하면 TIL 디렉토리에서 VSC가 열리게 된다. 