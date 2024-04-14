---
title: "AirTest, Poco를 이용한 Unity 앱의 자동 테스트"
author:
  name: 이
date: 2024-03-18 18:30:00 +0900
categories: [Git]
tags: [자동화 테스트, Unity, iOS, Android]
---

## 무엇을 할 수 있나요?

- Test Framework(Airtest+Poco) 및 Python 코드를 사용하여 Unity 앱을 자동으로 테스트하는 기술
- BlueStacks, Android, iOS를 지원합니다. (iOS의 경우, Apple 제품의 컴퓨터가 필요합니다)

## 테스트 프로젝트 실행하기

- Unity 프로젝트를 다운로드하고, apk를 BlueStacks나 실제 디바이스에 설치합니다.
* [샘플 프로젝트](https://github.com/RiceUmai/airTest-poco-test)
  
- PC에서 Airtest IDE를 열고 "poco-test-2/python" 폴더 내의 "autoTest.py"를 엽니다.

- 디바이스를 Airtest IDE에 연결합니다.
    - **BlueStacks**의 경우,
      
        - BlueStacks를 실행합니다.
        - Devices의 remote connection란을 선택하고, BlueStacks의 ADB 주소를 입력한 후 Connect 버튼을 클릭합니다.
        - Python 스크립트의 아래 코드에도 ADB 주소를 수정합니다.
        
        ```py
        if not cli_setup():
        auto_setup(__file__, logdir=True, devices=["android adb 주소])
        ```
        ![Desktop View](/images/airTest-Poco/connection.jpg)
        ![Desktop View](/images/airTest-Poco/BlueStackADB.jpg)
        
    - **Android 실제 디바이스**의 경우
      
        - Android 디바이스를 USB로 연결하고, 리스트에서 connect 버튼을 클릭합니다.
        
          {{< figure src="/images/airTest-Poco/androidDevice.jpg" height= "500">}}
        
        - 연결에 성공하면 아래 이미지와 같아집니다.
          
          {{< figure src="/images/airTest-Poco/connection12.jpg">}}
          
        - 테스트가 종료된 후, 아래 아이콘을 클릭하면 웹 브라우저에서 테스트 보고서를 확인할 수 있습니다.
        
          {{< figure src="/images/airTest-Poco/report.jpg">}}

## 실제 동영상
{{< youtube 3Xkd5EqhUtI >}}

## 테스트 보고서
{{< youtube 2UXcHy_2qw0 >}}

## GitHub 링크
- [AirTest](https://github.com/AirtestProject/Airtest)
- [Poco-SDK](https://github.com/AirtestProject/Poco-SDK)
## 공식 문서
- [Airtest 문서](https://airtest.netease.com/)
- [Poco 문서](https://poco.readthedocs.io/en/latest/source/doc/poco-example/basic.html)

## 테스트한 Python 코드
```py
# unity poco driver를 이 경로에서 가져옵니다.
from poco.drivers.unity3d import UnityPoco
import time
import sys

# -*- encoding=utf8 -*-
__author__ = "test"

from airtest.core.api import *
from airtest.cli.parser import cli_setup

if not cli_setup():
    auto_setup(__file__, logdir=True, devices=["android://127.0.0.1:{임의의 포트 번호}/127.0.0.1:{임의의 포트 번호}",])


# 스크립트 내용
print("start...")

poco = UnityPoco()

# ==========================
# ==========================
# GUI 데모 ui의 버튼을 누릅니다.
newgame_btn = poco('Newgame', type='Button')
newgame_btn.click()

contunue_btn = poco('Continue', type='Button')
contunue_btn.focus('anchor').click()

settings_btn = poco('Settings', type='Button')
settings_btn.click()


# ==========================
# ==========================
# GameplaySetting 페이지의 버튼 및 Sliader 조작
settings = poco('SettingsWindow')
settings.wait_for_appearance()

gameplay_btn = poco('Gameplay', type='Button')
gameplay_btn.click()

gamePlayWindow = poco('GamePlayWindow')
gamePlayWindow.wait_for_appearance()

settingsSlders = gamePlayWindow.offspring(type = 'Slider').child('Handle Slide Area')

for slider in settingsSlders:
    startSlide = slider.focus([0, 0.5])
    endSlide = slider.focus([1, 0.5])
    startSlide.drag_to(endSlide)    
    endSlide.drag_to(startSlide)


AOToggle = gamePlayWindow.offspring(type = 'Toggle').child('Checkmark')
AOToggle.click()
AOToggle.click()

close_btn = gamePlayWindow.child('Panel').child(type='Button')
close_btn.click()
gamePlayWindow.wait_for_disappearance()


# ==========================
# ==========================
# VideoSetting 페이지의 버튼 및 Sliader 조작

video_btn = poco('Video', type='Button')
video_btn.click()
videoWindow = poco('VideoWindow')
videoWindow.wait_for_appearance()

videoSettingSliders = videoWindow.offspring(type= 'Slider').child('Handle Slide Area')

for slider in videoSettingSliders:
    startSlide = slider.focus([0, 0.5])
    endSlide = slider.focus([1, 0.5])
    startSlide.drag_to(endSlide)
    endSlide.drag_to(startSlide)
    
videoSettingToggles = videoWindow.offspring(type= 'Toggle').child('Checkmark')

for toggle in videoSettingToggles:
    toggle.click()
    toggle.click()

close_btn = videoWindow.child('Panel').child(type='Button')
close_btn.click()
videoWindow.wait_for_disappearance()

# ==========================
# ==========================
# AudioSetting 페이지의 버튼 및 Sliader 조작

audio_btn = poco('Audio', type='Button')
audio_btn.click()

audioWindow = poco('AudioWindow')
audioSettingSliders = audioWindow.offspring(type= 'Slider').child('Handle Slide Area')

for slider in audioSettingSliders:
    startSlide = slider.focus([0, 0.5])
    endSlide = slider.focus([1, 0.5])
    startSlide.drag_to(endSlide)
    endSlide.drag_to(startSlide)
    
close_btn = audioWindow.child('Panel').child(type='Button')
close_btn.click()
audioSettingSliders.wait_for_disappearance()

# ==========================
# ==========================
#  Settings 페이지로 돌아가는 버튼을 누릅니다.
back_btn = poco('Back', type='