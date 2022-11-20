# 타입 좁히기 (narrowing)

타입 좁히기는 타입스크립트가 넓은 타입으로부터 좁은 타입으로 진행하는 과정을 말합니다.

</br>

## 1. null 체크

가장 일반적은 타입 좁히기 방법은 null 체크 입니다.

```ts
const el = document.getElementById("foo"); // Type is HTMLElement | null
if (el) {
	el; // Type is HTMLElement
	el.innerHTML = "Party Time".blink();
} else {
	el; // Type is null
	alert("No element #foo");
}
```

</br>

## 2. instanceof

instanceof는 생성자의 prototype 속성이 객체의 프로토타입 체인 어딘가 존재하는지 판별합니다.

```ts
function contains(text: string, search: string | RegExp) {
	if (search instanceof RegExp) {
		search; // Type is RegExp
		return !!search.exec(text);
	}
	search; // Type is string
	return text.includes(search);
}
```

</br>

## 3. 속성체크

in 연산자는 명시된 속성이 명시된 객체에 존재하면 true를 반환합니다.

```ts
interface A {
	a: number;
}
interface B {
	b: number;
}
function pickAB(ab: A | B) {
	if ("a" in ab) {
		ab; // Type is A
	} else {
		ab; // Type is B
	}
	ab; // Type is A | B
}
```

</br>

## 4. Array.isArray

Array.isArray 메서드는 인자가 Array인지 판별합니다.

```ts
function contains(text: string, terms: string | string[]) {
	const termList = Array.isArray(terms) ? terms : [terms];
	termList; // Type is string[]
	// ...
}
```

</br>

## 5. 명시적 태그 붙이기 (태그된 유니온, 구별된 유니온)

```ts
interface UploadEvent {
	type: "upload"; // 명시적 태그
	filename: string;
	contents: string;
}

interface DownloadEvent {
	type: "download"; // 명시적 태그
	filename: string;
}

type AppEvent = UploadEvent | DownloadEvent;

function handleEvent(e: AppEvent) {
	switch (e.type) {
		case "download":
			e; // Type is DownloadEvent
			break;
		case "upload":
			e; // Type is UploadEvent
			break;
	}
}
```

</br>

## 커스텀 함수 (사용자 정의 타입 가드)

```ts
function isInputElement(el: HTMLElement): el is HTMLInputElement {
	return "value" in el;
}

function getElementContent(el: HTMLElement) {
	if (isInputElement(el)) {
		el; // Type is HTMLInputElement
		return el.value;
	}
	el; // Type is HTMLElement
	return el.textContent;
}
```

</br>

## 주의할 점

타입스크립트는 조건문에서 타입 좁히기를 매우 잘 해내지만 조건문을 나누는 기준 타입 자체를 섣불리 판단하면 실수가 발생할 수 있습니다.

다음코드는 el의 타입이 object인 경우에 HTMLElement로 타입을 좁히려고 했지만 자바스크립트에서 null 또한 타입이 object가 되기 때문에 의도한대로 타입이 좁혀지지 못했습니다.

```ts
const el = document.getElementById("foo"); // type is HTMLElement | null
if (typeof el === "object") {
	el; // Type is HTMLElement | null
}
```

</br>

## 요약

- 타입 좁히기는 타입스크립트가 넓은 타입으로부터 좁은 타입으로 진행하는 과정을 말합니다.
- 타입 좁히기 방법으로는 null체크, instanceof, 속성체크, Array.isArray, 명시적 태그 붙이기, 타입가드 등의 방법이 있습니다.
