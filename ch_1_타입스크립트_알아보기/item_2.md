# 타입스크립트 설정 이해하기

타입스크립트 컴파일러는 언어의 핵심 요소에 영향을 미치는 몇가지 설정을 포함하고 있으며, 이러한 설정 요소들을 이해해야 타입스크립트를 잘 사용할 수 있습니다.

</br>

> ## 타입스크립트 설정은 tsconfig.json을 통해 할 수 있습니다

</br>

커맨드 라인을 사용해서 타입스크립트 설정을 할 수 도 있지만, 타입스크립트를 어떻게 사용할 것인지 동료들이나 도구들에 알리기 위해 가급적이면 tsconfig.json파일을 사용하는것이 좋습니다.

`tsc --init` 명령어를 통해 tsconfig.json파일을 생성할 수 있습니다.

</br>

> ## 설정 요소인 `noImplicitAny`와 `strictNullChecks`를 이해할 필요가 있습니다

</br>

### 1) noImplicitAny

`noImplicitAny`는 변수들이 미리 정의된 타입을 가져야 하는지 여부를 제어합니다. 타입스크립트는 타입 정보를 가지고 있을 때 가장 효과적이기 때문에 자바스크립트 프로젝트를 타입스크립트로 전환하는 경우를 제외하고는 noImplicitAny를 true로 설정하는 것이 좋습니다.

</br>

아래코드는 오류 없이 타입체커를 통과할 수 있을까요?

```jsx
function add(a, b) {
	return a + b;
}
```

정답은 `tsConfig: {"noImplicitAny":false}`로 설정되어있다면 타입체커를 통과할 것이고, `tsConfig: {"noImplicitAny":true}`로 설정되어있다면 타입체커를 통과하지 못해 런타임 오류가 발생한다, 입니다.

런타임 오류를 해결하기 위해서는 다음과 같이 명시적(explicit)으로 인자의 타입을 선언해 주어야 합니다.

```jsx
function add(a: number, b: number) {
	return a + b;
}
```

</br>

### 2) strictNullChecks

`strictNullChecks`는 null과 undefined가 모든 타입에서 허용되는지 확인하는 설정입니다. "undefined는 객체가 아닙니다"같은 런타임 오류를 방지하기 위해 strictNullChecks를 true로 설정하는 것이 좋습니다.

</br>

다음코드는 타입체커를 통과할 수 있을까요?

```jsx
const x: number = null;
```

정답은 `tsConfig: {"strictNullChecks":false}`로 설정되어 있다면 타입체커를 통과할 것이고, `tsConfig: {"strictNullChecks":true}`로 설정되어 있다면 타입체커를 통과하지 못해 런타임 오류가 발생한다, 입니다.

런타임 오류를 해결하기 위해서는 null 또는 undefined를 사용하겠다고 명시적(explicit)으로 선언해주어야 합니다.

```jsx
const x: number | null = null;
```

</br>

런타임 오류없이 null을 허용하지 않으려면, 1) null을 체크하는 코드나 2) 단언문(assertion)을 추가해야 합니다.

```jsx
// tsConfig: {"noImplicitAny":true,"strictNullChecks":true}

   const el = document.getElementById('status');
   el.textContent = 'Ready';
// ~~ 개체가 'null'인 것 같습니다.

   if (el) {
     el.textContent = 'Ready';  // 1) 정상, null은 제외됩니다.
   }
   el!.textContent = 'Ready';  // 2) 정상, el이 null이 아님을 단언합니다.
```

</br>

### 참고) 타입스크립트의 ! Non-null assertion operator

위 코드 마지막 줄에서 el 다음에 붙은 !는 null이 아님을 단언하는 연산자입니다. 컴파일러에게 피연산자인 el이 null이 아님을 말해주어 `{"strictNullChecks":true}`상황에서도 런타임 오류를 발생하지 않도록 해줍니다.

</br>

## 요약

- 타입스크립트 설정은 tsconfig.json을 통해 할 수 있습니다.
- 자바스크립트 프로젝트를 타입스크립트로 전환하는 경우를 제외하고는 `noImplicitAny`를 true로 설정하는 것이 좋습니다.
- "undefined는 객체가 아닙니다"같은 런타임 오류를 방지하기 위해 `strictNullChecks`를 true로 설정하는 것이 좋습니다.
