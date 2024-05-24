+++
authors = ["lgh"]
title = "OpenGl Game"
date = "2020-12-22"
description = ""
tags = [
    "c++", "OpenGL", "GLSL"
]
categories = [
    "C++",
]
series = ["C++"]
+++

# OpenGL 프로젝트 개요

## 계발 환경
* C++, GLSL, OpenGL
* Visual Studio 2019, Visual Studio Code

## 사용 라이브러리
* GLFW3: [https://www.glfw.org/](https://www.glfw.org/)
* glad: [https://glad.dav1d.de/](https://glad.dav1d.de/)
* glm: [https://github.com/g-truc/glm](https://github.com/g-truc/glm)
* std_image: [https://github.com/nothings/stb](https://github.com/nothings/stb)
* FreeType: [https://www.freetype.org/](https://www.freetype.org/)

## 제작 기간
* 2020년 10월 ~ 2020년 12월

## 개발 인원
* 개인 제작

## YouTube 동영상
{{< youtube C13nFixrUdM >}}

## 참고 사이트
* [https://learnopengl.com/](https://learnopengl.com/)

## GitHub URL
* [https://github.com/congibab/OpenGL_Game](https://github.com/congibab/OpenGL_Game)

# 제작 의도
C++ 기초지식을 배우고싶어서 조사하던중 Learn OpenGL이라는 사이트를 발견해서 해당 사이트의 소스 코드를 참고하면서 만든 첫 번째 C++ 프로그램입니다.

# 프로그램 화면
![Desktop View](/images/OpenGl-Game/ProgramScene.JPG)

## 프로그램 화면 설명
1. W, S, A, D로 카메라 이동
2. 마우스 이동으로 카메라 회전
3. Enemy(오른쪽 상단) 큐브 수
4. Camera position(오른쪽 하단) 상단부터 x, y, z
5. 큐브에 맞으면 큐브가 사라짐 (Enemy 숫자가 감소)
6. 큐브가 0이 되거나 Time(왼쪽 상단)이 0이 되면 프로그램 종료

## 큐브 오브젝트에 구현한 기능
1. 텍스처 적용
2. 큐브 이동 및 이동 방향에 따른 회전
3. 충돌 판정 적용

# 큐브 오브젝트 소스 코드 (일부 발췌)
## 큐브 정점 데이터 (Front 면만)

```cpp
//cube.cpp
    float vertices[] = {
        //local positions     //normals    //texture coords
        -0.5f, -0.5f, -0.5f,  0.0f,  0.0f, -1.0f,  0.0f,  0.0f,
         0.5f, -0.5f, -0.5f,  0.0f,  0.0f, -1.0f,  1.0f,  0.0f,
         0.5f,  0.5f, -0.5f,  0.0f,  0.0f, -1.0f,  1.0f,  1.0f,
         0.5f,  0.5f, -0.5f,  0.0f,  0.0f, -1.0f,  1.0f,  1.0f,
        -0.5f,  0.5f, -0.5f,  0.0f,  0.0f, -1.0f,  0.0f,  1.0f,
        -0.5f, -0.5f, -0.5f,  0.0f,  0.0f, -1.0f,  0.0f,  0.0f,
    };
```

## Shader에 값 (변수 vertices) 전달
```cpp
//cube.cpp
    glGenVertexArrays(1, &cubeVAO);
    glGenBuffers(1, &VBO);

    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

    glBindVertexArray(cubeVAO);

    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)0); //local positions data
    glEnableVertexAttribArray(0); //layout (location = 0)에 local Position을 전달
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(3 * sizeof(float))); //normals data
    glEnableVertexAttribArray(1); //layout (location = 1)에 normals data를 전달
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(6 * sizeof(float))); //texture coords data
    glEnableVertexAttribArray(2); //layout (location = 2)에 texture coords data를 전달
}
```

## 행렬 변환 (좌표, 회전, 스케일)
```cpp
//cube.cpp
	glm::vec3 Position;
	glm::vec3 Rotation;
	glm::vec3 Scale;
//============================
//중략
//============================
    glm::mat4 model = glm::mat4(1.0f);
    model = glm::translate(model, Position); //오브젝트의 좌표 (World Position)
    model = glm::rotate(model, angle, Rotation); //회전 (각도, 방향)
    model = glm::scale(model, Scale); // 스케일 설정

    glUniformMatrix4fv(glGetUniformLocation(ID, "model", 1, GL_FALSE, &mat[0][0]);
    //uniform mat4 model에 전달
```
```glsl
//Cube.vs
#version 330 core
layout (location = 0) in vec3 aPos; //cpu에서 받은 local positions data를 aPos로 선언
layout (location = 1) in vec3 aNormal;
layout (location = 2) in vec2 aTexCoords;

uniform mat4 model; //glUniformMatrix4fv(glGetUniformLocation(ID, "model", 1, GL_FALSE, &mat[0][0]);
```

## 사용 예
```cpp
//game.cpp
Shader shader("vertex shader path", "fragment shader path")
Cube* cube;
cube->Update(DeltaTime); //DeltaTime은 glfwGetTime() 함수를 사용하여 계산
cube->Draw(shader, projection, view); //projection, views는 카메라 속성
delete cube //오브젝트 해제
```

# 충돌 판정
## game.cpp
```cpp
bool Game::CollisionAABB(Cube* Target, Cube* box)
{
	return (Target->GetMinPos().x <= box->GetMaxPos().x && Target->GetMaxPos().x >= box->GetMinPos().x) &&
		(Target->GetMinPos().y <= box->GetMaxPos().y && Target->GetMaxPos().y >= box->GetMinPos().y) &&
		(Target->GetMinPos().z <= box->GetMaxPos().z && Target->GetMaxPos().z >= box->GetMinPos().z);
}
```

# 후처리 (화면 이동)

![Desktop View](/images/OpenGl-Game/GameScene.JPG)

![Desktop View](/images/OpenGl-Game/ScreenMovement.JPG)

## 적용 설명
1. 텍스처 좌표를 조금씩 오른쪽 위로 이동합니다.
2. 오른쪽 위의 끝 지점에 도달하면 텍스처 좌표를 왼쪽 아래로 이동시킵니다.
3. 1번과 2번을 반복합니다.

## 프로그램 조작 설명
1. 마우스 **왼쪽** 클릭으로 다음 Shader로 전환
2. 마우스 **오른쪽** 클릭으로 이전 Shader로 전환

## 적용 과정
1. FrameBuffer 생성
2. 씬을 그릴 위치 지정 (생성한 FrameBuffer)
3. 지정한 FrameBuffer에 GameScene을 그리고 저장
4. 생성한 FrameBuffer를 기본 FrameBuffer로 변경
5. 후처리를 적용할 객체 생성 (사각형)
6. 사각형에 텍스처 (후처리를 적용하여 객체 생성)를 적용
7. Shader 적용

## ScreenRender.cpp (FrameBuffers 생성)

```cpp
ScreenRender::ScreenRender()
{
	glGenFramebuffers(1, &framebuffer);
	glBindFramebuffer(GL_FRAMEBUFFER, framebuffer);
	//=============================================
	glGenTextures(1, &textureColorbuffer);
	glBindTexture(GL_TEXTURE_2D, textureColorbuffer);
	glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, Setting::SCR_WIDTH, Setting::SCR_HEIGHT, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
	glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
	glBindTexture(GL_TEXTURE_2D, 0);
	//=============================================
	glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_

2D, textureColorbuffer, 0);
	//=============================================
	glGenRenderbuffers(1, &rbo);
	glBindRenderbuffer(GL_RENDERBUFFER, rbo);
	glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, Setting::SCR_WIDTH, Setting::SCR_HEIGHT);
	glBindRenderbuffer(GL_RENDERBUFFER, 0);
	//=============================================
	glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
	glBindFramebuffer(GL_FRAMEBUFFER, 0);
}
```

## quad.h.cpp (사각형 생성)
```cpp
float quadVertices[] = {
		-1.0f,  1.0f,  0.0f, 1.0f, //positions(-1.0f, 1.0f) texCoords(0.0f,1.0f)
		-1.0f, -1.0f,  0.0f, 0.0f,
		 1.0f, -1.0f,  1.0f, 0.0f,

		-1.0f,  1.0f,  0.0f, 1.0f,
		 1.0f, -1.0f,  1.0f, 0.0f,
		 1.0f,  1.0f,  1.0f, 1.0f
};
//중략
    //shader 등록
	Shaders.push_back(Shader("Shader/framebuffers_screen.vs", "Shader/framebuffers_screen.fs"));
	Shaders[Shaders_indice].setFloat("Time", glfwGetTime());
	Shaders[Shaders_indice].use();
```

## 사용 예
```cpp
//Main.CPP
#include "ScreenRender.h"
#include "Game.h"
#include "quad.h"

//중략
	game = new Game();
	scrennRender = new ScreenRender();
	_quad = new quad();
	while (!glfwWindowShouldClose(window))
	{
		gametime.Time_Measure();
   		scrennRender->use(); //생성한 Framebuffer 설정

        //씬 그리기
		game->SetCameraPos(camera.Position);
		game->Update(gametime.GetDeltaTime());
		game->Draw(projection, view);
		
		scrennRender->free(); //기본 Framebuffer 설정
        //생성한 Framebuffer에서 씬 데이터를 텍스처화하여 얻음
		_quad->SetTexture(scrennRender->GetTextureColorbuffer());
		_quad->Draw();

		gametime.DeltaTime_Update();
		glfwSwapBuffers(window);
		glfwPollEvents();
	}
```

## vertex shader
```glsl
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec2 aTexCoords;

out vec2 TexCoords;

void main()
{
    TexCoords = aTexCoords;
    gl_Position = vec4(aPos.x, aPos.y, 0.0f, 1.0f);
}
```

## fragment shader
```glsl
#version 330 core
out vec4 FragColor;

in vec2 TexCoords;

uniform sampler2D screenTexture;
uniform float Time;

void main()
{
    vec3 col = texture(screenTexture, TexCoords + sin(Time)).rgb;
    FragColor = vec4(col, 1.0f);
}
```

# 기타 후처리

## 화면 분할
![Desktop View](/images/OpenGl-Game/postshader2.JPG)

## 모자이크
![Desktop View](/images/OpenGl-Game/postshader3.JPG)

## 그레이스케일
![Desktop View](/images/OpenGl-Game/postshader4.JPG)