# 타입 공간과 값 공간의 심벌 구분하기

</br>

interface의 Cylinder는 타입으로 쓰였고, const Cylinder는 값으로 쓰였습니다.
이처럼 심벌 이름이 같더라도 상황에 따라 타입으로 쓰일수도, 값으로 쓰일 수도 있기 때문에 오류를 발생시킬 수 있습니다.

```js
interface Cylinder {
	radius: number;
	height: number;
}

const Cylinder = (radius: number, height: number) => ({ radius, height });

function calculateVolume(shape: unknown) {
	if (shape instanceof Cylinder) {
		shape.radius;
		// ~~~~~~ Property 'radius' does not exist on type '{}'
	}
}
```

타입스크립트에서 타입과 값은 번갈아 나올 수 있습니다.
타입선언(:) 또는 단언문(as) 다음에 나오는 심벌은 타입이고, = 다음에 나오는 모든 것은 값입니다.

```js
interface Person {
	first: string;
	last: string;
}
const p: Person = { first: "Jane", last: "Jacobs" };
//    -           --------------------------------- Values
//       ------ Type
function email(p: Person, subject: string, body: string): Response {
	//     ----- -          -------          ----  Values
	//              ------           ------        ------   -------- Types
	// COMPRESS
	return new Response();
	// END
}
```

## 요약

- 타입스크립 코드를 읽을 때 타입인지 값인지 구분하는 방법을 터득해야 합니다. 타입선언(:) 또는 단언문(as) 다음에 나오는 심벌은 타입이고, = 다음에 나오는 모든 것은 값입니다.
- 모든 값은 타입을 가지지만, 타입은 값을 가지지 않습니다. type과 interface같은 키워드는 타입 공간에만 존재합니다.
- class나 enum같은 키워드는 타입과 값 두가지로 사용될 수 있습니다.
- "foo"는 문자열 리터럴이거나, 문자열 리터럴 타입일 수 있습니다.
- typeof, this 그리고 많은 다른 연산자들과 키워드들은 타입 공간과 값 공간에서 다른 목적으로 사용 될 수 있습니다.
