# number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기 (number-index)

자바스크립트는 숫자를 객체의 키로 사용할 수 없지만, 타입스크립트는 숫자 키를 허용하고 문자열 키와 다른 것으로 인식합니다.

</br>

### 1) Array

Array에 대한 타입을 lin.es.5에서 확인해 보면 아래와 같습니다.

```ts
interface Arrat<T> {
	// ...
	[n: number]: T;
}
```

런타임에는 ECMAScript 표준대로 문자열을 키로 인식하므로 타입스크립트의 숫자 키를 가진 코드는 가상이라고 할 수 있지만, 타입체크 시점에 오류를 잡을 수 있으므로 유용합니다.

```ts
const xs = [1, 2, 3];
const x0 = xs[0]; // OK
const x1 = xs["1"];
// ~~~ Element implicitly has an 'any' type
//      because index expression is not of type 'number'

function get<T>(array: T[], k: string): T {
	return array[k];
	// ~ Element implicitly has an 'any' type
	//   because index expression is not of type 'number'
}
```

</br>

### 2) ArrayLike

어떤 길이를 가지는 배열과 비슷한 형태의 튜플을 사용하고 싶다면 ArrayLike타입을 사용합니다.

```ts
const xs = [1, 2, 3];
function checkedAccess<T>(xs: ArrayLike<T>, i: number): T {
	if (i < xs.length) {
		return xs[i];
	}
	throw new Error(`Attempt to access ${i} which is past end of array.`);
}
```

아래 예제는 길이와 숫자 인덱스 시그니처만 있습니다. 이런 경우는 드물지만 필요하다면 ArrayLike를 사용하면 됩니다. ArrayLike를 사용해도 키는 여전히 문자열이라는 점을 잊지말아야 합니다.

```ts
const xs = [1, 2, 3];
const tupleLike: ArrayLike<string> = {
	"0": "A",
	"1": "B",
	length: 2,
}; // OK
```

## 요약

- 배열은 객체이므로 키는 숫자가 아니라 문자열입니다. 인덱스 시그니처로 사용된 number 타입은 버그를 잡기 위한 순수 타입스크립트 코드입니다.
- 인덱스 시그니처에 number를 사용하기보다 Array, 튜플, ArrayLike타입을 사용하는 것이 좋습니다.
