---
title: "ollama, Unity연동하는 방법"
author:
  name: lgh
date: 2024-03-19 15:00:00 +09:00
categories: [AI]
tags: [AI, Unity, Server]
---

## 무엇이 가능한가
* Unity 앱에서 질문 내용을 로컬의 챗봇 서버로 보내고 서버로부터 응답이 가능

## 서버 구조
{{< figure src="/images/Ollama-Unity/whimsical.JPG" width="400" caption="構造" >}}

## 사용한 장비, 소프트웨어
1. OrangePi 5(메모리8GB 모델)
2. Ollama, Docker
3. Ubuntu Server

## 동영상
{{< youtube X3Jx69wiGVk >}}

## 장점
1. ChatGPT같은 서비스를 로컬서버로 구축하는게 가능

## 단점
1. ChatBot서버를 구축한 하드웨어의성능상 한계로인해 서버로부터 답변을 받는데까지 약20초 정도가 소요됨
3. 영어 이외의 언어로 질문하면 답변의 질이 영어로 질문할때보다 떨어짐

## 앞으로의 계획
* ChatBot으로부터 답변받은 내용을 AI TTS로 음성을 재생하는 기능 추가

## 참고 사이트
- [ollama-GitHub](https://github.com/ollama/ollama)
- [ollama공식 사이트](https://ollama.com/)