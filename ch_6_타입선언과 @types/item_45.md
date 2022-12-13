# devDependencies에 typescript와 @types 추가하기

1. dependencies
   현재 프로젝트를 실행하는 데 필수적인 라이브러리들이 포함됩니다.

2. devDependencies
   현재 프로젝트를 개발하고 테스트하는 데 사용되지만, 런타임에는 필요 없는 라이브러리들이 포함됩니다.

3. peerDependencies
   런타임에 필요하긴 하지만, 의존성으르 직접 관리하지 않는 라이브러리들이 포함됩니다.

## 요약

- 타입스크립트를 시스템 레벨 (dependencies)에 설치하면 안됩니다. 타입스크립트를 프로젝트의 devDependencies에 포함시키고 팀원 모두가 동일한 버전을 사용해야 합니다.
- @types 의존성 또한 devDependencies에 포함시켜야 합니다.
