+++
authors = ["lgh"]
title = "데이터베이스에 저장된 이미지를 얻는 프로그램"
date = "2021-04-10"
description = ""
tags = [
    "cs"
]
categories = [
    "cs",
]
series = ["cs"]
+++

# 데이터베이스에 저장된 이미지를 얻는 프로그램

## 제작 이유
계발툴의 개발에 관심이 있어서 제작했습니다.

## 실행 환경
* Windows 10

## 사용 도구
* Visual Studio 2019

## 사용 언어
* C#

## 제작 기간
* 2021년 4월 8일(목) ~ 2021년 4월 10일(토)

## 동영상 (YouTube)
{{< youtube AXCVxSYtJSU >}}

## 제작 인원
* 개인

<div style="page-break-before:always"></div> 

# 소스 코드 (Form1.cs)

## 버튼 클릭 이벤트
```C#
    private void button1_Click(object sender, EventArgs e)
    {
        string url = "http://localhost/Gachar.php";
        var JsonObject = GetJsonData(url);

        url = "http://localhost/" + JsonObject.id;

        textBox1.Text = "Card Rank : " + JsonObject.Rank;
        pictureBox1.Image = GetImage(url);
        pictureBox1.SizeMode = PictureBoxSizeMode.StretchImage;
    }
```

<div style="page-break-before:always"></div> 

## 이미지 정보를 가져오기
```C#
    private Person GetJsonData(string url)
    {
        try
        {
            var request = WebRequest.Create(url);
            request.Method = "GET";

            var webResponse = request.GetResponse();
            var webStream = webResponse.GetResponseStream();

            var reader = new StreamReader(webStream);

            var data = reader.ReadToEnd();

            var JsonObject = JsonSerializer.Deserialize<Person>(data);

            return JsonObject;
        }
        catch (Exception e)
        {
            Console.WriteLine(e.Message);
            return null;
        }
    }
```

## 이미지 표시
```C#
    private Image GetImage(string url)
    {
        using (WebClient client = new WebClient())
        {
            byte[] imgArray;
            imgArray = client.DownloadData(url);

            using (MemoryStream memstr = new MemoryStream(imgArray))
            {
                Image img = Image.FromStream(memstr);
                return img;
            }
        }
    }
```