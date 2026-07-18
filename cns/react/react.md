# 리엑트
- 웹 화면을 만들기 위한 JavaScript 라이브러리.
- SPA(Single Page Application) 기반
    - 단 하나의 HTML 파일만 사용하여 구동되는 웹 애플리케이션 구조
- 컴포넌트를 기본으로 하는 라이브러리

## Node.js
- JavaScript를 브라우저 밖, 즉 컴퓨터와 터미널에서도 실행할 수 있게 해주는 환경
- React 자체를 실행하기 위해서라기보다 다음과 같은 React 개발 도구를 실행하기 위해 필요
    - 개발 서버 실행
    - 프로젝트 생성
    - 코드 변환 및 빌드
    - 패키지 설치
    - 테스트와 코드 검사

## npm
- Node.js의 패키지 관리자
- Java의 Gradle이나 Maven과 비슷하게 React와 여러 라이브러리를 설치하고 관리

## npx
- npm 패키지가 제공하는 실행 프로그램을 직접 실행하는 도구입니다.
- npm이 패키지를 설치하고 관리한다면, npx는 패키지의 명령어를 실행

## Component와 Element

### Component

- 화면의 구조와 동작을 정의하는 **설계도**
- UI를 재사용 가능한 단위로 나눈 코드 블록
- 일반적으로 `props`를 전달받아 React Element를 반환한다.
- 같은 Component를 여러 번 사용해도 각각 독립적으로 렌더링된다.

```jsx
function Greeting({ name }) {
  return <h1>안녕하세요, {name}님</h1>;
}
```

### State

- Component가 렌더링 사이에도 기억해야 하는 **현재 데이터**
- 값이 변경됐을 때 화면도 함께 변경되어야 하는 경우 사용한다.
- React 내부에서 관리되며, 렌더 트리의 특정 Component와 연결된다.

State로 관리하는 값의 예시는 다음과 같다.

- 현재 숫자
- 입력창의 값
- 메뉴 열림 여부
- 로그인 여부

함수형 Component에서는 `useState` Hook으로 State를 관리한다.

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      현재 숫자: {count}
    </button>
  );
}
```

```jsx
const [count, setCount] = useState(0);
```

- `count`: 현재 State 값
- `setCount`: State 변경 함수
- `0`: 최초 렌더링에서 사용할 초기값

```text
setCount 호출
    ↓
State 변경
    ↓
Component 다시 실행
    ↓
새로운 Element 생성
    ↓
화면 업데이트
```

React가 일반 변수의 변화를 자동으로 감지하는 것은 아니다.

`setCount`와 같은 State 변경 함수를 호출해야 React가 Component를 다시 렌더링한다.

### Hook

- 함수형 Component에서 React의 기능을 사용할 수 있게 해주는 함수
- 일반적으로 이름이 `use`로 시작한다.

```text
useState   : 상태 관리
useEffect  : 렌더링 이후 작업
useRef     : 값 또는 DOM 참조 보관
useContext : Context 값 사용
```

`useState`는 함수형 Component에서 State를 사용할 수 있게 해주는 Hook이다.

### Element

- 특정 시점에 화면이 어떤 모습이어야 하는지 설명하는 **불변 JavaScript 객체**
- Component가 실행된 결과로 생성된다.
- 실제 브라우저 DOM 객체와는 다르다.

```jsx
const element = <h1>안녕하세요</h1>;
```

위 JSX는 개념적으로 다음과 같은 객체를 생성한다.

```javascript
{
  type: "h1",
  props: {
    children: "안녕하세요"
  }
}
```

React는 Element를 해석해 실제 DOM에 반영한다.

```text
Component 실행
    ↓
React Element 생성
    ↓
React가 Element 해석
    ↓
DOM에 반영
    ↓
브라우저 화면
```

Element는 생성된 후 직접 수정하지 않는다.

State가 변경되면 Component가 다시 실행되고 새로운 Element가 생성된다.

## 전체 관계

```text
Component
= 화면의 구조와 동작을 정의하는 설계도

State
= Component가 기억해야 하는 현재 데이터

Hook
= 함수형 Component에서 State 등의 기능을 사용하게 해주는 함수

Element
= 특정 시점의 화면 모습을 설명하는 불변 객체

DOM Element
= 브라우저에 실제로 표시되는 객체
```

```text
Component 실행
    ↓
현재 State를 이용해 Element 생성
    ↓
React가 DOM에 반영
    ↓
사용자 동작
    ↓
State 변경 함수 호출
    ↓
Component 재실행
    ↓
새로운 Element 생성
    ↓
화면 업데이트
```

