---
layout: post
title:  "WinAPI/DX11 : 03 . VertexBuffer를 가지고 삼각형 만들기"
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

### 0. 이번 장의 목적
<p>이번 장에서는 VertexBuffer를 만들어 쉐이더를 적용한 다음 랜더링 파이프라인을 이용해 랜더링을 해서 직접 윈도우 창에 띄워보도록 하겠다.</p>

<p>
VertexBuffer를 만들기 위해서는 몇 가지 과정을 거쳐야하는데, 그 과정은 다음과 같다.
<ol>
<li> VertexBuffer 생성 </li>
<li> VertexShader 컴파일 </li>
<li> VertexShader 생성 </li>
<li> PixelShader 컴파일 </li>
<li> PixelShader 생성</li>
<li> InputLayout 생성</li>
</ol>
</p>

<p>
그 이후에는 생성한 VertexBuffer와 VertexLayout을 가지고 랜더링 파이프라인을 통해서 랜더링할 것이다. 우선 이 장에서 새롭게 추가된 과정은 다음과 같다.
<ol>
<li> IA : VertexBuffer 세팅</li>
<li> IA : InputLayout 세팅 </li>
<li> IA : PrimitiveTopology 세팅</li>
<li> VS : VertexShader 세팅 </li>
<li> PS : PixelShader 세팅 </li>
<li> Draw </li>
</ol>
</p>

순서대로 이 과정을 차례대로 밟아보도록 하자.

<hr />

### 1. VertexBuffer 생성하기

#### ㄱ) Sample 클래스 전체 보기

<p>Sample 클래스는 이번 장에서 하나의 삼각형을 생성하는 객체로 동작할 것이다. </p>

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
	BOOL CreateInputLayout();
};
```

#### ㄴ) Sample 클래스 멤버들 살펴보기

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
```

<p>우선, SimpleVertex 같은 경우 위치를 담당할 x,y,z 값과 색을 담당할 r,g,b,a로 구성되어 있다. VertexBuffer의 점 하나를 SimpleVertex로 구성해 위치 뿐 아니라 색까지 입혀보자.</p>


```c++
// m_pVertexBuffer : 생성된 VertexBuffer를 담을 포인터 변수
ID3D11Buffer* m_pVertexBuffer;
// m_pVertexLayout : 생성된 인풋 레이아웃을 담을 포인터 변수 
ID3D11InputLayout* m_pVertexLayout;
// VS CODE : 버텍스 쉐이더 코드로, txt 파일로 만들어진 코드를 컴파일해 코드로 담고 있을 포인터 변수이다.
ID3DBlob* m_pVSCODE;
// Vertex Shader : 생성된 Vertex Shader를 담고 있을 포인터 변수이다.
ID3D11VertexShader* m_pVS;


// PS CODE : 픽셀 쉐이더 코드로, txt 파일로 만들어진 코드를 컴파일해 코드로 담고 있을 포인터 변수이다.
ID3DBlob* m_pPSCODE;
// Pixel Shader : 생성된 Pixel Shader를 담고 있을 포인터 변수이다.
ID3D11PixelShader* m_pPS;
```

#### ㄷ) Sample 클래스 멤버들 살펴보기

```c++
BOOL Sample::Init()
{
    
    if (FAILED(m_hr = CreateVertexBuffer())) return FALSE;

    if (FAILED(m_hr = CreateVertexShaderCode())) return FALSE;

    if (FAILED(m_hr = CreateVertexShader())) return FALSE;

    if (FAILED(m_hr = CreatePixelShaderCode())) return FALSE;

    if (FAILED(m_hr = CreatePixelShader())) return FALSE;

    if (FAILED(m_hr = CreateInputLayout())) return FALSE;

    return TRUE;
}
```

<p>우리는 Init() 함수가 호출되면 차례대로 Create를 붙이고 있는 함수들을 이용해 차례대로 멤버 포인터 변수들에 객체들을 생성해 넘겨줄 것이다.</p>

자 함수들을 차례대로 살펴보자

##### 1) CreateVertexBuffer() : 버퍼 만들기

