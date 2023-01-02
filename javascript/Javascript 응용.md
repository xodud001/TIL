# 1. Truthy & Falsy

- Truthy : 참 같은 값
    - 비어있지 않은 문자열, 빈 배열 리터럴, 빈 객체 리터럴, 0을 제외한 숫자, Infinity
- Falsy : 거짓 같은 값
    - 빈 문자열, undefined, null, -Infinity, 0, NaN

# 2. 삼항 연산자

- 조건식이 간단할 때 식을 단축해서 쓸 수 있게 해줌
    - `조건식 ? 참일때 : 거짓일때`

```jsx
let a = 3;

a >= 0 ? console.log("양수") : console.log("음수")
```

# 3. 단락 회로 평가

- 왼쪽에서 오른쪽으로 식이 평가되는 논리 연산자의 특성을 이용
    - &&의 경우 둘다 true여야 true가 반환되기 때문에 왼쪽 값이 false일 경우 오른쪽의 여부와 상관없이 무조건 false이기 때문에 뒤에 오는 식을 평가하지 않고 false를 반환
    - ||도 한쪽만 true이면 되기 때문에 왼쪽이 true면 오른쪽은 평가하지 않고 true 반환

# 4. 조건문 Upgrade

## 4.1. 배열 내장 함수 `includes()`를 통한 업그레이드

**업그레이드 전** 

```jsx
function isKoreanFood(food){
	if(food === "불고기" || food === "비빔밥" || food === "떡볶이"){
		return true;
	}
	return false;
}
```

**업그레이드 후**

```jsx
function isKoreanFood(food){
	if( ["불고기", "비빔밥", "떡볶이"].includes(food)){
		return true;
	}
	return false;
}
```

## 4.2. 객체 프로퍼티의 Key와 괄호 표기법을 이용한 업그레이드

**업그레이드 전** 

```jsx
const getMeal = (mealType) => {
	if(mealType === '한식') return '불고기';
	if(mealType === '양식') return '파스타';
	if(mealType === '중식') return '멘보샤';
	if(mealType === '일식') return '초밥';
	return "굶기";
}
```

**업그레이드 후**

```jsx
const meal = {
	한식: "불고기",
	중식: "멘보샤",
	일식: "초밥",
	양식: "스테이크",
	인도식: "카레"
};

const getMeal = (mealType) => {
	return meal[mealType] || "굶기";
}
```

# 5. 비 구조화 할당(구조 분해 할당)

- 배열이나 객체에서 원하는 값을 쉽고 빠르게 꺼내는 방법

## 5.1. 배열

```jsx
let arr = ["one", "two", "three"];

// 배열의 1, 2, 3번째 요소가 각각의 변수에 앞에서 부터 할당
let [one, two, three] = arr;

// 배열의 길이는 3이기 때문에 원래 four 변수는 undefined지만 기본값을 할당
let [one, two, three, four = "four"] = arr;

let a = 10;
let b = 20;

// a와 b의 값 스왑하기
[a, b] = [b, a]
```

## 5.2. 객체

```jsx
let object = {
	one: "one",
	two: "two",
	three: "three"
}

// 객체 프로퍼티의 key를 명시해서 비구조화 할당이 이루어짐
let {one, two, three} = object;

// 원래는 객체 프로퍼티의 key 이름에 종속되지만 변수 이름을 변경할 수도 있음
let {one: first, two: second, three: thrid} = object;

// 배열에서와 똑같이 없는 값은 기본값 할당이 가능함
let {one, two, three, four = "four"} = object;
```

# 6.  Spread 연산자

- 배열과 객체를 한줄로 펼치는 방법
- 객체를 생성할 때 상속과 같은 형식으로 특정 객체의 프로퍼티를 모두 가져올 때 `…변수명` 형식으로 객체 리터럴 내부에 생성

```jsx
const person = {
	name: "John",
	age: 20
}

const developer = {
	...person,
	job: "backend"
}

console.log(developer); // {	name: "John",	age: 20, job: "backend"}
```

- 2개의 배열을 합칠 때 스프레드 연산자로 펼쳐서 합칠 수 있다

