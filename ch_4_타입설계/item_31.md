# 타입 주변에 null 값 배치하기

</br>

최솟값과 최댓값을 계산하는 함수를 작성해 보겠습니다.

`strictNullChecks`이 false라면 다음 코드는 타입체커를 통과하고 반환 타입은 number[]로 추론될 것입니다. 하지만 설계적 결함이 있습니다.

- 최솟값 혹은 최댓값이 0인 경우 값이 덧씌워져 버립니다. 예를들어 extent([0,1,2])의 반환값은 [0,2]가 아니라 [1,2]입니다.
- nums 배열이 비어있다면 [undefined, undefined]를 반환합니다.

```ts
// tsConfig: {"strictNullChecks":false}

function extent(nums: number[]) {
	let min, max;
	for (const num of nums) {
		if (!min) {
			min = num;
			max = num;
		} else {
			min = Math.min(min, num);
			max = Math.max(max, num);
		}
	}
	return [min, max];
}
```

`strictNullChecks`을 true로 설정하면 문제점을 확인할 수 있습니다.
반환타입이 (number|undefined)[]로 추론되어서 extent함수를 호출하는곳마다 타입 오류가 발생합니다.

```ts
function extent(nums: number[]) {
	let min, max;
	for (const num of nums) {
		if (!min) {
			min = num;
			max = num;
		} else {
			min = Math.min(min, num);
			max = Math.max(max, num);
			// ~~~ Argument of type 'number | undefined' is not
			//     assignable to parameter of type 'number'
		}
	}
	return [min, max];
}

const [min, max] = extent([0, 1, 2]);
const span = max - min;
// ~~~   ~~~ Object is possibly 'undefined'
```

해결 방법을 찾아봅시다. min과 max를 한 객체에 넣고 null이거나 null이 아니게 만들어 줍니다.
반환타입이 [number, number] | null로 사용되었습니다. 이때 null이 아님 단언(!)을 사용하면 min과 max값을 얻을 수 있습니다.

```ts
function extent(nums: number[]) {
	let result: [number, number] | null = null;
	for (const num of nums) {
		if (!result) {
			result = [num, num];
		} else {
			result = [Math.min(num, result[0]), Math.max(num, result[1])];
		}
	}
	return result;
}

const [min, max] = extent([0, 1, 2])!; // null이 아님 단언 사용
const span = max - min; // OK
```

</br>

## 요약

- strictNullChecks를 설정하면 코드에 많은 오류가 표시되겠지만, null값과 관련된 문제점을 찾아낼 수 있기 때문에 반드시 필요합니다.
