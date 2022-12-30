# 1. 변수와 상수

## 1.1 변수

- 변수는 프로그램 실행 중에 변하는 값을 저장하기 위한 공간

```jsx
let age = 25;
console.log(age); // 25

age = 30;
console.log(age); // 30
```

## 1.2 상수

- 상수(read-only)는 변수와 같이 이름으로 사용하지만 프로그램 실행 중에 변하지 않음

```jsx
const age = 25;
age = 30 // Error
```

# 2. 자료형과 형 변환

## 2.1. Primitive Data Type(원시 타입)

- 한번에 하나의 값 만 가질 수 있음
- 하나의 고정된 저장 공간 이용
- `Number`, `String`, `Boolean`, `Undefined`, `Null`

### 2.1.1. Number

- 일반적인 정수와 실수형을 포함
    - `25`, `10.5` ...
- 타입을 구분하지는 않음
- `Infinity`, `-Infinity`, `NaN` 등의 값도 숫자형에 해당

### 2.1.2. String

- 쌍 따옴표(`“”`), 작은 따옴표(`‘’`), 백틱(````) 으로로 감싼 문자의 집합
- 백틱은 내부에 변수를 사용할 수 있음 - 템플릿 리터럴이라고 함
    
    ```jsx
    const name = "John";
    console.log(`Hello, ${name}`);
    ```
    

### 2.1.3. Boolean

- 참(`true`) 또는 거짓(`false`)을 저장

```jsx
const isOn = true;
```

### 2.1.4. Null

- 아무것도 없다는 것을 지칭
- 자바스크립트에서는 null을 직접 지정해줘야 됨 아니면 undefined 값으로 선언
    
    ```jsx
    const a;
    console.log(a); // undefined
    
    const a = null;
    console.log(a) // null
    ```
    

### 2.1.5. Undefined

- 변수 선언 후 아무런 값을 할당하지 않았을 때 할당되는 값

## 2.2. Non-Primitive Data Type(비 원시 타입)

- 한번에 여러 개의 값을 가질 수 있음
- 여러 개의 고정되지 않은 동적 공간 사용
- `Object`, `Array`, `Fuction`

## 2.3 형변환(Casting)

- 값을 유지한채 타입을 변경하는 것
- 명시적으로 형변환하지 않고 런타임에 일어나는 것을 묵시적 형변환이라고 함
    
    ```jsx
    let a = 12;
    let b = "2";
    console.log(a + b) // 122
    ```
    
- 의도적으로 타입을 변환하는 것을 명시적 형변환이라고 함
    
    ```jsx
    let a = 12;
    let b = "2";
    console.log(a + parseInt(b)) // 14
    ```
    

# 3. 연산자

- 사칙연산 등 다양한 연산을 지원

## 3.1. 대입연산자

- `=`: 변수에 값을 대입할 때 사용
    
    ```jsx
    const a = 1;
    ```
    

## 3.2. 사칙연산자

- `+` : 덧셈
    - `5 + 2` : 7
- `-` : 뺄셈
    - `5 - 2` : 3
- `*` : 곱셈
    - `5 * 2` : 10
- `/` : 나눗셈
    - `5 / 2` : 2
- `%` : 나머지
    - `5 % 2` : 1

## 3.3. 복합연산자

- `+=` : 변수에 특정 값을 다시 더할 때 사용
    - `a += 10`
    - `+` 기호 이외에 `-`, `*`, `/` 기호도 사용 가능

## 3.4. 증감연사자

- 숫자형에만 사용가능한 연산자
- 변수의 값을 1씩 증가하거나 감소시킴
    - `a++` : 1 증가
    - `a--` : 1 감소
- 식에 사용될 경우 변수의 앞(`++a`)에 붙으면 연산 후의 값을 사용하고 변수의 뒤(`a++`)에 붙으면 연산 전의 값을 사용 후 증감

## 3.5. 논리연산자

- `!` : Boolean 자료형의 값을 반전시키는 연산자
    - `!true = false`
    - `!false = true`
- `&&` : 양측의 피연산자의 값이 모두 true 경우에만 true. 나머지는 false
- `||` : 양측의 피연산자의 값이 둘중 하나라도 true일 경우에 true. 나머지는 false

## 3.6. 비교연산자

- `==` : 값이 같은지 비교
- `===` : 값과 타입이 같은지 비교
- `!=`  : 값이 다른지 비교
- `!==` : 값과 타입이 다른지 비교
- `>`, `<` : 값이 크거나 작은지 비교
- `>=`, `<=` : 값이 크거나 같은지, 작거나 같은지 비교

## 3.7. typeof

- 변수에 할당된 값의 타입을 반환하는 연산자
    
    ```jsx
    int a = "Value";
    console.log(typeof a); // string
    ```
    

## 3.8. Null 병합 연산자

- `??` : 양측의 피연산자 중에 Null 또는 Undefined가 아닌 값을 선택
    
    ```jsx
    int a;
    a = a ?? 10;
    console.log(a); // 10
    ```
    

