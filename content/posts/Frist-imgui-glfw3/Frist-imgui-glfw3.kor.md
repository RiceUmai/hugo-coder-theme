+++
authors = ["lgh"]
title = "Frist-imgui-glfw3"
date = "2021-01-22"
description = ""
tags = [
    "C++"
]
categories = [
    "C++",
]
series = ["C++"]
+++

# Frist-imgui-glfw3

## 계발환경
C++, GLSL, OpenGL  
Visual Studio 2019, VS Code

## 사용 라이브러리
GLFW3: [https://www.glfw.org/](https://www.glfw.org/)  
glad: [https://glad.dav1d.de/](https://glad.dav1d.de/)  
glm: [https://github.com/g-truc/glm](https://github.com/g-truc/glm)  
stb_image: [https://github.com/nothings/stb](https://github.com/nothings/stb)  
imgui: [https://github.com/ocornut/imgui](https://github.com/ocornut/imgui/)  
assimp: [https://github.com/assimp/assimp](https://github.com/assimp/assimp)  

## 제작 기간
2021-01

## 개발 인원
개인 제작

## 참고 사이트
Learn OpenGL: [https://learnopengl.com/](https://learnopengl.com/)

## 동영상(YouTube)
{{< youtube h1-rcwjNfMk >}}

## GitHub URL
[https://github.com/congibab/Frist-imgui-glfw3](https://github.com/congibab/Frist-imgui-glfw3)

## 목표
1. Geometry Shader 학습
2. imgui 라이브러리 학습
3. 3D 모델에 라이트를 적용하여 Toon Shader 구현

<div style="page-break-before: always"></div>

# 실행화면
![Desktop View](/images/Frist-imgui-glfw3/GameScene1.jpg)
![Desktop View](/images/Frist-imgui-glfw3/GameScene2.jpg)

## 설명
1. 3D 모델링을 로드 (assimp, stb_image, glm)
2. 3D 모델링에 Shader 적용
3. UI 시스템을 구축하여 Shader 파라미터 등록 (imgui)
4. Shader 파라미터를 수정하여 Shader 변경

## 조작 설명
1. 카메라 회전: 마우스 오른쪽 클릭 후 드래그
2. 카메라 이동: 키보드 W(위), S(아래), A(왼쪽), D(오른쪽)

<div style="page-break-before: always"></div>

# Grid 그리기

## 소스 코드
```cpp
// Grid.cpp
void Grid::Draw(Shader shader, glm::mat4 projection, glm::mat4 view)
{
//=======================
//중략
//=======================
	for (int i = 0; i < Column; i++)
	{
		glm::mat4 model = glm::mat4(1.0f);
		model = glm::translate(model, glm::vec3(Position.x + i - (Column / 2), Position.y, Position.z));
		model = glm::scale(model, glm::vec3(Column));
		shader.setMat4("model", model);
		line.Draw();
	}

// Line.cpp
float VerticalLine[] = 
{
    0.0f , 0.0f,  -0.5f,
    0.0f , 0.0f,  0.5f,
};

void Line::Draw()
{
	glBindVertexArray(VAO);
	glDrawArrays(GL_LINES, 0, 2);
	glBindVertexArray(0);
}

// Game.cpp
Grid grid(100, 100);
grid.setPosition(glm::vec3(0, -2.0f, 0));
grid.Draw(gridShader, projection, view);
}
```

<div style="page-break-before: always"></div>

# 배경 색 변경
![Desktop View](/images/Frist-imgui-glfw3/BackGroundColor.JPG" width="50%">)
![Desktop View](/images/Frist-imgui-glfw3/BackGround.JPG" width="40%">)

## 설명
색상 팔레트를 수정하여 배경색을 변경할 수 있습니다.

## 소스 코드
```cpp
// UI_Manager.cpp
// Update 함수
glm::vec4 BackGroundColor;
ImGui::ColorEdit4("Back Ground Color", &BackGroundColor.x);

// Game.cpp
// Renderer 함수
glm::vec4 vec(ui_Manager->getBackGroundColor());
float* data = glm::value_ptr(vec);
glClearColor(data[0], data[1], data[2], data[3]);
```

# 3D 모델링

## Sphere
![Desktop View](/images/Frist-imgui-glfw3/Sphere2.jpg)
![Desktop View](/images/Frist-imgui-glfw3/Thorn.gif)

## 설명
시간에 따라 가시가 생성됩니다.

## Geometry Shader (Unity Shader 참고)
```glsl
// Thorn.gs
// 참고 사이트: http://www.shaderslab.com/demo-80---triangles-to-pyramids.html

#version 330 core
layout (triangles) in;
layout (triangle_strip, max_vertices = 12) out;

//=========================================
//중략
//=========================================

void main() {
    normal = GetNormal();
  
    vec4 CentralPos = (gl_in[0].gl_Position + gl_in[1].gl_Position + gl_in[2].gl_Position) / 3;
    vec2 CenterTex = (gs_in[0].texCoords + gs_in[1].texCoords + gs_in[2].texCoords) / 3;

    CentralPos += vec4(normal, 0) * abs(sin(time)) * 2;

    for(int i = 0; i < 3; i++)
    {
        gl_Position = gl_in[i].gl_Position;
        TexCoords = gs_in[i].texCoords;
        EmitVertex();
        gl_Position = CentralPos;
        TexCoords = CenterTex;
        EmitVertex();
        int nexti = (i + 1) % 3;
        gl_Position = gl_in[nexti].gl_Position;
        TexCoords = gs_in[nexti].texCoords;
        EmitVertex();
    }
    
    gl_Position = gl_in[2].gl_Position;
    EmitVertex();
    gl_Position = gl_in[1].gl_Position;
    EmitVertex();
    gl_Position = gl_in[0].gl_Position;
    EmitVertex();
    EndPrimitive();
}
```

## rabbit(explode)
![Desktop View](/images/Frist-imgui-glfw3/rabbit.JPG)
![Desktop View](/images/Frist-imgui-glfw3/explode.gif)

### 설명
시간에 따라 모델이 작은 폴리곤으로 분해됩니다.

### Geometry Shader
```glsl
// 참고 사이트: https://learnopengl.com/Advanced-OpenGL/Geometry-Shader
// explode.gs
#version 330 core
layout (triangles) in;
layout (triangle_strip, max_vertices = 3) out;
//=========================================
//중략
//=========================================
vec4 explode(vec4 position, vec3 normal) {
    float magnitude = 5.0f;
    vec3 direction = normal * ((sin(time) + 1.0) / 2.0) * magnitude;
    vec4 col = position + vec4(direction, 0.0) * step(normal.y, sin(time));
    return col;
}

void main() {
    normal = GetNormal();

    gl_Position = explode(gl_in[0].gl_Position, normal);
    TexCoords = gs_in[0].texCoords;
    EmitVertex();
    gl_Position = explode(gl_in[1].gl_Position, normal);
    TexCoords = gs_in[1].texCoords;
    EmitVertex();
    gl_Position = explode(gl_in[2].gl_Position, normal);
    TexCoords = gs_in[2].texCoords;
    EmitVertex();
    EndPrimitive();
}
```

## rabbit(nomal face)
![Desktop View](/images/Frist-imgui-glfw3/nomalFace2.JPG)
![Desktop View](/images/Frist-imgui-glfw3/nomalFace.JPG)

### 설명
폴리곤의 Normal Vector 방향을 표시합니다.

<div style="page-break-before: always"></div>

### Geometry Shader
```glsl
#version 330 core
layout (triangles) in;
layout (line_strip, max_vertices = 6) out;

uniform float normal_length;
//==============================
//중략
//==============================
void main()
{
    normal = GetNormal();

    for(int i = 0; i < gl_in.length(); i++)
    {
        vec4 p = gl_in[i].gl_Position;
        vec3 N = GetNormal();

        gl_Position = p;
        EmitVertex();

        gl_Position = p + vec4(N * normal_length , 1.0f);
        EmitVertex();

        EndPrimitive();
    }
}
```