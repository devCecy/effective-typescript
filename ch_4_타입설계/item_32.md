# 유니온 인터페이스보다는 인터페이스의 유니온을 사용하기

유니온 타입의 속성을 가지는 인터페이스를 작성 중이라면, 혹시 <u>인터페이스의 유니온 타입</u>을 사용하는 게 더 알맞지는 않을지 검토해 봐야 합니다.

</br>

벡터를 그리는 프로그램을 작성 중이고, 특정한 기하학적 타입을 가지는 계층의 인터페이스를 정의한다고 가정해 보겠습니다.
layout이 LineLayout이면서, paint속성이 FillPaint인 경우는 말이 되지 않습니다.

```ts
// 유니온 타입을 가지는 인터페이스
interface Layer {
	layout: FillLayout | LineLayout | PointLayout;
	paint: FillPaint | LinePaint | PointPaint;
}
```

더 나은 방법으로 모델링하려면 각각 타입의 계층을 분리된 인터페이스로 만들어야 합니다. 이런 형태로 Layer를 정의하면 layout과 paint속성이 잘못된 조합으로 사용되는 경우를 방지할 수 있습니다.

```ts
interface FillLayer {
	layout: FillLayout;
	paint: FillPaint;
}
interface LineLayer {
	layout: LineLayout;
	paint: LinePaint;
}
interface PointLayer {
	layout: PointLayout;
	paint: PointPaint;
}

// 인터페이스의 유니온타입
type Layer = FillLayer | LineLayer | PointLayer;
```

`태그된 유니온`을 사용하여 Layer의 타입 범위를 좁힐 수도 있습니다.

```ts
interface FillLayer {
	type: "fill";
	layout: FillLayout;
	paint: FillPaint;
}
interface LineLayer {
	type: "line";
	layout: LineLayout;
	paint: LinePaint;
}
interface PointLayer {
	type: "paint";
	layout: PointLayout;
	paint: PointPaint;
}
type Layer = FillLayer | LineLayer | PointLayer;

function drawLayer(layer: Layer) {
	if (layer.type === "fill") {
		const { paint } = layer; // Type is FillPaint
		const { layout } = layer; // Type is FillLayout
	} else if (layer.type === "line") {
		const { paint } = layer; // Type is LinePaint
		const { layout } = layer; // Type is LineLayout
	} else {
		const { paint } = layer; // Type is PointPaint
		const { layout } = layer; // Type is PointLayout
	}
}
```

</br>

여러개의 선택적 필드가 동시에 값이 있거나 undefined인 경우도 태그된 유니온 패턴이 잘 맞습니다.
다음 코드는 placeOfBirth와 dateOfBirth속성이 둘다 있거나 둘다 없어야 한다고 주석을 남겨 놓았습니다. 주석이 타입정보를 가지고 있으면 문제가 될 수 있습니다.

```ts
interface Person {
	name: string;
	// These will either both be present or not be present
	placeOfBirth?: string;
	dateOfBirth?: Date;
}
```

이럴때는 두속성을 하나의 객체로 모으는것이 더 나은 설계가 됩니다.

```ts
interface Person {
	name: string;
	birth?: {
		place: string;
		date: Date;
	};
}
```

이제 place만 있고 date가 없는 경우에는 오류가 발생합니다.

```ts
const alanT: Person = {
	name: "Alan Turing",
	birth: {
		// ~~~~ Property 'date' is missing in type
		//      '{ place: string; }' but required in type
		//      '{ place: string; date: Date; }'
		place: "London",
	},
};
```

Person객체를 매개변수로 받는 함수는 birth 하나만 체크하면 됩니다.

```ts
function eulogize(p: Person) {
	console.log(p.name);
	const { birth } = p;
	if (birth) {
		console.log(`was born on ${birth.date} in ${birth.place}.`);
	}
}
```

## 요약

- 유니온 타입의 속성을 여러 개 가지는 인터페이스에서는 속성 간의 관계가 분명하지 않기 때문에 실수가 자주 발생하므로 주의해야 합니다.
- 유니온의 인터페이스보다 인터페이스의 유니온이 더 정확하고 타입스크립트가 이해하기도 좋습니다.
- 타입스크립트가 제어 흐름을 분석할 수 있도록 타입에 태그를 넣는 것을 고려해야 합니다.
