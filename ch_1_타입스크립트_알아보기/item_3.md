# 코드 생성과 타입이 관계없음을 이해하기

타입스크립트 컴파일러는 두 가지 역할을 수행합니다.

1. 최신 타입스크립트/자바스크립트를 브라우저에서 동작할 수 있도록 구버전의 자바스크립트로 트랜스파일(transpile)합니다.
2. 코드의 타입 오류를 체크합니다.

그리고 이 두 가지 역할은 <strong>독립적으로</strong> 수행됩니다.

</br>

> ## 타입 오류가 있는 코드도 트랜스파일이 가능합니다

</br>

트랜스파일과 타입체크는 독립적으로 이루어지기 때문에 타입 오류가 있어도 트랜스파일을 멈추지 않고 트랜스파일된 코드가 담긴 .js 파일을 생성합니다.

만약, 타입 오류가 있을 때 트랜스파일하지 않으려면 `noEmitOnError`를 true로 설정하면됩니다.

</br>

> ## 런타임에는 interface, type의 타입체크가 불가능 합니다

</br>

타입스크립트의 interface와 type은 자바스크립트로 트랜스파일 되는 과정에서 제거됩니다. 그러므로 타입의 값은 런타임에 접근이 가능하지만 타입 자체는 런타임에 접근이 불가능 합니다.

instanceof 체크는 런타임에 일어나지만, Rectangle은 타입(interface, 런타임 접근 불가)이기 때문에 오류가 발생합니다.

```jsx
interface Square {
	width: number;
}
interface Rectangle extends Square {
	height: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if (shape instanceof Rectangle) {
		// ~~~~~~~~~ 'Rectangle' only refers to a type,
		//           but is being used as a value here
		return shape.width * shape.height;
		//         ~~~~~~ Property 'height' does not exist
		//                on type 'Shape'
	} else {
		return shape.width * shape.width;
	}
}
```

</br>

이를 해결하기 위해서 타입 정보를 유지하는 방법이 필요합니다.

1. 속성이 존재하는지 체크하기
2. 런타임에 접근 가능한 타입정보를 명시적으로 선언하는 태그기법 사용하기
3. 타입을 클래스로 만들기

</br>

### 1. 속성이 존재하는지 체크하기 (height 속성 체크)

```jsx
interface Square {
	width: number;
}
interface Rectangle extends Square {
	height: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if ("height" in shape) {
		return shape.width * shape.height;
	} else {
		return shape.width * shape.width;
	}
}
```

</br>

### 2. 런타임에 접근 가능한 타입정보를 명시적으로 선언하는 태그기법 사용하기

```jsx
interface Square {
	kind: "square"; // 타입 정보를 명시적으로 선언
	width: number;
}
interface Rectangle {
	kind: "rectangle"; // 타입 정보를 명시적으로 선언
	height: number;
	width: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if (shape.kind === "rectangle") {
		return shape.width * shape.height;
	} else {
		return shape.width * shape.width;
	}
}
```

</br>

### 3. 타입을 클래스로 만들기

Rectangle을 Class(런타임에 접근 가능)로 선언하면 타입과 값으로 모두 사용할 수 있어 오류를 해결할 수 있습니다.

```jsx
class Square {
  constructor(public width: number) {}
}
class Rectangle extends Square {
  constructor(public width: number, public height: number) {
    super(width);
  }
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) { // Type is Rectangle
    return shape.width * shape.height;
  } else { // Type is Square
    return shape.width * shape.width;  // OK
  }
}
```

</br>

> ## 타입 단언은 런타임에 영향을 주지 않습니다

</br>

다음 함수는 타입이 number 또는 string인 값을 받아 타입이 number인 값으로 리턴하는 함수입니다.

```jsx
function asNumber(val: number | string): number {
  return val as number;
}
```

위 코드를 트랜스파일한 함수는 다음과 같습니다. 타입을 정제해주는 코드가 모두 사라져 의도한대로 함수가 동작하지 않을 수 있습니다.

