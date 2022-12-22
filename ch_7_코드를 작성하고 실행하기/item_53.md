# 타입스크립트 기능보다는 ECMAScript 기능을 사용하기

타입스크립트가 태동하던 2010년경, 자바스크립트는 개선해야할 부분이 많은 언어였습니다. 그렇기 때문에 타입스크립트 초기 버전에는 독립적으로 개발한 클래스, 열거형(enum), 모듈 시스템이 포함되어있었습니다.

시간이 흐르면서 TC39(자바스크립트를 관장하는 표준 기구)는 부족했던 부분을 내장 기능으로 추가했는데, 이 부분들이 타입스크립트 초기 버전에서 독립적으로 개발했던 기능과 호환성 문제를 일으켰습니다.

타입스크립트는 자바스크립트의 신규기능을 그대로 채택하고 타입스크립트 초기버전과의 호환성을 포기하는 선택을 했습니다. TC39는 런타임 기능을 발전시키고, 타입스크립트는 타입 기능만 발전시킨다는 명확한 원칙을 세운것입니다.

이 원칙이 세워지기 전 이미 사용되던 기능들은 타입공간과 값 공간의 경계를 혼란스럽게 만들기 때문에 사용하지 않는것이 좋습니다.
사용하지 말아야 할 기능들에 대해서 알아보겠습니다.

## 1) 열거형 (enum) -> 리터럴 타입의 유니온

값의 모음을 나타내기 위한 `열거형(enum)`대신 `리터럴 타입의 유니온`을 사용하는 것이 좋습니다.

```ts
// enum
enum Flavor {
	VANILLA = 0,
	CHOCOLATE = 1,
	STRAWBERRY = 2,
}

let flavor = Flavor.CHOCOLATE; // Type is Flavor

Flavor; // Autocomplete shows: VANILLA, CHOCOLATE, STRAWBERRY
Flavor[0]; // Value is "VANILLA"
```

```ts
// 리터럴 타입의 유니온

type Flavor = "vanilla" | "chocolate" | "strawberry";

let flavor: Flavor = "chocolate"; // OK
flavor = "mint chip";
// ~~~~~~ Type '"mint chip"' is not assignable to type 'Flavor'
```

## 2) 매개변수 속성 -> 인터페스 + 객체 리터럴

클래스를 초기화할 때 속성을 할당하기 위해 생성자의 매개변수를 사용합니다.
public name부분을 `매개변수 속성`이라고 부릅니다.

```ts
class Person {
	constructor(public name: string) {}
}
```

클래스에 매개변수 속성만 존재한다면 클래스 대신 인터페이스로 만들고 객체 리터럴을 사용하는 것이 좋습니다.

```ts
// TODO: 인터페이스로 만들고 객채리터럴을 사용한 예시?
interface Person {
	name;
}
```

## 3) 네임스페이스와 트리플 슬래시 임포트 -> ECMAScript module

ECMAScript 2015 이전에는 자바스크립트에 공식적인 모듈 시스템이 없었습니다.
그래서 각 환경마다 자신만의 방식으로 모듈시스템을 만들었는데, 타입스크립트는 `module키워드`와 `트리플 슬래시 임포트`를 사용했습니다. 추후 ECMAScript 2015가 공식적으로 module시스템을 도입한 이후 충돌을 피하기 위해 namespace 키워드를 추가했습니다.

이는 현재 호환성을 위해 남아있을 뿐 이제는 모두 ECMAScript 2015 스타일의 모듈(import, export)을 사용해야 합니다.

```ts
// namesapce
namespace foo {
	function bar() {}
}

// 트리플 슬래시 임포트
/// <reference path="other.ts"/>
```

## 4) 데코레이터

데코레이터는 클래스, 메서드, 속성에 애너테이션을 붙이거나 기능을 추가하는 데 사용할 수 있습니다.
앵귤러를 사용하거나 애너테이션이 필요한 프레임워크를 사용하고 있는 게 아니라면 데코레이터가 표준이 되기 전에는 타입스크립트에서 데코레이터를 사용하지 않는 것이 좋습니다.

```ts
// tsConfig: {"experimentalDecorators":true}

class Greeter {
	greeting: string;
	constructor(message: string) {
		this.greeting = message;
	}
	@logged // 데코레이터
	greet() {
		return "Hello, " + this.greeting;
	}
}

function logged(target: any, name: string, descriptor: PropertyDescriptor) {
	const fn = target[name];
	descriptor.value = function () {
		console.log(`Calling ${name}`);
		return fn.apply(this, arguments);
	};
}

console.log(new Greeter("Dave").greet());
// Logs:
// Calling greet
// Hello, Dave
```

## 요약

- 타입스크립트의 역할을 명확하게 하려면, 열거형, 매개변수 속성, 트리플 슬래시 임포트, 데코레이터는 사용하지 않는 것이 좋습니다.
