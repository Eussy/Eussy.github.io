---
layout: post
title:  "WinAPI/DX11 03 : VertexBuffer를 가지고 삼각형 만들기"
summary: "VertexBuffer를 만들어 삼각형을 띄워보자"
author: Eussy
date: '2022-08-29 18:30:00 +0530'
category: '04_WinAPI/DX11'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/04_WinAPI_DX/03_MakeTriangle/
---

## 3장. VertexBuffer를 만들어 삼각형 만들기

### 이번 장의 목적
<p>이번 장에서는 VertexBuffer를 만들어 쉐이더를 적용한 다음 랜더링 파이프라인을 이용해 랜더링을 해서 직접 윈도우 창에 띄워보도록 하겠다.</p>

<p>
VertexBuffer를 만들기 위해서는 몇 가지 과정을 거쳐야하는데, 그 과정은 다음과 같다.
<ol>
<li> VertexBuffer 생성 </li>
<li> VertexShader 컴파일 </li>
<li> VertexShader 생성 </li>
<li> PixelShader 컴파일 </li>
<li> PixelShader 생성</li>
<li> VertexLayout 생성</li>
</ol>

<p>
그 이후에는 생성한 VertexBuffer와 VertexLayout을 가지고 랜더링 파이프라인을 통해서 랜더링할 것이다. 우선 이 장에서 새롭게 추가된 과정은 다음과 같다.
<li> IA : VertexBuffer 세팅</li>
<li> IA : InputLayout 세팅 </li>
<li> IA : PrimitiveTopology 세팅</li>
<li> VS : VertexShader 세팅 </li>
<li> PS : PixelShader 세팅 </li>
<li> Draw </li>
</p>

순서대로 이 과정을 차례대로 밟아보도록 하자.

<hr />

### 1. 기존 HWindow, HDevice 클래스 수정

<hr />

### 2. Sample 클래스에 멤버 변수, 함수 추가
<p></p>

#### ㄱ) Sample 클래스 전체 보기
```c++
struct SimpleVertex
{
public:
	float x;
	float y;
	float z;
	float r;
	float g;
	float b;
	float a;
};

class Sample : public HDevice
{
public:
	// VertexBuffer
	ID3D11Buffer* m_pVertexBuffer;
	// InputVertexLayout
	ID3D11InputLayout* m_pVertexLayout;
	// VS CODE : 버텍스 쉐이더 코드
	ID3DBlob* m_pVSCODE;
	// Vertex Shader
	ID3D11VertexShader* m_pVS;


	// PS CODE : 픽셀 쉐이더 코드
	ID3DBlob* m_pPSCODE;
	// Pixel Shader
	ID3D11PixelShader* m_pPS;

public:
	virtual BOOL Init();
	virtual BOOL Frame();
	virtual BOOL Render();
	virtual BOOL Release();

	BOOL CreateVertexBuffer();
	BOOL CreateVertexShaderCode();
	BOOL CreateVertexShader();
	BOOL CreatePixelShaderCode();
	BOOL CreatePixelShader();
	BOOL CreateVertexLayout();
};
```

#### ㄴ) Sample 멤버 함수들 살펴보기


<hr />

### 3. VertexShader, PixelShader

<hr />

### 4. HRender() 살펴보기

<hr />

### 5. 작업 결과 확인하기

 <img src="gif1.gif">
