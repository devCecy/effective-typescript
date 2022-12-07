# any를 구체적으로 변형해서 사용하기

any타입에는 모든 숫자, 문자열, 배열, 객체, 정규식, 함수, 클래스, DOM 엘리먼트는 물론 null과 undefined까지도 아우르는 매우 큰 범위의 타입입니다.
그러므로 일반적인 상황에서는 any보다 더 구체적인 타입이 존재할 가능성이 높습니다.

</br>

매개변수 타입에 any를 사용한 getLengthBad보다 any[]를 사용한 getLength가 더 좋은 함수입니다.

그 이유는,

- 함수 내의 array.length타입이 체크 됩니다.
- 함수의 반환타입이 any대신 number로 추론됩니다.
- 함수가 호출될 때 매개변수가 배열인지 체크됩니다.

```ts
function getLengthBad(array: any) {
	// Don't do this!
	return array.length;
}

function getLength(array: any[]) {
	return array.length;
}
```

만약, 함수의 매개변수가 객체이긴 하지만 값을 알수 없다면 `{[key: string]: any}`를 사용하여 다음과 같이 선언하면 됩니다.

```ts
function hasTwelveLetterKey(o: { [key: string]: any }) {
	for (const key in o) {
		if (key.length === 12) {
			return true;
		}
	}
	return false;
}
```

비슷한 방법으로 `object`타입을 사용할 수 있지만 <u>속성에 접근할 수 없다</u>는 점에서 `{[key: string]: any}`와 살짝 다릅니다.

```ts
function hasTwelveLetterKey(o: object) {
	for (const key in o) {
		if (key.length === 12) {
			console.log(key, o[key]);
			//  ~~~~~~ Element implicitly has an 'any' type
			//         because type '{}' has no index signature
			return true;
		}
	}
	return false;
}
```

함수의 타입에도 구체적으로 any를 사용해주는 것이 좋습니다.

```ts
type Fn0 = () => any; // 매개변수 없이 호출 가능한 모든 함수
type Fn1 = (arg: any) => any; // 매개변수 1개
type FnN = (...args: any[]) => any; // 모든 개수의 매개변수, Function타입과 동일합니다.
```

나머지 매개변수의 타입에 any를 사용하면 any를 반환하지만 any[]를 사용하면 더 구체적인 타입으로 반환됩니다.

```ts
const numArgsBad = (...args: any) => args.length; // any를 반환
const numArgsGood = (...args: any[]) => args.length; // number를 반환
```

## 요약

- any를 사용할 때는 정말로 모든 값이 허용되어야 하는지 면밀히 검토해야 합니다.
- any보다는 더 정확하게 모델링할 수 있도록 any[], {[id:string]: any} 또는 ()=>any 처럼 구체적인 형태를 사용해야 합니다.