```jsx
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

const newArr = [...arr1, ...arr2];

console.log(newArr); // [1, 2, 3, 4, 5, 6]
```

# 7. 동기 & 비동기

## 7.1 싱글 쓰레드 작업 수행 방식

- 코드가 작성된 순서대로 작업을 처리
- 이전 작업이 진행 중일때는 다음 작업을 수행하지 않고 대기
- 앞의 작업이 다 실행된 이후 다음 작업 수행
- → 동시 방식의 처리

## 7.2 멀티 쓰레드 작업 수행 방식

- 싱글 쓰레드는 다른 작업들이 대기해야 하지만 쓰레드를 여러개 쓰면 작업 분할이 가능
- 오래걸리는 작업이 있어도 다른 일을 할수 있음

## 7.3. 자바스크립트 동작 방식

- 자바스크립트는 싱글 쓰레드이기 때문에 동기 방식으로 처리됨
- 이 단점을 극복하기 위해 여러 task를 동시에 실행
- 먼저 작성된 코드를 실행하지 않고 다음 코드 실행 가능
- → 비동기 작업

### 7.3.1. 자바스크립트 엔진 동작 방식

- 자바스크립트 엔진은 Heap(메모리 할당) 과 Call Stack(코드 실행)으로 나뉨
    - Heap : 변수, 상수 등 저장
    - Call Stack : 함수 호출 스택 저장
- 자바스크립트 엔진과 웹 브라우저간의 상호작용을 위해 Web APIs, Event Loop, Callback Queue로 나뉨
    - Web APIs : setTimeout에 의해 대기중인 함수 호출을 보관. 이후 Call Stack 영역의 작업은 수행
    - Callback Queue : Web APIs에 대기중이던 함수 중 실행해야 하는 함수를 받아와서 쌓음
    - Event Loop : Callback Queue에 있는 함술흘 Call Stack으로 넘겨줌, Call Stack에 Main Context외에 다른 함수가 없을 때 전달

	# 8. Promise - 콜백 지옥에서 탈출하기

- 자바스크립트에서 비동기 메소드를 처리하는 방식
- 콜백을 메소드 체이닝으로 처리할 수 있음

## 8.1 비동기 작업이 가질 수 있는 3가지 상태

- 상태 3가지
    - Pending(대기)
    - Fulfilled(성공)
    - Rejected(실패)
- 상태의 변환
    - resolve : Pending → Fulfilled
    - reject : Pending → Rejected

## 8.2 Promise

- `new Promise( (resolve, reject) ⇒ { })`
    - Promise : 메소드를 비동기로 실행하기 위해 Promise를 생성하기 위한 객체 이름
    - resolve : Promise가 받는 executor가 실행 성공시 호출되는 메소드
    - reject : Promise가 받는 executor가 실행 실패시 호출되는 메소드
- Promise 객체는 메소드 체이닝으로 resolve와 reject의 결과값을 사용할 수 있음
    - `Promise.then( (res) ⇒ { })` : 해당 체인은 resolve 콜백으로 전달
    - `Promise.catch( (err) ⇒ { })` : 해당 체인은 reject 콜백으로 전달

```jsx
function methodA(num) {
  return new Promise((resolve, reject) => {
    resolve(num);
  });
}

methodA(1).then((res) => {
  console.log("depth1: " + res);
});
```

# 9. async & await

## 9.1. async

- async 키워드를 함수앞에 붙여주면 함수의 타입이 Promise로 변경됨

```jsx
async function method(){
	return "hello";
}

console.log(method()); // Promise {<pending>}
method().then( (res) => {
	console.log(res); // hello
});
```

## 9.2 await

- 비동기적인 함수를 동기적으로 동작하게 만들어 줌

```jsx
function delay(ms) {
	return new Promise( (resolve) => {
		setTimeout(resolve, ms);
	}
}

async function helloAsync() {
	await delay(3000); // 비동기 함수 앞에 await 키워드를 붙여서 동기적으로 동작
	return "hello async";
}

helloAsync().then( (res) => {
	console.log(res); // 3초뒤에 "hello async" 출력
};
```