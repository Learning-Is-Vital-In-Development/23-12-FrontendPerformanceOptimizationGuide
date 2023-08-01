# 4장. 이미지 갤러리 최적화

## 캐시 최적화

- `Cache-Control`
    - [읽으면 좋은 자료: RFC 5861 - SWR](https://datatracker.ietf.org/doc/html/rfc5861)
- 적절한 캐시 유효 시간
    - 파일 유형별 캐시 유효시간을 다르게 가져간다.
    - 빌드된 파일을 S3에 올릴 때 이전 파일 관리.
    - [읽으면 좋은 자료: 토스 기술 블로그](https://toss.tech/article/smart-web-service-cache)
- 캐시보다 빠른 레지스터
- [캐시무효화](https://velog.io/@gil0127/%EC%BA%90%EC%8B%9C-%EB%AC%B4%ED%9A%A8%ED%99%94)
- react-query SSR 관련 이슈
    - [공식문서](https://tanstack.com/query/v4/docs/react/guides/ssr#using-hydration)
    - ![](https://hackmd.io/_uploads/B1LJ4w8s2.png)


## 불필요한 CSS 제거

- PurgeCSS
    - [관련 이슈(저번주 숙제)](https://github.com/Learning-Is-Vital-In-Development/23-12-FrontendPerformanceOptimizationGuide/issues/34)

## 레이아웃 이동 피하기

- lighthouse의 CLS 지표
- 레이아웃 이동의 원인
- 레이아웃 이동의 해결
    - 공간을 미리 잡아둔다.
- [LS는 어떻게 계산되는가](https://web.dev/i18n/ko/cls/)
- Next.js를 쓰는 이유
    - 이미지 컴포넌트, 폰트 등 최적화를 알아서 잘 해주는 프레임워크.
- [layout thrashing](https://web.dev/avoid-large-complex-layouts-and-layout-thrashing/#avoid-layout-thrashing)
- https://patterns-dev-kr.github.io/

## 이미지 지연 로딩

- `react-lazyload`

## 리덕스 렌더링 최적화

- 리액트의 렌더링
- 리렌더링의 원인
- `useSelector` 문제 해결
- zustand, recoil, jotai
- vue에서는 vuex, pinia
- [`~~` 연산자 트릭](https://stackoverflow.com/a/4055675)
- useState initializer function
- [useState에서 함수를 쓰는 경우](https://react.dev/reference/react/useState#updating-state-based-on-the-previous-state)

### Topic: useState hook - Lazy Initializing
이때까지 항상 습관적으로 React에서 useState 훅의 기본값을 그냥 넣어서 사용해왔습니다.
하지만 이전에 어떤 영상에서 useState 기본값으로 콜백 함수를 넣는 것을 본 적이 있습니다.
당시에 무거운 데이터 같은 경우에 최적화를 고려하여 콜백 함수로 넣을 수도 있다(?)로만 막연하게 잘못 인지하였습니다.
오늘 저녁 당시의 궁금증이 다시 상기되어 찾아봤습니다.
우선 결론적으로 useState 기본값으로 항상 해왔던 방식으로 원시값 ex) 문자열, 숫자, 불리언 등 을 세팅하거나
react-query로 서버 상태 관리하기 이전 방식인 useEffect 를 사용하여 api 초기 데이터를 넣을 수 있습니다.
(물론 useEffect를 사용하면 초기에는 데이터가 없어서 깜빡거릴 수 있는데 스켈레톤이나 loading spinner로 보통 처리하곤 합니다)
useState의 기본값으로 그냥 넣거나 useEffect를 이용해서 초기화 하는 것은 방금 말씀드렸습니다.
그러면 기본값으로 콜백 함수를 넣는 것은 왜 그런 것일까요.
어떤 데이터에 대해서 사전에 연산 비용이 많은 경우에 콜백 함수를 넣어
리렌더링이 되더라도 콜백 함수가 재호출되어 사전 전처리 연산을 또 할 필요 없이 초기값으로 넣은 콜백 함수가 1번만 호출되도록 할 수 있습니다.
만약 1초가 걸리는 연산이 사전에 있고 콜백 함수를 이용하지 않으면 리렌더링 할 때 마다 함수형 컴포넌트가 재호출되면서 해당 연산을 계속 1초씩 하게 될 수 있지만,
콜백 함수를 이용한다면 맨 처음에 초기값을 생성 시에만 호출시킬 수 있게 됩니다.
state가 업데이트 되어 리렌더링이 될 때 초기값을 또 연산 비용을 들여가면서 생성할 이유가 없기에
맨 처음에 초기값 세팅 시 1번만 콜백 함수로 호출할 수 있게 하는 것입니다.
(우선 초기화할지 말지를 내부적으로 비교하고 초기값이 필요하면 그제서야 콜백 함수를 호출해서 초기값이 세팅됩니다.)
자 그러면 반대로 만약에 모든 useState의 초기값으로 콜백 함수를 활용한 Lazy Initializing 기법을 이용한다면 어떨까요.
두 가지를 비교해야 합니다.
콜백 함수 이용 시 한 번만 호출되어 연산 비용을 줄일 수 있지만
JS 엔진이 해당 실행 컨텍스트를 평가하는 과정에서 함수를 만드는 비용도 존재하게 됩니다.
(물론 함수 내부 코드의 평가/실행 과정은 호출되기 이전이라 아직입니다.)
만약 단순히 () => false 와 같은 원시값을 만든다면 그냥 false를 직접 넣는 것이 효율적입니다.
굳이 heap에 함수 공간을 차지하는 메모리를 잡아서 할 이유가 없습니다.
실제로 저는 해당 기법을 써본 적은 없지만, 보통 비싼 비용의 계산이 들거나 localStorage의 접근, 배열을 사전에 조작하는 연산, new Date() 등에 활용하는 것 같습니다.
마지막 참고로 이런 코드를 사용할 수도 있을 것 같습니다.
const [counter, setCounter] = useState(() => Math.floor(Math.random() * 16));
[레퍼런스 1] - [React 공식 문서](https://reactjs.org/docs/hooks-reference.html#lazy-initial-state)
[레퍼런스 2] - [[React] Lazy Initializing를 사용해 최적화](https://satisfactoryplace.tistory.com/277)
[레퍼런스 3] - [리액트의 useState와 lazy initialization](https://yceffort.kr/2020/10/IIFE-on-use-state-of-react)
[레퍼런스 4] - [[Hook 시리즈] Lazy initialization 이 대체 뭔데 그래서](https://velog.io/@samkong/Lazy-initialization)
[레퍼런스 5] - [ReactJS useState Hook - lazy initialization and previous state](https://blog.greenroots.info/react-hook-usestate-lazy-initialization-previous-state)
[공식문서](https://legacy.reactjs.org/docs/hooks-reference.html#lazy-initial-state)

## 병목 코드 최적화

- 메모이제이션
    - [메모이제이션 관련 글](https://d2.naver.com/helloworld/9223303)
    - 상태를 상위 컴포넌트로 올린다.
    - useReducer
- 깊은 복사
    - `JSON.stringify`, `JSON.parse`
    - 재귀 함수
    - [structuredClone](https://developer.mozilla.org/en-US/docs/Web/API/structuredClone)
