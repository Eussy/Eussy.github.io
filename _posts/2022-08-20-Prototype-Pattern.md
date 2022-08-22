---
layout: post
title:  "디자인 패턴 04 : 프로토 타입 패턴(Prototype Pattern)"
summary: "프로토 타입 패턴"
author: Eussy
date: '2022-08-19 16:00:00 +0530'
category: '03_Design Pattern'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/03_Design_Pattern/Prototype-Pattern/
---

## 프로토타입 패턴

### 프로토타입 패턴(Prototype Pattern)이란?
객체 생성 디자인 패턴 중에 하나로, 기존 객체를 복사해서 객체를 생성하는 패턴으로,
객체를 생성하는데 시간과 비용이 많이 들고 이미 객체가 생성되어 있을 경우에 사용된다.
클래스 안에 clone()과 같은 함수로 구현한다. 이때 clone() 함수를 만들 때 깊은 복사와 얕은 복사에 유의한다.
(**디폴트 복사 생성자**는 메모리를 그대로 복사해서 붙여 넣기 때문에 가장 비용이 저렴하다.
따라서 디폴트 복사 생성자를 사용할 수 있을 때는 사용하는 것이 가장 효율적이다.)


예제로 프로토타입을 한번 알아보도록 하자

## 예제

### Prototype 인터페이스와 이를 통해 만들어낼 Book이라는 클래스 정의하기

```c++
#include <iostream>
#include <string>
#include <vector>

using namespace std;

template <class T>
class Prototype {
public:
    virtual T clone() = 0;
};

class Book : Prototype<Book>{
public:
    string title;
    string author;
    vector<string> content;
public:
    Book clone() override {
        // 기본 복사 생성자를 통한 복사
        Book book = *this;
        return book;
    }
};
```

우리는 책을 복제하는 것을 예시로 들어 프로토타입 패턴을 살펴보도록 하겠다. 우선 위 코드는 Prototype이라는 인터페이스를 만들고 그 인터페이스를 가지고 만들어낸 Book이라는 클래스이다.
이 같이 인터페이스를 만들게 되면 반드시 clone() 함수를 구현해야한다는 강제성이 생긴다.

```c++
class Book {
public:
    string title;
    string author;
    vector<string> content;
public:
    Book clone(){
        // 기본 복사 생성자를 통한 복사
        Book book = *this;
        return book;
    }
};
```

물론 다음과 같이 클래스 안에 단순히 clone() 함수를 만들어 구현할 수도 있다. 상황에 맞춰 사용하면 될 듯 하다.

### 주의 점 : 깊은 복사와 얕은 복사

이 때 clone() 함수를 구현할 때 깊은 복사와 얕은 복사를 주의하여 만들어야한다.

```c++
// 깊은 복사, 얕은 복사 주의
class Person : Prototype<Person>{
public:
    char* name;
    int age;
public:
    Person clone() override {
        Person p = *this;
        p.name = new char[];
        return p;
    }
};
```

위 Person 클래스에서는 name을 char형 포인터로 받고 있기 때문에 그냥 디폴트 복사생성자를 사용하게 되면 기존 객체의 name을 참조하기 때문에 
기존 객체와 복사한 객체가 같은 자료를 가리키게 되기 때문에 문제가 발생할 수 있다. 따라서 깊은 복사를 구현해 문제를 해결하도록 한다.

### 사용 예제

```c++
int main(){
Book oriBook;
    oriBook.title = "Good Book";
    oriBook.author = "Eussy";
    oriBook.content.push_back("1장 : 시작");
    oriBook.content.push_back("2장 : 중간");
    oriBook.content.push_back("3장 : 끝");

    Book copyBook = oriBook.clone();
}
```

실제로 책을 하나 구성할 때는 제목부터 내용까지 엄청나게 많은 노력을 필요로 한다. 이렇게 책 하나를 구성하는데 많은 시간이 들기 때문에 
수백, 수천개의 책을 만들어야할때 엄청난 노력이 필요로 하게 된다. 
하지만 이 때 책을 찍어내는 활자가 있다면 어떨까? 활자가 존재한다면 책을 일일이 구성하지 않아도 되기 때문에 많은 비용과 시간을 아낄 수 있다.

이처럼 코드에서도 **clone()이라는 복사수단을 이용해 객체 생성 비용과 시간을 아낄 수 있도록 하는 것**이 바로 프로토타입(Prototype) 패턴의 가장 큰 특징이자 장점이라 할 수 있다.

### 전체 코드

전체 코드는 다음과 같다.

```c++
#include <iostream>
#include <string>
#include <vector>

using namespace std;

template <class T>
class Prototype {
public:
    virtual T clone() = 0;
};

// 템플릿 사용 O
class Book : Prototype<Book>{
public:
    string title;
    string author;
    vector<string> content;
public:
    Book clone() override {
        // 기본 복사 생성자를 통한 복사
        Book book = *this;
        return book;
    }
};

// 템플릿 사용 X
class Book2 {
public:
    string title;
    string author;
    vector<string> content;
public:
    Book2 clone(){
        // 기본 복사 생성자를 통한 복사
        Book2 book = *this;
        return book;
    }
};

int main()
{
    Book oriBook;
    oriBook.title = "Good Book";
    oriBook.author = "Eussy";
    oriBook.content.push_back("1장 : 시작");
    oriBook.content.push_back("2장 : 중간");
    oriBook.content.push_back("3장 : 끝");

    Book copyBook = oriBook.clone();
}

```