```c++
BOOL Sample::CreateVertexBuffer()
{
    SimpleVertex verticles[] = {    
		{-0.5f, 0.5f,  0.0f, 1.0f, 0.0f, 0.0f, 1.0f},  
        {+0.5f, 0.5f,  0.0f, 0.0f, 1.0f, 0.0f, 1.0f},  
        {-0.5f, -0.5f, 0.0f, 0.0f, 0.0f, 1.0f, 1.0f},  
        {-0.5f, -0.5f, 0.0f, 0.0f, 0.0f, 1.0f, 1.0f},
        {+0.5f, 0.5f,  0.0f, 0.0f, 1.0f, 0.0f, 1.0f},
        {+0.5f, -0.5f, 0.0f, 1.0f, 0.0f, 0.0f, 1.0f}
    };


    // 첫 번째 인자 : _In_  const D3D11_BUFFER_DESC* pDesc
    // 두 번째 인자 : _In_opt_  const D3D11_SUBRESOURCE_DATA* pInitialData
    // 세 번째 인자 : _COM_Outptr_opt_  ID3D11Buffer** ppBuffer

	// 버퍼 데스크 만들기
    D3D11_BUFFER_DESC bufDesc;
	// 메모리 모두 지우기 : 데스크 비우기
    ZeroMemory(&bufDesc, sizeof(bufDesc));

    // 바이트 용량
    bufDesc.ByteWidth = sizeof(verticles);
    // 버퍼의 할당 장소 내지는 버퍼용도 : D3D11_USAGE_DEFAULT는 GPU 메모리에 할당하는 것
    bufDesc.Usage = D3D11_USAGE_DEFAULT;
    // 바인드 플래그 : ?
    bufDesc.BindFlags = D3D11_BIND_VERTEX_BUFFER;
    // 나머지 플래그들은 나중에
    bufDesc.CPUAccessFlags = 0;
    bufDesc.MiscFlags = 0;
    bufDesc.StructureByteStride = 0;

	// 버퍼에 담을 자료들을 담는 데스크
    D3D11_SUBRESOURCE_DATA subResourse;
	// 메모리 모두 지우기 : 데스크 비우기
    ZeroMemory(&subResourse, sizeof(subResourse));

    // 담을 자료들
    subResourse.pSysMem = verticles;
    // ??
    subResourse.SysMemPitch;
    subResourse.SysMemSlicePitch;
    HRESULT hr = m_pD3dDevice->CreateBuffer(&bufDesc, &subResourse, &m_pVertexBuffer);
    return hr;
}
```

<p> CreateVertexBuffer에서는 m_VertexBuffer에 미리 만들어둔 SimpleVertex를 가지고 버퍼 데스크, 서브 리소스 데스크를 작성해서 직접 Buffer를 만들었다.</p>

##### 2) CreateVertexShaderCode() : 정점 쉐이더 컴파일

```c++
BOOL Sample::CreateVertexShaderCode()
{
    // 정점 쉐이더 컴파일 하기

    // D3DCompileFromFile()
        // 첫 번째 인자 : _In_ LPCWSTR pFileName   => 컴파일 할 파일명
        // 두 번째 인자 : _In_reads_opt_ CONST D3D_SHADER_MACRO* pDefines => ???
        // 세 번째 인자 : _In_opt_ ID3DInclude* pInclude => ???
        // 네 번째 인자 : _In_ LPCSTR pEntrypoint   => 쉐이더 파일에서 들어갈 부분
        // 다섯 번째 인자 : _In_ LPCSTR pTarget  => 컴파일 버전
        // 여섯 번째 인자 : _In_ UINT Flags1  => ???
        // 일곱 번째 인자 : _In_ UINT Flags2  => ???
        // 여덟 번째 인자 : _Out_ ID3DBlob** ppCode => OUTPUT : 컴파일된 코드
        // 아홉 번째 인자 : _Always_(_Outptr_opt_result_maybenull_) ID3DBlob** ppErrorMsg => OUTPUT : 에러 메시지

    HRESULT hr;
	// 에러가 난다면 에러 메시지를 담을 코드
    ID3DBlob* pErrorCode;
	// ***디버깅을 위한 플래그*** : 이 것을 추가하면 그래픽 디버깅이 가능해진다!
    DWORD dwShaderFlags = D3DCOMPILE_DEBUG;

	// 쉐이더 파일을 가지고 컴파일해 코드 형태로 생성한다.
    hr = D3DCompileFromFile(L"VertexShader.txt", nullptr, nullptr, "main", "vs_5_0", dwShaderFlags, 0, &m_pVSCODE, &pErrorCode);

    if (FAILED(hr))
    {
		// 에러가 생긴다면 에러 메시지를 출력창에 출력한다.
        if (pErrorCode)
        {
            OutputDebugStringA((char*)pErrorCode->GetBufferPointer());
            pErrorCode->Release();
        }
    }

    return hr;
}
```

