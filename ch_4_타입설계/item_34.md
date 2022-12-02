# 부정확한 타입보다는 미완성 타입을 사용하기

일반적으로 타입이 구체적일수록 버그를 더 많이 잡고 타입스크립트가 제공하는 도구를 활용할 수 있게 됩니다.
그러나 잘못된 타입은 차라리 타입이 없는 것 보다 못할 수 있습니다.

</br>

다음 타입선언들은 크게 문제는 없지만 coordinates의 타입 number[]가 약간 추상적입니다.

```ts
interface Point {
	type: "Point";
	coordinates: number[];
}
interface LineString {
	type: "LineString";
	coordinates: number[][];
}
interface Polygon {
	type: "Polygon";
	coordinates: number[][][];
}
type Geometry = Point | LineString | Polygon; // Also several others
```

여기서 number[]는 경도와 위도를 나타내므로 튜플타입으로 선언하는게 낫습니다.
타입을 더 구체적으로 선언했기때문에 더 나은 코드가 된 것 같지만 안타깝게도 위치정보에 세번째 정도로 고도가 있을 수도 있고, 또 다른 정보가 존재할 수도 있습니다.
추가정보가 있는데 이 타입을 계속 사용하게되면 타입단언문이나 as any같은 타입을 추가해서 타입체커를 무시해야만 합니다.

```ts
type GeoPosition = [number, number];
interface Point {
	type: "Point";
	coordinates: GeoPosition;
}
// ...
```

## 요약

- 타입이 없는 것보다 잘못된게 더 나쁩니다.
- (추후 정리) any와 unknwon을 구별해서 사용해야 합니다.
