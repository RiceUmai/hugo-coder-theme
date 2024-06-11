+++
authors = ["lgh"]
title = "Unity에서의 UDP 소켓 통신 테스트"
date = "2021-05-11"
description = ""
tags = [
    "Unity"
]
categories = [
    "Unity",
]
series = ["Unity"]
+++

# Unity_UdpSample

## 제작 이유
다른 학생들도 UDP 통신을 사용하여 네트워크 게임을 개발할 수 있도록 샘플 코드와 클래스를 제작

## 실행 환경
* Windows 10

## 사용 도구
* Visual Studio 2019
* Unity v2020.2.5f1

## 사용 언어
* C# (System.Net.Sockets)
* Node.js v14.15.0 (dgram)

## 제작 기간
* 2021/05/09 ~ 2021/05/11

## 구현된 기능
* 클라이언트 간의 매칭 (2명 제한)

## 동영상 (YouTube)
{{< youtube fKeTiaLIYA8 >}}

## 제작 인원
* 개인

# 사용 방법

## 클라이언트
1. `Assets/Prefab`에 있는 `NetworkManager`를 씬에 배치합니다.
2. `NetworkManager`의 인스펙터에서 `Address`와 `Port`를 입력합니다.
3. 실행하여 콘솔 로그에 오류가 없으면 성공입니다.

* 주의: 서버를 실행한 후에 클라이언트 (Unity)를 실행하십시오.

## 서버 (Windows)
1. *터미널*을 엽니다.
2. `cd "다운로드한 위치"/Unity_UdpSample/server/Sample2`를 입력합니다.
3. `node index.js`를 입력합니다.
4. `server listening on 0.0.0.0:6060`가 출력되면 성공입니다.
5. 종료할 때는 `Ctrl + C`를 입력합니다.

# 설명 (클라이언트)

## NetworkManager.cs
* 서버와의 송수신을 담당합니다.

```csharp
// 서버와 연결 (서버 IP, 서버 Port)
void Connect(string Address, int Port);
// 서버로 데이터 전송
void Data_Send(JsonOBJ Message);
```

```csharp
// eventType 추가
public enum eventType
{
    init,
    chat,
    Message,
    // eventType 추가 가능
}
//==================================
// EventType에 따라 처리 분기
private void eventHandler(JsonOBJ data)
{
    eventType state = (eventType)Enum.Parse(typeof(eventType), data.type);

    switch (state)
    {
        case eventType.init:
            information.rival_ip = data.rival.address;
            information.rival_port = data.rival.port;
            break;

        case eventType.Message:
            break;

        // case eventType 추가 가능:
        //    break;
        
        default:
            break;
    }
}
```

## JsonOBJ.cs
* JSON 데이터를 각 타입으로 변환하고 관리합니다.

```csharp
[Serializable]
public class inform
{
    public string address;
    public int port;

    public string name;

    public Vector3 pos;
    public Vector3 rot;
    public Vector3 scal;
}
```

```csharp
[Serializable]
public class JsonOBJ
{
    public string type;
    public string msg;

    public inform own;
    public inform rival;

    public JsonOBJ(eventType state)
    {
        this.type = state.ToString();
    }
}
```

## information.cs (커스터마이즈 가능)
* 서버로부터 받은 주소를 저장합니다.

```csharp
public class information
{
    public static string own_ip { get; set; }
    public static int own_port { get; set; }
    public static string rival_ip { get; set; }
    public static int rival_port { get; set; }
}
```

# 설명 (서버)

## index.js
* 클라이언트로 데이터를 전송하는 함수

```js
function Emit(JsonObj, senderInfo) {
  var msg = JSON.stringify(JsonObj);
  server.send(msg, senderInfo.port, senderInfo.address, () => {
    // console.log(`Message sent to ${senderInfo.address}:${senderInfo.port} = ${msg}`)
  });
}
```

```js
// JSON 데이터를 분석하여 이벤트 처리
function eventHandler(msg, senderInfo) {
  try {
    var data = JSON.parse(msg);
    console.log('Messages received ' + msg);
    var thisPlayerId = uuidv4();

    switch (data.type) {
      // 매칭 처리
      case 'init':
        if (Object.keys(clients).length == 0) {
          setTimeout(clientsClear, 5000, clients);
        }

        for (let index in clients) {
          var JsonObj = data;
          JsonObj.rival.address = clients[index].address;
          JsonObj.rival.port = clients[index].port;
          Emit(JsonObj, senderInfo);
        }

        var thisPlayerId = uuidv4();
        clients[thisPlayerId] = senderInfo;
        console.log(Object.keys(clients).length);
        console.log(senderInfo);

        var JsonObj = data;
        JsonObj.rival.address = senderInfo.address;
        JsonObj.rival.port = senderInfo.port;
        Broadcast(JsonObj, clients, senderInfo);
        break;

        // case 'anotherEventType': 
        // break;

        default:
        break;
    }
  } catch (error) {
    console.log(error);
  }
}
```

## JsonInit
* JSON 구조

```js
module.exports = class JsonInit {
  constructor() {
    this.jsonObj = {
      type: null,

      own: {
        address: null,
        port: null,
        uuid: null,

        pos: { x: null, y: null, z: null },
        rot: { x: null, y: null, z: null }
      },

      rival: {
        address: null,
        port: null,
        uuid: null,

        pos: { x: null, y: null, z: null },
        rot: { x: null, y: null, z: null }
      }
    };
  }
  get() {
    return this.jsonObj;
  }
}
```