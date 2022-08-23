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

### 2. 윈도우 클래스 생성

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


```c++
class HWindow : Dynamic
{
public:
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
    // 인터페이스에서 상속 받은 함수들 : 지금은 아무런 기능 X
	virtual BOOL	Init() override;
	virtual BOOL	Frame() override;
	virtual BOOL	Render() override;
	virtual BOOL	Release() override;
public:
	LRESULT			MsgProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);
	BOOL			SetWindow(HINSTANCE instance, LPCWSTR title, LPCWSTR ucName, LONG iWidth, LONG iHeight);
	ATOM			MyRegisterClass();
	BOOL			InitInstance(UINT iWidth, UINT iHeight);
	BOOL			Run();
public:
	// 유틸리티
	void			MoveWindowCenter();

	void			SetWindowStyle(int style);

	LONG			GetWindowWidth();
	LONG			GetWindowHeight();
	LONG			GetClientWidth();
	LONG			GetClientHeight();
};
```

