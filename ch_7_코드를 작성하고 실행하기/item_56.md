# 정보를 감추는 목적으로 private사용하지 않기

타입스크립트에는 public, protected, private 접근 제어자를 사용해서 공개규칙을 강제할 수 있는 것으로 오해할 수 있습니다.

```ts
class Diary {
	private secret = "cheated on my English test";
}

const diary = new Diary();
diary.secret;
// ~~~~~~ Property 'secret' is private and only
//        accessible within class 'Diary'
```

그러나 public, protected, private 접근 제어자는 타입스크립트 키워드이기 때문에 컴파일 후에는 제거됩니다. 위 코드를 자바스크립트 코드로 변환하면 다음과 같습니다.
private키워드는 사라졌고, secret은 일반적인 속성이므로 접근 가능합니다.

```ts
Class Diary {
  constructor(){
    this.secret = "cheated on my English test";
  }
}

const diary = new Diary();
diary.secret;
```

타입스크립트의 접근 제어자들은 단지 컴파일 시점에만 오류를 표시해 줄 뿐이며, 런타임에는 아무런 효력이 없습니다.
심지어 단언문(as any)을 사용하면 타입스크립트 상태에서도 private 속성에 접근 할 수 있습니다.

```ts
class Diary {
	private secret = "cheated on my English test";
}

const diary = new Diary();
(diary as any).secret; // OK
```

자바스크립트의 정보를 숨기기 위해서는,

## 1. `클로저`를 사용

```ts
declare function hash(text: string): number;

class PasswordChecker {
	checkPassword: (password: string) => boolean;
	constructor(passwordHash: number) {
		this.checkPassword = (password: string) => {
			return hash(password) === passwordHash;
		};
	}
}
const checker = new PasswordChecker(hash("s3cret"));
checker.checkPassword("s3cret"); // 결과는 true
```

## 2. 접두사로 `#`을 붙여 사용하는 비공개 필드 기능 사용

```ts
class PasswordChecker {
	#passwordHash: number;

	constructor(passwordHash: number) {
		this.passwordHash = passwordHash;
	}

	checkPassword(password: string) {
		return hash(password) === this.#passwordHash;
	}
}

const checker = new PasswordChecker(hash("s3cret"));
checker.checkPassword("secret"); // 결과는 false
checker.checkPassword("s3cret"); // 결과는 true
```

## 요약

- public, protected, private 접근 제어자는 타입 시스템에서만 강제될 뿐입니다. 런타임에서는 소용이 없으며 단언문을 통해 우회할 수 있습니다. 접근 제어자로 데이터를 감추려고 해서는 안됩니다.
- 확실히 데이터를 감추고 싶다면 클로저를 사용해야 합니다.