# 4. 조건문

- 어떤 식의 결과의 참, 거짓에 따라서 각각 서로 다른 명령으로 분기할 수 있도록 해주는 문법

## 4.1. If 문

- `if(조건식)` : 내부의 조건식이 참일 경우 실행
- `else if(조건식)` : 앞의 if 문의 조건식이 참이 아닐경우 연쇄적으로 조건식을 확인
- `else` : 앞의 if문과 else if 문 모두 거짓일 경우 실행
    
    ```jsx
    if(a) {
    	// when a is true
    } else if(b){
    	// when b is true
    } else{
    	// when a and b is false
    }
    ```
    

## 4.2. switch 문

- `switch(값)` :  `case 값:`에서 값이 switch에 선언된 값과 일치하 명령을 실행
    
    ```jsx
    switch(a){
    	case "A":
    		console.log("This is A");
    		break;
    	case "B":
    		console.log("This is B");
    		break;
    	default:
    		console.log("This is default");
    		break;
    }
    ```
    

# 5. 함수 선언식

- 똑같은 동작을 하는 동작을 하는 코드를 하나로 묶어서 함수로 사용할 수 있음
    
    ```jsx
    function sum(a, b){
    	return a + b
    }
    
    console.log(name(10, 20)); // 30
    ```
    
    - `function` : 함수 사용하겠다 선언
    - `sum` : 함수의 이름
    - `(a, b)` : 함수 호출 시 받을 파라미터 선언
    - `return a + b` : 함수의 결과값을 반환

# 6. 함수 표현식 & 화살표 함수

## 6.1. 함수 표현식

- 자바스크립트에서는 함수(Function)도 값의 타입이기 때문에 변수에 담아서 사용할 수 있다
    - 함수의 이름을 지정할 필요가 없음
    - 함수 선언식은 호이스팅이 되지만 함수 표현식은 호이스팅이 되지 않음
    
    ```jsx
    let hello = function (){
    	console.log(`hello`);	
    }
    ```
    

## 6.2. 화살표 함수

- 함수 표현식을 간략하게 줄여서 사용할 수 있음
    - `fucntion` 키워드를 지우고 괄호 뒤에 `->` 화살표를 넣어주면 된다.
    
    ```jsx
    let hello = () -> {
    	console.log(`hello`);
    }
    ```
    

# 7. 콜백함수

- 어떤 다른 함수의 매개변수로 넘겨준 함수를 뜻함
    
    ```jsx
    function checkMood(mood, good, sad){
    	if(mood === 'good'){
    		good(); // Callback
    	} else {
    		sad(); // Callback
    	}
    }
    
    function cry(){
    	console.log("ACTION :: CRY");
    }
    
    function sing(){
    	console.log("ACTION :: SING");
    }
    
    function dance(){
    	console.log("ACTION :: DANCE");
    }
    
    checkMood("good", sing, cry); // sing과 cry가 Callback 함수가 됨
    ```
    

# 8. 객체

- 여러 값을 저장할 수 있는 비원시 타입의 값
- 객체 생성 방법
    - `new` 키워드를 통해 생성 : `let person = new Object();`
    - `{}(중괄호)`를 통해 생성: `let person = {};`, 객체 리터럴이라고도 함
- 중괄호 내부에 `key: value` 형식으로 쉼표로 구분해 여러 값을 저장
    - `key: value` 형식을 객체 프로퍼티라고 함
    - 프로퍼티의 `value`에는 모든 자료형이 할당될 수 있다.
    
    ```jsx
    let person = {
    	name: "John",
    	age: 20
    };
    
    // . 표현식으로 조회
    console.log(person.name); // John
    console.log(person.age); // 20
    console.log(person.gender); // undefined
    
    // 프로퍼티 이름을 문자열로 조회
    console.log(person["name"]); // John
    console.log(person["age"]); // 20
    console.log(person["gender"]); // undefined
    
    // 새로운 프로퍼티 할당
    person.gender = "male";
    console.log(person.gender); // male
    
    // 기존 프로퍼티 삭제
    delete person.gender;
    console.log(person.gender); // undefined
    
    // 프로퍼티가 있는지 확인
    console.log("name" in person) // true
    ```
    

# 9. 배열

- 순서있는 요소들의 집합을 만들 수 있는 비원시 자료형
- 배열의 요소로 어떠한 타입이 들어와도 됨
- 생성하는 방법
    - `let arr = new Array()`
    - `let arr = []`(객체 리터럴 방식)
- 각 요소에는 index 번호를 통해서 접근. 맨 앞의 요소가 0부터 시작

```jsx
let arr = [1, 2, 3, 4];

console.log(arr); // [1, 2, 3, 4]
console.log(arr[2]); // 3

// 배열 자체도 객체로 이루어져 있기 때문에 객체의 프로퍼티에 접근하는 것 처럼 사용
console.log(arr.length); // 4
```