```jsx
function asNumber(val) {
	return val;
}
```

`as number` 은 `타입 단언`이며 타입 단언은 런타임에 어떠한 영향도 줄 수 없습니다. 의도한대로 함수의 리턴값을 만들어내기 위해서는 자바스크립트로 타입을 연산을 해주어야합니다.

```jsx
function asNumber(val: number | string): number {
	return typeof val === "string" ? Number(val) : val;
}
```

위 함수를 트랜스파일하면 다음과 같습니다. 타입으로 값을 정제하는 자바스크립트 코드가 남아있기 떄문에 의도한대로 함수가 작동하게 됩니다.

```jsx
function asNumber(val) {
	return typeof val === "string" ? Number(val) : val;
}
```

</br>

> ## 런타임 타입은 선언된 타입과 다를 수 있습니다

</br>

</br>

> ## 타입스크립트 타입으로는 함수를 오버로드 할 수 없습니다

동일한 이름에 매개변수만 다른 여러 함수를 허용하는것을 `함수 오버로딩` 이라고 합니다. 타입스크립트에서는 <strong>타입과 런타임 동작이 무관</strong>하기 때문에 함수 오버로딩이 불가능합니다.
</br>

```jsx
function add(a: number, b: number) {
	return a + b;
}
// ~~~ Duplicate function implementation
function add(a: string, b: string) {
	return a + b;
}
// ~~~ Duplicate function implementation
```

</br>

그러나 하나의 함수에 대한 여러개의 선언문을 작성하는 타입 수준에서의 함수 오버로딩은 가능합니다.

```jsx
function add(a: number, b: number): number;
function add(a: string, b: string): string;

function add(a, b) {
return a + b;
}

const three = add(1, 2); // Type is number
const twelve = add('1', '2'); // Type is string

```

위 함수를 트랜스파일하면 선언문은 모두 제거되지만 함수 자체는 남게 됩니다.

```jsx
function add(a, b) {
	return a + b;
}
var three = add(1, 2); // Type is number
var twelve = add("1", "2"); // Type is string
```

</br>

> ## 타입스크립트 타입은 런타임 성능에 영향을 주지 않습니다

</br>
타입스크립트의 타입과 타입 연산자는 자바스크립트로 트랜스파일 되는 시점에 제거되기 때문에, 런타임의 성능에 아무런 영향을 주지 않습니다.
</br>

</br>

### \* 참고) `컴파일(compile)`과 `트랜스파일(transpile)`

트랜스파일하는 경우도 컴파일한다고 혼용하여 사용되고는 하지만, 트랜스파일된 결과물은 결국 기계어로 컴파일하는 과정이 필요하기 때문에 트랜스파일과 컴파일은 분리해서 이해하는것이 좋습니다.

- `컴파일(complie)` : 한 언어로 작성된 소스코드를 다른 언어로 변환하는 것.

  - ex) java => bytecode

  </br>

- `트랜스파일(transpile)` : 한 언어로 작성된 소스코드를 비슷한 수준의 추상화를 가진 다른언어로 변환하는 것.
  - ex) es6 => es5, typescript => javascript

(책의 저자가 트랜스파일을 의미하는 부분을 모두 컴파일이라는 단어로 내용을 설명하고 있지만, 명확한 이해를 위해 트랜스파일이라는 단어로 변경하여 본 글을 작성했습니다.)

</br>

## 요약

- 타입스크립트 코드의 생성(트랜스파일)과 타입은 무관합니다.

  그러므로,

- 타입 오류가 존재하더라도 코드 생성은 가능합니다.
- 타입은 런타임에 사용할 수 없습니다.
- 타입 단언은 런타임에 영향을 주지 않습니다
- 런타임 타입은 선언된 타입과 다를 수 있습니다
- 타입스크립트 타입으로는 함수를 오버로드 할 수 없습니다
- 타입은 런타임 동작이나 성능에 영향을 주지 않습니다.
