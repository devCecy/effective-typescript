# 동적 데이터에 인덱스 시그니처 사용하기 (index-for-dynamic)

</br>

타입스크립트에서는 인덱스 시그니처를 사용하여 유연하게 매핑을 표현할 수 있습니다.

```ts
type Rocket = { [property: string]: string };

const rocket: Rocket = {
	name: "Falcon 9",
	variant: "v1.0",
	thrust: "4,940 kN",
}; // OK
```

</br>

`[property: string]: string` 이 부분이 인덱스 시그니처 이며, 다음의 의미를 담고 있습니다.

- `키의 이름` : 키의 이름은 property는 키의 위치만 표시하는 용도 입니다. 타입체커에서는 사용하지 않기 때문에 무시할 수 있는 참고 정보라고 생각해도 됩니다.
- `키의 타입` : `string`, `number`, `symbol` 중 하나를 사용할 수 있으며 보통 string을 사용합니다.
- `값의 타입` : 어떤 것이든 될 수 있습니다.

</br>

## 인덱스 시그니처 사용시 단점

인덱스 시그니처를 사용하면 다음과 같은 단점이 존재합니다.

- 잘못된 키를 포함해 모든 키를 허용합니다. name 대신 Name으로 작성해도 유효합니다.
- 특정 키가 필요하지 않습니다. {}도 유효합니다.
- 키마다 다른 타입을 가질 수 없습니다.
- 키의 이름으로 무엇이든 가능하기 떄문에 자동완성 기능이 동작하지 않습니다.

</br>

예를들어, thrust_kN의 타입이 number여야 한다면 인덱스 시그니처 대신 interface 혹은 type을 사용해주면 됩니다. 그럼 위의 단점을 모두 바로 잡아줍니다.

```ts
interface Rocket {
	name: string;
	variant: string;
	thrust_kN: number;
}
const falconHeavy: Rocket = {
	name: "Falcon Heavy",
	variant: "v1",
	thrust_kN: 15_200,
};
```

</br>

## 그럼 인덱스 시그니처는 언제 사용할까요?

인덱스 시그니처는 `동적 데이터`를 표현할 때 사용합니다.

예를들어, CSV파일처럼 행과 열의 이름을 가지고 있는데 그 이름으로 무엇이 들어올지 모르는 상황이라면 인덱스 시그니처를 사용하면됩니다.

```ts
function parseCSV(input: string): { [columnName: string]: string }[] {
	const lines = input.split("\n");
	const [header, ...rows] = lines;
	return rows.map((rowStr) => {
		const row: { [columnName: string]: string } = {};
		rowStr.split(",").forEach((cell, i) => {
			row[header[i]] = cell;
		});
		return row;
	});
}
```

</br>

## 인덱스 시그니처를 사용하면 안되는 경우

어떤 타입에 가능한 필드가 제한되어 있는 경우에는 인덱스 시그니처를 사용하지 말아야합니다.

예를들어, a,b,c,d 와 같은 키가 있음을 알고 있지만, 얼마나 많이 있는지 모른다면 `선택적 필드` 혹은 `유니온 타입`으로 모델링하면 됩니다.

```ts
// 인덱스 시그니처 -> 너무 광범위함
interface Row1 {
	[column: string]: number;
}

// 선택적 필드 -> 최선
interface Row2 {
	a: number;
	b?: number;
	c?: number;
	d?: number;
}

// 유니온 타입 -> 가장 정확하지만 사용하기 번거로움
type Row3 =
	| { a: number }
	| { a: number; b: number }
	| { a: number; b: number; c: number }
	| { a: number; b: number; c: number; d: number };
```

</br>

string 타입이 너무 광범위해서 인덱스 시그니처를 사용하는데 문제가 있다면 다음과 같은 두가지 대안을 생각해 볼 수 있습니다.

1. `Record` 사용
   Record는 키 타입에 유연성을 제공하는 제너릭 타입입니다. 특히, string의 부분 집합을 사용할 수 있습니다.

```ts
type Vec3D = Record<"x" | "y" | "z", number>;
// Type Vec3D = {
//   x: number;
//   y: number;
//   z: number;
// }
```

2.  `매핑된 타입` 사용
    매핑된 타입은 키마다 별도 타입을 사용하게 해줍니다. `k extends "b" ? string : number`를 통해 키 b의 값의 타입은 string이 되도록 조건을 줄 수 있습니다.

```ts
type Vec3D = { [k in "x" | "y" | "z"]: number };
// Same as above
type ABC = { [k in "a" | "b" | "c"]: k extends "b" ? string : number };
// Type ABC = {
// a: number;
// b: string;
// c: number;
// }
```

## 요약

- 런타임에 객체의 속성을 알 수 없을 경우에만 인덱스 시그니처를 사용하도록 합니다.
- 가능하다면 인터페이스, Record, 매핑된 타입 같은 인덱스 시그니처 보다 정확한 타입을 사용하는 것이 좋습니다.
