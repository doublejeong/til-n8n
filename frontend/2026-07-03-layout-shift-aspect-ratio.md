### Context
Cumulative Layout Shift (CLS)는 웹 페이지의 시각적 안정성을 측정하는 Core Web Vitals 중 하나로, 사용자 경험에 직접적인 영향을 미칩니다. 이 지표는 페이지 콘텐츠가 예상치 못하게 움직이는 빈도와 정도를 측정하며, 점수가 높을수록 사용자 불편이 커집니다. 주로 이미지를 비롯한 미디어 콘텐츠, 광고, 동적 삽입 콘텐츠 등이 로딩될 때 해당 공간이 미리 확보되지 않아 주변 요소들이 밀려나면서 레이아웃이 출렁이는 현상으로 인해 발생합니다. 이는 사용자가 특정 버튼을 클릭하려다 갑자기 요소의 위치가 바뀌어 원치 않는 곳을 클릭하게 만드는 등의 부정적인 경험을 초래합니다.

### Core
이러한 레이아웃 시프트를 효과적으로 방지하기 위한 대책으로 CSS의 `aspect-ratio` 속성이 활용될 수 있습니다. `aspect-ratio` 속성은 요소의 너비와 높이 비율을 명시적으로 정의하여, 콘텐츠가 로딩되기 전에 브라우저가 미리 필요한 공간을 확보하도록 합니다.

**`aspect-ratio` 사용 방법:**
- `aspect-ratio: <width> / <height>;` 형태로 값을 지정합니다. 예를 들어, 16:9 비율의 동영상이나 이미지는 `aspect-ratio: 16 / 9;`로 설정할 수 있습니다.
- 정사각형(1:1 비율) 요소는 `aspect-ratio: 1 / 1;` 또는 단축형으로 `aspect-ratio: 1;`로 지정합니다.
- 이 속성을 통해 브라우저는 이미지, 비디오, iframe 등의 미디어가 로드되기 전에 지정된 비율에 따라 공간을 미리 예약하여, 콘텐츠가 로드된 후에도 레이아웃이 변하지 않도록 합니다.
- HTML의 `<img>` 태그는 `width` 및 `height` 속성을 통해 이미지의 원본 너비와 높이를 지정할 수 있으며, 이는 브라우저가 해당 공간을 미리 확보하는 데 도움을 줍니다. 하지만 CSS의 `aspect-ratio` 속성은 이러한 HTML 속성이 없거나, 동적으로 크기가 조절되는 반응형 미디어 요소에 더욱 유연하고 강력하게 레이아웃 시프트를 방지할 수 있는 명시적인 방법을 제공합니다.

**이전 방법과의 비교:**
`aspect-ratio` 속성이 도입되기 전에는 레이아웃 시프트를 방지하기 위해 소위 `padding-bottom` 핵(hack)을 사용했습니다. 이는 부모 요소의 `padding-bottom` 속성에 백분율 값을 주어 높이를 확보하는 방식이었습니다. 예를 들어, 16:9 비율을 위해 `padding-bottom: 56.25%;` (`(9 / 16) * 100%`)를 사용하는 방식이 있었으나, 이는 다음과 같은 단점을 가졌습니다.
- **추가적인 HTML 구조:** `position: absolute`를 사용하는 자식 요소를 감싸기 위한 추가적인 부모 요소가 필요했습니다.
- **복잡한 CSS:** `position`, `top`, `left`, `width`, `height` 등을 함께 설정해야 했으며, 의미론적으로 직관적이지 않았습니다.

**`padding-bottom` 핵 예시:**
```html
<div class="aspect-ratio-box">
  <img src="your-image.jpg" alt="Description">
</div>
```
```css
.aspect-ratio-box {
  position: relative;
  width: 100%;
  height: 0; /* padding-bottom으로 높이를 확보 */
  padding-bottom: 56.25%; /* 16:9 비율 */
  overflow: hidden; /* 자식 요소가 넘어갈 경우 대비 */
}
.aspect-ratio-box img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```
`aspect-ratio` 속성은 이러한 복잡한 방법들을 대체하는 보다 직관적이고 표준적인 솔루션을 제공하여, 더 적은 코드로 동일한 효과를 얻을 수 있게 합니다.

**활용 예시:**
```css
/* 이미지에 aspect-ratio 적용 */
img {
  width: 100%;
  height: auto; /* aspect-ratio가 높이를 결정할 수 있도록 설정 */
  aspect-ratio: 16 / 9; /* 이미지의 원본 비율에 맞게 설정 (예: 16:9) */
  object-fit: cover; /* 이미지가 지정된 공간에 맞춰지는 방식 (잘림 방지 또는 채움) */
  object-position: center; /* 이미지가 요소 내에서 정렬되는 방식 */
}

/* 비디오 및 iframe에 aspect-ratio 적용 */
video, iframe {
  width: 100%;
  aspect-ratio: 16 / 9; /* 비디오/iframe의 비율에 맞게 설정 */
}
```
이 속성을 사용하면 이미지가 로드되기 전에도 브라우저가 올바른 크기의 공간을 확보하여, 로딩 완료 시점에 발생하는 레이아웃 시프트를 근본적으로 차단할 수 있습니다. `object-fit` 속성은 이미지나 비디오가 지정된 `aspect-ratio` 공간에 어떻게 맞춰질지 제어하여, 콘텐츠가 왜곡되지 않고 적절히 표시되도록 돕습니다.

### Insight
`aspect-ratio` 속성은 단순하지만 웹 성능 최적화, 특히 CLS 개선에 매우 강력한 도구입니다. 이 속성을 적절히 활용함으로써 웹 페이지의 시각적 안정성을 크게 향상시키고 사용자에게 더 부드럽고 예측 가능한 경험을 제공할 수 있습니다. 이는 단순히 기술적인 개선을 넘어 사용자 만족도를 높이고, 궁극적으로는 검색 엔진 최적화(SEO)에도 긍정적인 영향을 미칠 수 있습니다. `aspect-ratio`는 현재 주요 브라우저(Chrome, Firefox, Safari, Edge 등)에서 광범위하게 지원되므로, 안심하고 프로덕션 환경에 적용할 수 있습니다. 동적 콘텐츠가 많은 현대 웹 환경에서 `aspect-ratio`를 통한 사전 공간 확보는 필수적인 웹 개발 기법으로 자리 잡고 있습니다.