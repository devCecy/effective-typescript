# 객체 래퍼 타입 피하기

</br>

타입스크립트는 기본형 객체 레퍼 타입을 별도로 모델링 합니다.

</br>

string 타입을 String이라고 잘못 타이핑을하면 처음에는 잘 동작하는 것 처럼 보입니다.

```js
function getStringLen(foo: String) {
	return foo.length;
}

getStringLen("hello"); // OK
getStringLen(new String("hello")); // OK
```

그러나 `string`을 매개변수로 받는 메서드에 `String`객체를 전달하는 순간 문제가 발생합니다. `string`은 `String`에 할달할 수 있지만, `String`은 `string`에 할당할 수 없기 때문입니다.

```js
function isGreeting(phrase: String) {
	return ["hello", "good day"].includes(phrase);
	// ~~~~~~
	// Argument of type 'String' is not assignable to parameter
	// of type 'string'.
	// 'string' is a primitive, but 'String' is a wrapper object;
	// prefer using 'string' when possible
}
```

### \* 참고) 래퍼객체

- 원시 타입의 값을 감싸는 형태의 객체를 말합니다.
- number, string, boolean, symbol 데이터 타입에 각각 대응하는 Number, String, Boolean, Symbol이 제공됩니다.

### 예시 )

- `.first`로 `'a'`를 할당하려면 . 앞에 객체를 가르키는 식별자가 와야합니다. 그런데 아래 예제에서는 string타입이 왔음에도 `str.first = 'a'`가 정상적으로 할당됩니다. 이유는 문자열의 프로퍼티에 접근할 때 자바스크립트가 문자열을 `new String()`을 호출한 것과 같이 문자열을 (래퍼)객체로 변환해 주기 때문입니다.

- `console.log(str.first)`가 `undefined`가 되는 이유는 래퍼 객체는 프로퍼티를 참조할 때 생성되고 프로퍼티 참조가 끝나면 사라지기 때문입니다.

```js
let str = "abcde";
str.first = "a"; // new String(str).first = 'a'

console.log(str.first); // undefined
```

## 요약

- 기본형 값에 메서드를 제공하기 위해 객체 래퍼 타입이 어떻게 쓰이는지 이해해야 합니다. 직접 사용하거나 인스턴스를 생성하는 것은 피해야 합니다.
- 타입스크립트 객체 래퍼 타입은 지양하고, 대신 기본형 타입을 사용해야 합니다. String 대신 string, Number 대신 number, Boolean대신 boolean, Symbole대신 symbol, BigInt대신 bigint를 사용해야 합니다.
