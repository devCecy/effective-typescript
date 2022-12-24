# DOM 계층 구조 이해하기

- (표7-1 DOM계층의 타입들 이미지 추가)

- `EventTarget`은 DOM타입 중 가장 추상화된 타입입니다. 이벤트 리스너를 추가하거나 제거하고, 이벤트를 보내는 것을 할 수 있습니다.
- `Node` 타입은 element뿐만 아니라 텍스트 조각과 주석을 포함하고 있습니다.
- `Element`와 `HTMLElement`
- `HTMLxxxElement`는 자신만의 고유한 속성을 가지고 있습니다. 예를들어, HTMLImageElement에는 src속성이 있고, HTMLInputElement에는 value 속성이 있습니다.

## 요약

- DOM에는 타입 계층 구조가 있습니다. DOM타입은 타입스크립트에서 중요한 정보이며, 브라우저 관련 프로젝트에서 타입스크립트를 사용할 때 유용합니다.
- Node, Element, HTMLElement, EventTarget간의 차이점, 그리고 Event와 Mouse Event의 차이점을 알아야 합니다.
- DOM 엘리먼트의 이벤트에는 충분히 구체적인 타입 정보를 사용하거나, 타입스크립트가 추론할 수 있도록 문맥 정보를 활용해야 합니다.