<p> CreateVertexShaderCode()에서는 미리 만들어둔 쉐이더 파일을 컴파일해서 파일 형태로 가지고 반환하는 작업을 거친다. 이 코드들을 가지고 인풋 레이아웃을 생성할 때 사용할 것이다.</p>


<p> 사용한 쉐이더 파일은 다음과 같다.</p>

```HLSL
struct VS_in
{
	float3 p : POSITION;
	float4 c : COLOR;
};

struct VS_out
{
	float4 p : SV_POSITION;
	float4 c : COLOR0;	// COLOR1
};

VS_out main(VS_in input)
{
	VS_out output = (VS_out)0;
	// Swizzling
	output.p = float4(input.p, 1.0f);
	output.c = input.c;
	return output;
}
```

##### 3) CreateVertexShader() : 정점 쉐이더 생성

```c++
BOOL Sample::CreateVertexShader()
{
    // CreateVertexShader() 
        // 첫 번째 인자 : _In_reads_  const void* pShaderBytecode => 쉐이더 코드
        // 두 번째 인자 : _In_  SIZE_T BytecodeLength => 코드 길이
        // 세 번째 인자 : _In_opt_  ID3D11ClassLinkage* pClassLinkage => ???
        // 네 번째 인자 : _COM_Outptr_opt_  ID3D11VertexShader** ppVertexShader  => 버텍스 쉐이더 포인터

	// 만들어둔 코드와 코드의 버퍼사이즈를 가지고 쉐이더를 만든다. 
    return m_pD3dDevice->CreateVertexShader(m_pVSCODE->GetBufferPointer(), m_pVSCODE->GetBufferSize(), NULL, &m_pVS);
}
```

<p> CreateVertexShader()에서는 미리 컴파일 해둔 코드를 가지고 쉐이더를 생성한다.</p>


##### 4) CreatePixelShaderCode() : 픽셀 쉐이더 컴파일

```c++
BOOL Sample::CreatePixelShaderCode()
{
    // 픽셀 쉐이더 컴파일 하기

    // D3DCompileFromFile()
        // 첫 번째 인자 : _In_ LPCWSTR pFileName => 컴파일 할 파일명
        // 두 번째 인자 : _In_reads_opt_ CONST D3D_SHADER_MACRO* pDefines => ???
        // 세 번째 인자 : _In_opt_ ID3DInclude* pInclude => ???
        // 네 번째 인자 : _In_ LPCSTR pEntrypoint => 쉐이더 파일에 들어갈 때 사용할 엔트리 포인트
        // 다섯 번째 인자 : _In_ LPCSTR pTarget, => 컴파일 버전
        // 여섯 번째 인자 : _In_ UINT Flags1 => ???
        // 일곱 번째 인자 : _In_ UINT Flags2 => ???
        // 여덟 번째 인자 : _Out_ ID3DBlob** ppCode => OUTPUT : 컴파일된 코드
        // 아홉 번째 인자 : _Always_(_Outptr_opt_result_maybenull_) ID3DBlob** ppErrorMsgs => OUTPUT : 에러 메시지

    HRESULT hr;
	// 에러가 난다면 에러 메시지를 담을 코드
    ID3DBlob* pErrorCode;
	// ***디버깅을 위한 플래그*** : 이 것을 추가하면 그래픽 디버깅이 가능해진다!
    DWORD dwShaderFlags = D3DCOMPILE_DEBUG;
	// 쉐이더 파일을 가지고 컴파일해 코드 형태로 생성한다.
    hr = D3DCompileFromFile(L"PixelShader.txt", nullptr, nullptr, "PS", "ps_5_0", dwShaderFlags, 0, &m_pPSCODE, &pErrorCode);

    if (FAILED(hr))
    {
		// 에러가 생긴다면 에러 메시지를 출력창에 출력한다.
        if (pErrorCode)
        {
            OutputDebugStringA((char*)pErrorCode->GetBufferPointer());
            pErrorCode->Release();
        }
    }

    return hr;
}
```

