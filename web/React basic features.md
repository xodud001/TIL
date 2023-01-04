## 1. useState

- react 컴포넌트 내부에 상태를 만들 수 있음

```jsx
// react 모듈에서 useState 함수 추가
import { useState } from "react";

const Component = () => {
	// useState 함수를 이용해서 상태와 상태를 변경할 수 있는 함수 생성
	// useState는 배열을 반환해서 구조 분해 할당으로 각각 받아옴
	const [state, setState] = useState("");
}
```

## 2. input의 onChange

- `input` 태그로 사용자의 입력을 받아 상태를 변경해야할 때 `onChange` 속성을 사용
- `onChange`는 event를 매개변수로 받는 콜백을 인자로 받음
- event는 사용자의 입력에 대한 event 정보를 담고 있는 객체
- 사용자의 입력에 의해 변경된 값은 `event.target.value`를 통해 확인

## 3. Spread로 State 합치기

- name과 age 상태 2가지가 있을 때 두 상태를 객체로 묶음
- Spread 연산자와 괄호 접근자로 onChange 이벤트 합치기

```jsx
import { useState } from "react";

const Person = () => {
	const [state, setState] = useState({
		name: "",
		age: 0,
	});

	const handleStateChange = (e) => {
		setState({
			...state,
			[e.target.name]: e.target.value
		})
	}

	return (
		<div>
			<input name="name" value={state.name} onChange={handleStateChange}></div>
			<input name="age" value={state.age} onChange={handleStateChange}></div>
		</div>
	)
}
```

## 4. useRef

- React에서 DOM 엘리멘트를 선택할 수 있게 해주는 함수

```jsx
import { useRef } from "react";

const Component = () => {
	const input = useRef();
	
	const handleInput = (e) => {
			...
			input.current.focus();
	}

	return (<input ref={input} onChange={handleInput} />);

}
```

## 5. List 형식의 데이터 다루기

### 5.1. 리스트를 이용해서 컴포넌트 렌더링

- 배열의 map 내장함수를 이용한 렌더링
- list 형식의 컴포넌트는 각 컴포넌트가 고유한 key 속성을 가져야 함

```jsx
return (<div>
	{list.map((e) => (
	    <Item key={e.id} item={e}/>
	))}
<div>);
```

### 5.2. 리스트에 데이터 추가하기

- 기존 리스트와 새로운 아이템을 합쳐서 새로운 리스트를 만들어서 상태 변경

```jsx
function App() {
  const [data, setData] = useState([]);
  const dataId = useRef(0);

  const onCreate = (author, content, emotion) => {
    dataId.current += 1;
    const created_date = new Date().getTime();
    const newTime = {
      author,
      content,
      emotion,
      created_date,
      id: dataId.current,
    };
    
    setData([newTime, ...data])
  }

  return (
			...
  );
}
```

### 5.3. 리스트에 데이터 삭제

- 리스트에서 삭제할 아이템만 걸러내서 새로운 리스트를 만들어서 상태 변경

```jsx
const onDelete = (targetId) => {
  const newArr = data.filter( (e) => e.id !== targetId);

  setData([...newArr]);
}
```

## 6. React Lifecycle 제어하기

### 6.1. Lifecycle

- 탄생, Mount: 화면에 나타나는것
- 변화, Update: 변경되는것, 리렌더
- 죽음, UnMount: 화면에서 사라짐

### 6.2. useEffect

- useEffect : React Hook 컴포넌트에서 라이프사이클 제어하는 기능
    
    ```jsx
    useEffect( () => {
    	// todo callback..
    	return (() => {
    		/// todo unmount
    	})
    }, []); // []는 의존성 배열, 이 배열 내에 들어있는 값이 변화하면 콜백 함수가 수행됨
    ```
    
    - 의존성 배열이 비어있다면 Mount 시점에만 호출
    - 인자로 의존성 배열을 전달하지 않는다면 컴포넌트가 리렌더 되는 순간에 호출
    - Callback을 return하면 UnMount 시점에 해당 Callback이 호출됨

## 7. 최적화

### 7.1. useMemo

- Memoization : 이미 계산 해 본 연산 결과를 기억 해 두었다가 동일한 계산을 시키면, 다시 연산하지 않고 기억 해 두었던 데이터를 반환 시키는 방법
- useMemo 기능을 이용해서 자주 호출되는 연산의 결과를 메모이제이션 할 수 있음
    - 의존성 배열을 통해서 메모이제이션을 다시 호출 가능

```jsx
const value = useMemo( () => {
	// todo...
	return {value1, value2, value3};
}, [])

// useMemo의 결과는 function이 아니라 Callback에서 리턴한 데이터 타입이다
const {value1, value2, value3} = value;
```

### 7.2. React.memo

- 부모 컴포넌트가 리렌더되면 자식 컴포넌트까지 리렌더 되는데 이때 자식 컴포넌트가 리렌더될 필요가 없을때 컴포넌트를 메모이제이션 하는 기법
- 컴포넌트를 React.memo로 래핑하고 컴포넌트의 props가 변경되지 않으면 메모이제이션 된 컴포넌트를 반환
    
    ```jsx
    const TextView = React.memo( ({text}) => {
      useEffect( () => {
        console.log("Update :: Text: " + text)
      });
    
      return (
        <div>{text}</div>
      )
    });
    
    const CountView = React.memo(({count}) => {
      useEffect(() => {
        console.log("Update :: Count: " + count);
      });
      return (
        <div>{count}</div>
      )
    });
    
    const OptimizeTest = () => {
        const [count, setCount] = useState(1);
        const [text, setText] = useState("");
    
        return (
          <div style={{ padding: 50 }}>
            <div>
              <h2>count</h2>
              <CountView count={count}/>
              <button onClick={() => setCount(count + 1)}>+</button>
            </div>
            <div>
              <h2>text</h2>
              <TextView text={text}/>
              <input value={text} onChange={ (e) => setText(e.target.value)} />
            </div>
          </div>
        );
    }
    
    export default OptimizeTest;
    ```
    
- 내부에 상태가 변경할 때 객체는 똑같이 바뀌더라도 객체 자체가 바뀌기 때문에 무조건 재렌더링됨. 이 문제는 React.memo의 2번째 인자로 props  비교 함수를 만들어서 넘겨주면 객체의 깊은 복사 비교를 수행할 수 있다.