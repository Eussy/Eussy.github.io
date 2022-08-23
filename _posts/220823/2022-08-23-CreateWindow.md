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

### 이번 장에서는?
이번 장에서는 윈도우에 관한 클래스를 정의하고 사용하는 방법을 구현토록 할 것이다.

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
