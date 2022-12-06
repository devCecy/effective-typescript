# any 타입은 가능한 한 좁은 범위에서만 사용하기

</br>

## 함수) any를 사용해야한다면 좁은 범위에 사용하기

다음 코드의 오류를 제거하는 방법은 두가지 입니다.

```ts
interface Foo {
	foo: string;
}
interface Bar {
	bar: string;
}
declare function expressionReturningFoo(): Foo;
function processBar(b: Bar) {
	/* ... */
}

function f() {
	const x = expressionReturningFoo();
	processBar(x);
	//         ~ Argument of type 'Foo' is not assignable to
	//           parameter of type 'Bar'
}
```

아래 두가지 방법 중 2번 방법을 사용하는 것이 좋습니다. 변수 x에 any를 선언해주면 f1함수의 반환타입 또한 any가 됩니다. 반면 any의 사용범위를 좁혀서 매개변수에 any를 선언해주면 any가 다른 코드에는 영향을 주지 않습니다.

1. any를 변수 x에 선언해주는 방법

```ts
function f1() {
	const x: any = expressionReturningFoo(); // Don't do this
	processBar(x);
}
```

2. any를 매개변수 x에 선언해주는 방법

```ts
function f2() {
	const x = expressionReturningFoo();
	processBar(x as any); // Prefer this
}
```

</br>

## 함수의 반환타입으로 any사용하지 않기

f1()함수는 any타입을 가진 변수x를 반환합니다. f1()함수의 반환타입이 any인것 입니다. 반환타입이 any인 f1()함수를 변수 foo에 할당하면 foo.fooMethod()를 호출할때 타입이 체크되지 않습니다. 변수 foo에 할당된 값이 any를 반환하기 때문입니다. 이처럼 함수의 반환타입으로 any를 사용하면 프로젝트 전반에 영향을 주기 때문에 이렇게 사용하지 않는 것이 좋습니다. (참고로, 함수의 반환타입을 명시하면 any타입이 함수의 바깥으로 영향을 미치는 것을 방지할 수 있습니다.)

```ts
function f1() {
	const x: any = expressionReturningFoo();
	processBar(x);
	return x;
}

function g() {
	const foo = f1(); // Type is any
	foo.fooMethod(); // This call is unchecked!
}
```

</br>

## any대신 @ts-ignore 사용하기

processBar(x as any)로 사용하는대신 // @ts-ignore를 사용해주면 강제로 타입 오류를 제거해 줄 수 있습니다. 하지만 근본적인 원인을 찾아 해결하는것이 가장 좋은 방법입니다.

```ts
function f1() {
	const x = expressionReturningFoo();
	// @ts-ignore
	processBar(x);
	return x;
}
```

## 객체)

다음 코드의 객체 내부에서 발생한 에러를 해결해 봅시다.

```ts
interface Foo {
	foo: string;
}
interface Bar {
	bar: string;
}
declare function expressionReturningFoo(): Foo;
function processBar(b: Bar) {
	/* ... */
}
interface Config {
	a: number;
	b: number;
	c: {
		key: Foo;
	};
}
declare const value: Bar;
const config: Config = {
	a: 1,
	b: 2,
	c: {
		key: value,
		// ~~~ Property ... missing in type 'Bar' but required in type 'Foo'
	},
};
```

아래 두가지 방법으로 오류를 해결할 수 있지만 역시나 2번 방법을 사용하여 any의 사용범위를 좁혀주는 것이 좋습니다. 그래야 a와 b에 대해서 타입체커를 사용할 수 있기 때문입니다.

1. config객체 전체에 as any를 선언해주어 해결할 수 있습니다.

```ts
const config: Config = {
	a: 1,
	b: 2,
	c: {
		key: value,
	},
} as any; // Don't do this!
```

2. 오류가 발생한 key: value 부분에 as any를 선언해 오류를 해결할 수 있습니다.

```ts
const config: Config = {
	a: 1,
	b: 2, // These properties are still checked
	c: {
		key: value as any,
	},
};
```

## 요약

- 의도치 않은 타입 안전성의 손실을 피하기 위해서 any의 사용 범위를 최소한으로 좁혀야 합니다.
- 함수의 반환 타입이 any인 경우 타입 안정성이 나빠집니다. 따라서 any타입을 반환하면 절대 안 됩니다.
- 강제로 타입 오류를 제거하려면 any대신 @ts-ignore를 사용하는 것이 좋습니다.
