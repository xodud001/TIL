# 1. Why React.js

- 컴포넌트
    - 컴포넌트의 재사용이 가능, 변경이 일어날 때 중복코드에 의해서 산탄총 수술이 발생을 줄일 수 있는 장점
    - 작성해야하는 코드의 양이 줄어둠
- 선언형 프로그래밍
    - 명령형(jQuery)은 절차를 하나하나 다 나열해야하는데 선언형은 목적을 바로 나열
- Virtual DOM
    - DOM, Document Object Model : 브라우저가 실제로 사용하는 객체, html의 태그같은게 하나의 dom
    - Virtual DOM은 실제로 변경되는 DOM만 추적해서 새로 렌더링해줌

# 2. Create React App

- 리액트를 구동하려면 다양한 세팅이 필요한테 Spring Boot 처럼 이미 보일러 플레이트 같은게 존재해서 해당 방법으로 빠르게 서비스를 개발할 수 있게 환경설정을 해줌

```jsx
npx create-react-app app-name // 설치
npm start // 리액트 서버 시작
```

# 3. JSX

- 자바스크립트로 HTML 처럼 xml 형식으로 작성할 수 있게 해주는 기능

```jsx
const header = () => {
	return <header> 
		<span>React Header<span> 
	</header>
}

export defauls header;
```

- JSX 태그는 모두 닫는 태그로 종료되어야 한다
    - `<div></div>`
    - `<image/>` : self-closing
- JSX 태그는 return 될 때 무조건 하나의 최상위 태그로만 반환되어야 한다
    - 최상위 태그가 없을 경우에는 `<React.Fragement>` 태그나 와일드카드(`<></>`) 태그로 감싸줘야 한다
- JSX 태그에도 HTML 처럼 속성을 줄 수 있음. 근데 class 같이 Javascript의 키워드는 대체되어 사용됨.
    - class → className
- JSX 내부에서 `{}` 태그 안에 Javascript의 식을 사용할 수 있음
    - `{변수}`
    - `{함수호출()}`
    - `{1+2}`
    - `{”문자열”}`

# 4. State(상태)란?

- 계속해서 변화하는 특정 상태. 상태에 따라 각각 다른 동작을 수행

```jsx
import { useState } from "react";

const Counter = () => {
	//초기값이 0인 count 변수와 count의 상태를 변경할 수 있는 setCount 함수 생성
	const [count, setCount] = useState(0); 
	
	const onIncrease = () => {
		// count의 상태를 +1 해서 변경
		setCount(count + 1);
	}

	
	const onDecrease = () => {
		// count의 상태를 -1 해서 변경	
		setCount(count - 1);
	}

	return (
		<div>
			<h2> {count} </div> // count의 변경에 의해서 계속 계속 재렌더링 됨
			<button onClick={onIncrease}>+</button>
			<button onClick={onDecrease}>-</button>
		</div>
	)
}
```

# 5. Props

- 부모에서 상태, 데이터, 함수 등을 자식 컴포넌트로 전달할 때 사용

```jsx
import { useState } from "react";

const Counter = (props) => {
	// 부모가 전달한 value를 초기값으로 설정
	const [count, setCount] = useState(props.value); 
	
	const onIncrease = () => {
		// count의 상태를 +1 해서 변경
		setCount(count + 1);
	}

	const onDecrease = () => {
		// count의 상태를 -1 해서 변경	
		setCount(count - 1);
	}

	return (
		<div>
			<h2> {count} </div> // count의 변경에 의해서 계속 계속 재렌더링 됨
			<button onClick={onIncrease}>+</button>
			<button onClick={onDecrease}>-</button>
		</div>
	)
}
```

- 부모에서 잘못된 값이 전달되는것을 막기 위해서 props에 기본값을 설정할 수 있음

```jsx
Counter.defaultProps = {
	value = 0;
}
```

- 부모의 props가 변경되면 자식 컴포넌트가 재렌더링 됨
    - 부모의 props가 변경되지 않더라도 부모가 재렌더링되면 자식도 재렌더링 됨