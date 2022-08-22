---
layout: post
title:  "디자인 패턴 03 : 빌더 패턴(Builder Pattern)"
summary: "빌더 패턴"
author: Eussy
date: '2022-08-20 15:22:00 +0530'
category: '03_Design Pattern'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: 
usemathjax: true
permalink: /blog/03_Design_Pattern/Builder-Pattern/
---

## 빌더 패턴

### 빌더 패턴(Builder Pattern)이란?
객체 생성 디자인 패턴 중에 하나로, 객체의 생성 단계들을 각각 함수로 진행하고
이 단계들의 값들을 다르게 함으로써 다른 객체의 표현이 가능해진다.
하나의 객체를 반복적으로 찍어내는 팩토리 메소드에서 조금 더 발전한 버전이라고 생각하면 편하다.
(+ 추상 팩토리 패턴은 제품군의 제품군을 강조하고, 빌더 패턴은 하나의 제품군의 제작 단계를 강조한다.)
다양한 부품으로 구성된 객체를 단계적으로 빌드하면서 객체를 생성한다는 것이 가장 특징이다.

빌더 패턴을 통해 피자를 만들어내는 예제를 살펴보자.

## 예제

### 빌더에서 만들어낼 생성해낼 피자 클래스 생성

```c++
#include <iostream>
#include <string>

using namespace std;


class Pizza {
public:
	string name;
	string toping;
	string flavor;
};

```

Pizza라는 클래스를 만들고 그 안에 name, toping, flavor라는 멤버 변수들을 두었다.

### Builder 클래스 생성

이 Pizza 객체들을 만들어낼 Builder 클래스를 살펴보자

```c++
class PizzaBuilder {
public:
	string name;
	string toping;
	string flavor;
public:
	PizzaBuilder setName(string name) { this->name = name; return *this; };
	PizzaBuilder setToping(string toping) { this->toping = toping; return *this; };;
	PizzaBuilder setFlavor(string flavor) { this->flavor = flavor; return *this; };;
	Pizza		 Build() {
		Pizza pizza;
		pizza.name = this->name;
		pizza.toping = this->toping;
		pizza.flavor = this->flavor;

		return pizza;
	}
};
```

이 PizzaBuilder 클래스에서는 setName(), setToping(), setFlavor()와 같은 함수들을 통해 Pizza 객체를 단계별로 생성할 수 있게 설계되어 있다.
다음 사용 예제를 통해 어떻게 사용하는지 살펴보자.

### 사용 예제

```c++
int main()
{
	PizzaBuilder pizBuilder;
	Pizza pizza = pizBuilder
		.setName("Bacon Potato Pizza")
		.setToping("Bacon")
		.setFlavor("Potato")
		.Build();

	cout << pizza;
}
```

PizzaBuilder의 함수들을 통해 **단계를 거쳐** 최종적으로 Build()를 통해 Pizza 객체를 생성하는 것을 볼 수 있다.
이처럼 단계를 통해 객체를 생성하는 생성 패턴을 빌더 패턴(Builder Pattern)이라고 한다.

### 전체 코드

전체 코드는 다음과 같다.

```c++
#include <iostream>
#include <string>

using namespace std;


class Pizza {
public:
	string name;
	string toping;
	string flavor;
public:
	friend ostream& operator<<(ostream& os, Pizza& pizza);
};

class PizzaBuilder {
public:
	string name;
	string toping;
	string flavor;
public:
	PizzaBuilder setName(string name) { this->name = name; return *this; };
	PizzaBuilder setToping(string toping) { this->toping = toping; return *this; };;
	PizzaBuilder setFlavor(string flavor) { this->flavor = flavor; return *this; };;
	Pizza		 Build() {
		Pizza pizza;
		pizza.name = this->name;
		pizza.toping = this->toping;
		pizza.flavor = this->flavor;

		return pizza;
	}
};

int main()
{
	PizzaBuilder pizBuilder;
	Pizza pizza = pizBuilder
		.setName("Bacon Potato Pizza")
		.setToping("Bacon")
		.setFlavor("Potato")
		.Build();

	cout << pizza;
}


ostream& operator<<(ostream& os, Pizza& pizza)
{
	os << "Pizza name : " << pizza.name << endl
		<< "Pizza toping : " << pizza.toping << endl
		<< "Pizza flavor : " << pizza.flavor << endl;
	return os;
}
```
