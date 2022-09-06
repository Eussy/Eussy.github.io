---
layout: post
title:  "WinAPI/DX11 : 02 . Device 만들어 랜더링 하기"
summary: "Device를 만들고 배경색을 랜더링을 통해 바꿔보자"
author: Eussy
date: '2022-08-26 18:30:00 +0530'
category: '04_WinAPI/DX11'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/04_WinAPI_DX/02_MakeDevice/
---

## 2장. Device를 만들고 랜더링을 통해 배경색을 바꿔보자

이번 장에서는 DirectX11을 통해 랜더링 파이프라인을 구축하기 위한 기초 세팅을 해보도록 하자.

<hr />

### 1. HDevice 클래스 생성

#### ㄱ) HDevice 클래스 전체 보기
```c++
class HDevice : public HWindow
{
public:
	// 인터페이스를 통해 하드웨어를 직접 제어하도록 하기 위해 객체들을 획득한다.
	// 1. 디바이스 포인터
	ID3D11Device* m_pD3dDevice = nullptr;
	// 2. 디바이스 컨텍스트
	ID3D11DeviceContext* m_pImmediateContext = nullptr;
	// 3. 팩토리 포인터
	IDXGIFactory* m_pGIFactory = nullptr;
	// 4. 스왑 체인
	IDXGISwapChain* m_pSwapChain = nullptr;
	// 5. 렌더 타겟뷰
	ID3D11RenderTargetView* m_pRTV = nullptr;
public:
	virtual BOOL Init() override;
	virtual BOOL Frame() override;
	virtual BOOL Render() override;
	virtual BOOL Release() override;
	
	BOOL CreateDevice();
	BOOL CreateFactory();
	BOOL CreateSwapChain();
	BOOL CreateRenderTargetView();
	void CreateViewPort();
};
```

<p> HDevice 클래스는 HWindow를 상속한 클래스로써 Init(), Frame(), Render(), Release() 함수들을 오버라이딩해서 직접 사용하도록 할 것이다. </p>

<p> 그렇다면 이제 멤버 변수와 함수들을 살펴보도록 하자 </p>

```c++
// 인터페이스를 통해 하드웨어를 직접 제어하도록 하기 위해 포인터에 객체들을 획득한다.

// 1. 디바이스 포인터 : 리소스의 생성과 관련된 작업 담당
ID3D11Device* m_pD3dDevice = nullptr;
// 2. 디바이스 컨텍스트 : 생성된 리소스들을 사용하여 랜더링 처리 및 제어
ID3D11DeviceContext* m_pImmediateContext = nullptr;
// 3. DXGI 팩토리 포인터 : DXGI 객체들을 생성하는 작업 담당
IDXGIFactory* m_pGIFactory = nullptr;
// 4. 스왑 체인 : 전면 버퍼 및 후면 버퍼들 생성 및 제어
IDXGISwapChain* m_pSwapChain = nullptr;
// 5. 렌더 타겟뷰 : 생성된 후면 버퍼들 중 랜더링 될 대상버퍼를 지정
ID3D11RenderTargetView* m_pRTV = nullptr;

// 1. 디바이스를 생성하는 함수
BOOL CreateDevice();
// 2. 팩토리 포인터를 생성하는 함수
BOOL CreateFactory();
// 3. 스왑 체인을 생성하는 함수
BOOL CreateSwapChain();
// 4. 렌더 타겟뷰를 생성하는 함수
BOOL CreateRenderTargetView();
// 5. 뷰포트를 생성하는 함수
void CreateViewPort();
```
<hr />

#### ㄴ) HDevice 함수 살펴보기

이제 여러 Create 함수들이 있지만 이 함수들안의 내용은 생소하기 때문에 자세히 살펴볼 필요가 있다. 하나씩 알아보도록 하자

