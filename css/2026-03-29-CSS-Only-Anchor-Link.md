### Context
웹 페이지 내에서 특정 섹션으로 이동하는 앵커 링크(`<a>` 태그의 `href="#id"`)는 흔하게 사용되는 기능입니다. 일반적으로 이러한 앵커 링크 클릭 시 부드러운 스크롤 효과나 이동된 섹션을 시각적으로 강조하기 위해 JavaScript를 사용하는 경우가 많습니다. 하지만 최신 CSS 기능들을 활용하면 JavaScript 코드 없이도 이러한 요구사항을 대부분 충족시킬 수 있습니다. 이는 웹 페이지의 성능을 개선하고 코드 복잡성을 줄이는 데 기여합니다.

### Core
JavaScript 없이 CSS만을 사용하여 앵커 링크 기능을 구현하는 핵심은 `:target` 의사 클래스와 `scroll-behavior` 속성을 활용하는 것입니다.

1.  **HTML 구조:**
    앵커 링크를 위한 기본적인 HTML 구조는 다음과 같습니다.

    ```html
    <!-- 목차/내비게이션 링크 -->
    <nav>
      <a href="#section1">섹션 1로 이동</a>
      <a href="#section2">섹션 2로 이동</a>
    </nav>

    <!-- 이동될 섹션 -->
    <section id="section1">
      <h2>섹션 1</h2>
      <p>섹션 1의 내용입니다.</p>
    </section>

    <section id="section2">
      <h2>섹션 2</h2>
      <p>섹션 2의 내용입니다.</p>
    </section>
    ```

2.  **부드러운 스크롤 효과 (`scroll-behavior`):**
    페이지 전체 또는 특정 스크롤 가능한 요소에 `scroll-behavior: smooth;`를 적용하면 앵커 링크 클릭 시 스크롤이 즉시 이동하는 대신 부드럽게 애니메이션됩니다.

    ```css
    html {
      scroll-behavior: smooth; /* 모든 앵커 링크에 부드러운 스크롤 적용 */
    }
    ```

3.  **고정된 헤더 처리 (`scroll-padding-top` 또는 `scroll-margin-top`):**
    페이지 상단에 고정된 헤더(fixed header)가 있을 경우, 앵커 링크로 이동한 섹션의 일부가 헤더에 가려질 수 있습니다. 이를 방지하기 위해 `scroll-padding-top` 또는 `scroll-margin-top`을 사용합니다.

    가장 일반적이고 권장되는 방법은 스크롤 컨테이너(대부분 `html` 또는 `body`)에 `scroll-padding-top`을 적용하는 것입니다. 이는 모든 앵커 링크의 목표 위치에 일괄적으로 상단 패딩을 추가하여 헤더 높이만큼 여유 공간을 확보합니다.

    ```css
    html {
      scroll-behavior: smooth; /* 기존 부드러운 스크롤 */
      scroll-padding-top: 50px; /* 고정 헤더 높이만큼 스크롤 위치 보정 (예: 헤더 높이가 50px일 경우) */
    }
    ```

    또는, 대상 요소 자체에 `scroll-margin-top`을 적용할 수도 있습니다. `scroll-margin-top`은 요소의 바깥쪽에 여백을 두어 스크롤 위치를 조정하며, 특정 섹션에만 다른 보정을 적용해야 할 때 유용합니다.

    ```css
    section[id] { /* ID를 가진 모든 섹션에 적용 */
      scroll-margin-top: 50px; /* 고정 헤더 높이만큼 스크롤 위치 보정 */
    }
    ```
    `scroll-padding-top`을 `:target` 의사 클래스에 적용하는 방식도 가능하지만, 스크롤이 시작된 후에 스타일이 적용되어 미묘한 점프 현상이 발생할 수 있어 일반적으로 `html`에 적용하는 방식이 더 부드럽습니다.

4.  **대상 섹션 스타일링 (`:target`):**
    `:target` 의사 클래스는 현재 URL의 프래그먼트 식별자(예: `#section1`)와 일치하는 ID를 가진 요소에 스타일을 적용할 수 있게 해줍니다. 이를 통해 사용자가 이동한 섹션을 시각적으로 강조할 수 있습니다.

    ```css
    :target {
      background-color: #fffacd; /* 배경색 변경 */
      border: 2px solid #daa520; /* 테두리 추가 */
      padding: 10px; /* 패딩 추가 */
      transition: background-color 0.5s ease; /* 부드러운 전환 효과 */
    }
    ```
    위 CSS를 적용하면 `#section1` 링크를 클릭하여 `#section1`으로 이동했을 때, `#section1` 요소에 지정된 스타일이 적용됩니다. 다른 섹션으로 이동하면 이전 섹션의 스타일은 해제되고 새로운 대상 섹션에 스타일이 적용됩니다.

### Insight
JavaScript 없이 CSS만으로 앵커 링크 기능을 구현하는 것은 다음과 같은 이점을 제공합니다.

*   **성능 향상:** JavaScript 번들 크기를 줄이고, 파싱 및 실행 시간을 단축하여 초기 로딩 성능을 개선합니다.
*   **간결한 코드:** 복잡한 JavaScript 코드 없이 HTML과 CSS만으로 기능을 구현하여 유지보수성이 높아집니다.
*   **접근성:** 브라우저의 기본 기능을 활용하므로 접근성 측면에서 별다른 추가 작업 없이도 잘 작동합니다.
*   **폴백(Fallback):** 오래된 브라우저에서 `scroll-behavior: smooth;`나 `:target`이 지원되지 않더라도, 기본적으로 즉시 이동하는 앵커 링크 기능은 여전히 작동하여 사용자 경험에 큰 지장을 주지 않습니다.

이러한 기법은 주로 목차, FAQ 페이지, 장문의 문서 내 내비게이션 등 정적인 콘텐츠가 많은 웹 페이지에 매우 유용합니다. 동적인 애니메이션이나 복잡한 사용자 인터랙션이 필요한 경우에는 여전히 JavaScript가 필요할 수 있지만, 단순한 앵커 링크 기능이라면 CSS만으로 충분히 강력하고 효과적인 사용자 경험을 제공할 수 있습니다.