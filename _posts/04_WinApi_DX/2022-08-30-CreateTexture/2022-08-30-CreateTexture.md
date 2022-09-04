---
layout: post
title:  "WinAPI/DX11 04 : 텍스쳐 적용
summary: "Resource, 툴킷을 이용해 이미지 적용"
author: Eussy
date: '2022-08-30 18:30:00 +0530'
category: '04_WinAPI/DX11'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/04_WinAPI_DX/04_MakeTexture/
---

## 3장. TK(ToolKit)과 Resource를 이용한 텍스쳐 적용

### 0. 이번 장의 목적
<p>이번 장에서는 DirectX에서 따로 지원하는 ToolKit과 ID3D11Reource, ID3D11ShaderResourceView를 이용해 이미지를 저번 장에서 만든 도형에 적용해보도록 하겠다.</p>

<p>
Texture를 생성하기 위한 과정은 다음과 같다.
<ol>
<li> 헤더 파일과 라이브러리 프로젝트에 적용 </li>
<li> ID3D11Reource, ID3D11ShaderResourceView 생성 </li>
<li> Vertex 생성 시 텍스쳐 요소들 추가 </li>
<li> InputLayout에 텍스쳐 추가</li>
<li> Shader 파일에 텍스쳐 요소 추가</li>
<li> 랜더링 파이프라인 과정(VS)에서 쉐이더 리소스 적용 </li>
<li> PixelShader 생성</li>
<li> InputLayout 생성</li>
</ol>
</p>

순서대로 이 과정을 차례대로 밟아보도록 하자.

<hr />

### 1. 헤더 파일과 라이브러리 적용하기

<p> 이번에 Texture를 적용하기 위해서는 새로운 헤더 파일과 라이브러리를 사용할 것이다.</p>

<p> 구글에 DirectX ToolKit을 검색하면 깃이 하나 나온다. 다음 주소에서 파일을 받은 뒤 버전에 맞게 빌드해서 .lib 파일과 .h 파일들을 포함하도록 하자.</p>

<p> https://github.com/microsoft/DirectXTK</p>

<p>프로젝트 속성 -> 구성 속성 -> C/C++ -> 추가 포함 디렉터리</p>
<p>프로젝트 속성 -> 구성 속성 -> 링커 -> 추가 라이브러리 디렉터리</p>

```c++
#include "WICTextureLoader.h"
#include "DDSTextureLoader.h"
#pragma comment(lib, "DirectXTK.lib")
```

<p> 필요한 헤더 두 개의 헤더를 적용시키고 라이브러리도 불러오도록 한다.</p>


### 2. Texture를 만들기 위한 인터페이스 생성 후 요소 추가

