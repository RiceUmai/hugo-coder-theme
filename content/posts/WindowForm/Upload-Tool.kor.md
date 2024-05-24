+++
authors = ["lgh"]
title = "이미지 업로드 툴"
date = "2021-04-09"
description = ""
tags = [
    "c#"
]
categories = [
    "c#",
]
series = ["Unity"]
+++

# 이미지 업로드 도구(요약)

## 제작 이유
효율성 향상을 위해 게임 기획자가 가챠 데이터를 데이터베이스에 업로드할 수 있도록 도와주는 도구를 제작하였습니다.

## 실행 환경
- Windows 10
- XAMPP v3.2.4 (Apache, MySQL)
- Chrome v89.0.4389.114
- Firefox v87.0

## 사용 도구
- Visual Studio Code

## 사용 언어
- PHP 7.4.15
- HTML5

## 제작 기간
2021년 4월 6일(화) ~ 2021년 4월 9일(금)

## 제작 인원
개인

## 동영상(YouTube)
{{< youtube f2tdzeMfCnM >}}

# 트리
```
─Upload-Tool
    │  DB_Init.php
    │  img_Upload.php
    │  README.md
    │  Tool.php
    │
    └─doc
```

<div style="page-break-after: always; visibility: hidden"> </div>

# DB_Init.php 설명서
``` console
php DB_Init.php DB_user DB_password DB_name Table_name
```
- 이미지 업로드 위치를 생성합니다.


# 실행 화면(웹 브라우저 화면)

## Main View(Tool.php)
![Desktop View](/images/Upload-Tool/view1.jpg)


### 작업 설명
1. 참조 버튼을 클릭하여 PNG 파일을 선택합니다(다중 선택 가능).
2. 랭크를 선택하고 Send 버튼을 클릭합니다.

## Result View(img_Upload.php)
![Desktop View](/images/Upload-Tool/result1.jpg)


- 업로드 성공, 실패 결과가 표시됩니다.
- 터미널 화면의 데이터베이스에 등록되어 있습니다.
- 업로드된 데이터의 이름은 암호화하여 등록합니다(**빨간 상자**).

<div style="page-break-after: always; visibility: hidden"> </div>

## 업로드 중복 방지
![Desktop View](/images/Upload-Tool/result2.jpg)

- 파일이 중복된 경우, "fail : this file uploaded"가 표시됩니다.

# 소스 코드(Tool.php)
```html
<body>
    <h1> png형식으로 업로드해주세요. </h1>
    <h2> 이미지 선택(다중 선택 가능) </h2>
    <form method="post" action="img_Upload.php" enctype="multipart/form-data">
        <input type="file" name="upload[]" multiple>

        <h3>랭크 설정</h3>
        <input type="radio" id ="B" name="Rank" value="B">
        <label for="B"> 랭크：B </label><br>
        <input type="radio" id ="A" name="Rank" value="A">
        <label for="A"> 랭크：A </label><br>
        <input type="radio" id ="S" name="Rank" value="S">
        <label for="S"> 랭크：S </label><br>

        <input type="submit" value="send"><br>
    </form>
</body>
```

<div style="page-break-after: always; visibility: hidden"> </div>

# 소스 코드(img_Upload.php)

```php
    $dir = "uploads/"; //저장될 위치

    //파일 이름은 사람이 식별할 수 없도록 암호화합니다.
    $File_Name_MD5 = md5(microtime() . '.' . $_FILES['upload']['name'][$i]). "." .$imageType;
```

## 에러 처리

```php
//형식을 PNG로 제한
  if($imageType != "png"){
        echo "Error　{$TargetFile} : please image type is png <br />";
        continue;
    }
//사이즈는 500KB 이하로 제한
    if($_FILES['upload']['size'][$i] > 500000){
        echo "{$TargetFile} : this image in too large <br />";
        continue;
    }
//============================
//중략
//============================

//서버에 중복 파일을 확인
    if(isset($row["id"])){
        if(file_exists($dir. $row["id"])){
            echo "fail : this file uploaded <br />";
            continue;
        }
    }
```

<div style="page-break-after: always; visibility: hidden"> </div>

## 업로드 처리

```php
$upload_Success =  move_uploaded_file($_FILES['upload']['tmp_name'][$i],$uploadPath);
        
    if(!$upload_Success){
        echo "업로드 실패했습니다.<br />";
        echo  $_FILES['upload']['error'][$i];
        continue;
    }

    $sql = "SELECT * FROM " . $Rank;
    $result = $conn->query($sql);
    $Row_count = $result->num_rows;
        
    $sql = "INSERT INTO $Rank (num, id, imgName)
            VALUES ('$Row_count', '$File_Name_MD5', '$TargetFile')";

    if($conn->query($