##### 1) CreateDevice()
```c++
BOOL HDevice::CreateDevice()
{
    // 1) 디바이스 생성 : D3D11CreateDevice()
        // 1. 첫 번째 인자 : _IN_OPT_ IDXGIAdapter* pAdapter : 비디오 어댑터 포인터
        // 2. 두 번째 인자 : _IN_OPT_ D3D_DRIVER_TYPE DriverType : 드라이버 타입으로, D3D_DRIVER_TYPE_HARDWARE를 사용해야 GPU를 사용한다고 한다(?)
        // 3. 세 번째 인자 : _IN_OPT_ HMODULE SoftWare : DriverType이 SOFTWARE일 경우 NULL일 수 없다
        // 4. 네 번째 인자 : _IN_OPT_ UINT flags : ???
        // 5. 다섯 번째 인자 : _IN_OPT_ const D3D_FEATURE_LEVEL* pFeatureLevels : DirectX의 버전인 것 같다. 이에 따라 사용할 수 있는 쉐이더가 다르다.
        // 6. 여섯 번째 인자 : _IN_OPT_ UINT FeatureLevels : pFeatureLevels의 길이
        // 7. 일곱 번째 인자 : _IN_OPT_ UINT SDKVersion : SDK 버전
        // 8. 여덟 번째 인자 : _OUT_OPT_ ID3D11Device** ppDevice : 생성된 디바이스를 담을 주소
        // 9. 아홉 번째 인자 : _OUT_OPT_ D3D_FEATURE_LEVEL* pFeatureLevel : 성공 시 pFeatureLevels의 가장 첫 번째 요소를 반환한다.
        // 10. 열 번째 인자  : _OUT_OPT_ ID3D11DeviceContext** pImmediateContext : 생성된 Context를 담을 주소 

    // 임시 레벨들 생성 후 생성
    D3D_FEATURE_LEVEL pFeatureLevel;
    D3D_FEATURE_LEVEL pFeatureLevels[] = { D3D_FEATURE_LEVEL_11_0, };
    return D3D11CreateDevice(nullptr, D3D_DRIVER_TYPE_HARDWARE, NULL, 0, pFeatureLevels, 1, D3D11_SDK_VERSION, &m_pD3dDevice, &pFeatureLevel, &m_pImmediateContext);
}
```

CreateDevice() 에서는  Device와 Context를 생성했다. 인자들의 내용은 차분히 배워가도록 하자.

##### 2) CreateFactory()
```c++
BOOL HDevice::CreateFactory()
{
    // 2) 팩토리 생성 : CreateDXGIFactory()
        // 1. 첫 번째 인자 : const IID &riid
        // 2. 두 번째 인자 : void** ppFactory : 생성한 팩토리를 담을 포인터
        // * __uuid() 예약어는 표현식에 지정된 클래스명이나 인터페이스명에 대한 GUID 값을 리턴해준다.
    return CreateDXGIFactory(__uuidof(IDXGIFactory), (void**)&m_pGIFactory);
}
```

CreateFactory() 에서는 DXGI 팩토리를 생성한다. __uuid를 왜 사용하는지는 아직 모르겠다.

##### 3) CreateSwapChain()
```c++
BOOL HDevice::CreateSwapChain()
{
    // 3) 스왑체인 생성 : m_pGIFactory->CreateSwapChain()
        // 후면(백) 버퍼를 생성한다. 그리고 전면 버퍼와 교체한다.

    // 스왑체인 데스크를 만들고 그 안 값들을 설정해준다.
    DXGI_SWAP_CHAIN_DESC sd;
    ZeroMemory(&sd, sizeof(sd));

    // 만들 백 버퍼의 갯수
    sd.BufferCount = 1;
    // 백 버퍼의 너비
    sd.BufferDesc.Width = m_ClientRect.right;
    // 백 버퍼의 높이
    sd.BufferDesc.Height = m_ClientRect.bottom;
    // 백 버퍼의 픽셀 포맷
    sd.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM;
    // 디스플레이 갱신율?
    sd.BufferDesc.RefreshRate.Numerator = 60;
    sd.BufferDesc.RefreshRate.Denominator = 1;
    // 샘플 데스크
    sd.SampleDesc.Count = 1;
    sd.SampleDesc.Quality = 0;

    sd.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
    // 윈도우 핸들
    sd.OutputWindow = m_hWnd;
    sd.Windowed = true;

    sd.Flags = DXGI_SWAP_CHAIN_FLAG_ALLOW_MODE_SWITCH;

    return m_pGIFactory->CreateSwapChain(m_pD3dDevice, &sd, &m_pSwapChain);
}
```