<p> CreateVertexCode()와 형태가 동일하다.</p>


<p> 사용한 쉐이더 파일은 간단하다.</p>

```HLSL
// 해당 픽쉘(SV_Target레지스터에)의 컬러를 반환한다.
float4 PS(float4 p : SV_POSITION) : SV_Target
{
	return float4(1,1,0,1);
}
```

##### 5) CreatePixelShader() : 픽셀 쉐이더 생성

```c++
BOOL Sample::CreatePixelShader()
{
    // CreatePixelShader()
        // 첫 번째 인자 : _In_reads_  const void* pShaderBytecode => 쉐이더 코드
        // 두 번째 인자 : _In_  SIZE_T BytecodeLength => 코드 길이
        // 세 번째 인자 : _In_opt_  ID3D11ClassLinkage* pClassLinkage => ???
        // 네 번째 인자 : _COM_Outptr_opt_  ID3D11PixelShader** ppPixelShader => 픽셀 쉐이더 포인터
    return m_pD3dDevice->CreatePixelShader(m_pPSCODE->GetBufferPointer(), m_pPSCODE->GetBufferSize(), NULL, &m_pPS);
}
```

<p> CreatePixelShader()도 CreateVertexShader()과 동일하다.</p>

##### 6) CreateInputLayout() : 인풋 레이아웃 생성

```c++
BOOL Sample::CreateInputLayout()
{

    // CreateInputLayout()
        // 첫 번째 인자 : _In_reads_(NumElements)  const D3D11_INPUT_ELEMENT_DESC* pInputElementDescs  => 넣는 엘리먼트 데스크
        // 두 번째 인자 : _In_range_(0, D3D11_IA_VERTEX_INPUT_STRUCTURE_ELEMENT_COUNT)  UINT NumElements   => 몇 개의 엘리먼트를 넣는지
        // 세 번째 인자 : _In_reads_(BytecodeLength)  const void* pShaderBytecodeWithInputSignature => 쉐이더 코드
        // 네 번째 인자 : _In_  SIZE_T BytecodeLength  => 코드 버퍼 사이즈
        // 다섯 번째 인자 : _COM_Outptr_opt_  ID3D11InputLayout** ppInputLayout => OUTPUT : 인풋 레이아웃 포인터

    D3D11_INPUT_ELEMENT_DESC ied[] =
    {
        { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0,0,D3D11_INPUT_PER_VERTEX_DATA, 0},
        { "COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0,12,D3D11_INPUT_PER_VERTEX_DATA, 0},
    };
    UINT NumElements = sizeof(ied) / sizeof(ied[0]);
    return m_pD3dDevice->CreateInputLayout(ied, NumElements, m_pVSCODE->GetBufferPointer(), m_pVSCODE->GetBufferSize(), &m_pVertexLayout);
}
```

<p> 마지막으로 CreateInputLayout()에서는 그동안 작성한 코드들에서 어느 EntryPoint에 들어갈 것인지를 확실하게 정하는 작업을 거친다. 이 레이아웃에 따라 코드에 진입하는 형태인 것 같다. </p>

<hr />

### 2. 랜더링 파이프 라인을 따라 삼각형 랜더링

<p> 이제 랜더링 파이프 라인을 따라 삼각형을 직접 띄워보도록 하자. </p>

