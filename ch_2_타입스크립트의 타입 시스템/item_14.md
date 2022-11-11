# 타입 연산과 제너릭 사용으로 반복 줄이기 ing

DRY(don't repreat yourself)원칙을 타입에도 적용해 봅시다.

</br>

## 1) 타입에 이름 붙이기

반복을 줄이는 가장 간단한 방법은 타입에 이름을 붙이는 것입니다.

```js
function distance(a: { x: number, y: number }, b: { x: number, y: number }) {
	return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}
```

타입 `{ x: number, y: number }`가 반복됨으로 Point2D라는 이름을 붙여 중복을 제거해 줄 수 있습니다.

```js
interface Point2D {
	x: number;
	y: number;
}
function distance(a: Point2D, b: Point2D) {
	/* ... */
}
```

</br>

같은 타입 시그니처를 가진 함수의 반복 또한 시그니처를 명명된 타입으로 분리해 내어 중복을 제거해 줄 수 있습니다.

```js
// 중복 제거 전
function get(url: string, opts: Options): Promise<Response> {
	return Promise.resolve(new Response());
}
function post(url: string, opts: Options): Promise<Response> {
	return Promise.resolve(new Response());

	// 중복 제거 후
	type HTTPFunction = (url: string, options: Options) => Promise<Response>;

	const get: HTTPFunction = (url, options) => {
		return Promise.resolve(new Response());
	};
	const post: HTTPFunction = (url, options) => {
		return Promise.resolve(new Response());
	};
}
```

</br>

## 2) 확장하기

확장을 통해서도 반복을 제거해 줄 수 있습니다.

```js
interface Person {
	firstName: string;
	lastName: string;
}

interface PersonWithBirthDate extends Person {
	birth: Date;
}
```

</br>

## 3) 매핑된 타입 사용하기

매핑된 타입으로 중복을 제거할 수 있습니다.

TopNavState는 State의 일부 타입만 가지고 있습니다.

```js
interface State {
	userId: string;
	pageTitle: string;
	recentFiles: string[];
	pageContents: string;
}
interface TopNavState {
	userId: string;
	pageTitle: string;
	recentFiles: string[];
}
```

이때 State를 State["userId"]와 같이 인덱싱하여 속성의 타입에서 중복을 제거 할 수 있습니다.
그럼에도 `State[""]` 부분이 중복됩니다.

```js
interface State {
	userId: string;
	pageTitle: string;
	recentFiles: string[];
	pageContents: string;
}
interface TopNavState {
	userId: State["userId"];
	pageTitle: State["pageTitle"];
	recentFiles: State["recentFiles"];
}
```

매핑된 타입

```js
type TopNavState = {
  [k in 'userId' | 'pageTitle' | 'recentFiles']: State[k]
};
```

Pick

```js
type TopNavState = Pick<State, "userId" | "pageTitle" | "recentFiles">;
```

partial

```js
interface Options {
	width: number;
	height: number;
	color: string;
	label: string;
}
class UIWidget {
	constructor(init: Options) {
		/* ... */
	}
	update(options: Partial<Options>) {
		/* ... */
	}
}
```

typeof
값의 형태에 해당하는 타입을 정의하고 싶을때

```js
const INIT_OPTIONS = {
	width: 640,
	height: 480,
	color: "#00FF00",
	label: "VGA",
};

// typeof 사용 전
interface Options {
	width: number;
	height: number;
	color: string;
	label: string;
}

// typeof 사용 후
type Options = typeof INIT_OPTIONS;
```

ReturnType
함수나 메서드의 반환값에 명명된 타입을 만들고 싶은 경우

```js
function getUserInfo(userId: string) {
	// COMPRESS
	const name = "Bob";
	const age = 12;
	const height = 48;
	const weight = 70;
	const favoriteColor = "blue";
	// END
	return {
		userId,
		name,
		age,
		height,
		weight,
		favoriteColor,
	};
}
// Return type inferred as { userId: string; name: string; age: number, ... }

// ReturnType 사용
type UserInfo = ReturnType<typeof getUserInfo>;
```

extends를 통해 제네릭 타입에서 매개변수를 제한하기

```js
interface Name {
  first: string;
  last: string;
}
type DancingDuo<T extends Name> = [T, T];

const couple1: DancingDuo<Name> = [
  {first: 'Fred', last: 'Astaire'},
  {first: 'Ginger', last: 'Rogers'}
];  // OK
const couple2: DancingDuo<{first: string}> = [
                       // ~~~~~~~~~~~~~~~
                       // Property 'last' is missing in type
                       // '{ first: string; }' but required in type 'Name'
  {first: 'Sonny'},
  {first: 'Cher'}
];

```

## 요약

- DRY(don't repreat yourself) 원칙을 타입에도 적용해야 합니다.
- 타입에 이름을 붙이거나 extends를 사용해 타입의 반복을 줄여줄 수 있습니다.
- 타입들 간의 매핑을 위해 keyof, typeof, 인덱싱, 매핑된 타입을 알 필요가 있습니다.
- 제너릭 타입은 타입을 위한 함수와 같습니다. 타입을 반복하는 대신 제너릭타입을 사용하여 타입들 간에 매핑을 하는 것이 좋습니다. 제너릭 타입을 제한하려면 extends를 사용하면 됩니다.
- 표준 라이브러리에 정의된 Pick, Partial, ReternType같은 제너릭 타입에 익숙해져야 합니다.
