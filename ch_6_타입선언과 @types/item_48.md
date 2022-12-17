# API 주석에 TSDoc 사용하기

- 함수에 주석을 사용하려면 JSDoc 스타일로 주석을 만듭시다. 그러면 주석을 툴팁에 표시해 줍니다.

```ts
// 일반주석
/** JSDoc */
```

- @params와 @returns를 추가하면 툴팁에 각 설명을 보여줍니다.

```ts
/**
 * Generate a greeting.
 * @param name Name of the person to greet
 * @param salutation The person's title
 * @returns A greeting formatted for human consumption.
 */
function greetFullTSDoc(name: string, title: string) {
	return `Hello ${title} ${name}`;
}
```

- TSDoc을 이용하면 각 타입마다 설명을 툴팁으로 보여줍니다.

```ts
/** A measurement performed at a time and place. */
interface Measurement {
	/** Where was the measurement made? */
	position: Vector3D;
	/** When was the measurement made? In seconds since epoch. */
	time: number;
	/** Observed momentum */
	momentum: Vector3D;
}
```

- TSDoc은 마크다운 형식으로 꾸며지므로 굵은 글씨, 기울림, 글머리기호 목록을 사용할 수 있습니다.

```ts
/**
 * This _interface_ has **three** properties:
 * 1. x
 * 2. y
 * 3. z
 */
interface Vector3D {
	x: number;
	y: number;
	z: number;
}
```

## 요약

- export된 함수, 클래스, 타입에 주석을 달 때는 JSDoc/TSDoc 형태를 사용합시다. JSDoc/TSDoc 형태의 주석을 달면 편집기가 주석 정보를 표시해 줍니다.
- @params, @returns 구문과 문서 서식을 위해 마크다운을 사용할 수 있습니다.
- 주석에 타입정보를 포함하면 안 됩니다.
