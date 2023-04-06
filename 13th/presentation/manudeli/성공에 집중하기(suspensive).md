# 성공에 집중하기(@suspensive/react)

## 프론트엔드 개발자의 어려운 문제: 비동기 프로그래밍

비동기 프로그래밍은 왜 어려울까?

### 성공과 실패가 섞인 코드(callback): 에러 처리와 성공에 대한 처리가 섞여서 처리된다.

1. 성공/실패하는 경우가 섞여서 처리되기 때문에 함수가 하는 역할을 파악하기 어려움
2. 비동기 처리를 할 때마다 에러를 처리해야함

```js
const fetchAccounts((callback) => {
  fetchUser((err, user) => {
    if(err){
      callback(err, null);
      return
    }

    fetchUserAccounts(user.no, (err, accounts) => {
      if(err){
        callback(err, null);
       return
      }

      callback(null, accounts)
    })
  })
}

```

### 성공에 집중된 코드(async/await): 위의 callback을 활용한 코드를 아래와 같이 async/await를 활용해보면 어떨까?

1. 성공하는 경우들만 모아서 볼 수 있음
2. 실패는 외부로 위임해 한 번에 처리함

```js
const fetchAccounts = async () => {
  const user = await fetchUser()
  const acounts = await fetchAccounts(user.id)

  return accounts
}
```

## 우리 컴포넌트는?

### 기존 (@tanstack/react-query 활용)

1. 실패, 성공의 경우가 서로 섞여 처리된다.
2. 비즈니스 로직을 파악하기 어렵다.

```js
const Component = () => {
  const query = useQuery(...)

  if(query.isError) return <>...query.error...</>
  if(query.isLoading) return <>로딩 중입니다...</>
  return <AccountList userId={query.data.user.id} />
}

const AccountList = ({userId}) => {
  const query = useQuery(..., userId)

  if(query.isError) return <>...query.error...</>
  if(query.isLoading) return <>로딩 중입니다...</>
  return <>{query.data.map(() => {...})}</>
}
```

성공에 집중할 수 있는 코드로 만들 수 없을까?

### 성공에 집중하는 코드 (try catch를 활용한 실패를 외부에 위임하는 마인드 코드)

```js
const Component = () => {
  const query = useQuery(...)

  return <AccountList userId={query.data.user.id}/>
}

const AccountList = ({userId}) => {
  const query = useQuery(..., userId)

  return <>{query.data.map(() => {...})}</>
}

try{
  <Component/>
} catch(query){
  if(query.isError) return <>...query.error...</>
  if(query.isLoading) return <>로딩 중입니다...</>
}
```

### 마인드 코드를 리액트 코드로 (Suspense for Data fetching)

결과: 컴포넌트 내부에서는 성공한 케이스에만 집중할 수 있음

1. Suspense: catch thrown pending
2. ErrorBoundary: catch thrown error

```js
const Component = () => {
  const query = useQuery(...)

  return <AccountList userId={query.data.user.id}/>
}

const AccountList = ({userId}) => {
  const query = useQuery(..., userId)

  return <>{query.data.map(() => {...})}</>
}

<ErrorBoundary fallback={(boundary) => {...}}>
  <Suspense fallback={...}>
    <Component/>
  </Suspense>
</ErrorBoundary>
```

## Suspense for Data fetching 실전편

### suspense를 사용하기 위해 많은 라이브러리를 설치하거나 직접 구현해야 합니다.

하지만 막상 라이브러리를 사용하려고 보니 아래와 같은 문제가 있었습니다.