CreateSwapChain() 에서는 버퍼 데스크를 만들고 이를 통해 SwapChain을 만든다.

##### 4) CreateRenderTargetView()
```c++
BOOL HDevice::CreateRenderTargetView()
{
    HRESULT hr;
    // 4) 랜더타겟뷰 생성 : m_pD3dDevice->CreateRenderTargetView()

    ID3D11Texture2D* pBackBuffer = nullptr;
    m_pSwapChain->GetBuffer(0, __uuidof(ID3D11Texture2D), (void**)&pBackBuffer);

    // 1. 첫 번째 인자 : ID3D11Resource* pResource
    // 2. 두 번째 인자 : const D3D11_RENDER_TARGET_VIEW_DESC* pDesc
    // 3. 세 번째 인자 : ID3D11RenderTargetView** ppRTView

    hr = m_pD3dDevice->CreateRenderTargetView(pBackBuffer, NULL, &m_pRTV);
    pBackBuffer->Release();

    return hr;
}
```

CreateRenderTargetView() 에서는 스왑체인에서 백 버퍼를 받아온 다음 그 버퍼를 다음 타겟뷰로 정해놓는다.

##### 5) CreateViewPort()
```c++
void HDevice::CreateViewPort()
{
    // 5) 뷰포트 설정
    D3D11_VIEWPORT vp;
    // 뷰포트의 너비
    vp.Width = m_ClientRect.right;
    // 뷰포트의 높이
    vp.Height = m_ClientRect.bottom;
    // 뷰포트 설정
    vp.TopLeftX = 0;
    vp.TopLeftY = 0;
    vp.MinDepth = 0.0f;
    vp.MaxDepth = 0.0f;
    // 1. 첫 번째 인자 : UINT NumViewports
    // 2. 두 번째 인자 : const D3D11_VIEWPORT* pViewDesc
    // 만든 뷰포트를 세팅한다.
    m_pImmediateContext->RSSetViewports(1, &vp);
}
```

CreateViewPort()에서는 말 그대로 뷰포트를 생성해 RS 단계에서 뷰포트를 설정해둔다.

<hr />

### 2. Render()에서 랜더링 하기

기본 세팅들은 끝났다. 이제 백버퍼를 이용해 랜더링을 한 번 진행해보도록 하자.

```c++
BOOL HDevice::Render()
{
    static float fTimer = 0.0f;
    fTimer += 0.0001f;
    // OM 단계에서 랜더링 타겟 뷰를 설정한다.
    m_pImmediateContext->OMSetRenderTargets(1, &m_pRTV, nullptr);


    float color[4] = { cosf(fTimer) * 0.5f + 0.5f, sinf(fTimer) * 0.5f + 0.5f, cosf(fTimer * 2) * 0.5f + 0.5f, 1.0f };

    // 화면을 모두 지우고 color로 색을 지정한다.
    m_pImmediateContext->ClearRenderTargetView(m_pRTV, color);
    // 백 버퍼와 프론트 버퍼를 바꾼다.
    m_pSwapChain->Present(0, 0);

    return TRUE;
}
```
<p>Render() 함수에서 백 버퍼의 색깔을 바꾼 뒤 계속해서 프론트 버퍼와 바꿔주면서 랜더링을 해보았다.</p>

<hr />

### 3. 작업 결과 확인하기
<p>
 <img src="gif1.gif" class="img-fluid">
 </p>
