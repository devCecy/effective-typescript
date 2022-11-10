# 함수 표현식에 타입 적용하기

</br>

## 함수 선언식 보다 함수 표현식을 사용하는 것이 좋습니다

타입스크립트에서는 함수 문장(statement, 선언문)보다 `함수 표현식(expression)`을 사용하는 것이 좋습니다.

```js
function rollDice1(sides: number): number {
	return 0;
} // 함수 선언문
const rollDice2 = function (sides: number): number {
	return 0;
}; // 함수 표현식
const rollDice3 = (sides: number): number => {
	return 0;
}; // 함수 표현식
```

</br>

함수의 매개변수부터 반환값까지 전체를 함수 타입으로 선언하여 함수 표현식에 재사용할 수 있기 때문입니다.

```js
type DiceRollFn = (sides: number) => number;
const rollDice: DiceRollFn = (sides) => {
	return 0;
};
```

</br>

## 다른 함수의 시그니처를 참조하려면 typeof fn을 사용할 수 있습니다

아래 예시는 함수 선언문으로 작성된 checkedFetch함수의 매개변수마다 타입을 선언해 주었습니다. 아래 코드는 잘 작동하지만 함수 표현식과 typeof fn을 이용해 더 간결하게 작성 할 수 있습니다.

```js
async function checkedFetch(input: RequestInfo, init?: RequestInit) {
	const response = await fetch(input, init);
	if (!response.ok) {
		// Converted to a rejected Promise in an async function
		throw new Error("Request failed: " + response.status);
	}
	return response;
}
```

fetch함수의 타입을 선언은 아래와 같이 작성되어 있기 때문에 typeof fetch를 사용하여 함수 타입을 선언해 줄 수 있습니다.

```js
declare function fetch(
	input: RequestInfo,
	init?: RequestInit
): Promise<Response>;
```

함수 선언문을 함수 표현식으로 변경한 뒤 함수 자체에 typeof fetch로 타입을 적용해 주었습니다.

```js
const checkedFetch: typeof fetch = async (input, init) => {
	const response = await fetch(input, init);
	if (!response.ok) {
		throw new Error("Request failed: " + response.status);
	}
	return response;
};
```

## 요약

- 매개변수나 반환 값에 타입을 명시하기 보다는 함수 표현식 전체에 타입구문을 적용하는 것이 좋습니다.
- 다른 함수의 시그니처를 참조하려면 typeof fn를 사용할 수 있습니다.