#### ㄱ) SimpleVertex에 Texture 좌표 추가해주기
<p>우리는 버텍스를 만들 때 SimpleVertex를 이용해 Position과 Color를 주었다. 이 Texture 또한 x, y 두 가지 값이 필요하기 때문에 SimpleVertex에 두 가지 요소를 추가하도록 하겠다.</p>

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
	float tx;
	float ty;
};
```

<p> 또한, 텍스쳐 위치는 0~1까지의 값까지 밖에 갖지 못하기 때문에 삼각형의 점에 알맞게 배치한다.</p>
```c++
{-0.5f, 0.5f,  0.0f, 1.0f, 0.0f, 0.0f, 1.0f, 0.0f, 0.0f}, // v1
{+0.5f, 0.5f,  0.0f, 0.0f, 1.0f, 0.0f, 1.0f, 1.0f, 0.0f}, // v2
{-0.5f, -0.5f, 0.0f, 0.0f, 0.0f, 1.0f, 1.0f, 0.0f, 1.0f}, // v3
{-0.5f, -0.5f, 0.0f, 0.0f, 0.0f, 1.0f, 1.0f, 0.0f, 1.0f}, // v4
{+0.5f, 0.5f,  0.0f, 0.0f, 1.0f, 0.0f, 1.0f, 1.0f, 0.0f}, // v5
{+0.5f, -0.5f, 0.0f, 1.0f, 0.0f, 0.0f, 1.0f, 1.0f, 1.0f}  // v6
```

#### ㄴ) Sample 클래스에 텍스쳐 관련 변수들 만들어주기
<p>이제 앞에서 삼각형을 만들었던 Sample 클래스에 멤버 포인터 변수들을 추가한다.</p>

```c++
// Texture 리소스에 대한 포인터
ID3D11Resource* m_pTexture;
// 리소스 뷰
ID3D11ShaderResourceView* m_pShaderRV;
```

#### ㄷ) Sample 클래스 Init() 함수에 텍스쳐 생성 단계 만들어주기

<p> 이제 Sample 객체를 만들 때 Init()에서 새로운 단계를 추가해 이 포인터 변수들에 객체를 부여할 것이다.</p>

```c++
bool Sample::Init()
{

    if (FAILED(CreateVertexBuffer())) return false;

    if (FAILED(CreateVertexShaderCode())) return false;

    if (FAILED(CreateVertexShader())) return false;

    if (FAILED(CreatePixelShaderCode())) return false;

    if (FAILED(CreatePixelShader())) return false;

    /// 새로 추가된 단계
    if (FAILED(CreateTexture())) return false;

    if (FAILED(CreateInputLayout())) return false;

    return true;
}
```
<p>사실 어느 단계에 들어가도 상관 없지만, 마지막 단계인 InputLayout을 생성하기 전에 추가해주었다.</p>

```c++
HRESULT HBaseObject::CreateTexture()
{
    // DirectX::CreateWICTextureFromFile() : Texture를 파일에서 불러와 만드는 함수
        // 첫 번째 인자 : _In_ ID3D11Device* d3dDevice : 디바이스 객체
        // 두 번째 인자 : _In_z_ const wchar_t* szFileName : 파일의 주소
        // 세 번째 인자 : _Outptr_opt_ ID3D11Resource** texture,
        // 네 번째 인자 : _Outptr_opt_ ID3D11ShaderResourceView** textureView,
        // 다섯 번째 인자 : _In_ size_t maxsize = 0
        
    HRESULT hr;
    if (FAILED(hr = DirectX::CreateWICTextureFromFile(m_pD3dDevice, L"../../data/kgcabk.bmp", &m_pTexture, &m_pShaderRV)))
        return false;
    else
        return true;
}
```

#### ㄹ) CreateInputLayout()의 D3D11_INPUT_ELEMENT_DESC에 TEXTURE 요소 추가하기

<p> 함수는 인자로 다섯 가지를 요구하며, 디바이스 객체와 불러올 리소스의 주소, 그리고 생성된 객체를 받을 두 포인터를 요구한다. 마지막 maxsize는 디폴트가 0으로 설정되어 있어 따로 추가하지 않았다.</p>

```c++
D3D11_INPUT_ELEMENT_DESC ied[] =
    {
        { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0,0,D3D11_INPUT_PER_VERTEX_DATA, 0},
        { "COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0,12,D3D11_INPUT_PER_VERTEX_DATA, 0},
        { "TEXTURE", 0, DXGI_FORMAT_R32G32_FLOAT, 0, 28, D3D11_INPUT_PER_VERTEX_DATA, 0}
    };
```
<p>마지막으로 InputLayout 생성 시 필요한 D3D11_INPUT_ELEMENT_DESC에 텍스쳐 요소들을 추가함으로써 생성은 끝이다.</p>


### 3. 쉐이더 파일 수정과 랜더링 파이프라인 추가

#### ㄱ) 쉐이더 파일 수정

<p>이제 쉐이더 파일에 Texture가 들어올 수 있는 부분을 만들어주자 </p>

```HLSL
struct VS_in
{
	float3 p : POSITION;
	float4 c : COLOR;
	float2 t : TEXTURE;
};

struct VS_out
{
	float4 p : SV_POSITION;
	float4 c : COLOR0;	// COLOR1
	float2 t : TEXCOORD0;
};

VS_out main(VS_in input)
{
	VS_out output = (VS_out)0;
	// Swizzling
	output.p = float4(input.p, 1.0f);
	output.c = input.c;
	output.t = input.t;
	return output;
}


Texture2D g_txTexture : register(t0);
SamplerState g_SampleA : register(s0);

struct PS_in
{
	float4 p : SV_POSITION;
	float4 c : COLOR0;
	float2 t : TEXCOORD0;
};

float4 PS(PS_in input) : SV_Target
{
	return g_txTexture.Sample(g_SampleA, input.t);
}
```

<p> 전 장과 다르게 조금 수정한 버전이다. Texture는 VS_in의 TEXTURE로 들어와 PS에서는 TEXCOORD0 으로 들어와 Texture2D와 SamplerState를 통해 PS에서 값이 빠져나간다. Texture2D와 SamplerState는 다음에 더 자세히 알아보도록 하자</p>

#### ㄴ) 랜더링 파이프라인에서 텍스쳐 추가

```c++
// 4. VSSetShaderResource()
    // 첫 번째 인자 :  _In_range_(0, D3D11_COMMONSHADER_INPUT_RESOURCE_SLOT_COUNT - 1)  UINT StartSlot -> 시작 주소
    // 두 번째 인자 : _In_range_(0, D3D11_COMMONSHADER_INPUT_RESOURCE_SLOT_COUNT - StartSlot)  UINT NumViews  -> 리소스 갯수
    // 세 번째 인자 : _In_reads_opt_(NumViews)  ID3D11ShaderResourceView* const* ppShaderResourceViews  

    m_pImmediateContext->PSSetShaderResources(0, 1, &m_pShaderRV);
```

<p>Context를 이용해 PS단계에서 PSSetShaderResources() 함수를 통해 쉐이더에 리소스를 전달하도록 하는 작업이다. 이로써 텍스쳐 작업은 끝난다.</p>

### 3. 작업 결과 확인하기

 <img src="1.png">
