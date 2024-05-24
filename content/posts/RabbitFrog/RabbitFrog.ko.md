+++
authors = ["lgh"]
title = "Unity Game(팀 제작)"
date = "2021-05-01"
description = ""
tags = [
    "Unity"
]
categories = [
    "Unity",
]
series = ["Unity"]
+++

# 水墨鳥獣

## 제작 이유
* 팀 제작 경험을 쌓기 위한 학교 수업입니다.

## 장르
* 타워 디펜스

## 실행 환경
* iOS

## 사용 도구
* Unity

## 사용 언어
* C#

## 제작 기간
* 2020년 5월 ~ 2021년 2월 (매주 금요일)

## 제작 인원
* 10명

## 동영상 (YouTube)
{{< youtube mxTw6bZQGsE >}}

# 프로그래밍 담당

## UI 전반
![Desktop View](/images/RabbitFrog/RabbitFrog_UI.JPG)

![Desktop View](/images/RabbitFrog/RabbitFrog_UI2.JPG)

### 시작 확인 화면 스크립트
```csharp
public void NextScene()
{
    StartCoroutine(effect_sketch.NextScene(StageSelectControl.NextScene));
    DeckManager.SetDeckObject();
    confirmCanvas.rootCanvas.enabled = !confirmCanvas.rootCanvas.enabled;
}

public void Cancel()
{
    confirmCanvas.rootCanvas.enabled = !confirmCanvas.rootCanvas.enabled;
}
```

## 플레이어 스킬
![Desktop View](/images/RabbitFrog/RabbitFrog_skill.JPG)

* 마우스 왼쪽 클릭으로 직선이나 원을 그릴 수 있습니다.
* 직선을 그리면 적을 멈추는 벽을 생성합니다 (노란 상자).
* 원을 그리면 적을 불어내는 객체를 생성합니다 (빨간 상자).

### 직선 및 원 판정 스크립트
```csharp
// 마우스를 드래그하여 선이 끝나는 지점을 결정
if (Input.GetMouseButton(0) && is_Drawing)
{
    endPos = Camera.main.ScreenToWorldPoint(new Vector3(Input.mousePosition.x,
                                                        Input.mousePosition.y,
                                                       -Camera.main.transform.position.z));

    if (!draw_able()) return;
    // 마우스가 움직이지 않으면 리스트에 추가하지 않음
    if (Vector2.Distance(tempPos, endPos) < 0.5f) return;

    Points.Add(endPos);
    Points_X.Add(endPos);
    Points_Y.Add(endPos);

    Drawing_obj.transform.position = endPos;
    mouce_obj.transform.position = endPos;
    tempPos = endPos;
}
```

## 이펙트 (동영상 참조)
* [동영상](https://www.youtube.com/watch?v=mxTw6bZQGsE#t=22s)

### 두루마리 애니메이션
```csharp
public IEnumerator Open(RectTransform ObjTarget)
{
    OptionController.is_runing = true;
    ObjTarget.sizeDelta = new Vector2(75, 650);
    while (image.fillAmount <= 1)
    {
        if (image.fillAmount >= 1) break;
        yield return new WaitForSeconds(Time.deltaTime);
        image.fillAmount += Time.deltaTime;
    }
    OptionController.is_runing = false;
}

// 편성 화면을 닫는 처리
public IEnumerator Close(RectTransform ObjTarget)
{
    OptionController.is_runing = true;
    while (image.fillAmount >= 0)
    {
        if (image.fillAmount <= 0) break;
        yield return new WaitForSeconds(Time.deltaTime);
        image.fillAmount -= Time.deltaTime;
    }
    ObjTarget.sizeDelta = new Vector2(75, 450);
    OptionController.is_runing = false;
}
```

## 대화 씬 전반
![Desktop View](/images/RabbitFrog/RabbitFrog_Scenario.JPG)

* 기획자가 Excel로 작업한 대화 데이터를 적용

### 대화 데이터(CSV)를 Unity에서 읽는 스크립트
```csharp
void CSVReader(string CSVPATH,
                List<string> text,
                List<string> Name,
                List<string> num)
{
    TextAsset data;

    data = Resources.Load(CSVPATH, typeof(TextAsset)) as TextAsset;
    StringReader sr = new StringReader(data.text);

    string line;
    string[] text_split;
    line = sr.ReadLine();

    while (line != null)
    {
        text_split = line.Split(',');
        Name.Add(text_split[0]); // name, text, num
        text.Add(text_split[1]); // 0  , 1   , 2
        num.Add(text_split[2]);
        line = sr.ReadLine();
    }
    // 1행 제거
    text.RemoveAt(0);
    Name.RemoveAt(0);
    num.RemoveAt(0);
}
```