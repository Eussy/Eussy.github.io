---
layout: post
title:  "디자인 패턴 02 : 추상 팩토리 패턴(Abstract Factory Pattern)"
summary: "추상 팩토리 패턴"
author: Eussy
date: '2022-08-19 19:00:00 +0530'
category: '03_Design Pattern'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/03_Design_Pattern/Abstract-Factory-Pattern/
---

## 추상 팩토리 패턴

### 추상 팩토리 패턴(Abstract Factory Pattern)이란?
팩토리 메서드 패턴을 한번 더 캡슐화해서 객체를 만들어내는 팩토리들을 만드는 패턴으로, 
객체를 제품이라고 비유했을 때 제품군에 따라 팩토리를 분류하는 것을 중요시한다.

### 추상 팩토리 패턴(Abstract Factory Pattern)과 팩토리 메소드 패턴(Factory Method Pattern)의 차이점
**추상 팩토리 패턴** : 서로 관련이 있는 객체들을 통째로 묶어서 팩토리 클래스로 만들고, 
이들 팩토리를 조건에 따라 생성하도록 다시 팩토리를 만들어서 객체를 생성하는 패턴

**팩토리 메소드 패턴** : 조건에 따라 객체 생성을 팩토리 클래스안의 함수에서 만들어서 반환하도록 하는 패턴

## 예제

### 팩토리에서 만들어낼 아이템 클래스들

음식과 옷들을 Asian, Western으로 구분해보았다.

```c++
class Food {};
class AsianFood : public Food {};
class WesternFood : public Food {};

class Clothes {};
class AsianClothes : public Clothes {};
class WesternClothes : public Clothes {};
```



### 객체를 만들 추상 Factory 클래스

이제 이 아이템들을 만들어 낼 Factory 클래스들을 살펴보자

```c++
class Factory{
public:
    virtual Food* CreateFood() = 0;
    virtual Clothes* CreateClothes() = 0;
};
class AsianFactory : public Factory{
public:
    virtual Food* CreateFood(){ return new AsianFood; }
    virtual Clothes* CreateClothes(){ return new AsianClothes; }
}
class WesternFactory: public Factory{
public:
    virtual Food* CreateFood(){ return new WesternFood; }
    virtual Clothes* CreateClothes(){ return new WesternClothes; }
}
```


### 사용 예제

이 객체들을 만들어내는 팩토리 클래스들을 살펴보자.

```c++
int main()
{
    // 아시안 팩토리를 통한 아이템 생성
    Factory* fac1 = new AsianFactory;
    Food* = fac1.CreateFood();
    Clothes* = fac1.CreateClothes();

    // 웨스턴 팩토리를 통한 아이템 생성
    Factory* fac2 = new WesternFactory;
    Food* = fac2.CreateFood();
    Clothes* = fac2.CreateClothes();
}
```

이 ObjFactory에서 NewObject()라는 인터페이스를 순수 가상 함수로 만들어줌으로써
ObjFactory2D와 ObjFactory3D에서 같은 NewObject() 함수를 사용하더라도 다른 객체를 생성한다.


### 전체 코드

전체 코드는 다음과 같다.

```c++
#include <iostream>
// 아이템
class Food {};
class AsianFood : public Food {};
class WesternFood : public Food {};

class Clothes {};
class AsianClothes : public Clothes {};
class WesternClothes : public Clothes {};

// 팩토리
class Factory{
public:
    virtual Food* CreateFood() = 0;
    virtual Clothes* CreateClothes() = 0;
};
class AsianFactory : public Factory{
public:
    virtual Food* CreateFood(){ return new AsianFood; }
    virtual Clothes* CreateClothes(){ return new AsianClothes; }
}
class WesternFactory: public Factory{
public:
    virtual Food* CreateFood(){ return new WesternFood; }
    virtual Clothes* CreateClothes(){ return new WesternClothes; }
}

// 메인 함수
int main()
{
    // 아시안 팩토리를 통한 아이템 생성
    Factory* fac1 = new AsianFactory;
    Food* = fac1.CreateFood();
    Clothes* = fac1.CreateClothes();

    // 웨스턴 팩토리를 통한 아이템 생성
    Factory* fac2 = new WesternFactory;
    Food* = fac2.CreateFood();
    Clothes* = fac2.CreateClothes();
}
```

## template을 사용한 Abstract Factory Pattern
