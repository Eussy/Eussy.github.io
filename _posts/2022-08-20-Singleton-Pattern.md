---
layout: post
title:  "디자인 패턴 06 : 싱글톤 패턴(Singleton Pattern)"
summary: "싱글톤 패턴"
author: Eussy
date: '2022-08-20 17:50:00 +0530'
category: '03_Design Pattern'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/03_Design_Pattern/Singleton-Pattern/
---

## 싱글톤 패턴

### 싱글톤 패턴(Singleton Pattern)이란?
객체 생성 디자인 패턴 중에 하나로, 여러 개의 객체들로 구성된 복합 객체와 단일 객체를 클라이언트에서 구별 없이 다루게 해주는 패턴이다.
전체-부분의 관계를 갖는 객체들(Ex.Directory-File) 사이의 관계를 정의할 때 유용하다.
부분과 전체의 계층을 표현하기 위해 객체들을 모아 트리 구조로 구성하며 사용자로 하여금 복합 객체와 단일 객체를 동일하게 사용할 수 있도록 한다.

폴더와 파일의 관계를 예제로 만들어 컴포지트 패턴을 살펴보도록 하자.

## 예제

### Composite 추상 클래스

```c++
#include <iostream>
#include <vector>

using namespace std;

class Composite {
public:
	string name;
	int size;
public:
	Composite(string name, int size) : name(name), size(size) {}
	virtual ~Composite(){}
	virtual string getName() { return ""; };
	virtual int getSize() { return 0; };
};
```

위 Composite 추상 클래스를 상속받을 클래스들은 폴더와 파일이다. 이는 각자 이름(name)과 크기(size) 변수를 가지고 있다.
아래 Folder와 File 클래스는 getSize()와 getName() 함수를 구현함으로써 이름과 사이즈를 반환할 수 있다.

그리고 폴더는 리스트를 가지고 있기 때문에 파일을 포함 할 수 있어 File은 Leaf로 동작하고 Folder는 이러한 Leaf들을 m_list 안에 포함해 Composite 패턴을 구현하였다.

```c++
class Folder : public Composite {
public:
	vector<Composite*> m_list; 
public:
	Folder(string name, int size) : Composite(name, size){}
	~Folder() {
		for (Composite* com : m_list)
		{
			delete com;
		}
	}
	virtual string getName() override {
		return name;
	}
	virtual int getSize() override {
		for (Composite* com : m_list)
		{
			size += com->getSize();
		}
		return size;
	}
};

class File : public Composite {
public:
	File(string name, int size) : Composite(name, size) {}
	virtual string getName() override {
		return name;
	}
	virtual int getSize() override {
		return size;
	}
};
```


### 사용 예제

```c++
int main()
{
	// 맨 상위 폴더 배경화면 생성 후 카카오톡 넣기
	Folder* background = new Folder("배경화면", 0);
	background->m_list.push_back(new File("카카오톡", 5));

	// 폴더 하나 생성 후 크롬, 엣지 파일 넣기
	Folder* folder1 = new Folder("폴더1", 0);
	folder1->m_list.push_back(new File("크롬", 2));
	folder1->m_list.push_back(new File("엣지", 4));

	//폴더1을 배경화면 폴더에 넣기
	background->m_list.push_back(folder1);

	cout << background->getSize();

	delete background;
}
```

현재 파일의 구성을 보면, "바탕화면"이라는 폴더 안에는 사이즈 5의 "카카오톡"이라는 파일과 "폴더1"이라는 폴더가 존재한다.
이 폴더 안에는 사이즈 2의 "크롬", 사이즈 4의 "엣지"라는 파일이 존재하므로 이 폴더의 크기는 2 + 4 = 6이다.
따라서 폴더1과 카카오톡의 크기를 합친 크기가 배경화면의 크기가 된다. 이렇게 컴포지트 패턴은 폴더라는 복합 객체 또한 단일 객체와 같이 동작할 수 있도록
list, vector를 통해 관리하도록 하는 패턴이다.


### 전체 코드

전체 코드는 다음과 같다.

```c++
#include <iostream>
#include <vector>

using namespace std;

class Composite {
public:
	string name;
	int size;
public:
	Composite(string name, int size) : name(name), size(size) {}
	virtual ~Composite(){}
	virtual string getName() { return ""; };
	virtual int getSize() { return 0; };
};

class Folder : public Composite {
public:
	vector<Composite*> m_list; 
public:
	Folder(string name, int size) : Composite(name, size){}
	~Folder() {
		for (Composite* com : m_list)
		{
			delete com;
		}
	}
	virtual string getName() override {
		return name;
	}
	virtual int getSize() override {
		for (Composite* com : m_list)
		{
			size += com->getSize();
		}
		return size;
	}
};

class File : public Composite {
public:
	File(string name, int size) : Composite(name, size) {}
	virtual string getName() override {
		return name;
	}
	virtual int getSize() override {
		return size;
	}
};

int main()
{
	// 맨 상위 폴더 배경화면 생성 후 카카오톡 넣기
	Folder* background = new Folder("배경화면", 0);
	background->m_list.push_back(new File("카카오톡", 5));

	// 폴더 하나 생성 후 크롬, 엣지 파일 넣기
	Folder* folder1 = new Folder("폴더1", 0);
	folder1->m_list.push_back(new File("크롬", 2));
	folder1->m_list.push_back(new File("엣지", 4));

	//폴더1을 배경화면 폴더에 넣기
	background->m_list.push_back(folder1);

	cout << background->getSize();

	delete background;
}


```
