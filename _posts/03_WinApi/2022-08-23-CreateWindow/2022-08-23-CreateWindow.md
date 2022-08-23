---
layout: post
title:  "WinAPI 01 : 윈도우 생성"
summary: "윈도우 생성"
author: Eussy
date: '2022-08-23 18:30:00 +0530'
category: '04_WinAPI'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/04_WinAPI/01_MakeWindow/
---

## 1장. 윈도우 생성하기

이번 장에서는 윈도우에 관한 클래스를 정의하고 사용하는 방법을 구현토록 할 것이다.

### 1. 솔루션 및 프로젝트 생성

***사진 첨부 필요***

지금까지는 콘솔 앱 / 빈 프로젝트를 통해 생성했다면 이제는 Windows 데스크톱 마법사 / Windows 데스크톱 애플리케이션을 통해 생성한다.

### 2. 앞으로 사용할 추상 클래스 생성

윈도우 클래스를 생성하기 이전에 앞으로 모든 클래스에는 Init(), Frame(), Render(), Release() 함수를 포함해야한다.

```c++
class Dynamic
{
	// 동적 인터페이스
public:
	virtual BOOL Init() = 0;
	virtual BOOL Frame() = 0;
	virtual BOOL Render() = 0;
	virtual BOOL Release() = 0;
};
```

이에 따라 인터페이스 역할을 할 Dynamic(이름은 바뀔 수 있음) 클래스를 먼저 생성했다.
앞으로 만들 윈도우 클래스 또한 이 Dynamic 클래스를 상속 받을 것이다.

### 3. 새로운 main 함수 살펴보기

다음은 main 함수를 먼저 살펴보며 윈도우를 만드는 과정을 살펴보도록 하자.

```c++
int APIENTRY wWinMain(_In_ HINSTANCE hInstance,
    _In_opt_ HINSTANCE hPrevInstance,
    _In_ LPWSTR    lpCmdLine,
    _In_ int       nCmdShow)
{
    HWindow demo;
    demo.SetWindow(hInstance, L"KGCA 윈도우", L"KGCA 창", 1024, 780);
    demo.Run();

    return true;
}
```

특이하게 main() 함수가 아닌 wWinMain() 함수를 통해 프로그램에 접근하고 있다. ms Document에서 매개 변수들을 살펴보도록 하자.

hInstance : ms에서 "인스턴스에 대한 핸들" or "모듈에 대한 핸들"로 정의하고 있다. 운영 체제는 이 값을 사용하여 메모리에 로드될 때 EXE(실행 파일)를 식별한다.
hPreInstance : 16비트 Windows 에서 사용되었지만 지금은 아무 의미 없다. 항상 0이다.
pCmdLine : 명령줄 인수를 유니코드 문자열로 포함한다.
nCmdShow : 기본 어플리케이션 창을 최소화할지, 최대화할지 또는 정상적으로 표시할지 나타내는 플래그이다.

아직은 잘 모르겠지만 진행하면서 차차 알아보도록 하자.

우선 이 wWinMain에서는 HWindow 객체를 만들고 그 객체에 윈도우 창을 셋팅해주고 그 객체를 실행한다.

이제 HWindow 클래스의 헤더로 들어가 내부를 살펴보도록 하자.

### 4. HWindow 클래스 헤더 살펴보기

```c++
class HWindow : Dynamic
{
public:
	// 1. 멤버 변수들
	// 인스턴스 핸들
	HINSTANCE		m_hInstance;
	// 윈도우 핸들
	HWND			m_hWnd;
	// 등록할 클래스 이름
	LPCWSTR			m_className;
	// 윈도우 창 이름
	LPCWSTR			m_ucName;
	// 윈도우 스타일
	ULONG			m_style;
	// 윈도우 사이즈
	RECT			m_WindowRect;
	RECT			m_ClientRect;

public:
    // 2. 인터페이스에서 상속 받은 함수들 : 지금은 아무런 기능 X
	virtual BOOL	Init() override;
	virtual BOOL	Frame() override;
	virtual BOOL	Render() override;
	virtual BOOL	Release() override;
public:
	// 3. 윈도우 동작 함수들
	BOOL			SetWindow(HINSTANCE instance, LPCWSTR title, LPCWSTR ucName, LONG iWidth, LONG iHeight);
	ATOM			MyRegisterClass();
	BOOL			InitInstance();
	LRESULT			MsgProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);
	BOOL			Run();
public:
	// 4. 유틸리티
	void			MoveWindowCenter();

	void			SetWindowStyle(int style);

	LONG			GetWindowWidth();
	LONG			GetWindowHeight();
	LONG			GetClientWidth();
	LONG			GetClientHeight();
};
```
내부가 아는 자료형이 많이 없고 다 새로 전처리된 자료형들로 존재한다. 당황하지 않고 살펴보도록 하자.

