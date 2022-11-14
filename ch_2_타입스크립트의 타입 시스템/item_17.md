# 변경 관련된 오류를 방지를 위해 readonly 사용하기

</br>

아래코드를 `printTriangles(5)`로 실행하면 0,1,3,6,10 이 나오길 기대했지만 0,1,2,3,4가 출력됩니다. 이 함수는 배열안의 숫자들을 모두 합칩니다. 그런데 계산이 끝나면 원래 배열이 전부 비게 됩니다. 자바스크립트 배열은 내용을 변경할 수 있기 떄문에, 타입스크립트에서도 역시 오류 없이 통과하게 됩니다.

```ts
function arraySum(arr: number[]) {
	let sum = 0,
		num;
	while ((num = arr.pop()) !== undefined) {
		sum += num;
	}
	return sum;
}
function printTriangles(n: number) {
	const nums = [];
	for (let i = 0; i < n; i++) {
		nums.push(i);
		console.log(arraySum(nums));
	}
}
```

오류의 범위를 좁히기 위해 arraySum이 배열을 변경하지 않는다는 선언을 해보겠습니다. readonly 접근 제어자를 사용하면 됩니다. 그러자 arr배열에 pop메서드를 사용할 수 없다는 오류가 발생하게 됩니다.

```ts
function arraySum(arr: readonly number[]) {
	let sum = 0,
		num;
	while ((num = arr.pop()) !== undefined) {
		// ~~~ 'pop' does not exist on type 'readonly number[]'
		sum += num;
	}
	return sum;
}
```

## readonly의 특징

- 배열의 요소를 읽을 수 있지만, 쓸 수는 없습니다.
- length를 읽을 수 있지만, 바꿀 수는 없습니다. (배열 변경x)
- 배열을 변경하는 pop을 비롯한 다른 메서드를 호출 할 수 없습니다.

- 변경가능한 배열을 readonly 배열에 할당 할 수 있지만, readonly배열을 변경가능한 배열에 할당 할 수 없습니다.

```ts
const a: number[] = [1, 2, 3];
const b: readonly number[] = a;
const c: number[] = b;
// ~ Type 'readonly number[]' is 'readonly' and cannot be
//   assigned to the mutable type 'number[]'
```

## 요약

- 만약 함수가 매개변수를 수정하지 않는다면 readonly로 선언하는 것이 좋습니다. readonly 매개변수는 인터페이스를 명확하게 하며, 매개변수가 변경되는 것을 방지합니다.
- readonly를 사용하면 변경하면서 발생하는 오류를 방지할 수 있고, 변경이 발생하는 코드도 쉽게 찾을 수 있습니다.
- const와 readonly의 차이를 이해해야합니다.
- readonly는 얕게 동작한다는 것을 명심해야합니다.
