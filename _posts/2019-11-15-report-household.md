---
title: "FE 과제 - 가계부 만들기"
date: 2019-10-20 20:29:28 +0900
categories: react report household
---

오늘은 `React`를 이용하여 학교 Front-End 과제를 해보려 한다.

사실 교수님께서 앵귤러 혹은 `XML`을 이용하여 제작하라고 하셨는데 `React`를 이용해 만들어도 되겠냐고 여쭤보니 채점하기 어렵다고 하시다가 고심끝에 가능하다고 하셨다. ~~교수님 감사합니다.😂~~

그래서 웬만하면 새로운 기술을 도입하지 않고 `React` + `TypeScript` 정도로만 제작해보려한다! (~~사실 `TypeScript`도 빼야할거같지만 이제 막 배우는 중이라 빼고싶지않다...~~)

# 요구사항

![report household draft](/assets/images/report-household-draft.png)

1. 개발은 아래의 두 가지 방법 중 하나를 선택 합니다.
   - `JavaScript` + `HTML` + `CSS`
   - ~~`XML` + `XSL`~~
2. 빨강색 박스의 내용은 계산합니다.
   - 개수 : 날짜별 구입한 물건의 수
   - 총 지출 : 날짜별 품목 가격의 합
   - 잔액 : `날짜별 수입` - `총 지출`
     > 단, 잔액이 마이너스일 경우 `2019/12/03`일의 경우처럼 빨강색으로 `[적자]`라는 표시와 금액을 표시할 것)
3. 기타
   - 금액란은 천원단위 편집
   - 각 항목은 예시 화면처럼 정렬(중앙, 좌측, 우측)
   - 날짜별로 구입처별 내림차별 정렬하여 표시(데이터는 무작위 입력)

위 기능을 모두 구현하고 아이디어나 기능이 추가 되면 추가 점수를 주신다고 하셨다. 나같은 경우에는 로컬스토리지를 이용하여 추가 삭제 기능을 구현해보려한다!

# 기술 스택

- React
- TypeScript
- styled-components

`TypeScript`는 거의 사용해본 적이 없지만 앞으로 계속 써보려고 한다

# 구현

## 프로젝트 생성

먼저 `create-react-app`으로 프로젝트를 생성해준다.

```bash
$ create-react-app household --typescript
```

> `--typescript` 를 사용하면 타입스크립트가 적용된 리액트 앱이 생성된다.

그리고 `styled-components`를 설치한다.

```bash
$ yarn add styled-components

$ yarn add @types/styled-components
```

> `@types`로 시작하는 패키지는 `TypeScript` 언어에서 해당 패키지를 사용하기 위한 확장 패키지이다.
>
> `JavaScript`로 개발된 패키지는 `TypeScript`에서도 사용할 수 있지만 `TypeScript`로 `declare` 되어 있어야 한다. 하지만 기존의 패키지들은 `JavaScript`로 `export` 되어 있기 때문에 `TypeScript`에서 사용하려면 반드시 `TypeScript`에서 declare 해주어야 사용이 가능하다.

`src`폴더를 깔끔하게 정리했다.

![src](/assets/images/report-household-src.png)
