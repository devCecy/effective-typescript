# 공개 API에 등장하는 모든 타입을 export하기

어떤 타입을 숨기고 싶어서 export 하지 않았다고 가정해봅시다.
해당 라이브러리 사용자는 SecretName과 SecretSanta를 직접 import 할 수 없고 getGift만 import 할 수 있습니다.

```ts
interface SecretName {
	first: string;
	last: string;
}

interface SecretSanta {
	name: SecretName;
	gift: string;
}

export function getGift(name: SecretName, gift: string): SecretSanta {
	// COMPRESS
	return {
		name: {
			first: "Dan",
			last: "Van",
		},
		gift: "MacBook Pro",
	};
	// END
}
```

그러나 getGift의 함수시그니처에 SecretName과 SecretSanta가 등장하기 때문에 추출해 낼 수 있습니다.

```ts
type MySanta = ReturnType<typeof getGift>; // SecretSanta
type MyName = Parameters<typeof getGift>[0]; // SecretName
```

## 요약

- 공개 메서드에 등장한 어떤 현태의 타입이든 export합시다. 어차피 라이브러리 사용자가 추출할 수 있으므로 export하기 쉽게 만드는 것이 좋습니다.
