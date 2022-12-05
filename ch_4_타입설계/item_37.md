# 공식 명칭에는 상표를 붙이기

</br>

구조적 타이핑의 특성 때문에 가끔 코드가 이상한 결과를 낼 수 있습니다.
다음 코드는 구조적 타이핑 관적에서는 문제가 없지만 수학적으로 따지면 calculateNorm함수의 인자로는 2차원 벡터만을 사용해야 맞습니다.

```ts
interface Vector2D {
	x: number;
	y: number;
}
function calculateNorm(p: Vector2D) {
	return Math.sqrt(p.x * p.x + p.y * p.y);
}

calculateNorm({ x: 3, y: 4 }); // OK, result is 5
const vec3D = { x: 3, y: 4, z: 1 };
calculateNorm(vec3D); // OK! result is also 5
```

</br>

calculateNorm함수가 3차원 벡터를 허용하지 않게 하려면 `공식 명칭`을 사용하면 됩니다. 공식 명칭을 사용하는 것은 타입이 아니라 값의 관점에서 Vector2D라고 말하는 것입니다. 공식 명칭 개념을 타입스크립트에서 흉내내려면 `상표(brand)`를 붙이면 됩니다.

```ts
interface Vector2D {
	_brand: "2d"; // 상표 붙이기
	x: number;
	y: number;
}
function vec2D(x: number, y: number): Vector2D {
	return { x, y, _brand: "2d" };
}
function calculateNorm(p: Vector2D) {
	return Math.sqrt(p.x * p.x + p.y * p.y); // Same as before
}

calculateNorm(vec2D(3, 4)); // OK, returns 5

const vec3D = { x: 3, y: 4, z: 1 };
calculateNorm(vec3D);
// ~~~~~ Property '_brand' is missing in type...
```

## 요약

- 값을 구분하기 위해 공식 명칭이 필요하다면 상표를 붙이는 것을 고려해야 합니다.
- 상표 기법은 타입 시스템에서 동작하지만 런타임에 상표를 검사하는 것과 동일한 효과를 얻을 수 있습니다.
