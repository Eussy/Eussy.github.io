---
layout: post
title:  "디자인 패턴 01 : 팩토리 메소드 패턴(Factory Method Pattern)"
summary: "팩토리 메소드 패턴"
author: Eussy
date: '2022-08-19 18:30:00 +0530'
category: '03_Design Pattern'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/03_Design_Pattern/Factory-Method-Pattern/
---

## 팩토리 메소드 패턴
객체를 생성하기 위해 부모 클래스에서 인터페이스를 정의하고, 그 아래 서브 클래스에서 어떠한 객체를 생성할지를 결정하게 만드는 패턴이다.

```c++
#include <iostream>
#include <vector>

class TBaseObject 
{
public:
    virtual ~TBaseObject(){}
};
class TObject3D : public TBaseObject{};
class TObject2D : public TBaseObject {};
```

현재 코드에서는 BaseObject라는 부모 클래스가 존재하고
이를 상속받은 Object3D와 Object2D라는 클래스가 존재한다.

이 객체들을 만들어내는 팩토리 클래스들을 살펴보자.

```c++
class ObjFactory
{
public:
    virtual TBaseObject* NewObject() = 0;
};

class ObjFactory2D : public ObjFactory 
{
public:
    virtual TBaseObject* NewObject()
    {
        return new TObject2D();
    }
};

class ObjFactory3D : public ObjFactory 
{
public:
    virtual TBaseObject* NewObject()
    {
        return new TObject3D();
    }
};
```

이 ObjFactory에서 NewObject()라는 인터페이스를 순수 가상 함수로 만들어줌으로써
ObjFactory2D와 ObjFactory3D에서 같은 NewObject() 함수를 사용하더라도 다른 객체를 생성한다.

```c++
int main()
{
    // 팩토리 포인터
    ObjFactory* factory = nullptr;

    // 2D 팩토리 생성
    factory = new ObjFactory2D;
    TBaseObject* obj = factory->NewObject();    // 1번

    // 3D 팩토리 생성
    factory = new ObjFactory3D;
    TBaseObject* obj = factory->NewObject()     // 2번
}
```

따라서 1번에서는 Object2D가, 2번에서는 Object3D가 생성되어 obj에 담긴다.

전체 코드는 다음과 같다.

```c++
#include <iostream>
#include <vector>

class BaseObject 
{
public:
    virtual ~TBaseObject(){}
};
class Object3D : public BaseObject{};
class Object2D : public BaseObject {};

class ObjFactory
{
public:
    virtual TBaseObject* NewObject() = 0;
};

class ObjFactory2D : public ObjFactory 
{
public:
    virtual TBaseObject* NewObject()
    {
        return new TObject2D();
    }
};

class ObjFactory3D : public ObjFactory 
{
public:
    virtual TBaseObject* NewObject()
    {
        return new TObject3D();
    }
};

int main()
{
    // 2D 팩토리 생성
    ObjFactory* factory = new ObjFactory2D;
    // 3D 팩토리 생성
    ObjFactory* factory = new ObjFactory3D;

    TBaseObject* obj = sd->NewObject();
}
```



## template을 사용했을 때 장점과 단점