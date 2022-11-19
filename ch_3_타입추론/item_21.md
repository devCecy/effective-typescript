# 타입 넓히기 (widening)

런타임에 모든 변수는 유일한 값을 가집니다.
그러나 타입스크립트가 작성된 코드를 체크하는 정적분석 시점에 변수는 가능한 값들의 집합인 타입을 가집니다. 상수를 사용해서 변수를 초기화할 때 타입을 명시하지 않으면 타입체커는 타입을 결정해야 합니다. 이 말은 <u>지정된 단일 값을 가지고 할당 가능한 값들의 집합을 유추</u>해야 한다는 뜻입니다. 타입스크립트의 이러한 과정을 `넓히기`라고 부릅니다.

</br>

다음 코드는 런타임에는 오류없이 실행되지만, 편집기에서는 오류가 표시됩니다.
getComponent함수는 두번째 매개변수로 "x" | "y" | "z" 타입을 기대했지만, `let x = "x"`의 타입은 할당 시점에 넓히기가 동작해서 `string으로 추론`되기 때문입니다.

다시말해, 변수 x는 <u>할당가능한 선언 방식인 let으로 선언</u>했기 때문에 추후 다른 string으로 재 할당될 가능성이 있습니다. 그리고 string 타입은 "x" | "y" | "z"에 할당이 불가능하므로 오류가 발생하는 것입니다.

```ts
interface Vector3 {
	x: number;
	y: number;
	z: number;
}
function getComponent(vector: Vector3, axis: "x" | "y" | "z") {
	return vector[axis];
}

let x = "x";
let vec = { x: 10, y: 20, z: 30 };

getComponent(vec, x);
// ~ Argument of type 'string' is not assignable to
//   parameter of type '"x" | "y" | "z"'
```

</br>

타입 넓히기가 진행 될 때 주어진 값으로 추론 가능한 타입이 여러개이기 떄문에 어떤 타입으로 추론될지 상당히 모호합니다.

mixed의 타입으로 추론될 수 있는 후보는 다음과 같이 상당히 많습니다. 그러므로 타입스크립트가 아무리 영리하더라도 항상 옳은 추론을 할 수는 없습니다.

```ts
const mixed = ["x", 1];
```

- ('x' | 1 )[]
- ['x', 1]
- [string, number]
- (string | number)[]
- readonly [string, number]
- readonly (string, number)[]
- [any, any]
- any[]

</br>

타입스크립트의 넓히기를 제어할 수 있는 방법을 알아봅시다.

## const 사용하기

위에서 봤던 코드에서 let을 const로 바꿔 사용하면 오류가 해결됩니다. const는 재할당이 불가능하기때문에 변수x의 값이 'x'라는 사실을 정확하기 알 수 있기 때문입니다.

```ts
const x = "x"; // type is "x"
let vec = { x: 10, y: 20, z: 30 };
getComponent(vec, x); // OK
```

</br>

그러나 const가 만능은 아닙니다. mixed 예제는 배열을 사용했는데 이를 튜플타입으로 추론할지, 요소는 어떤 타입으로 추론해야할지 알기 어렵습니다.

타입추론의 강도를 직접 제어하려면 타입스크립트의 기본 동작을 재정의 해야합니다.
`타입스크립트의 기본 동작을 재 정의 하는 세가지 방법`을 알아봅시다.

</br>

## 명시적 타입 구문 제공

변수 x에 명시적으로 타입 x:1|3|5 를 선언해주었습니다.

```ts
const v: { x: 1 | 3 | 5 } = {
	x: 1,
}; // Type is { x: 1 | 3 | 5; }
```

</br>

## 타입 체커에 추가적인 문맥 제공

예를들어, 함수의 매개변수로 값을 전달하는 방법이 있습니다. 이는 아이템 26에서 자세히 알아봅니다.

</br>

## const 단언문 사용

여기서 const는 변수 const가 아닙니다.
값 뒤에 as const를 사용하면, 타입스크립트는 최대한 좁은 타입으로 추론합니다.

```ts
const v1 = {
	x: 1,
	y: 2,
}; // Type is { x: number; y: number; }

const v2 = {
	x: 1 as const,
	y: 2,
}; // Type is { x: 1; y: number; }

const v3 = {
	x: 1,
	y: 2,
} as const; // Type is { readonly x: 1; readonly y: 2; }
```

배열을 튜플타입으로 추론때에도 as const를 사용할 수 있습니다.

```ts
const a1 = [1, 2, 3]; // Type is number[]
const a2 = [1, 2, 3] as const; // Type is readonly [1, 2, 3]
```

</br>

## 요약

- 타입스크립트가 넓히기를 통해 상수의 타입을 추론하는 법을 이해해야 합니다.
- 동작에 영향을 줄 수 있는 방법인 const, 타입구문, 문맥, as const에 익숙해져야 합니다.
