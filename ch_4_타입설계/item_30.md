# 문서에 타입 정보를 쓰지 않기

</br>

다음 코드에서 잘못된 부분을 찾아봅시다.

- 주석에는 함수가 string을 반환한다고 적혀 있지만 실제로는 {r,g,b} 객체를 반환합니다.
- 주석에는 함수가 0개 혹은 1개의 매개변수를 받는다고 적혀있지만 이건 타입시그니처만 봐도 알 수 있는 정보입니다.
- 불필요하게 장황합니다. 함수보다 주석이 더 깁니다.

```ts
/**
 * Returns a string with the foreground color.
 * Takes zero or one arguments. With no arguments, returns the
 * standard foreground color. With one argument, returns the foreground color
 * for a particular page.
 */
function getForegroundColor(page?: string) {
	return page === "login" ? { r: 127, g: 127, b: 127 } : { r: 0, g: 0, b: 0 };
}
```

누가 강제하지 않는 이상 주석과 코드가 동기화 되기는 어렵습니다. 그러나 타입 구문은 타입스크립트 타입체커가 타입 정보를 동기화하도록 강제 합니다.
위의 코드를 타입스크립트를 사용하여 아래와같이 간결하게 나타내 줄 수 있습니다.

```ts
type Color = { r: number; g: number; b: number };

/** Get the foreground color for the application or a specific page. */
function getForegroundColor(page?: string): Color {
	return page === "login" ? { r: 127, g: 127, b: 127 } : { r: 0, g: 0, b: 0 };
}
```

</br>

값을 변경하지 않는다고 주석에 적는것도 좋지 않습니다.

```ts
/** Does not modify nums */
function sort(nums: number[]) {
	/* ... */
}
```

타입스크립트가 규칙을 강제하게 하는 것이 더 좋습니다.

```ts
function sort(nums: readonly number[]) {
	/* ... */
}
```

</br>

## 요약

- 주석과 변수명에 타입 정보를 적는 것은 피해야 합니다. 타입 선언이 중복되는 것으로 끝나면 다행이지만 최악의 경우는 타입 정보에 모순이 발생하게 됩니다.
- 타입이 명확하지 않은 경우 변수명에 단위정보를 포함하는 것을 고려하는 것이 좋습니다. 예를들어, timeMs, temperatureC.
