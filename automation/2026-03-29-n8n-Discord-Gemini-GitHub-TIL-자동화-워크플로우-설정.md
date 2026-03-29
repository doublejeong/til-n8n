### Context

n8n은 코드를 사용하지 않고 다양한 애플리케이션과 서비스를 연결하여 자동화 워크플로우를 구축할 수 있는 오픈 소스 자동화 툴입니다. 이 TIL에서는 n8n을 활용하여 다음과 같은 자동화 워크플로우를 설정하는 과정과 핵심 요소를 다룹니다.

*   **Discord 연동:** 특정 Discord 채널에서 발생한 메시지를 감지합니다.
*   **Gemini AI 연동:** 감지된 메시지를 Google Gemini AI로 전송하여 요약, 재구성 또는 특정 형식으로 가공합니다.
*   **GitHub 연동:** Gemini에서 처리된 내용을 GitHub 저장소에 TIL(Today I Learned) 형태로 자동으로 기록합니다.

이 자동화는 수동으로 TIL을 작성하는 번거로움을 줄이고, AI의 도움을 받아 콘텐츠의 질을 높이며, 꾸준한 학습 기록을 유지하는 데 기여할 수 있습니다.

### Core

n8n 워크플로우는 크게 세 가지 단계로 구성됩니다.

1.  **Discord 트리거 설정:**
    *   **목표:** Discord 특정 채널의 메시지를 n8n이 감지하게 합니다.
    *   **방법:**
        *   **Discord 웹훅 (Webhook):** Discord 서버 설정에서 웹훅을 생성하고, n8n의 `Webhook` 트리거 노드가 제공하는 URL을 해당 웹훅의 대상으로 설정합니다. Discord에서 메시지가 발생하면 웹훅을 통해 n8n으로 데이터가 전송됩니다.
        *   **n8n Discord 노드 (옵션):** n8n에 Discord 앱이 설치되어 있다면 `Discord` 노드를 사용하여 특정 이벤트(예: 새 메시지)를 직접 감지할 수 있습니다. 이 경우 Discord 봇 설정 및 n8n과의 인증 과정(봇 토큰 사용)이 필요합니다.
    *   **데이터 파싱:** Discord에서 넘어온 JSON 데이터에서 실제 메시지 내용(예: `{{ $json.body.embeds[0].description }}` 또는 `{{ $json.body.content }}`)을 추출합니다. 정확한 경로는 Discord 메시지의 형식(일반 메시지, 임베드 등)에 따라 달라질 수 있습니다.

2.  **Gemini AI 연동 및 처리:**
    *   **목표:** Discord에서 받은 메시지를 Gemini AI로 보내 가공된 텍스트를 받습니다.
    *   **방법:**
        *   **API 키 확보:** Google AI Studio에서 Gemini API 키를 발급받고, n8n Credentials에 안전하게 등록합니다.
        *   **n8n `HTTP Request` 노드:**
            *   **메서드:** `POST`
            *   **URL:** Gemini API 엔드포인트 (예: `https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key={{ $env.GEMINI_API_KEY }}` - `$env.GEMINI_API_KEY`는 n8n Credential을 통해 설정)
            *   **Header:** `Content-Type: application/json`
            *   **Body:** Discord에서 받은 메시지를 포함하여 Gemini에게 전달할 프롬프트(Prompt)를 JSON 형태로 작성합니다.
                ```json
                {
                  "contents": [
                    {
                      "parts": [
                        {
                          "text": "다음 내용을 TIL 형식으로 요약하고 마크다운으로 작성해줘:

{{ $json.message_content }}"
                        }
                      ]
                    }
                  ]
                }
                ```
                (`$json.message_content`는 이전 노드에서 추출한 Discord 메시지 내용)
        *   **응답 처리:** Gemini API의 응답에서 처리된 텍스트(`{{ $json.candidates[0].content.parts[0].text }}`)를 추출합니다.

