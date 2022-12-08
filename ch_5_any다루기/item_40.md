# 함수 안으로 타입 단언문 감추기

</br>

cacheLast는 함수를 캐싱하는 래퍼함수입니다.
cacheLast내부의 return 되고 있는 함수와 cacheLast함수의 T타입 사이의 관련을 파악하지 못해 타입에러가 발생했습니다.

```ts
declare function shallowEqual(a: any, b: any): boolean;

function cacheLast<T extends Function>(fn: T): T {
	let lastArgs: any[] | null = null;
	let lastResult: any;
	return function (...args: any[]) {
		// ~~~~~~~~~~~~~~~~~~~~~~~~~~
		//          Type '(...args: any[]) => any' is not assignable to type 'T'
		if (!lastArgs || !shallowEqual(lastArgs, args)) {
			lastResult = fn(...args);
			lastArgs = args;
		}
		return lastResult;
	};
}
```

함수 내부에 `as unknown as T` 타입단언문을 추가해주면 오류를 제거할 수 있습니다. cacheLast함수 내부에서 any를 많이 사용했지만 cacheLast함수를 호출하는 부분에서는 any사용을 알지 못합니다.

```ts
declare function shallowEqual(a: any, b: any): boolean;
function cacheLast<T extends Function>(fn: T): T {
	let lastArgs: any[] | null = null;
	let lastResult: any;
	return function (...args: any[]) {
		if (!lastArgs || !shallowEqual(lastArgs, args)) {
			lastResult = fn(...args);
			lastArgs = args;
		}
		return lastResult;
	} as unknown as T; // 타입 단언문
}
```

## 요약

- 타입 단언문을 불가피하게 사용해야 한다면, 정확한 정의를 가지는 함수 안으로 숨기도록 합시다.
