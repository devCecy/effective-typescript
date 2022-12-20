# 의존선 분리를 위해 미러 타입 사용하기

## \*참고) 미러링 (Mirroring)

예를들어, 작성 중인 라브러리가 의존하는 라이브러리의 구현과 무관하게 타입에만 의존한다면, <u>필요한 선언부만 추출하여 작성 중인 라이브러리에 넣는 것</u>을 말합니다.

<br/>

csv파일을 파싱하는 라이브러리를 작성한다고 가정해 봅시다.
csv파일의 내용을 매개변수로 받고, NodeJS사용자를 위해 매개변수에 Buffer타입을 허용합니다. Buffer타입은 @types/node를 설치해 사용할 수 있습니다.

```ts
function parseCSV(contents: string | Buffer): { [column: string]: string }[] {
	if (typeof contents === "object") {
		// It's a buffer
		return parseCSV(contents.toString("utf8"));
	}
	// COMPRESS
	return [];
	// END
}
```

위의 csv파싱 라이브러리를 공개하게되면 @types/node에 의존하게 됩니다. 그러므로 devDependencies에 포함해야 하는데 그렇게 되면 다음 두 그룹의 사용자들에게 문제가 생깁니다.

- @types와 무관한 자바스크립트 개발자
- NodeJS와 무관한 타입스크립트 웹 개발자

위 문제를 해결하기 위해서 Buffer를 선언하는 대신 CsvBuffer라는 인터페이스를 만들어 사용해 줄 수 있습니다.

```ts
interface CsvBuffer {
	toString(encoding: string): string;
}
function parseCSV(
	contents: string | CsvBuffer
): { [column: string]: string }[] {
	// COMPRESS
	return [];
	// END
}
```

CsvBuffer는 Buffer 인터페이스에서 미러링 해온 것으로 NodeJS프로젝트에서 Buffer인스턴스로 parseCSV를 호출하는 것이 가능합니다.

```ts
parseCSV(new Buffer("column1,column2\nval1,val2", "utf-8")); // OK
```

<br/>

## 요약

- 필수가 아닌 의존성을 분리할 때는 구조적 타이핑을 사용하면 됩니다.
- 공개한 라이브러리를 사용하는 자바스크립트 사용자가 @types 의존성을 가지지 않게 해야 합니다. 그리고 웹 개발자가 NodeJS 관련된 의존성을 가지지 않게 해야 합니다.
