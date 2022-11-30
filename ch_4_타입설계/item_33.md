# string타입보다 더 구체적인 타입 사용하기

string으로 변수를 선언하려한다면 혹시 그보다 더 좁은 타입이 적절하지 않을지 검토해 보아야 합니다.

</br>

앨범의 타입을 정의한다고 가정해 보겠습니다. 속성의 타입을 모두 string으로 선언해준 뒤 주석을 달았습니다.

```ts
interface Album {
	artist: string;
	title: string;
	releaseDate: string; // YYYY-MM-DD
	recordingType: string; // E.g., "live" or "studio"
}
```

주석은 타입을 잡아줄 수 없으므로 아래와 같이 원하지 않은 값을 사용해도 타입체커가 잡아낼 수 없습니다.

```ts
const kindOfBlue: Album = {
	artist: "Miles Davis",
	title: "Kind of Blue",
	releaseDate: "August 17th, 1959", // Oops!
	recordingType: "Studio", // Oops!
}; // OK
```

다음 코드의 경우에도 매개변수의 순서가 바뀌었지만 타입체커는 둘다 string타입이 맞기 때문에 오류를 발생시키지 않습니다.

```ts
function recordRelease(title: string, date: string) {
	/* ... */
}
recordRelease(kindOfBlue.releaseDate, kindOfBlue.title); // OK, should be error
```

artist나 title에는 어떤 값이 사용될지 알 수 없으므로 string타입을 사용하는 것은 적절합니다. 그러나 releaseDate는 날짜형식만 사용하려고 하므로 Date로 타입을 선언해주고, recordingType은 studio와 live 두가지 경우만 존재하므로 유니온 타입으로 정의해 줍니다.

```ts
type RecordingType = "studio" | "live";

interface Album {
	artist: string;
	title: string;
	releaseDate: Date;
	recordingType: RecordingType;
}
```

위 처럼 타입을 선언해주면 타입체커는 더 세밀하게 타입을 잡아낼 수 있습니다.

```ts
const kindOfBlue: Album = {
	artist: "Miles Davis",
	title: "Kind of Blue",
	releaseDate: new Date("1959-08-17"),
	recordingType: "Studio",
	// ~~~~~~~~~~~~ Type '"Studio"' is not assignable to type 'RecordingType'
};
```

## 요약

- 문자열을 남발하여 선언된 코드를 피합시다. 모든 문자열을 할당할 수 있는 string타입보다는 더 구체적인 타입을 사용하는 것이 좋습니다.
- 변수의 범위를 보다 정확하게 표현하고 싶다면 string 타입보다는 문자열 리터럴 타입의 유니온을 사용하면 됩니다. 타입체크를 더 엄격히 할 수 있고 생산성을 향상시킬 수 있습니다.
- (정리 필요 p.180-183) 객체의 속성 이름을 함수 매개변수로 받을 때는 string보다 keyof T를 사용하는 것이 좋습니다.
