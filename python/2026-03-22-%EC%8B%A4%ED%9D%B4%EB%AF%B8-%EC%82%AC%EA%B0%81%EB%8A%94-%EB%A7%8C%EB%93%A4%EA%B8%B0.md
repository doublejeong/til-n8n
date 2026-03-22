### Context
웹 페이지에서 사용자가 버튼을 클릭하면 동적으로 팝업 창이 나타나는 기능을 만들기 위해 JavaScript와 HTML을 사용한다. 이 문서에서는 클릭 이벤트를 처리하여 팝업 창을 띄우는 방법을 알아본다.

### Core
#### HTML 코드
```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>팝업창 예제</title>
</head>
<body>
    <!-- 버튼 추가 -->
    <button id="popupButton">팝업 열기</button>

    <script src="popup.js"></script>
</body>
</html>
```

#### JavaScript 코드 (popup.js)
```javascript
document.addEventListener('DOMContentLoaded', function() {
  const button = document.getElementById('popupButton');

  // 버튼 클릭 이벤트 핸들러 추가
  button.addEventListener('click', function(event) {
    event.preventDefault(); // 폼 제출을 막음

    // 팝업 창 띄우기
    alert("팝업 창이 열렸습니다!");
  });
});
```

### Insight
위의 코드에서 `DOMContentLoaded` 이벤트는 HTML 문서가 완전히 로드된 후 실행되도록 JavaScript 코드를 감싸주며, `addEventListener` 메서드는 버튼 클릭 이벤트에 반응하여 팝업 창을 띄운다. 

**출처:** [MDN Web Docs: addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)