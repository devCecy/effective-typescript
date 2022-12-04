# 해당 분야의 용어로 타입 이름 짓기

엄선된 타입, 속성, 변수의 이름은 의도를 명확히 하고 코드와 타입의 추상화 수준을 높여 줍니다.
잘못 선택한 타입 이름은 코드의 의도를 왜곡하고 잘못된 개념을 심어주게 됩니다.

</br>

동물들의 데이터베이스를 구축한다고 가정해 보겠습니다. 아래의 코드에는 네가지 문제가 있습니다.

1. name은 매우 일반적인 용어 입니다. 동물의 학명인지 일반적인 명칭인지 알 수 없습니다.
2. endangered 속성이 멸종위기를 표현하기 위함인지 이미 멸종된 동물을 표현하기 위함인지 알 수 없습니다.
3. 서식지를 나타내는 habitat 속성은 너무 범위가 넓은 string타입입니다. 또한 서식지라는 뜻 자체도 모호합니다.
4. 객체의 변수명이 leopard이지만, name속성의 값은 Snow Leopard입니다. 객체의 이름과 속성의 name이 다른 의도로 사용된 것인지 불분명 합니다.

```ts
interface Animal {
	name: string;
	endangered: boolean;
	habitat: string;
}

const leopard: Animal = {
	name: "Snow Leopard",
	endangered: false,
	habitat: "tundra",
};
```

아래와 같이 타입을 명확하게 바꿔주었습니다.

1. name은 commonName, genus, species로 더 구체적인 용어로 대체했습니다.
2. endangered는 동물 보호 등급에 대한 IUCN의 표준 분류 체계인 ConservationStatus 타입의 status로 변경되었습니다.
3. habitat은 기후를 뜻하는 climate로 변경되었으며, 쾨펜 기후 뿐류를 사용합니다.

```ts
interface Animal {
	commonName: string;
	genus: string;
	species: string;
	status: ConservationStatus;
	climates: KoppenClimate[];
}
type ConservationStatus = "EX" | "EW" | "CR" | "EN" | "VU" | "NT" | "LC";
type KoppenClimate =
	| "Af"
	| "Am"
	| "As"
	| "Aw"
	| "BSh"
	| "BSk"
	| "BWh"
	| "BWk"
	| "Cfa"
	| "Cfb"
	| "Cfc"
	| "Csa"
	| "Csb"
	| "Csc"
	| "Cwa"
	| "Cwb"
	| "Cwc"
	| "Dfa"
	| "Dfb"
	| "Dfc"
	| "Dfd"
	| "Dsa"
	| "Dsb"
	| "Dsc"
	| "Dwa"
	| "Dwb"
	| "Dwc"
	| "Dwd"
	| "EF"
	| "ET";
const snowLeopard: Animal = {
	commonName: "Snow Leopard",
	genus: "Panthera",
	species: "Uncia",
	status: "VU", // vulnerable
	climates: ["ET", "EF", "Dfd"], // alpine or subalpine
};
```

자체적으로 용어를 만들어 내려고 하지말고, 해당 분야에 이미 존재하는 용어를 사용해야 합니다.

</br>

## 요약

- 가독성을 높이고 추상화 수준을 올리기 위해서 해당 분야의 용어를 사용해야 합니다.
- 같은 의미에 다른 이름을 붙이면 안 됩니다. 특별한 의미가 있을 때만 용어를 구분해야 합니다.
