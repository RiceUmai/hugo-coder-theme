+++
authors = ["lgh"]
title = "Unity와 ChatGPT, AI TTS 사용"
date = "2024-04-14"
description = "Unity에서ChatGPT와AI TTS 사용"
tags = [
    "ai",
    "tts",
    "chatGPT",
]
categories = [
    "ai",
]
+++

# Unity 앱의 GitHub 링크

* [GitHub 링크](https://github.com/RiceUmai/ai-chat-void-bot)

# 주요 기능

* Unity에서 질문을 보내면 ChatGPT에서 응답을 받아 그 내용을 AI TTS로 읽어주는 앱.

# 전체적인 구조 

{{< figure src="/images/ai-chatBot-tts/ai-tts-server.JPG">}}

# Style-Bert-VLTS2의 API 서버 실행

* 설치

  ```py
  git clone https://github.com/litagin02/Style-Bert-VITS2.git
  cd Style-Bert-VITS2
  pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
  pip install -r requirements.txt
  python initialize.py  # 필요한 모델과 기본 TTS 모델을 다운로드합니다.
  ```

* API 서버 실행

  ```py
  python server_fastapi.py
  ```

  실행하면 "http://127.0.0.1:5000"에서 API 서버가 실행됩니다. 웹에서 "http://127.0.0.1:5000/docs"에 연결하여 API 사양을 확인할 수 있습니다.

  {{< figure src="/images/ai-chatBot-tts/SVC-api.JPG">}}

# Unity 측의 설정

## OpenAI API 키 설정

* OpenAI의 API를 입력합니다.

{{< figure src="/images/ai-chatBot-tts/unity-open-api.JPG" caption="Assets\Scripts\ScripableObject\OpenAPIKey">}}

## Style-Bert-VLTS2의 API 서버 설정
* BaseURL에 Style-Bert-VLTS2의 API 서버 주소를 입력합니다. (로컬서버인 경우 "http://127.0.0.1:5000/")

{{< figure src="/images/ai-chatBot-tts/Style-Bert-VITS2-setUp.JPG">}}

# 동영상

{{< youtube EwodFp2bt0c>}}

# 한계
* Style-Bert-VITS2는 한국어가 지원이 안됨 (중국어, 영어, 일본어 가능)

# 사용한 리소스

* つくよみちゃん 프로젝트 ([링크](https://tyc.rei-yumesaki.net/about/project/))

# 참고 사이트

* [링크](https://note.com/eurekachan/n/nad3a3ded1c76)

# 사용한 소프트웨어 및 플러그인

* Style-Bert-VITS2 ([링크](https://github.com/litagin02/Style-Bert-VITS2))
* OpenAI API ([링크](https://openai.com/blog/openai-api))
* UniBudouX ([링크](https://github.com/Accky/UniBudouX))
* UniTask ([링크](https://github.com/Cysharp/UniTask))
* UniRx ([링크](https://github.com/neuecc/UniRx))
* DOTween ([링크](https://dotween.demigiant.com/))
```