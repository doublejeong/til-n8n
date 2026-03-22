### Context
뉴스 웹사이트에서 특정 기사의 제목, 본문, 작성일과 같은 정보를 추출하는 것은 데이터 분석, 아카이빙 또는 콘텐츠 재구성을 위해 흔히 필요한 작업입니다. BeautifulSoup4 라이브러리는 HTML 및 XML 문서를 파싱하여 필요한 데이터를 효율적으로 추출할 수 있도록 돕는 강력한 도구입니다. 이 문서는 BeautifulSoup4와 requests 라이브러리를 활용하여 뉴스 기사를 크롤링하는 기본적인 방법을 설명합니다.

### Core
다음 파이썬 코드는 `requests` 라이브러리를 사용하여 웹 페이지의 HTML을 가져온 다음, `BeautifulSoup4`를 이용해 기사의 제목, 본문, 작성일을 추출하는 예시입니다. 특정 뉴스 웹사이트의 구조에 맞춰 셀렉터를 조정해야 합니다.

```python
import requests
from bs4 import BeautifulSoup

# 크롤링할 뉴스 기사 URL (예시)
# 실제 웹사이트의 robots.txt를 확인하고, 과도한 요청을 삼가야 합니다.
url = "https://example.com/news/article/123" # 실제 뉴스 기사 URL로 대체해야 합니다.

try:
    response = requests.get(url, timeout=5)
    response.raise_for_status() # HTTP 오류 발생 시 예외 발생

    soup = BeautifulSoup(response.text, 'html.parser')

    # 기사 제목 추출 (예시: h1 태그)
    title_tag = soup.find('h1', class_='article-title') # 실제 웹사이트의 클래스명 확인 필요
    title = title_tag.get_text(strip=True) if title_tag else "제목을 찾을 수 없습니다."

    # 기사 본문 추출 (예시: div 태그, 특정 클래스)
    content_div = soup.find('div', class_='article-body') # 실제 웹사이트의 클래스명 확인 필요
    content = ' '.join([p.get_text(strip=True) for p in content_div.find_all('p')]) if content_div else "본문을 찾을 수 없습니다."

    # 작성일 추출 (예시: span 태그, 특정 클래스)
    date_span = soup.find('span', class_='article-date') # 실제 웹사이트의 클래스명 확인 필요
    date = date_span.get_text(strip=True) if date_span else "작성일을 찾을 수 없습니다."

    print(f"제목: {title}")
    print(f"작성일: {date}")
    print(f"본문:\n{content[:200]}...") # 본문이 길 경우 앞부분만 출력

except requests.exceptions.RequestException as e:
    print(f"웹 페이지 요청 중 오류 발생: {e}")
except Exception as e:
    print(f"데이터 파싱 중 오류 발생: {e}")
```

### Insight
위 코드는 뉴스 기사를 크롤링하는 기본적인 틀을 제공합니다. 실제 웹사이트마다 HTML 구조가 다르므로, `soup.find()` 또는 `soup.select()` 메서드를 사용하여 올바른 CSS 선택자나 태그 속성을 찾아야 합니다. 개발자 도구(Developer Tools)를 활용하여 대상 웹 페이지의 HTML 구조를 분석하는 것이 중요합니다.

크롤링 시 유의할 점은 다음과 같습니다:
*   **`robots.txt` 확인**: 웹사이트 루트 경로의 `robots.txt` 파일을 확인하여 크롤링이 허용되는지, 어떤 경로가 허용/금지되는지 반드시 확인해야 합니다.
*   **과도한 요청 금지**: 짧은 시간 내에 너무 많은 요청을 보내는 것은 서버에 부담을 줄 수 있으며, IP 차단으로 이어질 수 있습니다. `time.sleep()` 등을 활용하여 요청 사이에 지연 시간을 두는 것이 좋습니다.
*   **동적 콘텐츠 처리**: JavaScript로 렌더링되는 동적 콘텐츠는 `requests`와 `BeautifulSoup4`만으로는 직접 크롤링하기 어렵습니다. 이런 경우 Selenium이나 Playwright와 같은 헤드리스 브라우저 자동화 도구를 고려해야 합니다.
*   **오류 처리**: 네트워크 문제, HTML 구조 변경 등으로 인해 크롤링이 실패할 수 있으므로, 예외 처리(try-except)를 견고하게 구현하는 것이 중요합니다.

**출처:**
*   [Beautiful Soup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
*   [Requests: HTTP for Humans™](https://requests.readthedocs.io/en/latest/)
