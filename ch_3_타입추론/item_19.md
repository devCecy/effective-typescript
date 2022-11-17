# 추론 가능한 타입을 사용해 장황한 코드 방지하기

타입 추론이 된다면 명시적 타입 구문은 필요하지 않습니다.
타입이 추론되는 경우와 타입이 추론됨에도 명시적으로 타입을 선언해 주는것이 더 좋은 경우를 알아봅시다.

</br>

## 타입 추론이 되는 경우

1번은 변수, 2번은 객체의 예시로, 타입추론이 되기 때문에 타입 구문을 넣는 것이 오히려 거추장스럽다는 것을 보여 줍니다.

```ts
// 1. 변수
let x: number = 12; // 놉!

let x = 12; // 에쓰!

// 2. 객체
const person: {
	// 놉!
	name: string;
	born: {
		where: string;
		when: string;
	};
	died: {
		where: string;
		when: string;
	};
} = {
	name: "Sojourner Truth",
	born: {
		where: "Swartekill, NY",
		when: "c.1797",
	},
	died: {
		where: "Battle Creek, MI",
		when: "Nov. 26, 1883",
	},
};

const person = {
	// 에쓰!
	name: "Sojourner Truth",
	born: {
		where: "Swartekill, NY",
		when: "c.1797",
	},
	died: {
		where: "Battle Creek, MI",
		when: "Nov. 26, 1883",
	},
};
```

</br>

타입스크립트가 우리보다 더 정학하게 타입 추론을 하기도 합니다. axis2의 타입을 string이라고 생각하기 쉽지만 타입추론대로 실제로는 'y'가 더 정확한 타입입니다.

```ts
const axis1: string = "x"; // Type is string
const axis2 = "y"; // Type is "y"
```

</br>

함수의 매개변수에는 타입 구문을 포함하는것이 좋지만 기존값이 있다면 타입이 추론됨으로 타입 생략이 가능합니다. base의 기본값이 10으로 지정되어있기 때문에 base의 타입은 number로 추론됩니다.

```ts
function parseNumber(str: string, base = 10) {
	// ...
}
```

</br>

## 타입이 추론될 수 있음에도 명시적 타입을 선언하면 좋은 경우

객체는 타입추론이 되지만 `객체 리터털`로 정의할때 타입 구문을 넣어주면 잉여 속성체크가 동작하므로 명시적 타입을 선언해주면 좋습니다.

```ts
interface Product {
	id: string;
	name: string;
	price: number;
}

const elmo: Product = {
	name: "Tickle Me Elmo",
	id: "048188 627152",
	price: 28.99,
};
```

logProduct함수에서 받고있는 매개변수 product에 Product타입을 선언해준 뒤 furby객체를 넘겨주면 객체를 선언해주고 있는 `const id = product.id;`에서 오류가 나지만,

```ts
function logProduct(product: Product) {
	const id = product.id;
	// ~~ Type 'string' is not assignable to type 'number'
	const name = product.name;
	const price = product.price;
	console.log(id, name, price);
}
const furby = {
	name: "Furby",
	id: 630509430963,
	price: 35,
};

logProduct(furby);
```

Product타입을 선언하지 않았다면 잘못된 객체를 사용하고 있는 `logProduct(furby)` 에서 오류가 납니다.

```ts
logProduct(furby);
// ~~~~~ Argument .. is not assignable to parameter of type 'Product'
//         Types of property 'id' are incompatible
//         Type 'number' is not assignable to type 'string'
```

</br>

`함수의 반환`에도 타입을 명시해주면 오류를 방지할 수 있습니다.

주식 시세를 조회하는 함수를 작성했다고 가정해 보겠습니다.

```ts
function getQuote(ticker: string) {
	return fetch(`https://quotes.example.com/?q=${ticker}`).then((response) =>
		response.json()
	);
}
```

그리고 이미 조회한 종목에 대한 정보를 다시 요청하지 않도록 캐시하는 구문을 추가해 줍니다.

```ts
const cache: { [ticker: string]: number } = {};
function getQuote(ticker: string) {
	if (ticker in cache) {
		return cache[ticker];
	}
	return fetch(`https://quotes.example.com/?q=${ticker}`)
		.then((response) => response.json())
		.then((quote) => {
			cache[ticker] = quote;
			return quote;
		});
}
```

그리고 getQuote("MSFT")에 then을 사용하면 오류가닙니다. 캐시를 위한 if문에 진입하면 함수의 반환값이 Promise를 반환하는 것이 아니라 cache[ticker]를 반환하기 때문입니다.

```ts
getQuote("MSFT").then(considerBuying);
// ~~~~ Property 'then' does not exist on type
//        'number | Promise<any>'
//      Property 'then' does not exist on type 'number'
```

의도한 반환값의 타입 Pomise<number>를 명시해주었다면 캐시를 위한 if문에서 오류를 잡아주었을 것입니다.

```ts
const cache: { [ticker: string]: number } = {};
function getQuote(ticker: string): Promise<number> {
	if (ticker in cache) {
		return cache[ticker];
		// ~~~~~~~~~~~~~ Type 'number' is not assignable to 'Promise<number>'
	}
	// COMPRESS
	return Promise.resolve(0);
	// END
}
```

</br>

## \* 참고)

eslint의 `no-iferrable-types`를 사용하면 작성된 타입 구문이 정말로 필요한지 확인할 수 있습니다.

</br>

## 요약

- 타입스크립트가 타입을 추론할 수 있다면 타입 구문을 작성하지 않는것이 좋습니다.
- 이상적인 경우 함수/메서드의 시그니처에는 타입 구문이 있지만, 함수 내의 지역변수에는 타입 구문이 없습니다.
- 추론될 수 있는 경우라도 객체 리터럴과 함수반환에는 타입 명시를 고랴해야 합니다. 이는 내부 구현의 오류가 사용자 코드 위치에 나타나는 것을 방지해 줍니다.
