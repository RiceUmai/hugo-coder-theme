+++
authors = ["lgh"]
title = "UnityとChatGPT、AI TTSの連携"
date = "2024-04-14"
description = "Guide to emoji usage in Hugo"
tags = [
    "ai",
    "tts",
    "chatGPT",
]
categories = [
    "ai",
]

+++



# UnityアプリのGitHub Link

* https://github.com/RiceUmai/ai-chat-void-bot

# 主な機能

* Unityで質問内容を送ると、ChatGPTから返信が来て、返信した内容を AI TTSで読むアプリ

# 全体的な構造 

{{< figure src="/images/ai-chatBot-tts/ai-tts-server.JPG">}}


# Style-Bert-VLTS2のAPIサーバ実行

* インストール

  ```py
  git clone https://github.com/litagin02/Style-Bert-VITS2.git
  cd Style-Bert-VITS2
  pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
  pip install -r requirements.txt
  python initialize.py  # 必要なモデルとデフォルトTTSモデルをダウンロード
  ```

* APIサーバ実行

  ```py
  python server_fastapi.py
  ```

  実行すると、「http://127.0.0.1:5000」でAPIサーバが実行される

  Web上で「 http://127.0.0.1:5000/docs」に接続すると、API仕様の確認ができる。

  {{< figure src="/images/ai-chatBot-tts/SVC-api.JPG">}}

# Unity側の設定

## OpenAI API key設定

* OpenAIのAPIを入力

{{< figure src="/images/ai-chatBot-tts/unity-open-api.JPG" caption="Assets\Scripts\ScripableObject\OpenAPIKey">}}

## Style-Bert-VLTS2のAPIサーバ設定
* BaseURLにStyle-Bert-VLTS2のAPIサーバのアドレスを入力(Local上なら、「http://127.0.0.1:5000/」)

{{< figure src="/images/ai-chatBot-tts/Style-Bert-VITS2-setUp.JPG">}}

# 実行動画

{{< youtube EwodFp2bt0c>}}


# 使用したリソース

* つくよみちゃんプロジェクト (https://tyc.rei-yumesaki.net/about/project/)

# 参考サイト

* https://note.com/eurekachan/n/nad3a3ded1c76


# 使用したソフト, Plugin

* Style-Bert-VITS2 (https://github.com/litagin02/Style-Bert-VITS2)
* OpenAI API (https://openai.com/blog/openai-api)
* UniBudouX (https://github.com/Accky/UniBudouX)
* UniTask (https://github.com/Cysharp/UniTask)
* UniRx (https://github.com/neuecc/UniRx)
* DOTween (https://dotween.demigiant.com/)