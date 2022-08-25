---
layout: post
title:  "C/C++ Container : 02. Deque"
summary: "컨테이너 덱 정리"
author: Eussy
date: '2022-08-01 19:00:00 +0530'
category: '01_C/C++'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: deque, container, c, c++, stl
usemathjax: true
permalink: /blog/01_C_C++/Container_02_deque
---

## 덱(deque)


### 덱(deque)란?
덱는 


### 덱의 필요 선언
---
```c++
#include <deque>
using namespace std;
```
---


### 덱의 선언 방식과 생성, 소멸

```c++
// 원소 없이 빈 덱 생성
deque<int> dec;
// dec를 복사하여 생성
deque<int> dec2(dec);
// 20개의 원소와 함께 생성 : size = 20
deque<int> dec3(20);
// 20개의 원소를 1로 초기화하며 생성
deque<int> dec4(20, 1);
// 해당 범위의 원소로 deque를 초기화하여 생성
deque<int> dec5(dec4.begin(), dec4.end());
// 모든 원소 파괴 후 메모리 해제
dec.~deque<int>();
```


### 덱의 메모리, 크기와 관련된 함수들

```c++
// 덱의 크기
dec4.size();
// 덱가 비어있는지 확인
dec4.empty();
// 가질 수 있는 최대 원소의 개수 : 진짜 메모리에서 계속해서 가질 수 있는 어마어마하게 큰 수이다. capacity와 다름
dec4.max_size();		// 4654132154
// resize : 원소의 개수를 num개로 변경하고 size()가 증가하면 디폴트 생성자에 의해 생성되고 새로운 원소는 복사를 통해 초기화할 수도 있다.
dec4.resize(40);
dec4.resize(50, 2);
```


### 덱의 할당 방법

```c++
// dec3의 모든 원소들을 dec2에 할당 (dec3가 원소가 없다면 모든 원소 사라짐)
dec2 = dec3;
// 현재 원소들을 모두 없애고 10개의 원소(3)을 할당한다.
dec2.assign(10, 3);
// 현재 원소들을 모두 없애고 dec4.begin() ~ dec4.end() 범위의 원소들을 할당한다.
dec2.assign(dec4.begin(), dec4.end());
// 현재 원소들을 모두 없애고 initializer_list class를 이용한 assign을 수행한다.
dec2.assign({ 1, 2, 3, 4, 5 });
// dec2와 dec3의 데이터를 교체한다.
dec2.swap(dec3);
swap(dec2, dec3);
```


### 덱에서 지원하는 비교 연산자

```c++
bool right = false;
right = dec4 == dec5;	// false	:  size 상관 O
right = dec4 != dec5;	// true
right = dec4 < dec5;	// false
right = dec4 > dec5;	// true
```


### 덱의 인덱싱

```c++
// 인덱스가 idx인 원소를 반환 (범위를 벗어난 idx라면 범위에러 발생)
dec4.at(20);
// 인덱스가 idx인 원소를 반환 (범위를 벗어나도 에러 X, assert는 발생)
dec4[20];
// 첫 번째 원소를 반환 (벡터에 아무 원소도 없으면 assert 발생)
dec4.front();
// 마지막 원소를 반환 (벡터에 아무 원소도 없으면 assert 발생)
dec4.back();
```


### 덱의 반복자(iterator, reverse_iterator)

```c++
// 첫번째 원소를 가리키는 iterator를 반환한다.
deque<int>::iterator iter1 = dec4.begin();
// 마지막 원소의 주소 + 1 가리키는 iterator를 반환한다.
deque<int>::iterator iter2 = dec4.end();
// 마지막 원소를 가리키는 reverse_iterator를 반환한다.
deque<int>::reverse_iterator iter3 = dec4.rbegin();
// 첫번째 원소의 주소 - 1 가리키는 reverse_iterator를 반환한다.
deque<int>::reverse_iterator iter4 = dec4.rend();
```


### 덱의 삽입

```c++
deque<int>::iterator iter5, iter6, iter7, iter8;
// iterator 위치에 원소의 복사본(2)를 삽입 후 그 위치의 iterator 반환
iter5 = dec2.insert(dec2.begin(), 2);
// iterator 위치에 원소의 복사본(3)을 n(2)개 삽입 후 그 위치의 iterator 반환
iter6 = dec2.insert(iter5, 2, 3);
// iterator 위치에 begin(dec.begin())~end(dec2.end()) 범위의 모든 원소들을 복사하여 삽입 후 그 위치의 iterator 반환
iter7 = dec4.insert(dec4.begin(), dec2.begin(), dec2.end());
// iterator 위치에 initializer_list를 삽입 후 그 위치의 iterator 반환
iter8 = dec2.insert(iter6, { 10, 9 });
// 벡터 마지막에 원소를 복사해 추가한다. 반환값 X (void)
dec2.push_back(5);
// 벡터 처음에 원소를 복사해 추가한다. 반환값 X (void)
dec2.push_front(5);
// 벡터 마지막 원소를 삭제한다. 반환값 X (void)
dec2.pop_back();
// 벡터 첫 원소를 삭제한다. 반환값 X (void)
dec2.pop_front();
// **emplace() : 원하는 곳 뒤에 () 안에 있는 데이터를 컨테이너 안에서 생성자를 통해 객체를 생성 후 넣는다. (복사비용이 X) but 포인터를 넣을때는 의미 X
// **emplace_back()  : 맨 뒤에 넣는다.
// **emplace_front() : 맨 앞에 넣는다.
{
	deque<TItem> emDec;
	// 1. 객체를 생성자를 통해서 생성하고 생성된 객체를 복사해서 컨테이너 안에 넣는다.
	{
		TItem item(1);
		emDec.push_back(item);
		emDec.push_front(item);
	}
	// 2. emplace 로 넣는 값을 컨테이너 안에서 직접 생성자 호출해서 통해서 생성해 넣는다 (복사를 한 것이 아니라 복사비용X)
	{
		emDec.emplace_front(4);
		emDec.emplace_back(3);
	}
	// 3. 두 번 생성해서 넣는다.
	{
		TItem item(2);
		emDec.emplace_back(item);
	}
}
```


### 덱의 제거

```c++
deque<int>::iterator iter9, iter10, iter11;
// iterator 위치의 원소를 삭제하고 그 다음 원소 위치의 iterator 반환	
// => 이때 erase된 iter11는 재사용 불가 => erase를 반환받아 사용해야함.
iter11 = dec2.begin();
iter9 = dec2.erase(iter11);
// 해당 범위(dec2.begin() ~ iter9 => 마지막 위치(iter9)의 원소는 포함x)의 모든 원소를 제거 한 후 그 다음 원소 위치의 iterator 반환
++iter9;
++iter9;
iter10 = dec2.erase(dec2.begin(), iter9);
// 모든 원소들을 제거하고 빈 컨테이너로 만든다
dec2.clear();
```
