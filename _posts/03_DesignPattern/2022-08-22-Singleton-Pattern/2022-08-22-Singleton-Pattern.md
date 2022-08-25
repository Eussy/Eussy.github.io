---
layout: post
title:  "디자인 패턴 06 : 싱글톤 패턴(Singleton Pattern)"
summary: "싱글톤 패턴"
author: Eussy
date: '2022-08-22 18:30:00 +0530'
category: '03_Design Pattern'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/03_Design_Pattern/Singleton-Pattern/
---

## 싱글톤 패턴

### 싱글톤 패턴(Singleton Pattern)이란?
객체 생성 디자인 패턴 중에 하나로, '하나'의 인스턴스만 생성하여 사용하는 디자인 패턴이다.
즉, 인스턴스가 필요할 때, 똑같은 인스턴스를 만드는 것이 아닌 만들어져 있는 인스턴스를 계속해서 활용한다.
인스턴스가 하나만 존재해야할 때, 이 싱글톤 패턴을 많이 사용한다.

대한민국의 주민등록번호를 예시로 만들어 싱글톤 패턴을 살펴보도록 하자.

## 예제

### RegisterNumber 클래스

```c++
#include <iostream>

class RegisterNumber {
private:
    static RegisterNumber* instance;
    int number;
    RegisterNumber() {}
public:
    static RegisterNumber GetInstance(){
        if (instance == nullptr)
        {
            instance = new RegisterNumber;
            instance->number = rand();
        }
        return *instance;
    }
};
```

위 RegisterNumber 클래스는 싱글톤 패턴으로 구현한 클래스이다. 생성자의 접근권한이 private이기 때문에 외부에서 생성자를 사용할 수 없고
GetInstance()만 사용해서 접근할 수 있다. 이 때 GetInstance() 함수 안에서는 이미 instance가 존재하면 그대로 반환하기 때문에 단 하나의 RegisterNumber 객체만이 존재한다는 것을 알 수 있다.

```c++
template<class T>
class Singleton
{
public:
    static T& GetInstance()
    {
        static T instance;
        return instance;
    }
};

class RegisterNumber2 : public Singleton<RegisterNumber2> {
    friend class Singleton<RegisterNumber2>;
private:
    int number;
    RegisterNumber2(){ number = rand(); }
};
```
위의 코드는 template을 이용해 싱글톤을 구현하였다. 이와 같이 사용한다면 가독성이 높아지고 여러 싱글톤을 만들때 편하게 사용할 수 있다는 장점이 있다.



### 사용 예제

```c++
int main()
{
	// RegisterNumber는 단 하나의 객체만 생성이 가능
	RegisterNumber myNum = RegisterNumber::GetInstance();
    RegisterNumber2 myNum2 = RegisterNumber2::GetInstance();
}
```

RegisterNumber 클래스 안에 있는 GetInstance() 함수를 통해서만 객체를 사용할 수 있으며 이 객체는 단 하나만 존재하게 구현하였다.
이 같이 클래스의 객체를 단 하나만 생성하고 사용하는 패턴을 싱글톤 패턴이라고 한다.


### 전체 코드

전체 코드는 다음과 같다.

```c++
#include <iostream>

class RegisterNumber {
private:
    static RegisterNumber* instance;
    int number;
    RegisterNumber() {}
public:
    static RegisterNumber GetInstance(){
        if (instance == nullptr)
        {
            instance = new RegisterNumber;
            instance->number = rand();
        }
        return *instance;
    }
};

template<class T>
class Singleton
{
public:
    static T& GetInstance()
    {
        static T instance;
        return instance;
    }
};

class RegisterNumber2 : public Singleton<RegisterNumber2> {
    friend class Singleton<RegisterNumber2>;
private:
    int number;
    RegisterNumber2(){ number = rand(); }
};

int main()
{
    RegisterNumber myNum = RegisterNumber::GetInstance();
    RegisterNumber2 myNum2 = RegisterNumber2::GetInstance();
}
```
