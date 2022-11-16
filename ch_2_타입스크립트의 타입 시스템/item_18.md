# 매핑된 타입을 사용하여 값을 동기화하기

매핑된 타입은 한 객체가 또 다른 객체와 정확히 같은 속성을 가지게 할 때 유용하게 사용할 수 있습니다.

</br>

데이터와 디스플레이가 업데이트되면 차트를 다시 그리고 이벤트가 업데이트되면 차트를 다시 그리지 않도록 함수를 구현해야 한다고 생각해 봅시다.

```ts
interface ScatterProps {
	// 데이터
	xs: number[];
	ys: number[];

	// 디스플레이
	xRange: [number, number];
	yRange: [number, number];
	color: string;

	// 이벤트
	onClick: (x: number, y: number, index: number) => void;
}
```

</br>

1. 첫번째 방법 - 보수적 접근법, 실패에 닫힌 접근법

새로운 속성이 추가되면 shouldUpdate함수는 차트를 다시 그리게 됩니다. 차트는 정확하지만 너무 자주 그려지게 될 수 있습니다.

```ts
function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
	let k: keyof ScatterProps;
	for (k in oldProps) {
		if (oldProps[k] !== newProps[k]) {
			if (k !== "onClick") return true;
		}
	}
	return false;
}
```

</br>

2. 두번째 방법 - 실패에 열린 접근법

차트를 불필요하게 다시 그리는 단점은 해결했습니다. 그러나 타입에 새로운 속성이 추가되면 shouldUpdate 또한 업데이트 해줘야하는데 이 단계가 누락되면 차트를 다시 그려야하는 경우를 놓치게 됩니다.

```ts
function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
	return (
		oldProps.xs !== newProps.xs ||
		oldProps.ys !== newProps.ys ||
		oldProps.xRange !== newProps.xRange ||
		oldProps.yRange !== newProps.yRange ||
		oldProps.color !== newProps.color
		// (no check for onClick)
	);
}
```

</br>

3. 세번째 방법 - 매핑된 타입과 객체를 이용한 방법

다음은 타입체커가 동작하도록 개선한 코드입니다.
`[k in keyof ScatterProps]`는 타입체커에게 `REQUIRES_UPDATE`가 `ScatterProps`와 동일한 속성을 가져야 한다는 정보를 제공합니다.

```ts
const REQUIRES_UPDATE: { [k in keyof ScatterProps]: boolean } = {
	xs: true,
	ys: true,
	xRange: true,
	yRange: true,
	color: true,
	onClick: false,
};

function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
	let k: keyof ScatterProps;
	for (k in oldProps) {
		if (oldProps[k] !== newProps[k] && REQUIRES_UPDATE[k]) {
			return true;
		}
	}
	return false;
}
```

그러므로 ScatterProps에 새로운 속성을 추가하면 REQUIRES_UPDATE는 오류를 냅니다. 속성을 삭제하거나 속성의 이름을 변경해도 오류를 잡아냅니다.

```ts
interface ScatterProps {
	// ...
	onDoubleClick: () => void;
}
const REQUIRES_UPDATE: { [k in keyof ScatterProps]: boolean } = {
	//  ~~~~~~~~~~~~~~~ Property 'onDoubleClick' is missing in type
	// COMPRESS
	xs: true,
	ys: true,
	xRange: true,
	yRange: true,
	color: true,
	onClick: false,
	// END
};
```

</br>

## 요약

- 매핑된 타입을 사용해서 관련된 값과 타입을 동기화하도록 합니다.
- 인터페이스에 새로운 속성을 추가할 때, 선택을 강제하도록 매핑도니 타입을 고려해야 합니다.
