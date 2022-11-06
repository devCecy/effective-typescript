# 타입 단언보다는 타입 선언 사용하기

</br>

타입스크립트에서 변수에 값을 할당하고 타입을 부여하는 방법에는 타입 단언과 타입 선언이 있습니다.

</br>

alice: Person은 타입 선언으로 alice라는 값이 Person이라는 타입임을 명시하며 만약 Person이 아닐 경우 오류를 냅니다. as Person은 타입 단언으로 타입스크립트가 추론한 타입이 있더라도 타입을 Person으로 간주 합니다.

```js
interface Person { name: string };

const alice: Person = { name: 'Alice' };  // 타입 선언
const bob = { name: 'Bob' } as Person;  // 타입 단언
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

- 타입스크립트보다 타입 정보를 더 잘 알고 있는 상황에서는 타입 단언문과 non null assertion(!)을 사용합니다.
- 그 외의 경우에는 타입 선언(:Type)을 사용해야 합니다.
