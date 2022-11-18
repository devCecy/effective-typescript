# 다른 타입에는 다른 변수 사용하기

한 변수를 다른 목적을 가지는 다른 타입으로 재사용하면 오류가 발생합니다.

```ts
function fetchProduct(id: string) {}
function fetchProductBySerialNumber(id: number) {}
let id = "12-34-56";
fetchProduct(id);

id = 123456;
// ~~ '123456' is not assignable to type 'string'.
fetchProductBySerialNumber(id);
// ~~ Argument of type 'string' is not assignable to
//    parameter of type 'number'
```

</br>

타입을 바꿀 수 있는 방법은 타입의 범위를 좁히는 것입니다. 위의 코드에서 오류를 제거해 주려면 string과 number 모두 포함 할 수 있도록 타입의 범위를 좁혀주면 됩니다. string|number로 표현하며, 이를 `유니온 타입`이라고 합니다.

```ts
let id: string | number = "12-34-56";
fetchProduct(id);

id = 123456; // OK
fetchProductBySerialNumber(id); // OK
```

</br>

유니온 타입의 도입으로 코드가 동작하기는 하지만, id변수를 사용할 때마다 어떤 타입을 가지고 있는지 확인해야 하는 번거로움이 생깁니다. 이럴때는 차라리 별도의 변수를 도입하는 것이 낫습니다. 위 예제의 첫번째 id와 두번째 id는 변수명은 같지만 서로 관련이 없습니다. 그러므로 별도의 변수로 구분하여 사용하는 것이 좋습니다.

```ts
const id = "12-34-56";
fetchProduct(id);

const serial = 123456; // OK
fetchProductBySerialNumber(serial); // OK
```

</br>

다른 타입에는 별도의 변수를 사용하는것이 좋은 이유는 다음과 같습니다.

- 서로 관련이 없는 두개의 값을 분리합니다.
- 변수명을 더 구체적으로 지을 수 있습니다.
- 타입 추론을 향상시키며, 타입 구문이 불필요해집니다.
- 타입이 좀 더 간결해집니다.
- let 대신 const로 변수를 선언하게 됩니다. const로 변수를 선언하면 코드가 간결해지고, 타입체커가 타입을 추론하기에도 좋습니다.

</br>

## 요약

- 변수의 값은 바뀔 수 있지만 타입은 일반적으로 바뀌지 않습니다.
- 혼란을 막기 위해 타입이 다른 값을 다룰 때에는 변수를 재사용하지 않도록 합니다.