- [bvaughn/react-error-boundary](https://github.com/bvaughn/react-error-boundary), [@toss/error-boundary](https://slash.page/libraries/react/error-boundary/src/ErrorBoundary.i18n)
  - ErrorBoundary.fallback 외부에서 reset 어려움
  - 다수의 ErrorBoundary를 reset하기 어려움 (resetKeys에 일일이 연결 혹은 prop-drilling 혹은 Context로 따로 구현해야 함)
  - ErrorBoundary.fallback interface의 복잡성
- [@toss/async-boundary](https://slash.page/libraries/react/async-boundary/src/AsyncBoundary.i18n)
  - SSR환경에서 불안정성 (18버전 미만의 경우)
- [@tanstack/react-query](https://tanstack.com/query/latest)
  - suspense옵션의 data를 타입으로 보장하기 위해 isSuccess조건문을 사용해야만 하는 문제
- [@toss/react-query](https://slash.page/libraries/react/react-query/src/hooks/useSuspendedQuery.i18n)
  - react-query v3만 지원하고 useInfiniteQuery를 위한 suspense래핑 훅이 없음

### 아래와 같은 것을 제공해주면 좋겠다.

- Suspense CSROnly 모드를 제공하면 어떨까?
- FallbackComponent, fallbackRender, fallback를 fallback하나로 통일하면 어떨까?
- 전용으로 외부에서 reset할 수 있는 컴포넌트 제공하면 어떨까?
- 다수의 ErrorBoundary를 ErrorBoundaryGroup으로 묶어서 처리하면 어떨까?
- @tanstack/react-query suspense옵션의 data를 타입으로 보장하면 어떨까?
- @tanstack/react-query v4를 지원하고 useSuspenseInfiniteQuery를 제공하면 어떨까?

그래서 준비했습니다.

# Suspensive 라이브러리를 소개합니다.

- [🔗 Suspensive 공식문서](https://suspensive.org/)
- [🔗 Suspensive GitHub 레포지토리](https://github.com/suspensive/react)
- [🔗 Suspensive 시각화자료](https://visualization.suspensive.org/react)

<div align="center">
  <a href="https://suspensive.org" title="Suspensive Libraries - TypeScript/JavaScript packages to use React Suspense easily">
    <img src="https://raw.githubusercontent.com/suspensive/react/main/websites/docs/static/banner.png" alt="Suspensive Libraries Logo - TypeScript/JavaScript packages to use React Suspense easily." />
  </a>
</div>

suspensive는 React Suspense를 사용하기 위한 모든 인터페이스를 제공합니다.

## [@suspensive/react](https://suspensive.org/docs/react/README.i18n)

[![npm version](https://img.shields.io/npm/v/@suspensive/react?color=61DAFB)](https://www.npmjs.com/package/@suspensive/react) ![npm bundle size](https://img.shields.io/bundlephobia/minzip/@suspensive/react?color=blue) ![npm](https://img.shields.io/npm/dm/@suspensive/react?color=blue)

### 설치하기

```zsh
npm install @suspensive/react
```

```zsh
yarn add @suspensive/react
```

```zsh
pnpm add @suspensive/react
```

### 기능

- Suspense (containing CSROnly mode)
- ErrorBoundary
- ErrorBoundaryGroup, useErrorBoundaryGroup
- AsyncBoundary (CSROnly mode)
- Delay (Experimental)
- SuspensiveProvider, SuspensiveConfigs (Experimental)
- HOC(Higher Order Component)s for all components

## [@suspensive/react-query](https://suspensive.org/docs/react-query/README.i18n)

[![npm version](https://img.shields.io/npm/v/@suspensive/react-query?color=61DAFB)](https://www.npmjs.com/package/@suspensive/react-query) ![npm bundle size](https://img.shields.io/bundlephobia/minzip/@suspensive/react-query?color=blue) ![npm](https://img.shields.io/npm/dm/@suspensive/react-query?color=blue)

### 설치하기

```zsh
npm install @suspensive/react @suspensive/react-query
```

```zsh
yarn add @suspensive/react @suspensive/react-query
```

```zsh
pnpm add @suspensive/react @suspensive/react-query
```

### 기능

- QueryErrorBoundary, QueryAsyncBoundary
- useSuspenseQuery
- useSuspenseInfiniteQuery

suspensive의 자세한 내용은 [공식문서에서 확인](https://suspensive.org/)해주세요.
