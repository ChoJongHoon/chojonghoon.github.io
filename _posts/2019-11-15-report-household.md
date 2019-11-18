---
title: "FE 과제 - 가계부 만들기"
date: 2019-10-20 20:29:28 +0900
categories: react report household
---

오늘은 `React`를 이용하여 학교 Front-End 과제를 해보려 한다.

사실 교수님께서 앵귤러 혹은 `XML`을 이용하여 제작하라고 하셨는데 `React`를 이용해 만들어도 되겠냐고 여쭤보니 채점하기 어렵다고 하시다가 고심끝에 가능하다고 하셨다. ~~교수님 감사합니다.😂~~

그래서 웬만하면 새로운 기술을 도입하지 않고 `React` + `TypeScript` 정도로만 제작해보려한다! (~~사실 `TypeScript`도 빼야할거같지만 이제 막 배우는 중이라 빼고싶지않다...~~)

# 요구사항

![draft](/assets/images/report-household-draft.png)

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

# 설계

**data.json**

```json
{
  "data": [
    {
      "date": "20191202",
      "income": 200000,
      "expenses": [
        {
          "name": "볼펜",
          "price": 1000,
          "place": "인하문구"
        },
        {
          "name": "시계",
          "price": 100000,
          "place": "인하금방"
        },
        {
          "name": "책",
          "price": 10000,
          "place": "공룡서점"
        },
        {
          "name": "노트",
          "price": 10000,
          "place": "인하문구"
        }
      ]
    },
    {
      "date": "20191201",
      "income": 300000,
      "expenses": [
        {
          "name": "우유",
          "price": 6000,
          "place": "인하슈퍼"
        },
        {
          "name": "과자",
          "price": 10000,
          "place": "인하슈퍼"
        },

        {
          "name": "콜라",
          "price": 1500,
          "place": "인하슈퍼"
        }
      ]
    },

    {
      "date": "20191203",
      "income": 2000000,
      "expenses": [
        {
          "name": "인형",
          "price": 10000,
          "place": "인하슈퍼"
        },
        {
          "name": "껌",
          "price": 100,
          "place": "인하슈퍼"
        },
        {
          "name": "CD",
          "price": 5000,
          "place": "신나라레코드"
        },
        {
          "name": "모니터",
          "price": 105000,
          "place": "컴퓨터세상"
        },
        {
          "name": "빵",
          "price": 1000,
          "place": "빵시장"
        },
        {
          "name": "콩나물",
          "price": 1000,
          "place": "동네슈퍼"
        },
        {
          "name": "컴퓨터",
          "price": 2000000,
          "place": "컴퓨터세상"
        }
      ]
    }
  ]
}
```

sort 기능이 추가되어야하므로 데이터의 순서는 무작위로 섞어 작성하였다.

## 컴포넌트 나누기

![components](/assets/images/report-household-components.png)

1. **Household(빨간색) :** 가계부 테이블을 감싸줍니다.
2. **Daily(주황색) :** 하루의 가계부를 보여줍니다.
3. **Expense(보라색) :** 하나의 지출을 보여줍니다.

일단은 이렇게 세개의 컴포넌트만 있으면 될거같다. `추가`, `삭제` 기능은 먼저 기본 기능을 다 구현 후 추가해주도록 하자.

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
> `JavaScript`로 개발된 패키지는 `TypeScript`에서도 사용할 수 있지만 `TypeScript`로 `declare` 되어 있어야 한다. 하지만 기존의 패키지들은 `JavaScript`로 `export` 되어 있기 때문에 `TypeScript`에서 사용하려면 반드시 `TypeScript`에서 `declare` 해주어야 사용이 가능하다.

`src`폴더를 깔끔하게 정리했다.

![src](/assets/images/report-household-src.png)

`lib` 폴더를 만들어주고 안에 `data.json`을 넣어주었다. 라이브러리 폴더에는 공용으로 사용되는 함수들을 넣어줄 것이다.

## Household 컴포넌트 만들기

`Household` 컴포넌트는 단순히 가계부 table을 감싸주는 역할을 한다.

**src/components/Household.tsx**

```tsx
import React from "react";
import styled from "styled-components";

const Wrapper = styled.table`
  /* Todo: css 추가 */
`;

type HouseholdProps = {
  children: JSX.Element[];
};

export default function Household({ children }: HouseholdProps) {
  return (
    <Wrapper>
      <caption>가계부</caption>
      {children}
    </Wrapper>
  );
}
```

## Daily 컴포넌트 작성

`Daily` 컴포넌트는 지출들을 `children`으로 받아 뿌려주고 하루동안의 통계를 나타낸다.

먼저 Daily컴포넌트를 작성하기 전에 두 가지 함수를 만들어줄 것이다.

1. **formatDate :** 날짜형식을 변환해주는 함수
   > ex) `20191116` -> `2019/11/16`
