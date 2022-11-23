# 비동기 코드에는 콜백대신 async함수 사용하기

콜백보다는 프로미스가 타입을 추론하기 쉽습니다.

</br>

`Promise.all`을 사용하면 병열로 페이지를 로드할 수 있습니다. 3가지 response는 각각 `Response` 타입이 추론됩니다.

```ts
async function fetchPages() {
	const [response1, response2, response3] = await Promise.all([
		fetch(url1),
		fetch(url2),
		fetch(url3),
	]);
	// ...
}
```

</br>

`Promise.race`는 입력된 프로미스들 중 첫 번째가 처리될 때 완료됩니다. 다음 코드는 타입 구문이 없어도 fetchWithTimeout의 반환 타입이 Promise<Response>로 추론됩니다.

```ts
function timeout(millis: number): Promise<never> {
	return new Promise((resolve, reject) => {
		setTimeout(() => reject("timeout"), millis);
	});
}

async function fetchWithTimeout(url: string, ms: number) {
	return Promise.race([fetch(url), timeout(ms)]);
}
```

setTimeout같은 콜백 api를 래핑하는 경우와 같이 프로미스를 직접 생성해야 할 때는 직접 프로미스를 생성하기 보다는 async/await을 사용하는 것이 좋습니다. 그 이유는 일단 코드가 간결하고 직관적이며, 타입적으로는 async함수는 항상 프로미스를 반환하도록 강제되기 때문입니다.

```ts
// function getNumber(): Promise<number>
async function getNumber() {
	return 42;
}

const getNumber = async () => 42; // Type is () => Promise<number>
```

async함수는 프로미스를 반환하면 또 다른 프로미스로 래핑되지 않습니다. 반환 타입은 Promise<Promise<T>>가 아니라 <Promise<T>입니다.

## 요약

- 콜백보다는 프로미스를 사용하는것이 코드 작성과 타입 추론 면에서 유리합니다.
- 가능하면 프로미스를 생성하기보다는 async await을 사용하는 것이 좋습니다. 간결하고 직관적으로 코드 작성이 가능하며 모든 종류의 오류를 제거할 수 있습니다.
- 어떤 함수가 프로미스를 반환한다면 async로 선언하는 것이 좋습니다.
