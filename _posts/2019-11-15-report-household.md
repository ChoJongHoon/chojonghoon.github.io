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

1. **<span style="color: #FF0000">Household(빨간색)</span> :** 가계부 테이블을 감싸줍니다.
2. **<span style="color: #FFC000">Daily(주황색)</span> :** 하루의 가계부를 보여줍니다.
3. **<span style="color: #70309F">Expense(보라색)</span> :** 하나의 지출을 보여줍니다.

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
const formatDate = (date: string): string => {
  const year = date.substr(0, 4);
  const month = date.substr(4, 2);
  const day = date.substr(6, 2);
  return year + "/" + month + "/" + day;
};

export default formatDate;
```

**src/lib/formatMoney.ts**

```ts
const formatMoney = (money: number): string =>
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

자바스크립트를 이용해 로마숫자로 바꾸는 함수를 검색해서 가져오고[(참조)](https://blog.usejournal.com/create-a-roman-numerals-converter-in-javascript-a82fda6b7a60) TypeScript에 맞게 조금 바꿔주었다.

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

## 작성한 컴포넌트들 App 컴포넌트에서 사용하기

이제 열심히 만들어준 컴포넌트들을 `App`에서 뿌려보자.

**src/App.tsx**

```tsx
import React from "react";
import { data } from "./lib/data.json";

// components
import Household from "./components/Household";
import Daily from "./components/Daily";
import Expense from "./components/Expense";

function App() {
  return (
    <div>
      <Household>
        {data.map((daily, idx) => (
          <Daily
            key={idx}
            index={idx + 1}
            date={daily.date}
            income={daily.income}
            total={daily.expenses.reduce((acc, cur) => acc + cur.price, 0)}
          >
            {daily.expenses.map((expense, idx) => (
              <Expense
                key={idx}
                index={idx + 1}
                name={expense.name}
                price={expense.price}
                place={expense.place}
              ></Expense>
            ))}
          </Daily>
        ))}
      </Household>
    </div>
  );
}

export default App;
```

![view1](/assets/images/report-household-view1.png)

이렇게 뷰가 나왔다! 하지만 아직 갈 길이 멀다.(~~못생겼다.~~)

남은 할 일 목록

1. 잔액 마이너스일 경우 빨간색으로 `[적자]` 표시
2. 날짜별 구입처별 정렬
3. `추가`, `삭제` 기능 추가
4. `local storage` 연동
5. ~~디자인 수정...?~~

## 잔액 마이너스 표시

이번 작업은 `Daily` 컴포넌트만 작업해주면 된다.

styled components에 `minus`라는 `props`를 `boolean` 타입으로 전달하여 css 작업을 해줄 것이다.

**src/components/Daily.tsx**

```tsx
(... 생략)

const LimeTd = styled.td`
  background: #bfff00;
  color: ${props => (props.minus ? "#FF0000" : "#000000")};
  text-align: ${props => props.align};
`;

(...생략)

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
        <LimeTd align="left" colSpan={3} minus={income < total}>
          {income < total ? "[적자]" : null}
          {formatMoney(income - total)}
        </LimeTd>
      </tr>
    </tbody>
  );

(...생략)
```

이렇게 해주니 타입스크립트 에러가 난다.

![minus-error](/assets/images/report-household-minus-error.png)

`td`에는 `minus`라는 `props`를 줄 수 없다는 에러인듯 하다.

**src/components/Daily.tsx**

```tsx
(...생략)

type LimeTdPropsType = {
  minus?: boolean;
};

const LimeTd = styled.td<LimeTdPropsType>`
  background: #bfff00;
  color: ${props => (props.minus ? "#FF0000" : "#000000")};
  text-align: ${props => props.align};
`;

(...생략)
```

다음과 같이 타입을 정의해주고 `styled.td`에 `Generic`으로 부여해주면 에러가 사라진다.

![view2](/assets/images/report-household-view2.png)

원하는 대로 결과가 나왔다.

## 날짜별, 구입처별 내림차순 정렬

자바스크립트 내장 함수인 `sort`를 이용해 정렬을 해줄 것이다.

정렬할 기준이 숫자였다면 아래와 같이 마이너스 연산을 해주면 된다.

```js
data.sort((a, b) => a.date - b.date);
```

날쩌와 구입처 모두 문자열이기 때문에 마이너스 연산이 되지 않는다. 하지만 비교 연산은 가능하기 때문에 이런 식으로 해주면 된다.

```js
data.sort((a, b) => {
  if (a.date > b.date) return 1;
  else if (b.date > a.date) return -1;
  else return 0;
});
```

**src/App.tsx**

```tsx
import React from "react";
import { data } from "./lib/data.json";

// components
import Household from "./components/Household";
import Daily from "./components/Daily";
import Expense from "./components/Expense";

function App() {
  const sortedData = data
    .sort((a, b) => {
      // 날짜별 정렬
      if (a.date > b.date) return 1;
      else if (b.date > a.date) return -1;
      else return 0;
    })
    .map(daily => {
      const sortedExpenses = daily.expenses.sort((a, b) => {
        // 구입처별 내림차순 정렬
        if (a.place > b.place) return -1;
        else if (b.place > a.place) return 1;
        else return 0;
      });
      return {
        ...daily,
        expenses: sortedExpenses
      };
    });

  return (
    <div>
      <Household>
        {sortedData.map((daily, idx) => (
          <Daily
            key={idx}
            index={idx + 1}
            date={daily.date}
            income={daily.income}
            total={daily.expenses.reduce((acc, cur) => acc + cur.price, 0)}
          >
            {daily.expenses.map((expense, idx) => (
              <Expense
                key={idx}
                index={idx + 1}
                name={expense.name}
                price={expense.price}
                place={expense.place}
              />
            ))}
          </Daily>
        ))}
      </Household>
    </div>
  );
}

export default App;
```

![view2](/assets/images/report-household-view3.png)

원하는 대로 정렬이 되었다.

## 추가 기능 만들기

오른쪽 화면에 추가 폼을 만들 것이다. 난 디자인 감각이 부족하니 [material-ui](https://material-ui.com/)를 써주도록 하자.

먼저 설치를 해줘야한다.

```bash
yarn add @material-ui/core

yarn add @material-ui/pickers
yarn add @date-io/date-fns
yarn add date-fns@next
yarn add date-fns
```

아래쪽 네개는 날짜를 선택하는 폼을 위해 설치해주었다.

이제 Form 컴포넌트를 생성해보자.

**src/components/Form.tsx**

```tsx
import React, { useState } from "react";
import DateFnsUtils from "@date-io/date-fns";
import koLocale from "date-fns/locale/ko";
import {
  MuiPickersUtilsProvider,
  KeyboardDatePicker
} from "@material-ui/pickers";
import { TextField, InputAdornment, Button } from "@material-ui/core";
import { createStyles, makeStyles, Theme } from "@material-ui/core/styles";
import styled from "styled-components";

const Wrapper = styled.div`
  flex: 1;
  text-align: center;
`;

const useStyles = makeStyles((theme: Theme) =>
  createStyles({
    textField: {
      maxWidth: 300
    },
    button: {
      marginTop: theme.spacing(3),
      maxWidth: 300
    }
  })
);

type Data = {
  date: string;
  income: number;
  expenses: {
    name: string;
    price: number;
    place: string;
  }[];
}[];

type FormProps = {
  data: Data;
  setData: (data: Data) => void;
};

export default function Form({ data, setData }: FormProps) {
  const [date, setDate] = useState<Date | null>(new Date());
  const [name, setName] = useState("");
  const [price, setPrice] = useState("");
  const [place, setPlace] = useState("");
  const classes = useStyles();

  const handleAdd = (): void => {
    if (!date) {
      // 오류
      return;
    }
    if (isNaN(Number(price))) {
      // 오류
      return;
    }

    const year = date.getFullYear().toString();
    const month = String(date.getMonth() + 1);
    const day = date.getDate().toString();

    const strDate =
      year + (month[1] ? month : "0" + month) + (day[1] ? day : "0" + day);

    const selectDataIndex = data.findIndex(daily => daily.date === strDate);

    if (selectDataIndex === -1) {
      setData([
        ...data,
        {
          date: strDate,
          income: 0,
          expenses: [
            {
              name,
              price: Number(price),
              place
            }
          ]
        }
      ]);
    } else {
      const filteredData = data.filter(daily => daily.date !== strDate);
      const selectData = data[selectDataIndex];
      selectData.expenses.push({ name, price: Number(price), place });
      setData([...filteredData, selectData]);
    }

    console.log(data);
    console.log(strDate);
  };

  return (
    <Wrapper>
      <MuiPickersUtilsProvider utils={DateFnsUtils} locale={koLocale}>
        <KeyboardDatePicker
          autoOk
          variant="inline"
          inputVariant="outlined"
          margin="normal"
          fullWidth
          className={classes.textField}
          format="yyyy/MM/dd"
          label="날짜"
          value={date}
          onChange={(date: Date | null) => setDate(date)}
        />
      </MuiPickersUtilsProvider>
      <br />
      <TextField
        variant="outlined"
        margin="normal"
        fullWidth
        className={classes.textField}
        label="품목"
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <br />
      <TextField
        variant="outlined"
        margin="normal"
        fullWidth
        className={classes.textField}
        label="가격"
        value={price}
        onChange={e => setPrice(e.target.value)}
        InputProps={{
          endAdornment: <InputAdornment position="end">원</InputAdornment>
        }}
      />
      <br />
      <TextField
        variant="outlined"
        margin="normal"
        fullWidth
        className={classes.textField}
        label="구입처"
        value={place}
        onChange={e => setPlace(e.target.value)}
      />
      <br />
      <Button
        onClick={() => handleAdd()}
        variant="contained"
        color="primary"
        fullWidth
        className={classes.button}
      >
        추가
      </Button>
    </Wrapper>
  );
}
```

어쩌다보니 코드가 굉장히 길고 지저분해졌다...😱

그리고 화면을 반반 나누기 위해 약간의 css작업을 해주었다.

**src/App.tsx**

```tsx
(...생략)
const Container = styled.div`
  display: flex;
`;
(...생략)
```

App 컴포넌트를 `Container`로 감싸주고

**src/Household.tsx**

```tsx
import React from "react";
import styled from "styled-components";

const Wrapper = styled.div`
  flex: 1;
`;

const HouseholdTable = styled.table`
  width: 100%;
`;

type HouseholdProps = {
  children: JSX.Element[];
};

export default function Household({ children }: HouseholdProps) {
  return (
    <Wrapper>
      <HouseholdTable>
        <caption>가계부</caption>
        {children}
      </HouseholdTable>
    </Wrapper>
  );
}
```

`Household`와 `Form`의 Wrapper에 `flex: 1` 속성을 주면 화면이 반반 꽉 차게 나온다.

> 여기서 1은 비율이다. (1:1)

![view4](/assets/images/report-household-view4.png)

오늘 날짜로 항목을 하나 추가해보았다. 아주 잘 나온다!
