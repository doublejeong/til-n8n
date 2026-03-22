### Context
웹 페이지에서 클릭 이벤트를 감지하고 그에 따라 팝업 창을 띄우는 기본적인 JavaScript 기능 구현입니다. 이 예제에서는 `onclick` 이벤트와 `alert()` 함수를 사용하여 간단한 다이어로그를 생성합니다.

### Core
다음은 JavaScript 코드의 샵플입니다:
```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>클릭 다이어로그</title>
</head>
<body>
    <!-- 클릭할 요소 -->
    <button id="popupButton">팝업 열기</button>

    <script>
        // 버튼의 onclick 속성 설정
        document.getElementById('popupButton').onclick = function() {
            alert("버튼이 클릭되었습니다!");
        }
    </script>
</body>
</html>
```

### Insight
위 코드는 HTML 문서 내에 `<button>` 요소를 사용하여 클릭 이벤트를 감지합니다. 클릭 시 `alert()` 함수를 통해 "버튼이 클릭되었습니다!"라는 메시지를 팝업 창으로 띄웁니다.

**출처:** [MDN Web Docs: Window.alert()](https://developer.mozilla.org/en-US/docs/Web/API/Window/alert)