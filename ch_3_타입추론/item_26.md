# 타입 추론에 문맥이 어떻게 사용되는지 이해하기

타입스크립트는 타입을 추론할 때 단순히 값만 고려하지 않고 값이 존재하는 곳의 문맥까지도 살핍니다.

</br>

다음 코드에서는 인라인 형태의 함수 선언을 통해 매개변수가 Language타입이여야 함을 알고 있습니다. 그러므로 정상입니다.

```ts
type Language = "JavaScript" | "TypeScript" | "Python";
function setLanguage(language: Language) {
	/* ... */
}

setLanguage("JavaScript"); // OK
```

그러나 <u>값을 변수로 분리하면 타입스크립트는 할당 시점에 타입을 추론</u>합니다. 그러므로 변수 language의 타입은 string이 되어 Language에 할당 불가능하므로 오류가 발생합니다.

```ts
let language = "JavaScript";
setLanguage(language);
// ~~~~~~~~ Argument of type 'string' is not assignable
//          to parameter of type 'Language'
```

</br>

이 문제를 해결하는 두 가지 방법이 있습니다.

1. 타입선언에서 변수 language의 가능한 값을 제한하는 것입니다.

```ts
type Language = "JavaScript" | "TypeScript" | "Python";
function setLanguage(language: Language) {
	/* ... */
}
let language: Language = "JavaScript";
setLanguage(language); // OK
```

2. language를 const를 사용하여 상수로 만드는 방법도 있습니다.

```ts
type Language = "JavaScript" | "TypeScript" | "Python";
function setLanguage(language: Language) {
	/* ... */
}
const language = "JavaScript";
setLanguage(language); // OK
```

그런데 위 두 해결방법에서는 문맥으로부터 값을 분리해서 사용했습니다. 문맥과 값을 분리하면 추후 문제가 발생 할 수 있습니다.

## 튜플 사용 시

다음 코드에서도 문맥과 값을 분리해서 사용했습니다. 매개변수 where의 타입은 [number, number]이지만, 변수 loc의 타입은 할당 시 추론되어 number[]로 추론됩니다. 그러므로 number[]형식의 타입은 [number, number]에 속할 수 없어 오류가 발생했습니다.

```ts
// Parameter is a (latitude, longitude) pair.
function panTo(where: [number, number]) {
	/* ... */
}

panTo([10, 20]); // OK

const loc = [10, 20];
panTo(loc);
//    ~~~ Argument of type 'number[]' is not assignable to
//        parameter of type '[number, number]'
```

const로 선언해주어 상수임을 알려주었음에도 오류가 납니다.
이러한 문제를 해결하기 위해서는,

1. 변수 loc에 직접 타입을 선언해 줍니다.

```ts
const loc: [number, number] = [10, 20];
panTo(loc); // OK
```

2. as const를 사용하여 값의 내부까지 상수임을 알려줍니다. 그러나 이런식으로 해결을 하면 변수 loc의 타입은 readonly[10,20]으로 추론되는데 이는 너무 과한 추론입니다. [number, number]가 아니라 [10,20]으로 밖에 매개변수타입을 사용할 수 없기 떄문입니다.

```ts
const loc = [10, 20] as const;
panTo(loc);
```

3. 가장 적절한 방법은 as const와 함께 매개변수 타입에 readonly 구문을 추가하는 것입니다.

```ts
function panTo(where: readonly [number, number]) {
	/* ... */
}
const loc = [10, 20] as const;
panTo(loc); // OK
```

</br>

## 객체 사용 시

문맥과 값의 분리는 문자열 리터럴이나 튜플을 포함하는 큰 객체에서 상수를 뽑아낼 때도 발생합니다. 이 문제는 위와 마찬가지로 변수에 타입을 선언해주거나 as const를 사용하여 해결할 수 있습니다.

```ts
type Language = "JavaScript" | "TypeScript" | "Python";
interface GovernedLanguage {
	language: Language;
	organization: string;
}

function complain(language: GovernedLanguage) {
	/* ... */
}

complain({ language: "TypeScript", organization: "Microsoft" }); // OK

const ts = {
	language: "TypeScript", // type : string
	organization: "Microsoft",
};
complain(ts);
//       ~~ Argument of type '{ language: string; organization: string; }'
//            is not assignable to parameter of type 'GovernedLanguage'
//          Types of property 'language' are incompatible
//            Type 'string' is not assignable to type 'Language'
```

</br>

## 콜백 사용 시

콜백을 다른 함수로 전달할 떄, 타입스크립트는 콜백의 매개변수 타입을 추론하기 위해 문맥을 사용합니다.

callWithRandomNumbers의 매개변수 fn의 타입선언으로 인해 콜백함수인 callWithRandomNumbers의 매개변수 a,b는 number로 타입이 추론될 수 있었습니다.

```ts
function callWithRandomNumbersNumbers(fn: (n1: number, n2: number) => void) {
	fn(Math.random(), Math.random());
}

callWithRandomNumbers((a, b) => {
	a; // Type is number
	b; // Type is number
	console.log(a + b);
});
```

그러나 콜백함수 fn을 상수로 뽑아내면 문맥이 손실되어 noImplicitAny 오류가 발생합니다.

```ts
function callWithRandomNumbers(fn: (n1: number, n2: number) => void) {
	fn(Math.random(), Math.random());
}
const fn = (a, b) => {
	// ~    Parameter 'a' implicitly has an 'any' type
	//    ~ Parameter 'b' implicitly has an 'any' type
	console.log(a + b);
};
callWithRandomNumbers(fn);
```

이런 경우에는 매개변수에 타입 구문을 추가하여 해결할 수 있습니다.

```ts
function callWithRandomNumbers(fn: (n1: number, n2: number) => void) {
	fn(Math.random(), Math.random());
}
const fn = (a: number, b: number) => {
	console.log(a + b);
};
callWithRandomNumbers(fn);
```

</br>
## 요약

- 타입 추론에서 문맥이 어떻게 쓰이는지 주의해서 살펴봐야 합니다.
- 변수를 뽑아서 별도로 선언했을 때 오류가 발생한다면 `타입 선언`을 추가해 야합니다.
- 변수가 정말로 상수라면 `상수 단언(as const)`를 사용해야 합니다. 그러나 상수 단언을 사용하면 정의한 곳이 아니라 사용한 곳에서 오류가 발생하므로 주의해야 합니다.
