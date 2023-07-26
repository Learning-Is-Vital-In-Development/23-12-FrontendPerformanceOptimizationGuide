
# 3장. 홈페이지 최적화

## 분석 툴

### 개발자 도구 - Coverage

- css overview
- Frame Rendering Stats
- Edge브라우저는 렌더링 과정에서 Layer를 볼 수 있는 기능 제공
    - https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/3d-view/

### Squoosh


### PurgeCSS

- https://antfu.me/posts/reimagine-atomic-css
    - tailwind는 사용하지 않는 css를 제거하는 최적화 과정에서 purgeCSS를 사용한다
- ![](https://hackmd.io/_uploads/rJSorQ6ch.png)

-----

## 이미지 지연 로딩

- [loading attribute](https://developer.mozilla.org/ko/docs/Web/HTML/Element/img#attr-loading)
    - img, iframe 태그에 사용 가능
    - 브라우저 지원을 확인해봐야함
    - 간단하게 쓰기 좋음

### 네트워크 분석

### intersection observer

- 정확한 픽셀 단위의 위치를 알 수 없다. 왜 Intersection Observer를 통해 알 수 없는 것은 겹쳐진 정확한 픽셀단위 위치와 유저의 정확한 위치를 알 수 없다는 것

### debounce, throttle 

- useDebounce, useThrottle 쓰자...

### data attribute

- https://www.npmjs.com/package/vue-lazyload

### iterator, iterable

- https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%9D%B4%ED%84%B0%EB%9F%AC%EB%B8%94-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%F0%9F%92%AF%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4
- https://erinyees.tistory.com/21

-----

## 이미지 사이즈 최적화

- squoosh
- png, jpg
- `picture` 태그
- `window.devicePixelRatio`
    - 현재 디바이스의 dpr을 알 수 있다!
    - [retina display](https://namu.wiki/w/Retina%20%EB%94%94%EC%8A%A4%ED%94%8C%EB%A0%88%EC%9D%B4)
- srcset으로 미디어 쿼리 적용 가능.?
```html
<img
  srcset="elva-fairy-320w.jpg, elva-fairy-480w.jpg 1.5x, elva-fairy-640w.jpg 2x"
  src="elva-fairy-640w.jpg"
  alt="Elva dressed as a fairy" 
/>
```

- avif 포맷
    - 압축률이 webp보다 좀 더 좋다?
- previous sibliing
    - https://www.inflearn.com/questions/711407/%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%A7%80%EC%97%B0-%EB%A1%9C%EB%94%A9-%EC%A7%88%EB%AC%B8-%EC%9E%88%EC%8A%B5%EB%8B%88%EB%8B%A4


---

## 동영상 최적화

- OGV
- WebM이란?
    - web media
- IE지원이 미흡해서 mp4를 많이 썻었음
- 토스 슬래시 페이지 보면, 스크롤에 따라 동영상 재생 시간을 변경해서 인터랙티브하게 만들었음
    - https://sasha1107.github.io/TIL/projects/video_tag_toss/
- 유튜브는 보면 이미지를 계속 준다
- UDP 
    - 라이브 스트리밍
- HLS
    - https://www.cloudflare.com/ko-kr/learning/video/what-is-http-live-streaming/
    - https://en.wikipedia.org/wiki/HTTP_Live_Streaming

---

## 지속가능한 컴포넌트

- 컴포넌트 합성
- https://www.youtube.com/watch?v=fR8tsJ2r7Eg

----


## 폰트 최적화

- FOUT, FOIT
- ![](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fda151ef3-951c-426d-8b44-0f6622492d6a%2Ffoit-fout-animation.gif?table=block&id=ec019010-5c0c-4f1d-96ed-154482fa105d&cache=v2)
- 구글 폰트 얘기..
- 다국어 서비스
    - unicode-range
        - 원하는 글자에만 폰트 적용 가능
        - subset font
- ![](https://hackmd.io/_uploads/r1qjWNa5h.png) 
- [next/font](https://blog.mathpresso.com/how-next-font-works-8bb72c2bae39)
- [](https://github.com/vercel/next.js/blob/canary/packages/font/src/google/get-fallback-font-override-metrics.ts)
    - zero-layout shift 지원
-----

## 숙제

- Tailwind 에서 purge.css를 왜 더이상 필수값이 아닌지 알아보자!
- intersection observer 을 정확히 측정하기 위한 툴을 찾아보자!
- network throttle 이랑 throttle 이 뭔 차이여!
- UDP
- 컴포넌트 -> 컴파운드 컴포넌트 패턴
- [Rxjs](https://yozm.wishket.com/magazine/detail/1753/)