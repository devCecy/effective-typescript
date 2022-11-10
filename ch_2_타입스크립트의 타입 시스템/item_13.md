# 타입(type)과 인터페이스(interface)의 차이점 알기

</br>

타입스크립트에서 명명된 타입(named type)을 정의하는 방법에는 두 가지가 있습니다.

```js
// 타입 사용
type TState = {
	name: string,
	capital: string,
};

// 인터페이스 사용
interface IState {
	name: string;
	capital: string;
}
```

</br>

## 타입과 인터페이스의 비슷한 점

### 1) 인덱스 시그니처

`[key: type]: type`와 같이 사용하는 인덱스 시그니처는 타입과 인터페이스 모두 사용 가능합니다.

```js
type TDict = { [key: string]: string };

interface IDict {
	[key: string]: string;
}
```

### 2) 함수 타입

함수 타입도 타입과 인터페이스 모두 사용가능합니다.

```js
type TFn = (x: number) => string;

interface IFn {
	(x: number): string;
}

const toStrT: TFn = (x) => "" + x; // OK
const toStrI: IFn = (x) => "" + x; // OK
```

### 3) 제너릭

제너릭도 타입과 인터페이스로 표현 가능합니다.

```js
type TPair<T> = {
	first: T,
	second: T,
};

interface IPair<T> {
	first: T;
	second: T;
}
```

### 4) 타입 확장

타입 확장 또한 타입과 인터페이스 모두 가능합니다.
다만, 타입체크 시 TStateWithPop는 IState의 타입을 먼저 확인하고 population 속성의 타입을 체크하며, IStateWithPop는 TState와 population 속성의 타입을 한번에 체크합니다.

```js
type TStateWithPop = IState & { population: number };

interface IStateWithPop extends TState {
	population: number;
}
```

### 5) 클래스 구현(implements)

클래스 구현 또한 타입과 인터페이스 모두 가능합니다.

```js
type TState = {
	name: string,
	capital: string,
};
interface IState {
	name: string;
	capital: string;
}

class StateT implements TState {
	name: string = "";
	capital: string = "";
}

class StateI implements IState {
	name: string = "";
	capital: string = "";
}
```

</br>

## 타입과 인터페이스의 차이점

### 1) 유니온 타입o, 유니온 인터페이스x

유니온 '타입'은 있지만, 유니온 '인터페이스'라는 개념은 없습니다.

```js
type AorB = "a" | "b"; // ok

interface AorB { "a" | "b" } // not working!
```

### 2) 튜플(Tuple)

튜플은 길이와 타입이 고정된 배열입니다. 인터페이스를 이용한 튜플보다 타입을 이용한 튜플이 더 간결하고 직관적으로 표현 가능합니다. 튜플은 타입을 이용하여 구현하는 것이 좋습니다.

```js
// type을 이용한 튜플
type Pair = [number, number];
type StringList = string[];
type NamedNums = [string, ...number[]];

// interface를 이용한 튜플
interface Tuple {
  0: number;
  1: number;
  length: 2;
}
const t: Tuple = [10, 20];  // OK
```

### 3) 인터페이스에만 있는 보강 기능

아래와 같이 타입을 보강하는 기능은 인터페이스에서만 가능합니다.

```js
interface IState {
	name: string;
	capital: string;
}
interface IState {
	population: number;
}
const wyoming: IState = {
	name: "Wyoming",
	capital: "Cheyenne",
	population: 500_000,
}; // OK
```

## 요약

타입(type)과 인터페이스(interface)키워드 중 어떤 것을 사용해야 할까요?

- 프로젝트에서 일관되게 사용하고 있는 것을 사용하면됩니다.
- 아직 스타일이 확립되지 않은 프로젝트라면, 추후 보강의 가능성이 있는지(interface), 복잡한 타입을 사용하는지(type), api에 대한 타입 선언을 작성해야 하는지(interface) 등을 고려해야 합니다.
