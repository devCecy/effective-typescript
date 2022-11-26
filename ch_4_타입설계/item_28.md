# 유효한 상태만 표현하는 타입을 지향하기

효과적으로 타입을 설계하려면, 유효한 상태만 표현할 수 있는 타입을 만들어내는 것이 가장 중요합니다.

</br>

웹 애플리케이션에서 페이지를 선택하면, 페이지의 내용을 로드하고 화면에 표시하는 예 입니다.

페이지의 상태를 다음과 같이 설계했습니다.

```ts
interface State {
	pageText: string;
	isLoading: boolean;
	error?: string;
}
```

페이지를 그리는 render함수를 작성할 때는 상태 객체의 필드를 모두 고려하여 성태 표시를 분기합니다.
하지만, 다음 코드에서는 분기 조건이 명확하지 않습니다. isLoading이 true이면서도 error값이 존재하게된다면 로딩 중인지 오류인지 알 수가 없습니다.

```ts
function renderPage(state: State) {
	if (state.error) {
		return `Error! Unable to load ${currentPage}: ${state.error}`;
	} else if (state.isLoading) {
		return `Loading ${currentPage}...`;
	}
	return `<h1>${currentPage}</h1>\n${state.pageText}`;
}
```

이번에는 페이지를 전환하는 함수입니다.
이 함수에도 오류가 발생했을때 로딩을 false로 만들어 주거나, 로딩 중 페이지를 전환하되면 어떤 일이 일어날지 예상하기 어려운 등의 문제가 있습니다.

```ts
function getUrlForPage(p: string) {
	return "";
}
async function changePage(state: State, newPage: string) {
	state.isLoading = true;
	try {
		const response = await fetch(getUrlForPage(newPage));
		if (!response.ok) {
			throw new Error(`Unable to load ${newPage}: ${response.statusText}`);
		}
		const text = await response.text();
		state.isLoading = false;
		state.pageText = text;
	} catch (e) {
		state.error = "" + e;
	}
}
```

이는 모두 상태 값의 정보를 동시에 알기 어렵거나 상태 값이 충돌하면서 발생하는 문제입니다. 문제를 해결해보겠습니다.

먼저, 네트워크요청 과정 각각의 상태를 명시적으로 모델링하는 태그된 유니온을 사용했습니다. 타입코드의 길이는 길어졌지만 무효한 상태는 허용하지 않게 되었습니다.
그리고 현재 페이지에서 발생하는 요청의 상태를 모두 명시적으로 나타내게 되었습니다.

```ts
interface RequestPending {
	state: "pending"; // 태그된 유니온
}
interface RequestError {
	state: "error"; // 태그된 유니온
	error: string;
}
interface RequestSuccess {
	state: "ok"; // 태그된 유니온
	pageText: string;
}

type RequestState = RequestPending | RequestError | RequestSuccess;

interface State {
	currentPage: string;
	requests: { [page: string]: RequestState };
}
```

renderPage와 changePage함수도 수정해보겠습니다.
이제, 현재페이지가 어디이며 요청상태가 무엇인지 명확해졌습니다.

```ts
function renderPage(state: State) {
	const { currentPage } = state;
	const requestState = state.requests[currentPage];
	switch (requestState.state) {
		case "pending":
			return `Loading ${currentPage}...`;
		case "error":
			return `Error! Unable to load ${currentPage}: ${requestState.error}`;
		case "ok":
			return `<h1>${currentPage}</h1>\n${requestState.pageText}`;
	}
}

async function changePage(state: State, newPage: string) {
	state.requests[newPage] = { state: "pending" };
	state.currentPage = newPage;
	try {
		const response = await fetch(getUrlForPage(newPage));
		if (!response.ok) {
			throw new Error(`Unable to load ${newPage}: ${response.statusText}`);
		}
		const pageText = await response.text();
		state.requests[newPage] = { state: "ok", pageText };
	} catch (e) {
		state.requests[newPage] = { state: "error", error: "" + e };
	}
}
```

</br>

## 요약

- 유효한 상태와 무효한 상태를 둘 다 표횬하는 타입은 혼란을 초래하고 오류를 유발합니다.
- 유효한 상태만 표현하는 타입을 지향해야 합니다.
