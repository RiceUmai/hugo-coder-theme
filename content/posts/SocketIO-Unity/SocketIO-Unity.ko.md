+++
authors = ["lgh"]
title = "SocketIO,Unity를 사용한 네트워크 게임"
date = "2020-12-01"
description = ""
tags = [
    "Unity"
]
categories = [
    "Unity",
]
series = ["Unity"]
+++

# SocketIO와 Unity로 구현한 네트워크 통신 게임
## 사용 언어
Node.js, C#
## 실행 환경
Unity 2019.4.15f1  
Nodejs v14.15.0  
## 사용 모듈 (서버)
socketio 1.0.0  
uuid 8.3.2  
express 4.17.1  
http 0.0.1-security  
## 사용 라이브러리 (클라이언트)
SocketIO for Unity - v1.0.0 (https://github.com/vedi/unity-socket.io) (**지원 종료**)
## 제작 기간
2020년 10월 ~ 2020년 12월
## 개발 인원
개인

## 동영상 (YouTube)
{{< youtube Q4Arop1ba_M >}}

## GitHub URL
[https://github.com/congibab/UnityServer](https://github.com/congibab/UnityServer)

<div style="page-break-before:always"></div>

# 디렉토리 경로
```
UnityServer
│  .gitignore
│  README.md
│  
├─.vs
└─serverTest
    ├─Build
    ├─Client
    └─Server
        │  node_modules
        │  package-lock.json
        │  package.json
        │  php_deployment.sh
        │  tesdt.txt
        │  test.php
        │  
        └─node_modules
```

# 서버 실행
```bash
cd <your Path>/UnityServer/Server
node index.js
//실행 화면=====================================================
URL = ws://localhost:3000/socket.io/?EIO=4&transport=websocket
```

<div style="page-break-before:always"></div>

# 클라이언트 실행 (기본 설정은 개인 렌탈 서버)
## 연결할 IP를 변경하려면
![Desktop View](/images/SocketIO-Unity/ipConnect.JPG)

빨간 박스 : ws://**localhost**:3000/socket.io/?EIO=4&transport=websocket  
localhost 부분을 변경하려는 IP로 수정

## 타이틀 화면 (클라이언트1)
![Desktop View](/images/SocketIO-Unity/Title1.JPG)

빨간 박스 : Room 이름을 입력하고 Room Create **Button**을 누르면 게임에 들어감  
노란 박스 : 메시지를 입력하고 Send **Button**을 누르면 메시지를 서버로 **전송**  

<div style="page-break-before:always"></div>

## 타이틀 화면 (클라이언트2)
![Desktop View](/images/SocketIO-Unity/Title2.JPG)

빨간 박스 : 생성된 Room 목록이 표시되고, 해당 **Button**을 누르면 게임에 들어감  
노란 박스 : 서버로부터 **전송**된 메시지가 있으면 메시지 내용을 표시  
파란 박스 : 게임의 승리 판정

## 게임 화면 (대기)
![Desktop View](/images/SocketIO-Unity/Game.JPG)

빨간 박스 : Player1의 UUID 표시 (먼저 Room에 들어온 클라이언트)  
노란 박스 : Player2의 UUID 표시 (나중에 Room에 들어온 클라이언트) **비어있으면 아직 Player가 들어오지 않은 상태**  
클라이언트1은 다른 클라이언트2가 들어올 때까지 대기

<div style="page-break-before:always"></div>

## 게임 화면 (플레이)
![Desktop View](/images/SocketIO-Unity/Game2.JPG)
![Desktop View](/images/SocketIO-Unity/Game3.JPG)

빨간 박스 : 상대방 (클라이언트에 따라 다르게 보임)  
파란 박스 : 자신 (클라이언트에 따라 다르게 보임)  
노란 박스 : 점수가 5가 된 플레이어가 승리  
보라색 박스 : 공

### 설명
기본적으로 Pong 게임과 비슷한 게임입니다. 공이 왼쪽 또는 오른쪽 화면 밖으로 나가면 해당 방향에 따라 점수가 올라갑니다.  
먼저 점수가 5가 된 플레이어가 승리하게 됩니다. **(현재는 타이틀 화면으로 돌아감)**

### 조작 방법 (키보드)
W 키로 위로 이동, S 키로 아래로 이동 **(화면 내에서만 이동 가능)**

<div style="page-break-before:always"></div>

# 서버 측 소스 코드 (간략)
```javascript
//index.js
const { DH_NOT_SUITABLE_GENERATOR } = require('constants');
const { json } = require('express');
const { v4: uuidv4 } = require('uuid');

var app = require('express')();
var server = require('http').Server(app);
var io = require('socket.io')(server);

//=======================================
//=======================================

var Clients = [];
var Rooms = [];

server.listen(3000, () => {
	console.log('URL = ws://localhost:3000/socket.io/?EIO=4&transport=websocket');
});

app.get('/', function (req, res) {
	res.send('Hellow world"/"');
});

io.on('connection', function (socket) {

	var thisPlayerId = uuidv4();
	var lobby = 'lobby';
	socket.join(lobby);

	Clients[thisPlayerId] = socket;
	console.log("Another user connection in lobbty :" + thisPlayerId);

	socket.emit("InitPlayerid", { id: thisPlayerId });

	for (var i in Rooms) {
		socket.emit("UpdateRoomList", Rooms[i]);
	}

	socket.on('creatRoom', function (data) {
		console.log('create room' + data);
		var Room = {
			name: data.name,
			currnetUUID: ['', ''],
		};
		Rooms[data.name] = Room;
		socket.to(lobby).emit('UpdateRoomList', Room);
    });
```

<div style="page-break-before:always"></div>

# 클라이언트 측 소스 코드 (간략)
``` C#
//NetworkManager.cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using SocketIO;
using System;

    private void Start()
    {
        socket.On("open", (SocketIOEvent e) => { 
            Debug.Log("[SocketIO] Open received: " + e.name + " " + e.data); 
        });
        socket.On("error", (SocketIOEvent e) => { 
            Debug.Log("[SocketIO] Error received: " + e.name + " " + e.data);
        });
        socket.On("close", (SocketIOEvent e) => { 
            Debug.Log("[SocketIO] Close received: " + e.name + " " + e.data); 
        });


        socket.On("UpdatePosition", OnUpdatePosition);
        socket.On("UpdateBallPosition", OnUpdataBallPosition);
        socket.On("ballPositionReset", OnballPositionReset);
        socket.On("UpdateSore", OnUpdateSore);

        socket.On("UpdateRoomList", UpdateRoomList);

        socket.On("InitPlayerid", InitPlayerid);
        socket.On("removeRoom", removeRoom);
        socket.On("UpdateChaingLog", (SocketIOEvent e) => {
            chatManager.ReceiveMsg(e);
        });

        socket.On("GameInit", GameInit);
    }
```

# Jenkins를 사용한 자동 배포
![Desktop View](/images/SocketIO-Unity/jenkins.JPG)

GitHub에 데이터 push -> Jenkins로 서버 업데이트 및 재시작 -> Slack에 알림  
이 흐름으로 자동화를 구축