# 10. 반복문

- 특정 문장을 반복해서 수행할 때 사용

## 10.1. for 문

- `for(초기식 ; 조건식 ; 연산(증감식..) ){ 명령 }`
    
    ```jsx
    for(int i = 0; i < 5 ; i++){
    	console.log(i);
    }
    ```
    

# 11. 배열 내장 함수

- 배열이 기본적으로 내장하고 있는 함수

## 11.1. forEach()

- 배열의 요소들을 순회하는 함수
- 인자로 요소를 파라미터로 받는 콜백 함수를 전달

```jsx
const arr = [1, 2, 3, 4];

arr.forEach( (e) => console.log(e));
```

## 11.2. push()

- 배열의 맨 끝에 새로운 요소들을 추가

```jsx
const arr = [];

arr.push(1);
arr.push(2);
arr.push(3);
arr.push(4);
```

## 11.3. map()

- 요소들을 순회하면서 기존의 값을 이용해 새로운 값으로 변경해서 반환

```jsx
const arr = [1, 2, 3, 4];
const newArr = arr.map( (e) => {
	reteurn e * 2;
});
console.log(newArr); // [2, 4, 6, 8]
```

## 11.4. includes()

- 배열 내부에 인자로 넘겨진 값과 일치하는 요소가 있는지 비교
- `===` 연산자를 사용해 비교하는 것과 같은 결과 반환

```jsx
const arr = [1, 2, 3, 4]

console.log(arr.includes(3)) // true
console.log(arr.includes("3")) // false
```

## 11.5. indexOf()

- 인자로 넘겨진 값과 일치하는 요소가 배열의 몇 번째에 있는지 반환

```jsx
const arr = [1, 2, 3, 4]

console.log(arr.indexOf(3)) // 2
console.log(arr.indexOf("3")) // -1, 일치하는 요소가 없음
```

## 11.6. findIndex()

- 배열의 요소를 순회하면서 콜백이 true를 반환하는 요소의 인덱스를 반환
- 똑같은 요소가 2개 이상 있다면 맨 처음 발견되는 요소의 인덱스를 반환

```jsx
const arr = [
	{ color: "red" },
	{ color: "black" },
	{ color: "blue" },
	{ color: "green" }
];

arr.findIndex( (e) => e.color === "green"); // 3
```

## 11.7. find()

- 배열의 요소를 순회하면서 콜백이 true를 반환하는 요소를 반환
- 똑같은 요소가 2개 이상 있다면 맨 처음 발견되는 요소의 를 반환
    
    ```jsx
    const arr = [
    	{ color: "red" },
    	{ color: "black" },
    	{ color: "blue" },
    	{ color: "green" }
    ];
    
    arr.findIndex( (e) => e.color === "green"); // { color: "green" }
    ```
    

## 11.8. filter()

- 배열의 요소를 순회하면서 콜백이 true를 반환하는 모든 요소들만 필터링

```jsx
const arr = [
	{ num: 1, color: "red" },
	{ num: 2, color: "black" },
	{ num: 3, color: "blue" },
	{ num: 4, color: "green" }
	{ num: 5, color: "blue" }
];

arr.filter( (e) => e.color === "blue"); 
// [{num: 3, color: "blue"},{num: 5, color: "blue"}]
```

## 11.9. slice()

- 배열의 요소를 인자로 주어진 인덱스만큼 자름
- 첫 번째 인자는 시작 인덱스, 두 번째 인자는 마지막 인덱스+1

```jsx
const arr = [1, 2, 3, 4]

arr.slice(0, 2); // [1, 2]
```

## 11.10. concat()

- 2개의 배열을 이어 붙이는 기능

```jsx
const arr1 = [1, 2, 3];
const arr2 = [4, 5];

arr1.concat(arr2); // [1, 2, 3, 4, 5]
```

## 11.11. sort()

- 배열의 요소를 정렬
- 원본 배열 자체를 변경함
- 문자열을 기준으로 사전순으로 정렬하기 때문에 숫자는 제대로 안됨

```jsx
let chars = ["b", "c", "a"];
let nums = [4, 2, 1, 3];

chars.sort(); // ["a", "b", "c"]
nums.sort(); // [1, 2, 3, 4]
```

### 11.11.1 비교 함수 만들기

- sort()의 비교 로직을 변경할 때 비교 함수를 만들어서 전달

```jsx
let nums = [40, 10, 2, 1 , 3, 20, 30, 4];
const compare = (a, b) => {
	if( a > b){
		return 1;
	}

	if( a < b) {
		reutnr -1;
	}

	return 0;
}

nums.sort(compare); // [1, 2, 3, 4, 10, 20, 30, 40]
```

## 11.12. join()

- 인자로 전달된 문자로 요소들을 구분하여 하나의 문자열로 합침

```jsx
const arr = [1 ,2 ,3 ,4];

arr.join(" "); // 1 2 3 4
```