---
layout: post
title:  "C/C++ Container : 01. Vector"
summary: "컨테이너 벡터 정리"
author: Eussy
date: '2022-08-01 18:30:00 +0530'
category: '01_C/C++'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: vector, container, c, c++, stl
usemathjax: true
permalink: /blog/01_C_C++/Container_01_vector
---

## 벡터(vector)


### 벡터란(vector)?


### 벡터의 필요 선언
---
```c++
#include <vector>
using namespace std;
```
---


### 벡터의 선언 방식과 생성, 소멸

```c++
	// 원소 없이 빈 벡터 생성
	vector<int> vec1;
	// vecter를 복사하여 생성
	vector<int> vec2(vec1);
	// 20개의 원소와 함께 생성 : size = 20, capacity = 20
	vector<int> vec3(20);
	// 20개의 원소를 1로 초기화하며 생성
	vector<int> vec4(20, 1);
	// 해당 범위의 원소로 vector를 초기화하여 생성
	vector<int> vec5(vec4.begin(), vec4.end());
	// 모든 원소를 파괴하고 메모리 해제
	vec1.~vector<int>();
```


### 벡터의 메모리, 크기와 관련된 함수들

```c++
	// 벡터의 크기
	vec4.size();
	// 벡터가 비어있는지 확인
	vec4.empty();
	// 가질 수 있는 최대 원소의 개수 : 진짜 메모리에서 계속해서 가질 수 있는 어마어마하게 큰 수이다. capacity와 다름
	vec4.max_size();		// 4654132154
	// 재할당 없이 가질 수 있는 최대 원소 개수
	vec4.capacity();		// 20
	// reserve : 재할당 빈도수를 줄이기 위해서 메모리를 미리 확보, 예약해두어서 그 수까지 재할당을 안하게 해두는 것 (resize와 다른 점 알아두기)
	vec4.reserve(40);		// 이 경우 capacity : 40, size는 20
	// resize : 아예 재할당을 하는 함수로 n만큼 재할당을 받고 초기화를 한다. 사이즈가 증가되면 그 새로 할당된 원소 값도 초기화 가능하다
	vec4.resize(40);
	vec4.resize(50, 2);
```


### 벡터의 할당 방법

```c++
	// vec3의 모든 원소들을 vec2에 할당 (vec3가 원소가 없다면 모든 원소 사라짐)
	vec2 = vec3;
	// 현재 원소들을 모두 없애고 10개의 원소(3)을 할당한다.
	vec2.assign(10, 3);
	// 현재 원소들을 모두 없애고 vec4.begin() ~ vec4.end() 범위의 원소들을 할당한다.
	vec2.assign(vec4.begin(), vec4.end());
	// 현재 원소들을 모두 없애고 initializer_list class를 이용한 assign을 수행한다.
	vec2.assign({ 1, 2, 3, 4, 5 });
	// vec2와 vec3의 데이터를 교체한다.
	vec2.swap(vec3);
	swap(vec2, vec3);
```


### 벡터에서 지원하는 비교 연산자

```c++
	bool right = false;
	right = vec4 == vec5;	// false	: capacity는 달라도 상관 X, size는 상관 O
	right = vec4 != vec5;	// true
	right = vec4 < vec5;	// false
	right = vec4 > vec5;	// true
```


### 벡터의 인덱싱

```c++
	// 인덱스가 idx인 원소를 반환 (범위를 벗어난 idx라면 범위에러 발생)
	vec4.at(20);
	// 인덱스가 idx인 원소를 반환 (범위를 벗어나도 에러 X, assert는 발생)
	vec4[20];
	// 첫 번째 원소를 반환 (벡터에 아무 원소도 없으면 assert 발생)
	vec4.front();
	// 마지막 원소를 반환 (벡터에 아무 원소도 없으면 assert 발생)
	vec4.back();
```


### 벡터의 반복자(iterator, reverse_iterator)

```c++

	// ------------[반복자(iterator)]------------
	// 첫번째 원소를 가리키는 iterator를 반환한다.
	vector<int>::iterator iter1 = vec4.begin();
	// 마지막 원소의 주소 + 1 가리키는 iterator를 반환한다.
	vector<int>::iterator iter2 = vec4.end();
	// 마지막 원소를 가리키는 reverse_iterator를 반환한다.
	vector<int>::reverse_iterator iter3 = vec4.rbegin();
	// 첫번째 원소의 주소 - 1 가리키는 reverse_iterator를 반환한다.
	vector<int>::reverse_iterator iter4 = vec4.rend();
```


### 벡터의 삽입

```c++

	vector<int>::iterator iter5, iter6, iter7, iter8;
	// iterator 위치에 원소의 복사본(2)를 삽입 후 그 위치의 iterator 반환
	iter5 = vec2.insert(vec2.begin(), 2);
	// iterator 위치에 원소의 복사본(3)을 n(2)개 삽입 후 그 위치의 iterator 반환
	iter6 = vec2.insert(iter5, 2, 3);
	// iterator 위치에 begin(vec1.begin())~end(vec2.end()) 범위의 모든 원소들을 복사하여 삽입 후 그 위치의 iterator 반환
	iter7 = vec4.insert(vec4.begin(), vec2.begin(), vec2.end());
	// iterator 위치에 initializer_list를 삽입 후 그 위치의 iterator 반환
	iter8 = vec2.insert(iter6, { 10, 9 });
	// 벡터 마지막에 원소를 복사해 추가한다. 반환값 X (void)
	vec2.push_back(5);
	// 벡터 마지막 원소를 삭제한다. 반환값 X (void)
	vec2.pop_back();
	// **emplace() : 원하는 곳 뒤에 () 안에 있는 데이터를 컨테이너 안에서 생성자를 통해 객체를 생성 후 넣는다. (복사비용이 X) but 포인터를 넣을때는 의미 X
	// **emplace_back() : 맨 뒤에 넣는다.
	{
		vector<TItem> emVec;
		emVec.reserve(20);
		// 1. 객체를 생성자를 통해서 생성하고 생성된 객체를 복사해서 컨테이너 안에 넣는다.
		{
			TItem item(1);
			emVec.push_back(item);
		}
		// 2. emplace 로 넣는 값을 컨테이너 안에서 직접 생성자 호출해서 통해서 생성해 넣는다 (복사를 한 것이 아니라 복사비용X)
		{
			emVec.emplace_back(3);
		}
		// 3. 두 번 생성해서 넣는다.
		{
			TItem item(2);
			emVec.emplace_back(item);
		}
	}
```


### 벡터의 제거

```c++
	vector<int>::iterator iter9, iter10, iter11;
	// iterator 위치의 원소를 삭제하고 그 다음 원소 위치의 iterator 반환
	// => 이때 erase된 iter11는 재사용 불가 => erase를 반환받아 사용해야함.
	iter11 = vec2.begin();
	iter9 = vec2.erase(vec2.begin());
	// 해당 범위(vec2.begin() ~ iter9 => iter9 위치의 원소는 포함 X)의 모든 원소를 제거 한 후 그 다음 원소 위치의 iterator 반환
	++iter9;
	++iter9;
	iter10 = vec2.erase(vec2.begin(), iter9);
	// 모든 원소들을 제거하고 빈 컨테이너로 만든다
	vec2.clear();
	// 새로운 for 문 동작
	// 복사를 통한 것이기 때문에 value를 바꿔도 vec2 안의 value 안 바뀜
	for (int value : vec2)
	{
		value++;
	}
	// 참조를 통한 것이기 때문에 value를 바꾸면 vec2 안에 있는 value가 바뀜
	for (int& value : vec2)
	{
		value++;
	}
```
