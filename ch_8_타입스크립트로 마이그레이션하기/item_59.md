# 타입스크립트 도입 전에 @ts-check와 JSDoc으로 시험해 보기

자바스크립트 파일에 @ts-check 지시자를 사용하면 타입체커가 파일을 분석하고, 발견된 오류를 보고하도록 할 수 있습니다.

<br/>

@ts-check 지시자 사용법은 다음과 같습니다.

```ts
// @ts-check
const person = { first: "Grace", last: "Hopper" };
2 * person.first;
// ~~~~~~~~~~~~ The right-hand side of an arithmetic operation must be of type
//              'any', 'number', 'bigint', or an enum type
```



## 요약

- 파일 상단에 // @ts-check를 추가하면 자바스크립트에서도 타입 체크를 수행할 수 있습니다.
- 전역 선언과 서드파티 라이브러리의 타입 선언을 추ㅏ하는 방법을 익힙시다.
- JSDoc주석을 잘 활용하면 자바스크립트 상태에서도 타입 단언과 타입 추론을 할 수 있습니다.
- JSDoc 주석은 중간단계이기 때문에 너무 공들일 필요는 없습니다. 최종 목표는 .ts로 된 타입스크립트 코드임을 명심합시다.
