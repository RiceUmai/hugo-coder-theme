+++
authors = ["lgh"]
title = "파이썬으로 AOS 디바이스 스크린샷 촬영후 컴퓨터에 저장"
date = "2024-08-25"
description = ""
tags = [
    "Python",
    "Android",
    "BlueStacks",
    "adb"
]
categories = [
    "Python",
    "Android"
]

+++

# 설명
파이썬으로 adb에 접속된 안드로이드 디바이스 화면을 캡쳐해서 png로 컴퓨터에 보존하는 프로그램

# 라이브러리 설치
```sh
pip install -U pure-python-adb
```
* https://pypi.org/project/pure-python-adb/

# adb접속
```sh
adb connect {android device address}
```
# SourceCode
```py
from ppadb.client import Client as AdbClient

# Default is "127.0.0.1" and 5037
client = AdbClient(host="127.0.0.1", port=5037)
print(client.version())
print("getting list of devices")

devices = client.devices()
for onedevice in devices:
    print(str(onedevice))

result = devices[0].screencap()
with open("screen.png", "wb") as fp:
    fp.write(result)    
```

# 실행결과
* Python script를 실행하면 Python script위치에 screen.png 파일이 생성됨