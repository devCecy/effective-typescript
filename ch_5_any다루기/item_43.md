# 몽키 패치보다는 안전한 타입을 사용하기

자바스크립트는 객체와 클래스에 임의의 속성을 추가할 수 있을 만큼 유연합니다.
예를들어, window또는 DOM 노드에 데이터를 추가한다고 가정해 보겠습니다.그러면 그 데이터는 기본적으로 전역 변수가 됩니다. 전역 변수를 사용하면 은연중에 프로그램 내에서 서로 멀리 떨어진 부분들 간에 의존성을 만들게 됩니다. 그러면 함수 호출시 마다 side effect를 고려해야합니다.
여기에 타입스크립트를 더하면 또 다른 문제가 발생합니다. 타입체커는 Document와 HTMLElement의 내장 속성은 알고 있지만 임의로 추가한 속성에 대해서는 알지 못합니다.

```ts
document.monkey = "Tamarin";
// ~~~~~~ Property 'monkey' does not exist on type 'Document'
```

이 문제를 해결하는 가장 간단한 방법은 any단언문을 사용하는 것입니다.

```ts
(document as any).monkey = "Tamarin"; // OK
```

any단언문 사용으로 인해 타입체커는 통과하지만 타입 안정성과 오타에 대한 체크를 하지 못하게 됩니다.

```ts
(document as any).monky = "Tamarin"; // Also OK, misspelled
(document as any).monkey = /Tamarin/; // Also OK, wrong type
```

최선의 해결책은 document 또는 DOM으로부터 데이터를 분리하는 것입니다.
분리할 수 없는 경우라면 1. interface의 보강기능을 사용하거나 2. 더 구체적인 타입단언문을 사용해야 합니다.

1. interface의 보강기능 사용하기

any보다 보강이 더 나은 이유는,

- 오타나 잘못된 타입의 할당을 오류로 체크합니다.
- 속성에 주석을 붙일 수 있습니다.
- 속성에 자동완성을 사용할 수 있습니다.
- 몽키 패치가 어던 부분에 적용되었는지 정확한 기록이 남습니다.

```ts
interface Document {
	/** Genus or species of monkey patch */
	monkey: string;
}

document.monkey = "Tamarin"; // OK
```

타입스크립트 파일이 import / export를 사용하는 경우, 제대로 동작하게 하려면 global로 선언을 추가해야 합니다.

```ts
export {};
declare global {
	interface Document {
		/** Genus or species of monkey patch */
		monkey: string;
	}
}
document.monkey = "Tamarin"; // OK
```

2. 더 구체적인 타입 단언문 사용하기

```ts
interface MonkeyDocument extends Document {
	/** Genus or species of monkey patch */
	monkey: string;
}

(document as MonkeyDocument).monkey = "Macaque";
```

## 참고) 몽키 패치 ?

- 런타임 중인 프로그램 메모리의 소스 내용(함수, 메서드, 속성)을 직접 바꾸는 것.

## 요약

- 지역 변수나 DOM에 데이터를 저장하지 말고, 데이터를 분리하여 사용해야 합니다.
- 내장 타입에 데이터를 저장해야하는 경우, 안전한 타입 접근법 중 하나(보강이나 사용자 정의 인터페이스로 단언)를 사용해야합니다.
- 보강의 모듈 영역 문제를 이해해야합니다.
