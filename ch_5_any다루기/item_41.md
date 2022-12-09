# any의 진화를 이해하기

타입은 일반적으로 변수를 선언할 때 결정이 됩니다. 그 후에 null체크 등으로 정제될 수 있지만 새로운 값이 추가되도록 확장할 수는 없습니다.
그러나 any타입은 타입이 확장될 수 있습니다.

</br>

다음코드는 일정 범위의 숫자들을 생성하는 함수입니다.
변수 out은 변수 선언시 any[]로 타입이 초기화되었지만 리턴되는 out의 타입은 number[]입니다. 배열에 number값을 넣어주자 타입이 확장되었습니다.

```ts
function range(start: number, limit: number) {
	const out = []; // Type is any[]
	for (let i = start; i < limit; i++) {
		out.push(i); // Type of out is any[]
	}
	return out; // Type is number[]
}
```

배열에 다양한 타입의 요소를 넣으면 그에 맞게 배열의 타입이 확장됩니다.

```ts
const result = []; // Type is any[]
result.push("a");
result; // Type is string[]
result.push(1);
result; // Type is (string | number)[]
```

조건문의 분기에 따라 타입이 변하기도 합니다.

```ts
let val; // Type is any
if (Math.random() < 0.5) {
	val = /hello/;
	val; // Type is RegExp
} else {
	val = 12;
	val; // Type is number
}
val; // Type is number | RegExp
```

any타입의 진화는 변수의 타입이 암시적인 경우에만 일어납니다. 명시적으로 any를 선언하면 타입이 그대로 유지됩니다.

```ts
let val: any; // Type is any
if (Math.random() < 0.5) {
	val = /hello/;
	val; // Type is any
} else {
	val = 12;
	val; // Type is any
}
val; // Type is any
```

## 요약

- 일반적인 타입들은 정제되기만 하는 반면, 암시적 any와 any[]타입은 진화할 수 있습니다.
- any를 진화시키는 방식보다 명시적 타입 구문을 사용하는 것이 안전한 타입을 유지하는 방법입니다.
