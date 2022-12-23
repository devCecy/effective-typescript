# 객체를 순회하는 노하우 : Object.entries

const k in abc의 k의 타입이 'a', 'b', 'c'가 아닌 string으로 추론되어 오류가 발생했습니다.
`let k: keyof typeof ABC;` 를 선언해주면 오류는 사라질 것입니다.

```ts
interface ABC {
	a: string;
	b: string;
	c: number;
}

function foo(abc: ABC) {
	for (const k in abc) {
		// const k: string
		const v = abc[k];
		// ~~~~~~ Element implicitly has an 'any' type
		//        because type 'ABC' has no index signature
	}
}
```

타입문제 없이 단지 객체의 키와 값을 순회하고 싶다면, `Object.entries`를 사용하면됩니다.

```ts
interface ABC {
	a: string;
	b: string;
	c: number;
}
function foo(abc: ABC) {
	for (const [k, v] of Object.entries(abc)) {
		k; // Type is string
		v; // Type is any
	}
}
```

## 요약

- 객체를 순회 할ㄷ 때, 키가 어떤 타입인지 정확히 파악하고 있다면 let k : keyof T와 for-in 루프를 사용합시다.
- 객체를 순회하며 키와 값을 얻는 가장 일반적인 방법은 `Object.entries`를 사용하는 것입니다.
