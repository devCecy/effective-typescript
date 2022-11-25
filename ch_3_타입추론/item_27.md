# 함수형 기법과 라이브러리로 타입 흐름 유지하기

</br>

## 로대시의 zipObject

csv 데이터를 파싱하는 코드입니다.
절차형 프로그래밍과 함수형 reduce로 구현 가능하지만 오류가 발생합니다.

```ts
const csvData = "...";
const rawRows = csvData.split("\n");
const headers = rawRows[0].split(",");
import _ from "lodash";
const rowsA = rawRows.slice(1).map((rowStr) => {
	const row = {};
	rowStr.split(",").forEach((val, j) => {
		row[headers[j]] = val;
		// ~~~~~~~~~~~~~~~ No index signature with a parameter of
		//                 type 'string' was found on type '{}'
	});
	return row;
});
const rowsB = rawRows.slice(1).map((rowStr) =>
	rowStr.split(",").reduce(
		(row, val, i) => ((row[headers[i]] = val), row),
		// ~~~~~~~~~~~~~~~ No index signature with a parameter of
		//                 type 'string' was found on type '{}'
		{}
	)
);
```

그러나 `로대시의 zipObject`를 사용하면 오류없이 타입체커를 통과 합니다.

```ts
// requires node modules: @types/lodash

const csvData = "...";
const rawRows = csvData.split("\n");
const headers = rawRows[0].split(",");
import _ from "lodash";
const rows = rawRows
	.slice(1)
	.map((rowStr) => _.zipObject(headers, rowStr.split(",")));
// Type is _.Dictionary<string>[]
```

</br>

## flat 메서드

NBA 팀의 선수 명단을 목록으로 만들려고 합니다.

concat을 이용하면 코드 동작은 가능하지만 타입체크는 실패합니다.

```ts
const csvData = "...";
const rawRows = csvData.split("\n");
const headers = rawRows[0].split(",");
import _ from "lodash";
interface BasketballPlayer {
	name: string;
	team: string;
	salary: number;
}

declare const rosters: { [team: string]: BasketballPlayer[] };
let allPlayers = [];
// ~~~~~~~~~~ Variable 'allPlayers' implicitly has type 'any[]'
//            in some locations where its type cannot be determined
for (const players of Object.values(rosters)) {
	allPlayers = allPlayers.concat(players);
	// ~~~~~~~~~~ Variable 'allPlayers' implicitly has an 'any[]' type
}
```

이 문제는 allPlayers에 타입구문을 추가함으로 해결할 수 있지만 더 나은 방법은 Array.prototype.flat을 사용하는 것입니다.

```ts
declare const rosters: { [team: string]: BasketballPlayer[] };
const allPlayers = Object.values(rosters).flat();
// OK, type is BasketballPlayer[]
```

</br>

## 로대시

allPlayers 데이터를 사용하여 각 팀별 연봉 순을 정렬해 봅시다.
로대시를 사용하지 않은 방법에서는 타입구문이 필요합니다.

```ts
const csvData = "...";
const rawRows = csvData.split("\n");
const headers = rawRows[0].split(",");
import _ from "lodash";
interface BasketballPlayer {
	name: string;
	team: string;
	salary: number;
}
declare const rosters: { [team: string]: BasketballPlayer[] };
const allPlayers = Object.values(rosters).flat();
// OK, type is BasketballPlayer[]
const teamToPlayers: { [team: string]: BasketballPlayer[] } = {};
for (const player of allPlayers) {
	const { team } = player;
	teamToPlayers[team] = teamToPlayers[team] || [];
	teamToPlayers[team].push(player);
}

for (const players of Object.values(teamToPlayers)) {
	players.sort((a, b) => b.salary - a.salary);
}

const bestPaid = Object.values(teamToPlayers).map((players) => players[0]);
bestPaid.sort((playerA, playerB) => playerB.salary - playerA.salary);
console.log(bestPaid);
```

로대시를 사용하면 코드길이는 절반으로 줄고, 깔끔하며 더 자연스러운 순서로 작성할 수 있습니다.

```ts
declare const rosters: { [team: string]: BasketballPlayer[] };
const allPlayers = Object.values(rosters).flat();
// OK, type is BasketballPlayer[]
const bestPaid = _(allPlayers)
	.groupBy((player) => player.team)
	.mapValues((players) => _.maxBy(players, (p) => p.salary)!)
	.values()
	.sortBy((p) => -p.salary)
	.value(); // Type is BasketballPlayer[]
```

</br>

## 요약

- 타입 흐름을 개선하고 가독성을 높이며, 명시적인 타입 구문의 필요성을 줄이기 위해서는 직접 구현보다는 내장된 함수형 기법과 로대시 같은 유틸리티 라이브러리를 사용하는 것이 좋습니다.