1. 멤버 변수
**HINSTANCE m_hInstance** : 위에서 보았던 인스턴트 핸들을 멤버로 가지고 있는 변수이다.
**HWND m_hWnd** : 윈도우 핸들이라고 부르고 있다. 프로시저 함수와 같이 많은 함수에서 쓰인다.
**LPCWSTR m_className** : 윈도우를 등록할때 사용할 윈도우 클래스(?) 이름이다. 여러 윈도우 클래스를 등록할 수 있지만 호출 시에 이 이름을 가지고 호출한다.
**LPCWSTR m_ucName** : 윈도우 창을 띄우게 되면 위 옵션바에 뜨게 되는 이름이다.
**ULONG m_style** : 윈도우 창의 스타일이다. 테두리의 유무, 최소화 버튼, 최대화 버튼의 유무 등을 정할 수 있다.

**RECT m_WindowRect** : 전체 윈도우 창의 점들을 담은 RECT 구조체이다.
**RECT m_ClientRect** : 클라이언트 창(위 옵션바를 제외한 창)의 점들을 담은 RECT 구조체이다.

2. 인터페이스에서 상속 받은 함수들 : Dynamic을 상속받아 구현해놓은 함수들로 지금은 아무런 기능이 없다.

3. 윈도우 동작 함수들
**BOOL			SetWindow(HINSTANCE instance, LPCWSTR title, LPCWSTR ucName, LONG iWidth, LONG iHeight))** : 등록시 가장 먼저 호출되는 함수로 기본 정보들을 매개변수로 가져온다.
**ATOM			MyRegisterClass()** : SetWindow()에서 실행되는 함수로 여러가지 정보들을 가지고 윈도우 클래스를 등록시킨다.
**BOOL			InitInstance()** : 등록된 클래스를 창으로 띄우는 함수이다.
**LRESULT		MsgProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)** : 윈도우 창 생성부터 종료까지 생성되는 많은 메시지들을 담당하는 프로시저를 래핑한 함수이다.
**BOOL			Run()** : 창을 띄우고 나서 메시지들을 가지고 실시간으로 동작하는 함수이다.

윈도우 등록 시에 함수 호출 순서는 : SetWindow() -> MyRegisterClass() -> InitInstance() 순이다.

4. 유틸리티
**void MoveWindowCenter()** : 창을 중앙으로 이동시켜주는 함수
**void SetWindowStyle(int style)** : 공용 윈도우 스타일을 정해주는 함수
**LONG GetWindowWidth()** : 윈도우 창의 너비 함수
**LONG GetWindowHeight()** : 윈도우 창의 높이 함수
**LONG GetClientWidth()** : 클라이언트 창의 너비 함수
**LONG GetClientHeight()** : 클라이언트 창의 높이 함수

### 5. HWindow.cpp 뜯어보기

자 헤더에서 구조파악이 끝났다면 윈도우 동작함수들만 자세하게 살펴보도록 하자

```c++
BOOL HWindow::SetWindow(HINSTANCE instance, LPCWSTR title, LPCWSTR ucName, LONG iWidth, LONG iHeight)
{
	// 프로시저 래핑을 위한 전역 포인터
    g_hWindow = this;

	// 인스턴스 핸들 설정
    m_hInstance = instance;
	// 윈도우 클래스 이름 설정
    m_className = title;
	// 윈도우 창 이름 설정
    m_ucName = ucName;
	// 공용 윈도우 창 스타일 설정
    SetWindowStyle(WS_OVERLAPPEDWINDOW | WS_VSCROLL | WS_HSCROLL);
	// 윈도우 창 크기 설정
    m_WindowRect = { 0, 0, iWidth, iHeight };

	// MyRegisterClass() 함수를 통해 윈도우 클래스를 등록
    WORD ret = MyRegisterClass();
	// InitInstance() 함수를 통해 등록된 윈도우 클래스를 윈도우 창으로 생성
    if (!InitInstance())
    {
        return FALSE;
    }
    return TRUE;
}
```
SetWindow() 함수는 안에서 MyRegisterClass() 함수와 InitInstance() 함수를 호출하면서 윈도우 창 기본 설정을 마친 뒤 등록하고 이를 생성까지 하는 함수이다.

따라서 이 함수 안에서 윈도우 창 생성의 모든 과정이 실행된다고 볼 수 있으며 생성이 잘 되었다면 TRUE, 생성이 되지 않았다면 FALSE를 리턴한다.

```c++
ATOM HWindow::MyRegisterClass()
{
	// 윈도우 클래스 생성
    WNDCLASSEXW wcex;

	// 생성한 클래스를 0으로 초기화
    ZeroMemory(&wcex, sizeof(WNDCLASSEXW));

	// 클래스의 크기를 알맞게 설정
    wcex.cbSize = sizeof(WNDCLASSEX);
    // 윈도우 스타일 설정
    wcex.style = CS_HREDRAW | CS_VREDRAW;
    // 프로시저 설정
    wcex.lpfnWndProc = WndProc;
    // 인스턴스 설정
    wcex.hInstance = m_hInstance;
    // 윈도우 배경색 설정
    wcex.hbrBackground = CreateSolidBrush(RGB(0,0,0));
    // 등록할 창의 이름 설정?
    wcex.lpszClassName = m_className;

	// 등록 후 리턴값 반환
    return RegisterClassEx(&wcex);
}
```

MyRegisterClass() 함수에서는 