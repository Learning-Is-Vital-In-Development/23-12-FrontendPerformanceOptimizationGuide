> 실제 블로그의 성능을 최적화해봤습니다.

## 이미지 최적화

### 기존 상황

- 기존 [블로그](https://dogyeong.me)는 lighthouse 퍼포먼스 점수가 낮았음
- 일부 이미지는 CDN 적용되지 않은 상태

<img width="750" alt="스크린샷 2023-07-09 오후 1 44 14" src="https://github.com/Learning-Is-Vital-In-Development/23-12-FrontendPerformanceOptimizationGuide/assets/40662323/ee71705b-e5d6-4e8f-9b4c-c4f02bbfcd9d">

<img width="815" alt="스크린샷 2023-07-09 오후 1 44 53" src="https://github.com/Learning-Is-Vital-In-Development/23-12-FrontendPerformanceOptimizationGuide/assets/40662323/231436a6-19e5-43b0-8c00-a6c2cef0b9e6">


### CDN 적용 및 이미지 사이즈 최적화

- CDN
  - cloudinary
  - 이미지 url에 사이즈를 명시하여 조절 가능
    - `https://res.cloudinary.com/demo/image/upload/w_500/docs/shoes.jpg`
- 디바이스 해상도
  - 디바이스마다 물리적 픽셀의 밀도가 다름
  - 브라우저는 css 1픽셀을 표현하기 위해 여러개의 물리적 픽셀을 사용할 수 있음
  - css 1픽셀을 표현하기 위해 몇 개의 물리적 픽셀을 사용하는 지를 `Device Pixel Ratio(dpr)`라고 함
    - 자바스크립트는 `window.devicePixelRatio`로 확인가능
    - [custom-device-emulation-chrome](https://github.com/amirshnll/custom-device-emulation-chrome)에서도 확인 가능
- 이미지 사이즈
  - `srcset`
    - 사이즈에 따라 사용할 이미지들을 정의
    - `<img srcset="400w.jpg 400w, 800w.jpg 800w" />`
    - `sizes` 속성이 없으면 100vw를 기준으로 하며, 물리적 픽셀 수에 따라 이미지를 선택
    - 예시) dpr 2인 디스플레이 사이즈가 300픽셀이면, 600w로 계산되어 최종적으로 800w.jpg가 렌더링됨
  - `sizes`
    - 이미지 사이즈 기준을 미디어 쿼리 형태로 정의
    - 예시) `(max-width: 600px) 480px, 800px`
- 지연 로딩
  - `loading`
    - 뷰포트 근처의 이미지만 로딩할 수 있는 속성
    - 브라우저마다 동작이 다름
    - `<img loading="lazy" />`

```html
<img 
  src="https://res.cloudinary.com/dpefbi4ts/image/upload/w_1568,q_auto,f_auto/v1679722820/thumb/034-thumb.png" 
  alt="객체지향, 오브젝트" 
  class="_image_1fcrx_7" 
  loading="lazy" 
  srcset="https://res.cloudinary.com/dpefbi4ts/image/upload/w_280,q_auto,f_auto/v1679722820/thumb/034-thumb.png 280w, https://res.cloudinary.com/dpefbi4ts/image/upload/w_560,q_auto,f_auto/v1679722820/thumb/034-thumb.png 560w, https://res.cloudinary.com/dpefbi4ts/image/upload/w_840,q_auto,f_auto/v1679722820/thumb/034-thumb.png 840w, https://res.cloudinary.com/dpefbi4ts/image/upload/w_1120,q_auto,f_auto/v1679722820/thumb/034-thumb.png 1120w, https://res.cloudinary.com/dpefbi4ts/image/upload/w_1400,q_auto,f_auto/v1679722820/thumb/034-thumb.png 1400w, https://res.cloudinary.com/dpefbi4ts/image/upload/w_1568,q_auto,f_auto/v1679722820/thumb/034-thumb.png 1568w" 
  sizes="(max-width: 639px) 80vw, (max-width: 1680px) 40vw, 1120px"
>
```
퍼포먼스 90점으로 상승

<img width="836" alt="스크린샷 2023-07-09 오후 2 17 41" src="https://github.com/Learning-Is-Vital-In-Development/23-12-FrontendPerformanceOptimizationGuide/assets/40662323/aa6835c6-66d8-47ad-824c-0c7b857e48cd">

### 이미지 플레이스홀더

- 이미지가 로딩되기 전에는 블러 처리된 플레이스홀더를 표시
- 대표적으로 [blurhash](https://blurha.sh/)
- [sqip](https://github.com/axe312ger/sqip)를 사용하여 base64문자열 생성
- 이미지가 완전히 로드되면 `opacity: 1`로 변경

### 이미지 포맷 변경

- `cloudinary`의 `f_auto` 옵션 추가
  - `https://res.cloudinary.com/demo/image/upload/w_500,f_auto/docs/shoes.jpg`
- png파일이 avif 포맷으로 자동 변환됨
- 사이즈는 절반정도 감소 

| before | after |
|--------|--------|
| <img width="824" alt="스크린샷 2023-07-09 오후 2 26 05" src="https://github.com/Learning-Is-Vital-In-Development/23-12-FrontendPerformanceOptimizationGuide/assets/40662323/2c084dd9-d502-4b0a-9418-be6eff0827ba"> | <img width="821" alt="스크린샷 2023-07-09 오후 2 39 57" src="https://github.com/Learning-Is-Vital-In-Development/23-12-FrontendPerformanceOptimizationGuide/assets/40662323/097adffb-89ba-4d67-8117-8a1b94a004bc"> | 

퍼포먼스 96점으로 상승

<img width="821" alt="스크린샷 2023-07-09 오후 2 41 03" src="https://github.com/Learning-Is-Vital-In-Development/23-12-FrontendPerformanceOptimizationGuide/assets/40662323/7fbdf2bf-81ce-46b2-8b33-51f4fcf50563">