```c++
bool Render()
{
	// PreRender
 	m_pImmediateContext->OMSetRenderTargets(1, &m_pRTV, NULL);
    float color[4] = { 1.0f, 1.0f, 1.0f, 1.0f };
    m_pImmediateContext->ClearRenderTargetView(m_pRTV, color);

	// Render

	// 1. IASetVertexBuffers() 
        // 첫 번째 인자 : _In_range_(0, D3D11_IA_VERTEX_INPUT_RESOURCE_SLOT_COUNT - 1)  UINT StartSlot   => 버퍼에서 어디에서 시작할건지???
        // 두 번째 인자 : _In_range_(0, D3D11_IA_VERTEX_INPUT_RESOURCE_SLOT_COUNT - StartSlot)  UINT NumBuffers => 버퍼 갯수???
        // 세 번째 인자 : _In_reads_opt_(NumBuffers)  ID3D11Buffer* const* ppVertexBuffers => 버퍼 포인터
        // 네 번째 인자 : _In_reads_opt_(NumBuffers)  const UINT* pStrides   => 정점 1개의 바이트 용량
        // 다섯 번째 인자 : _In_reads_opt_(NumBuffers)  const UINT* pOffset  => 정점버퍼에서 출발지점(바이트 단위) 
        
    UINT stride = sizeof(SimpleVertex);
    UINT offset = 0;
    m_pImmediateContext->IASetVertexBuffers(0, 1, &m_pVertexBuffer, &stride, &offset);

    // 2. IASetInputLayout()
        // 첫 번째 인자 : _In_opt_  ID3D11InputLayout *pInputLayout => 인풋 레이아웃 포인터 
    m_pImmediateContext->IASetInputLayout(m_pVertexLayout);

	// 3. IASetPrimitiveTopology() : 보여줄 형식 정하는 함수 ??
    m_pImmediateContext->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

    // 4. VSSetShader()
        // 첫 번째 인자 : _In_opt_  ID3D11VertexShader* pVertexShader                                        => 생성한 버텍스 쉐이더
        // 두 번째 인자 : _In_reads_opt_(NumClassInstances)  ID3D11ClassInstance* const* ppClassInstances    => ???
        // 세 번째 인자 : UINT NumClassInstances                                                             => ???
    m_pImmediateContext->VSSetShader(m_pVS, NULL, 0);

    // 5. VSSetShader()
        // 첫 번째 인자 : _In_opt_  ID3D11PixelShader* pVertexShader                                         => 생성한 픽셀 쉐이더
        // 두 번째 인자 : _In_reads_opt_(NumClassInstances)  ID3D11ClassInstance* const* ppClassInstances    => ???
        // 세 번째 인자 : UINT NumClassInstances                                                             => ???
    m_pImmediateContext->PSSetShader(m_pPS, NULL, 0);

    

    // 6. Draw()
        // 첫 번째 인자 : _In_  UINT VertexCount             : vertex 갯수
        // 두 번째 인자 : _In_  UINT StartVertexLocation)    : 시작 vertex 위치
    m_pImmediateContext->Draw(6, 0);

	// PostRender
	m_pSwapChain->Present(0, 0);
}
```

<p>PreRender 단계에서는 렌더타겟뷰를 설정해주고 배경색을 제외한 모든 것을 치웠다.</p>
<p>또한 PostRender에서는 스왑체인을 이용해 그려진 백 버퍼를 프론트 버퍼와 바꿔주는 역할을 했다. 이러한 단계는 지난 장에서 이미 작업해둔 내용이다. 그렇다면 이번에 작업한 중간 Render 단계를 살펴보자. </p>

<p>
<ol>
<li> IASetVertexBuffers 	: VertexBuffer 세팅</li>
<li> IASetInputLayout   	: InputLayout 세팅 </li>
<li> IASetPrimitiveTopology : PrimitiveTopology 세팅</li>
<li> VSSetShader 			: VertexShader 세팅 </li>
<li> PSSetShader 			: PixelShader 세팅 </li>
<li> Draw 					: 백 버퍼에 그리기</li>
</ol>
</p>

<p>이 것으로 간단한 삼각형을 띄웠다. 작업 결과는 다음과 같다.</p>

<hr />

### 3. 작업 결과 확인하기

 <img src="1.png">
