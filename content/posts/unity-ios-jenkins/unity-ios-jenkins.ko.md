+++
authors = ["lgh"]
title = "Jenkins, Unity, Xcode를 사용해서 자동빌드 환경 구축"
date = "2024-05-21"
description = "Unity프로젝트를 ios로 자동빌드"
tags = [
    "jenkins",
    "xcode",
    "unity",
    "ios",
]
categories = [
    "jenkins",
    "xcode",
    "unity",
    "ios"
]
series = ["jenkins"]
+++


# 실행환경
* jenkins
* mac mini m2
* unity 2023
* xcode 14
* git

# Unity Project측 build script
* Assets/Editor 폴더에 스크립트 작성
```cs
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using UnityEditor;
using UnityEditor.Build.Reporting;
using UnityEngine;

public class ApplicationBuilder
{
    public static void Build()
    {
        const string outputDirKey = "-output-dir";

        var args = Environment.GetCommandLineArgs();
        var locationPathName = GetArgumentValue(args, outputDirKey);

        var options = new BuildPlayerOptions();
        options.scenes = EditorBuildSettingsScene.GetActiveSceneList(EditorBuildSettings.scenes);
        options.locationPathName = GetArgumentValue(args, "-output-dir");
        options.target = BuildTarget.iOS;
        options.options = BuildOptions.None;

        var buildReport = BuildPipeline.BuildPlayer(options);

        if (buildReport.summary.result == BuildResult.Succeeded)
        {
            Debug.Log("[Success]");
            EditorApplication.Exit(0);
        }
        else
        {
            Debug.Log("[Failure]" + buildReport);
            EditorApplication.Exit(1);
        }
    }

    private static string GetArgumentValue(IReadOnlyList<string> args, string key)
    {
        var index = args.ToList().FindIndex(arg => arg == key);
        var paramIndex = index + 1;

        if (index < 0 || args.Count() <= paramIndex)
        {
            return null;
        }

        return args[paramIndex];
    }
}

```

# jenkins Plugin Settings
1. Jenkins Plugin : Xcode integration, Unity3d plugin 설치
2. Manage Jenkins -> Tools -> Xcode tools installations 항목 xcode Path 지정 (terminal에 'xcode-select -p' 입력시 표시되는 xcode Path를 입력)

![xcode-Path](/images/unity-ios-jenkins/xcodePath.png)

3. Manage Jenkins -> Tools -> Unity3d installations 항목의 unity Path 지정 

![xcode-Path](/images/unity-ios-jenkins/unityPath.png)

4. Manage Jenkins -> Credentials -> credentials 정보 추가
- Username : github 유저명
- Password : github에서 발행한 Personal access token
- Id : 인증명 
 
![xcode-Path](/images/unity-ios-jenkins/git-credentials.png)

# jenkins WorkSpace Settings
1. Dashboard -> New Item -> Freestyle project 선택
2. Source Code Management -> Git 설정

![xcode-Path](/images/unity-ios-jenkins/git-settings.png)

3. Configuration -> Build Build Steps항목에서 Invoke Unity3d Editor를 추가 
- Unity3d installation name에 Plugin Settings에서 설정한 unity 선택
- Editor command line arguments에 커멘드 입력

```bash
-quit -batchmode -projectPath ${WORKSPACE} -executeMethod ApplicationBuilder.Build -logFile ${WORKSPACE}/xcodeProject/build.log -output-dir ${WORKSPACE}/xcodeProject
```

- ${WORKSPACE}는 jenkins에서 설정한 고유변수 (workspace path)
- '-executeMethod ApplicationBuilder.Build' 는 unity project에서 Assets/Editor에 배치한 ApplicationBuilder Class내 Build 메소드를 실행하는 명령어

![xcode-Path](/images/unity-ios-jenkins/InvokeUnity.png)

4. Configuration -> Build Build Steps항목에서 Xcode 추가
- Development Team ID(10 자리 코드) 설정
- Configuration 항목에 Release 입력
  
![xcode-Path](/images/unity-ios-jenkins/xcode-Configuration.png)
  
- Xcode Schema File 항목에 Unity-iPhone 

![xcode-Path](/images/unity-ios-jenkins/Xcode-Schema-File.png)

- Generate Archive 설정

![xcode-Path](/images/unity-ios-jenkins/GenerateArchive.png)

- Code signing & OS X keychain options -> Code signing settings 항목에 Automatic Signing 체크
  
![xcode-Path](/images/unity-ios-jenkins/Automatic-Signing.png)

- Advanced Xcode build options 설정
- Xcode Project Directory 항목은 Unity에서 생성된 xcode project path
- Build output directory 항목은 xcode에서 빌드한 결과물 경로

![xcode-Path](/images/unity-ios-jenkins/AdvancedXcode-buildoptions.png)

5. Save 버튼클릭

# build 테스트

1. 생성한 workSpace -> Build Now 버튼 클릭

![xcode-Path](/images/unity-ios-jenkins/workSpace-buildNow.png)

2. build가 성공하면 이하 경로에 ips파일, archive파일이 생성됨
``` bash
/Users/{유저명}/.jenkins/workspace/{workSpace명}/builds/xcode/output
```

``` bash
/Users/{유저명}/.jenkins/workspace/{workSpace명}/builds/xcode/ips
```