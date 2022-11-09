# 잉여 속성 체크의 한계 인지하기 (excess-property-checking)

</br>

타입이 명시된 변수에 객체 리터럴을 할당할 때 타입스크립트는 해당 타입의 속성이 있는지, 그리고 <strong>해당 타입의 속성이 아닌것</strong>이 있지는 않은지를 확인합니다.

</br>

Room타입이 명시된 변수 r에 객체 리터럴을 할당하자 Room타입의 속성에 `elephant`가 없어 오류가 발생합니다. 잉여 속성체크가 되었습니다.

```js
interface Room {
	numDoors: number;
	ceilingHeightFt: number;
}
const r: Room = {
	numDoors: 1,
	ceilingHeightFt: 10,
	elephant: "present",
	// ~~~~~~~~~~~~~~~~~~ Object literal may only specify known properties,
	//                    and 'elephant' does not exist in type 'Room'
};
```

사실, 구조적 타이핑 관점에서 보면 오류가 발생하지 않아야 합니다. 아래 예제와 같이 `임시변수`를 도입하면 obj의 타입을 추론하며, obj의 타입이 Room타이의 부분집합을 포함하므로 Room에 할당 가능하고 타입체커를 통과합니다. 잉여 속성체를 건너뛰었습니다.

```js
interface Room {
  numDoors: number;
  ceilingHeightFt: number;
}
const obj = {
  numDoors: 1,
  ceilingHeightFt: 10,
  elephant: 'present',
};
const r: Room = obj;  // OK
```

## 요약

- 객체 리터럴을 변수에 할당하거나 함수에 매개변수로 전달할 때 잉여 속성 체크가 수행됩니다.
- 잉여 속성 체크에는 한계가 있는데 임수변수를 도입하면 잉여 속성 체크를 건너 띌 수 있습니다.
