# any 타입 지양하기

</br>

> ## any타입에는 타입 안전성이 없습니다

</br>

```jsx
  let age: number;
   age = '12';
// ~~~ Type '"12"' is not assignable to type 'number'
   age = '12' as any;  // OK
	 age += 1;  // OK; at runtime, age is now "121"
```

</br>

> ## any는 함수 시그니처를 무시해 버립니다

</br>

calculateAge의 매개변수인 birthDate의 타입은 Date이어야 하지만, 함수 밖 변수 birthDate의 타입을 any로 선언했기때문에 calculateAge함수의 매개변수로 string타입을 넣어줘도 오류가 나지않아 문제가 될 수 있습니다.

```jsx
function calculateAge(birthDate: Date): number {
  ...
}

let birthDate: any = '1990-01-19';
calculateAge(birthDate);  // OK
```

</br>

> ## any타입에는 언어 서비스가 적용되지 않습니다

</br>

타입이 있다면 타입스크립트는 자동완성 기능과 도움말을 제공합니다. 그러나 any를 사용하면 아무런 도움도 받을 수 없습니다.

</br>

그외에도, any 타입은 코드 리팩터링 때 버그를 감추며, 타입 설계를 감추고, 타입 시스템의 신뢰도를 떨어뜨립니다.

</br>

## 요약

- any타입을 사용하면 타입 체커와 타입스크립트 언어 서비스를 무력화시켜 버립니다. any타입은 진짜 문제점을 감추며, 개발 경험을 나쁘게하고, 타입 시스템의 신뢰도를 떨어뜨립니다. 최대한 사용을 피하도록 합시다!
