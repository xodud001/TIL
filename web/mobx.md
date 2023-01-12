- MobX는 상태 관리를 쉽고 확장성 있게 만들어주는 검증된 라이브러리
    - 쉬운 사용법
    - 간단한 렌더링 최적화
    - 자유로운 구조

### 설치

```jsx
npm install --save mobx
npm install --save [ mobx-react | mobx-react-lite ]
```

# 1. 개념

- MobX는 아래 세 가지 개념을 구분
    - state(상태)
    - action(동작)
    - derivation(파생)

## 1.1 state를 observable하게 만들기

- 원하는 데이터 구조에 state를 저장. 어떤 데이터 구조든 상관 없음
- MobX가 추적할 수 있도록 `observable`로 표시만 하면 됨

```jsx
import { makeObservable, observable, action } from "mobx"

class Todo {
    id = Math.random()
    title = ""
    finished = false

    constructor(title) {
        makeObservable(this, {
            title: observable, // 상태
            finished: observable, // 상태
            toggle: action
        })
        this.title = title
    }

    toggle() {
        this.finished = !this.finished
    }
}
```

## 1.2 action을 이용한 state 업데이트

- `action`은 사용자 이벤트, 백엔드 데이터 푸시, 예약된 이벤트 등과 같이 state를 변경하는 코드.
- 위 예에서 `toggle`은 `observable`을 변경하기 때문에 `action`으로 표시
    - MobX가 트랜잭션을 자동으로 적용하여 성능을 쉽게 최적화 할 수 있음
- 

## 1.3 state 변화에 자동으로 응답하는 derivation 만들기

- state에서 더 이상의 상호작용 없이 파생될 수 있는 모든건을 derivation이라 함
    - 사용자 인터페이스
    - 원래의 state에서 파생된 데이터
    - 백엔드와의 통합(서버에 변경사항 전송)
- MobX에서 derivation의 두 가지 분류
    - computed value: 현재의 observable state에서 순수 함수를 사용하여 파생될 수 있는 값
    - reaction: state가 변경될 때 발생해야하는 side-effect

### 1.3.1 computed를 사용하여 derivation value 모델링하기

- `computed value`를 생성하려면 JS의 `get` 키워드를 사용해서 정의하고 `makeObservable`에서 `computed`로 표시

```jsx
import { makeObservable, observable, computed } from "mobx"

class TodoList {
    todos = []

    get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length
    }

    constructor(todos) {
        makeObservable(this, {
            todos: observable,
            unfinishedTodoCount: computed
        })
        this.todos = todos
    }
}
```

### 1.3.2 반응형 리액트 컴포넌트

- `observer` 함수를 이용해서 컴포넌트를 감싸서 반응형으로 만들 수 있다

```jsx
import * as React from "react"
import { render } from "react-dom"
import { observer } from "mobx-react-lite"

const TodoListView = observer(({ todoList }) => (
    <div>
        <ul>
            {todoList.todos.map(todo => (
                <TodoView todo={todo} key={todo.id} />
            ))}
        </ul>
        Tasks left: {todoList.unfinishedTodoCount}
    </div>
))

const TodoView = observer(({ todo }) => (
    <li>
        <input type="checkbox" checked={todo.finished} onClick={() => todo.toggle()} />
        {todo.title}
    </li>
))

const store = new TodoList([new Todo("Get Coffee"), new Todo("Write simpler code")])
render(<TodoListView todoList={store} />, document.getElementById("root"))
```

- observer는 리액트 컴포넌트를 렌더링하는 데이터의 derivation으로 변환
- MobX에서는 필요할 때마다 컴포넌트가 다시 렌더링 되며, 그 이상은 렌더링 되지 않음