# 일관성 있는 별칭 사용하기

별칭을 남발해서 사용하면 제어 흐름을 분석하기 어렵습니다. 타입스크립트에서도 마찬가지 이므로 별칭을 신중하게 사용해야 합니다.

</br>

어떤 값에 새 이름을 할당해 보겠습니다.
borough.location배열에 loc라는 별칭(alias)을 할당해 주었습니다. <u>별칭의 값을 변경하면 원래 속성값에서도 변경</u>됨을 주의해야합니다.

```ts
const borough = { name: "Brooklyn", location: [40.688, -73.979] };
const loc = borough.location;

loc[0] = 0;
borough.location; // [0, -73.979]
```

</br>

다각형을 표현하는 자료구조를 가정해 봅시다.
아래코드는 잘 동작하지만 polygon.bbox부분이 5번이나 반복됩니다.

```ts
interface Coordinate {
	x: number;
	y: number;
}

interface BoundingBox {
	x: [number, number];
	y: [number, number];
}

interface Polygon {
	exterior: Coordinate[];
	holes: Coordinate[][];
	bbox?: BoundingBox;
}

function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
	if (polygon.bbox) {
		if (
			pt.x < polygon.bbox.x[0] ||
			pt.x > polygon.bbox.x[1] ||
			pt.y < polygon.bbox.y[1] ||
			pt.y > polygon.bbox.y[1]
		) {
			return false;
		}
	}

	// ... more complex check
}
```

코드의 중복을 줄이기 위해서 임시변수를 선언해 봅시다.
polygon.bbox를 box라는 변수에 담아 사용했습니다. 이 코드는 동작하지만 편집기에서는 오류로 표시됩니다. 별칭의 사용으로 제어 흐름 분석이 어려워졌기 때문입니다.

```ts
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
	const box = polygon.bbox;
	if (polygon.bbox) {
		if (
			pt.x < box.x[0] ||
			pt.x > box.x[1] ||
			//     ~~~                ~~~  Object is possibly 'undefined'
			pt.y < box.y[1] ||
			pt.y > box.y[1]
		) {
			//     ~~~                ~~~  Object is possibly 'undefined'
			return false;
		}
	}
	// ...
}
```

어떤 흐름을 코드가 동작했는지 살펴봅시다. polygon.bbox는 타입이 BoundingBox로 타입이 정제되었지만 box는 그렇지 않았기 때문에 오류가 발생했었다는걸 알 수 있습니다.

```ts
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
	polygon.bbox; // Type is BoundingBox | undefined
	const box = polygon.bbox;
	box; // Type is BoundingBox | undefined
	if (polygon.bbox) {
		polygon.bbox; // Type is BoundingBox
		box; // Type is BoundingBox | undefined
	}
}
```

이러한 문제는 `객체 비구조화`를 사용해서 해결할 수 있습니다. 객체 비구조화를 사용하면 일관된 이름을 사용할 수 있습니다.

```ts
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
	const { bbox } = polygon;
	if (bbox) {
		const { x, y } = bbox;
		if (pt.x < x[0] || pt.x > x[1] || pt.y < x[0] || pt.y > y[1]) {
			return false;
		}
	}
	// ...
}
```

그러나 객체 비구조화를 사용할 때는 두가지를 주의해야합니다.

1. 전체 bbox 속성이 아니라 x와 y가 선택적 속성일 경우에 속성 체크가 더 필요합니다. 따라서 타입의 경계에 null 값을 추가하는 것이 좋습니다.
2. bbox에는 선택적 속성이 적합했지만 holes는 그렇지 않습니다. holes가 선택적이라면, 값이 없거나 빈배열이었을 것입니다. 차이가 없는데 이름을 구별한 것입니다. 빈 배열은 holes없음을 나타내는 좋은 방법입니다.

</br>

## 요약

- 별칭은 타입스크립트가 타입을 좁히는 것을 방해합니다. 따라서 변수에 별칭을 사용할 때는 일관되게 사용해야 합니다.
- 비구조화 문법을 사용해서 일관된 이름을 사용하는 것이 좋습니다.
- 함수 호출이 객체 속성의 타입 정제를 무효화할 수 있다는 점을 주의해야 합니다. 속성보다 지역변수를 사용하면 타입 정제를 믿을 수 있습니다. (?)
