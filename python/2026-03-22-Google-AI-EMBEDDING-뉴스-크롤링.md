### Context
최근 뉴스 데이터를 분석하거나 유사한 정보를 찾는 작업은 효율적인 방법이 필요하다. 크롤링한 뉴스 기사를 embedding하여 데이터베이스에 저장하고, 세멘틱 검색을 통해 유사도를 찾아내는 방식으로 이 문제를 해결할 수 있다.

### Core
먼저, Google AI의 Text Embedding API를 사용해 크롤링한 뉴스 기사를 embedding한다. 이를 위해 `google-cloud-aiplatform` 패키지를 설치하고 필요한 모듈을 불러온다.
```python
pip install google-cloud-aiplatform
```
```python
from google.cloud import aiplatform_v1beta1 as aip

# 클라이언트 설정
client_options = {"api_endpoint": "us-central1-aiplatform.googleapis.com"}
embedding_client = aip.EdgeTpuTextEmbeddingServiceClient(client_options=client_options)

# 크롤링한 뉴스 기사
news_article = "크롤링된 뉴스 기사 내용"

# embedding 요청
response = embedding_client.embed_text_request(
    content=f"Input: {news_article}",
    model="textembedding-gecko@001",
)
```
위의 코드는 크롤링한 뉴스 기사를 embedding하고 결과를 저장한다. `response`에 반환되는 embedding 벡터를 사용해 데이터베이스에 저장한다.

데이터베이스에는 각 뉴스 기사와 해당하는 embedding 벡터가 들어간다.
```python
import mysql.connector

# MySQL 연결 설정
cnx = mysql.connector.connect(user='username', password='password',
                              host='localhost', database='news_db')
cursor = cnx.cursor()

# 데이터 저장
query = "INSERT INTO news_articles (title, content, embedding) VALUES (%s, %s, %s)"
values = ("뉴스 제목", news_article, response.embedded_text)
cursor.execute(query, values)

cnx.commit()
cursor.close()
```

### Insight
위의 코드를 통해 크롤링한 뉴스 기사를 embedding하고, 이를 데이터베이스에 저장한다. 세멘틱 검색을 수행하려면, 새로운 입력 기사의 embedding과 이미 저장된 기사들의 embedding을 비교하여 유사도를 계산하면 된다.

**출처:** [Google AI - Text Embedding API documentation](https://cloud.google.com/ai-platform-text/docs/text-embedding)