2. **formatMoney :** 금액은 천원단위로 쉼표를 찍어주는 함수
   > ex) `2000000` -> `2,000,000`

**src/lib/formatDate.ts**

```ts
const formatDate = (date: string) => {
  const year = date.substr(0, 4);
  const month = date.substr(4, 2);
  const day = date.substr(6, 2);
  return year + "/" + month + "/" + day;
};

export default formatDate;
```

**src/lib/formatMoney.ts**

```ts
const formatMoney = (money: number) =>
  money.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");

export default formatMoney;
```

이제 `Daily` 컴포넌트를 작성해주자.

**src/componets/Daily.tsx**

```tsx
import React from "react";
import styled from "styled-components";
import formatDate from "../lib/formatDate";
import formatMoney from "../lib/formatMoney";

const IndexTd = styled.td`
  background: #0000ff;
  color: #ffffff;
`;

const GreenTd = styled.td`
  background: #00ff00;
  color: #000000;
  text-align: ${props => props.align};
`;

const LimeTd = styled.td`
  background: #bfff00;
  color: #000000;
  text-align: ${props => props.align};
`;

type DailyProps = {
  index: number;
  date: string;
  income: number;
  total: number;
  children: JSX.Element[];
};

export default function Daily({
  index,
  date,
  income,
  total,
  children
}: DailyProps) {
  return (
    <tbody>
      <tr>
        <IndexTd rowSpan={children.length + 5}>{index}</IndexTd>
        <GreenTd align="center">날짜:{formatDate(date)}</GreenTd>
        <GreenTd align="center">수입</GreenTd>
        <GreenTd align="left" colSpan={2}>
          {formatMoney(income)}
        </GreenTd>
      </tr>
      <tr>
        <GreenTd align="center">번호</GreenTd>
        <GreenTd align="center">품목</GreenTd>
        <GreenTd align="center">가격</GreenTd>
        <GreenTd align="center">구입처</GreenTd>
      </tr>
      {children}
      <tr>
        <LimeTd align="center">개수</LimeTd>
        <LimeTd align="left" colSpan={3}>
          {children.length}
        </LimeTd>
      </tr>
      <tr>
        <LimeTd align="center">총지출</LimeTd>
        <LimeTd align="left" colSpan={3}>
          {formatMoney(total)}
        </LimeTd>
      </tr>
      <tr>
        <LimeTd align="center">잔액</LimeTd>
        <LimeTd align="left" colSpan={3}>
          {formatMoney(income - total)}
        </LimeTd>
      </tr>
    </tbody>
  );
}
```

![tbody](https://i.stack.imgur.com/eLZvt.png)

`table` 태그를 보다 의미에 맞는 시멘틱 마크 업을 하기 위해 `tbody`를 꼭 써준다. (~~처음에 안써주었더니 `warnning` 뿜뿜하더라~~)

## Expense 컴포넌트 작성

`Expense` 컴포넌트는 하나의 지출(row)을 보여준다.

하나의 함수가 더 필요하다.

1. **formatRoman :** 숫자를 로마숫자로 바꿔준다.
   > ex) `4` -> `IV`

**src/lib/formatRoman.ts**

```tsx
const formatRoman = (num: number): string => {
  let roman = "";
  const romanNumList: { [key: string]: number } = {
    M: 1000,
    CM: 900,
    D: 500,
    CD: 400,
    C: 100,
    XC: 90,
    L: 50,
    XV: 40,
    X: 10,
    IX: 9,
    V: 5,
    IV: 4,
    I: 1
  };
  let a;
  for (let key in romanNumList) {
    a = Math.floor(num / romanNumList[key]);
    if (a >= 0) {
      for (let i = 0; i < a; i++) {
        roman += key;
      }
    }
    num = num % romanNumList[key];
  }

  return roman;
};

export default formatRoman;
```

자바스크립트를 이용해 로마숫자로 바꾸는 함수를 검색해서 가져오고 TypeScript에 맞게 조금 바꿔주었다. [(참조)](https://blog.usejournal.com/create-a-roman-numerals-converter-in-javascript-a82fda6b7a60)

이제 `Expense`함수를 작성해주자.

**src/components/Extense.tsx**

```tsx
import React from "react";
import styled from "styled-components";
import formatRoman from "../lib/formatRoman";
import formatMoney from "../lib/formatMoney";

const YellowTd = styled.td`
  background: #ffff00;
  color: #000000;
  text-align: ${props => props.align};
`;

type ExpenseProps = {
  index: number;
  name: string;
  price: number;
  place: string;
};

export default function Expense({ index, name, price, place }: ExpenseProps) {
  return (
    <tr>
      <YellowTd align="center">{formatRoman(index)}.</YellowTd>
      <YellowTd align="left">{name}</YellowTd>
      <YellowTd align="left">{formatMoney(price)}</YellowTd>
      <YellowTd align="left">{place}</YellowTd>
    </tr>
  );
}
```