3.  **GitHub TIL 기록 설정:**
    *   **목표:** Gemini에서 처리된 텍스트를 GitHub 저장소에 새로운 TIL 파일로 생성하거나 기존 파일에 추가합니다.
    *   **방법:**
        *   **GitHub PAT (Personal Access Token) 생성:** GitHub `Settings` > `Developer settings` > `Personal access tokens`에서 `repo` 스코프를 가진 PAT를 생성합니다. n8n Credential에 등록하여 사용합니다. (보안 강화를 위해 필요한 최소한의 권한을 가진 Fine-grained PAT를 사용하는 것을 권장합니다.)
        *   **n8n `GitHub` 노드:**
            *   **Authentication:** 생성한 PAT를 사용하여 n8n Credential을 선택합니다.
            *   **Resource:** `Repository`
            *   **Operation:** `Create File` 또는 `Update File`
            *   **Repository:** TIL을 저장할 GitHub 저장소 이름 (예: `my-til-repo`)
            *   **File Path:** TIL 파일이 저장될 경로와 파일명 (예: `TIL/{{ new Date().toISOString().split('T')[0] }}-오늘의-TIL.md` 또는 `TIL/{{ $json.gemini_summary_title }}.md`)
            *   **Content:** Gemini에서 처리된 텍스트 (`{{ $json.gemini_processed_text }}`)
            *   **Commit Message:** 커밋 메시지 (예: `TIL: {{ new Date().toISOString().split('T')[0] }} 자동 기록`)
    *   **파일 존재 여부 확인 및 내용 추가 (선택 사항):**
        *   만약 매번 새로운 파일을 생성하는 것이 아니라, 특정 날짜 또는 제목의 기존 파일에 내용을 추가(Append)하려면 추가적인 로직이 필요합니다.
        *   예시: `GitHub` 노드의 `Get Content`를 사용하여 파일 존재 여부를 확인합니다.
        *   `If` 노드를 사용하여 파일이 없으면 `Create File` 노드로 새 파일을 생성하고, 파일이 있으면 `Get Content`로 기존 내용을 가져와 `Code` 노드 등에서 새로운 내용과 결합한 후 `Update File` 노드로 업데이트합니다.

### Insight

이 자동화 워크플로우는 단순히 TIL 작성의 번거로움을 줄이는 것을 넘어 여러 가지 이점을 제공합니다.

*   **생산성 향상:** 수동으로 작성하고 GitHub에 푸시하는 시간을 절약하여 본질적인 학습과 업무에 집중할 수 있습니다.
*   **콘텐츠 품질 향상:** Gemini AI를 활용하여 TIL 내용을 자동으로 요약하거나, 특정 형식에 맞춰 재구성함으로써 일관성 있고 고품질의 TIL을 유지할 수 있습니다. 특히 짧은 메모나 대화 내용에서 핵심을 추출하여 체계적인 TIL로 만드는 데 유용합니다.
*   **꾸준한 기록:** Discord와 같은 일상적인 소통 채널에서 아이디어가 발생하면 즉시 기록으로 남길 수 있는 트리거를 제공하여, TIL 작성 습관을 형성하고 유지하는 데 도움을 줍니다.
*   **확장성:** 이 워크플로우는 필요에 따라 확장될 수 있습니다. 예를 들어, Gemini에 특정 키워드를 분석하게 하여 관련 태그를 자동으로 생성하거나, 여러 소스(슬랙, 이메일 등)에서 학습 내용을 수집하여 통합된 TIL을 만들 수도 있습니다.
*   **보안 고려사항:** API 키와 PAT는 워크플로우 내에 직접 하드코딩하지 않고, n8n의 Credential 기능을 통해 안전하게 관리해야 합니다. 워크플로우가 공개되지 않도록 주의하고, 필요한 최소한의 권한만 부여하는 것이 중요합니다. GitHub PAT의 경우 `repo` 스코프는 광범위하므로, 특정 저장소에 대한 접근만 허용하는 Fine-grained PAT 사용을 적극 권장합니다.
*   **에러 처리:** Gemini API 호출 실패나 GitHub 푸시 실패 등의 예외 상황에 대비하여 n8n의 에러 처리 노드(`Catch Errors`)를 사용하여 관리자에게 알림을 보내거나 재시도 로직을 설정하는 등의 후속 조치를 설정하는 것이 좋습니다.