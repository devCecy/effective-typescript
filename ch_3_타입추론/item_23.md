# 한꺼번에 객체 생성하기

객체를 생성할 때는 속성을 하나씩 추가하기보다는 여러 속성을 포함해서 함꺼번에 생성해야 타입 추론에 유리합니다.

</br>

변수 pt를 먼저 빈객체로 선언하고 객체안에 x와, y를 사용해 새로운 값을 선언하면 오류가 발생합니다. 왜냐하면 const pt = {} 를 기준으로 타입이 추론되기 때문에 {}에는 x와 y가 없어 오류가 발생합니다.

```ts
const pt = {};
pt.x = 3;
// ~ Property 'x' does not exist on type '{}'
pt.y = 4;
// ~ Property 'y' does not exist on type '{}'
```

</br>

이 오류는 객체를 한꺼번에 정의히면 해결됩니다.

```ts
const pt = {
	x: 3,
	y: 4,
}; // OK
```

</br>

객체를 반드시 나눠서 만들어야 한다면, `타입 단언문(as)`를 사용해 타입체커를 통과하게 만들 수 있습니다.

```ts
interface Point {
	x: number;
	y: number;
}
const pt = {} as Point;
pt.x = 3;
pt.y = 4; // OK
```

하지만, 이 경우에도 객체를 한번에 생성하는 것이 좋습니다.

```ts
interface Point {
	x: number;
	y: number;
}
const pt: Point = {
	// 객체 리터럴 -> 잉여속성체크도 해준다.
	x: 3,
	y: 4,
};
```

</br>

작은 객체들을 조합해서 큰 객체를 만들어야 한다면 `객체 전개 연산자 (...)`를 사용하면 좋습니다. 객체 전개 연산자를 사용하면 타입 걱정없이 필드 단위로 객체를 생성할 수 있습니다.

```ts
interface Point {
	x: number;
	y: number;
}
const pt = { x: 3, y: 4 };
const id = { name: "Pythagoras" };
const namedPoint = { ...pt, ...id };
namedPoint.name; // OK, type is string
```

</br>

`조건부 속성`을 추가하려면, 속성을 추가하지 않는 null 또는 {}로 객체 전개를 사용하면 됩니다.

```ts
declare let hasMiddle: boolean;
const firstLast = { first: "Harry", last: "Truman" };
const president = { ...firstLast, ...(hasMiddle ? { middle: "S" } : {}) };
```

president의 추론된 타입을 보면 middle이 선택적 속성을 가진 타입으로 추론됨을 할 수 있습니다.

```ts
const president: {
	middle?: string;
	first: string;
	last: string;
};
```

## 요약

- 객체를 생성할 때는 속성을 제각각 추가하지 말고 한꺼번에 객체로 만들어야 합니다.
- 안전한 타입으로 속성을 추가하려면 객체 전개(...)를 사용해야 합니다.
- 객체를 조건부 속성으로 추가하는 방법도 사용할 수 있습니다.
