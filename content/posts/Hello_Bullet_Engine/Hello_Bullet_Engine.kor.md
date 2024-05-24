+++
authors = ["lgh"]
title = "Hello_Bullet_Engine"
date = "2020-02-26"
description = ""
tags = [
    "C++"
]
categories = [
    "C++",
]
series = ["C++"]
+++

# Hello_Bullet_Engine

## 사용 언어
* C++

## 사용 라이브러리
* Bullet Physics SDK: [https://github.com/bulletphysics/bullet3](https://github.com/bulletphysics/bullet3)
* GLEW: [http://glew.sourceforge.net/](http://glew.sourceforge.net/)
* FreeGLUT: [http://freeglut.sourceforge.net/](http://freeglut.sourceforge.net/)

## 참고 사이트
* 참고한 사이트: [https://github.com/THISISAGOODNAME/bulletOpenGL](https://github.com/THISISAGOODNAME/bulletOpenGL)
* FOV 이론: [https://blog.codinghorror.com/widescreen-and-fov/](https://blog.codinghorror.com/widescreen-and-fov/)
* Bullet Physics 공식 문서: [https://github.com/bulletphysics/bullet3/tree/master/docs](https://github.com/bulletphysics/bullet3/tree/master/docs)

## 계발 환경
* Visual Studio 2019

## 제작 인원
* 개인 

## 제작 기간
* 2020.02.26

## 오리지널 코드 비율
* 참고 사이트:오리지널 = 7:3

## 제작 의도
게임 엔진에 의존하지 않고 물리 연산을 구현하는 것에 흥미가 있어 **연구 목적**으로 참고 사이트를 보면서 프로그램을 제작

## 동영상(YouTube)
{{< youtube LPJUOyeQz5w >}}

## GitHub URL
[https://github.com/congibab/Hello_Bullet_Engine](https://github.com/congibab/Hello_Bullet_Engine)

# 프로그램 실행 화면
![Desktop View](/images/Hello_Bullet_Engine/GameScene1.gif)

## 조작법 (CapsLock이 꺼져 있는 상태 기준)
* W, S, A, D: 플레이어 이동
* R: 씬 리셋
* Z: 충돌 표시(충돌 판정 표시)
* ←, →, ↑, ↓: 카메라 회전
* 스페이스: 점프
* 객체 방향으로 마우스 왼쪽 클릭: **플레이어 변경**

# 클래스 설명
* main.cpp
* Callback.h: OpenGL 관련 콜백 클래스
* App.cpp: 렌더링, 조작 관련 클래스
* Basic.cpp(App 상속): 씬 초기화, 객체 배치, 플레이어 조작 관련 클래스
* GameObject.cpp: 객체 클래스
* DebugDrawer.cpp(btIDebugDraw 상속): 충돌 판정 표시
* OpenGLMotionState.cpp(btDefaultMotionState 상속): 객체의 Transform을 행렬 변환

<div style="page-break-before:always"></div>

# 씬 초기화
```cpp
// BasicDemo.cpp
void BasicDemo::InitPhysics()
{
	m_pCollisionConfiguration = new btDefaultCollisionConfiguration();
	m_pDispatcher = new btCollisionDispatcher(m_pCollisionConfiguration);
	m_pBroadphase = new btDbvtBroadphase();
	m_pSolver = new btSequentialImpulseConstraintSolver();
	m_pWorld = new btDiscreteDynamicsWorld(m_pDispatcher, m_pBroadphase, m_pSolver, m_pCollisionConfiguration);
	m_pWorld->setGravity(btVector3(0, -10, 0));

	CreateObjects();
}
```

# GameObject
```cpp
// GameObject.cpp
GameObject::GameObject(btCollisionShape* pShape, float mass, const btVector3& color, const btVector3& initialPosition, const btQuaternion& initialRotation)
{
	m_pShape = pShape;
	m_color = color;

	Tag = "default";

	btTransform transform;
	transform.setIdentity();
	transform.setOrigin(initialPosition);
	transform.setRotation(initialRotation);

	m_pMotionState = new OpenGLMotionState(transform);
	btVector3 locallnertia(0, 0, 0);

	if (mass != 0.0f)
		pShape->calculateLocalInertia(mass, locallnertia);

	btRigidBody::btRigidBodyConstructionInfo clnfo(mass, m_pMotionState, pShape, locallnertia);
	m_pBody = new btRigidBody(clnfo);
}
```

<div style="page-break-before:always"></div>

# RayCast, 좌표 변환
![Desktop View](/images/Hello_Bullet_Engine/GameScene2.GIF)

## 설명
* 윈도우 좌표를 월드 좌표로 변환 (FOV 계산)
* Bullet Physics의 rayTest 함수 사용
* 객체 위에서 마우스 왼쪽 클릭 시 플레이어 변경 가능

# Point to Point Constraint (프로그램 실행 화면 참고)
![Desktop View](/images/Hello_Bullet_Engine/chain.JPG)

## 설명
* btPoint2PointConstraint 클래스를 사용하여 체인 구현 (빨간 박스)
* 체인은 관성의 영향을 받음

<div style="page-break-before:always"></div>

## 소스 코드
```cpp
// BasicDemo.cpp
{
	GameObjects m_obj;
	for (int i = 0; i < 10; ++i)
	{
		float mass = 1;
		GameObject* p_obj;
		
		if (i == 0) mass = 0;		
		p_obj = CreateGameObject(new btBoxShape(btVector3(0.9, 0.25, 0.25)), mass, btVector3(0, i % 3, 0), btVector3(10, 20 , -10 - i * 2));
		m_obj.push_back(p_obj);
	}
	for (int i = 0; i < 9; ++i)
	{
		btPoint2PointConstraint* test1 = new btPoint2PointConstraint(*(m_obj[i]->GetRigidBody()), *(m_obj[i + 1]->GetRigidBody()), btVector3(-1.0, 0.5, 0), btVector3(1.0, 0.5, 0));
		m_pWorld->addConstraint(test1);

		btPoint2PointConstraint* test2 = new btPoint2PointConstraint(*(m_obj[i]->GetRigidBody()), *(m_obj[i + 1]->GetRigidBody()), btVector3(-1.0, -0.5, 0), btVector3(1.0, -0.5, 0));
		m_pWorld->addConstraint(test2);
	}
	Player = m_obj[0];
}
```