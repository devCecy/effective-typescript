# 모르는 타입의 값에는 any대신 unknwon을 사용하기

</br>

## 함수의 반환값과 관련된 형태

함수의 반환타입으로 any를 사용하는 것은 좋지 않습니다.
객체에 없는 값을 호출했지만 바로 오류가 체크되지 않고 런타임에 오류가 발생하게 됩니다.

```ts
function parseYAML(yaml: string): any {
	// ...
}
interface Book {
	name: string;
	author: string;
}
const book = parseYAML(`
  name: Jane Eyre
  author: Charlotte Brontë
`);
alert(book.title); // No error, alerts "undefined" at runtime
book("read"); // No error, throws "TypeError: book is not a
// function" at runtime
```

함수의 반환값에 타입을 강제할 수 없는 경우에는 any대신 `unknown`을 사용하는 것이 좋습니다. unknown을 사용하자 바로 타입체커가 오류를 잡아냅니다.

```ts
function safeParseYAML(yaml: string): unknown {
	return parseYAML(yaml);
}
const book = safeParseYAML(`
  name: The Tenant of Wildfell Hall
  author: Anne Brontë
`);
alert(book.title);
// ~~~~ Object is of type 'unknown'
book("read");
// ~~~~~~~~~~ Object is of type 'unknown'
```

</br>

## 변수 선언과 관련된 형태

어떤 값이 있지만 그 타입을 모르는 경우에는 unknown을 사용해야합니다.

```ts
interface Feature {
	id?: string | number;
	geometry: Geometry;
	properties: unknown;
}
```

타입 단언문이 unknown에서 원하는 타입으로 변환하는 유일한 방법은 아닙니다. instanceof를 체크한 후 unknown에서 원하는 타입으로 변환할 수 있습니다.

```ts
function processValue(val: unknown) {
	if (val instanceof Date) {
		val; // Type is Date
	}
}
```

또한 사용자 정의 타입 가드도 unknown에서 원하는 타입으로 변환할 수 있습니다.

```ts
function isBook(val: unknown): val is Book {
	return (
		typeof val === "object" && val !== null && "name" in val && "author" in val
	);
}
function processValue(val: unknown) {
	if (isBook(val)) {
		val; // Type is Book
	}
}
```

</br>

## 단언문과 관련된 형태

이중 단언문에서 any대신 unknown을 사용할 수도 있습니다.
barAny와 barUnksms 기능적으로 동일하지만, 나중에 두개의 단언문을 분리하는 리팩터링을 한다면 unknown은 분리되는 순간 즉시 오류가 발생됨으로 unknown형태가 더 안전합니다.

```ts
declare const foo: Foo;
let barAny = foo as any as Bar;
let barUnk = foo as unknown as Bar;
```

</br>

## unknown과 유사하지만 조금 다른 타입

- {} 타입은 null과 undefined를 제외한 모든 값을 포함합니다.
- object타입은 모든 비기본형(non-primitive)타입으로 이루어집니다. 여기에는 true 또는 12 또는 'foo'가 포함되지 않지만 객체와 배열은 포함됩니다.

</br>

## 요약

- unknown은 any대신 사용할 수 있는 안전한 타입입니다. 어떠한 값이 있지만 그 타입을 알지 못하는 경우라면 unknown을 사용하면 됩니다.
- 사용자가 타입 단언문이나 타입 체크를 사용하도록 강제하려면 unknown을 사용하면 됩니다.
- {}, object, unknown의 차이점을 이해해야합니다.
