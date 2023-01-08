# 오버로딩 타입보다는 조건부 타입을 사용하기

## \*참고 ) 함수 오버로딩

동일한 이름에 매개변수만 다른 여러 버전의 함수를 허용하는 것을 말합니다. 타입스크립트에서는 타입 수준에서만 함수 오버로딩이 가능합니다. 즉, 함수에 대한 여러개의 타입 선언문 작성은 가능하지만 구현체는 1개여야만 한다는 것입니다.

<br />

double이라는 함수에 타입정보를 추가해 봅시다.
double함수에는 매개변수로 number 또는 string가 들어올 수 있고 number 또는 string이 반환됩니다.

```ts
function double(x: number | string): number | string;
function double(x: any) {
	return x + x;
}
```

위 타입 선언이 틀린것은 아니지만 애매한 부분이 있습니다. 매개개변수로 string타입이 들어오면 string타입을 반환하게되지만, 실제 반환타입은 string 또는 number입니다. 좀 더 구체적으로 타입을 선언해 봅시다.

```ts
const num = double(12); // string | number
const str = double("x"); // string | number
```

제너릭을 사용했습니다. 그러나 타입이 너무 구체적이게 되었습니다.
매개변수로 string 타입인 'x'가 들어오면 반환타입이 string이 아니라 'x'가 됩니다.

```ts
function double<T extends number | string>(x: T): T;
function double(x: any) {
	return x + x;
}

const num = double(12); // Type is 12
const str = double("x"); // Type is "x"
```

이번엔 함수오버로딩을 사용해서 타입 선언을 분리해 봅시다.

```ts
function double(x: number): number;
function double(x: string): string;
function double(x: any) {
	return x + x;
}

const num = double(12); // Type is number
const str = double("x"); // Type is string
```

위 타입 선언은 잘 동작하는 것 같지만, 만약 x 매개변수에 유니온 타입이 들어온다면 오류를 반환하게 됩니다.

```ts
function f(x: number | string) {
	return double(x);
	// ~ Argument of type 'string | number' is not assignable
	//   to parameter of type 'string'
}
```

string타입 매개변수가 들어오면 string을 반환하고, number매개변수가 들어오면 number를 반환하도록 하는 가장 좋은 방법은, `조건부 타입`을 사용하는 것입니다.
조건부 타입은 타입의 if와 같으며 삼항 연산자 처럼 사용합니다.

아래 조건부 타입은,

- T가 string의 부분 집합이면, 반환타입이 string입니다.
- 그 외의 경우에는 반환타입이 number입니다.

```ts
function double<T extends number | string>(
	x: T
): T extends string ? string : number;
function double(x: any) {
	return x + x;
}
```

## 요약

- 오버로딩 타입보다는 조건부 타입을 사용하는 것이 좋습니다.
- 조건부 타입은 추가적인 오버로딩 없이 유니온 타입을 지원할 수 있습니다.
