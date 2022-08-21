---
layout: post
title:  "C/C++ Container : 03. List"
summary: "컨테이너 리스트 정리"
author: Eussy
date: '2022-08-01 19:00:00 +0530'
category: '01_C/C++'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: list, container, c, c++, stl
usemathjax: true
permalink: /blog/01_C_C++/Container_03_list
---

## 리스트(list)


### 리스트(list)란?
리스트는 LinkedList라고 알려진 연결리스트 컨테이너이며 vector와 달리 직접적인 인덱싱을 지원하지 않는 것이 가장 큰 특징이다.
또한 연속적인 연결이 아니기 때문에 삽입, 삭제가 자유롭다.


### 리스트의 필요 선언
---
```c++
#include <list>
using namespace std;
```
---


### 리스트의 선언 방식과 생성, 소멸

```c++
// 원소 없이 빈 덱 생성
list<int> list1;
// list1를 복사하여 생성
list<int> list2(list1);
// 20개의 원소와 함께 생성 : size = 20
list<int> list3(20);
// 20개의 원소를 1로 초기화하며 생성
list<int> list4(20, 1);
// 해당 범위의 원소로 list를 초기화하여 생성
list<int> list5(list4.begin(), list4.end());
// 모든 원소 파괴 후 메모리 해제
list1.~list<int>();
```


### 리스트의 메모리, 크기와 관련된 함수들

```c++
// 덱의 크기
list4.size();
// 덱가 비어있는지 확인
list4.empty();
// 가질 수 있는 최대 원소의 개수 : 진짜 메모리에서 계속해서 가질 수 있는 어마어마하게 큰 수이다. capacity와 다름
list4.max_size();		// 4654132154
// resize : 원소의 개수를 num개로 변경하고 size()가 증가하면 디폴트 생성자에 의해 생성되고 새로운 원소는 복사를 통해 초기화할 수도 있다.
list4.resize(40);
list4.resize(50, 2);
```


### 리스트의 할당 방법

```c++
// list3의 모든 원소들을 list2에 할당 (list3가 원소가 없다면 모든 원소 사라짐)
list2 = list3;
// 현재 원소들을 모두 없애고 10개의 원소(3)을 할당한다.
list2.assign(10, 3);
// 현재 원소들을 모두 없애고 list4.begin() ~ list4.end() 범위의 원소들을 할당한다.
list2.assign(list4.begin(), list4.end());
// 현재 원소들을 모두 없애고 initializer_list class를 이용한 assign을 수행한다.
list2.assign({ 1, 2, 3, 4, 5 });
// list2와 list3의 데이터를 교체한다.
list2.swap(list3);
swap(list2, list3);
```


### 리스트에서 지원하는 비교 연산자

```c++
bool right = false;
right = list4 == list5;	// false	: capacity는 달라도 상관 X, size는 상관 O
right = list4 != list5;	// true
right = list4 < list5;	// false
right = list4 > list5;	// true
```


### 리스트의 인덱싱

```c++
// 첫 번째 원소를 반환 (벡터에 아무 원소도 없으면 assert 발생)
list2.front();
// 마지막 원소를 반환 (벡터에 아무 원소도 없으면 assert 발생)
list2.back();
```


### 리스트의 반복자(iterator, reverse_iterator)

```c++
// 첫번째 원소를 가리키는 iterator를 반환한다.
list<int>::iterator iter1 = list4.begin();
// 마지막 원소의 주소 + 1 가리키는 iterator를 반환한다.
list<int>::iterator iter2 = list4.end();
// 마지막 원소를 가리키는 reverse_iterator를 반환한다.
list<int>::reverse_iterator iter3 = list4.rbegin();
// 첫번째 원소의 주소 - 1 가리키는 reverse_iterator를 반환한다.
list<int>::reverse_iterator iter4 = list4.rend();
```


### 리스트의 삽입

```c++
list<int>::iterator iter5, iter6, iter7, iter8;
// iterator 위치에 원소의 복사본(2)를 삽입 후 그 위치의 iterator 반환
iter5 = list2.insert(list2.begin(), 2);
// iterator 위치에 원소의 복사본(3)을 n(2)개 삽입 후 그 위치의 iterator 반환
iter6 = list2.insert(iter5, 2, 3);
// iterator 위치에 begin(list1.begin())~end(list2.end()) 범위의 모든 원소들을 복사하여 삽입 후 그 위치의 iterator 반환
iter7 = list4.insert(list4.begin(), list2.begin(), list2.end());
// iterator 위치에 initializer_list를 삽입 후 그 위치의 iterator 반환
iter8 = list2.insert(iter6, { 10, 9 });
// 벡터 마지막에 원소를 복사해 추가한다. 반환값 X (void)
list2.push_back(5);
// 벡터 처음에 원소를 복사해 추가한다. 반환값 X (void)
list2.push_front(5);
// 벡터 마지막 원소를 삭제한다. 반환값 X (void)
list2.pop_back();
// 벡터 첫 원소를 삭제한다. 반환값 X (void)
list2.pop_front();
// **emplace() : 원하는 곳 뒤에 () 안에 있는 데이터를 컨테이너 안에서 생성자를 통해 객체를 생성 후 넣는다. (복사비용이 X) but 포인터를 넣을때는 의미 X
// **emplace_back()  : 맨 뒤에 넣는다.
// **emplace_front() : 맨 앞에 넣는다.
{
	list<TItem> emList;
	// 1. 객체를 생성자를 통해서 생성하고 생성된 객체를 복사해서 컨테이너 안에 넣는다.
	{
		TItem item(1);
		emList.push_back(item);
		emList.push_front(item);
	}
	// 2. emplace 로 넣는 값을 컨테이너 안에서 직접 생성자 호출해서 통해서 생성해 넣는다 (복사를 한 것이 아니라 복사비용X)
	{
		emList.emplace_front(4);
		emList.emplace_back(3);
	}
	// 3. 두 번 생성해서 넣는다.
	{
		TItem item(2);
		emList.emplace_back(item);
	}
}
```


### 리스트의 제거

```c++
// 값이 val인 모든 원소를 제거한다.
list2.remove(1);
// 함수가 true를 반환하는 모든 원소를 제거한다.
list2.remove_if([](const int& val)-> bool {if (val == 2) return true; else return false; });
list<int>::iterator iter9, iter10, iter11;
// iterator 위치의 원소를 삭제하고 그 다음 원소 위치의 iterator 반환	
// => 이때 erase된 iter11는 재사용 불가 => erase를 반환받아 사용해야함.
iter11 = list2.begin();
iter9 = list2.erase(iter11);
// 해당 범위(list2.begin() ~ iter9 => 마지막 위치(iter9)의 원소는 포함x)의 모든 원소를 제거 한 후 그 다음 원소 위치의 iterator 반환
++iter9;
++iter9;
iter10 = list2.erase(list2.begin(), iter9);
// 모든 원소들을 제거하고 빈 컨테이너로 만든다
list2.clear();
```

### 리스트에서 지원하는 알고리즘

```c++
// 같은 값을 가지는 연속된 원소들의 중복을 제거한다.
list5.unique();
// 함수가 true를 반환하는 연속된 원소들의 중복을 제거한다.
list4.unique([](const int& prev, const int& current) -> bool {if (prev == current) return true; else return false; });

// list4의 모든 원소를 list5의 iterator(list5.begin()) 위치 앞으로 이동시킨다.
list5.splice(list5.begin(), list4);
// list5의 iterator(list5.begin())에 위치한 원소를 list4의 iterator(list4.begin()) 위치 앞으로 이동시킨다.
list4.splice(list4.begin(), list5, list5.begin());
list<int>::iterator iter12 = list5.begin();
iter12++;
iter12++;
// list5의 범위(list5.begin() ~ iter11 -> 마지막 iter11 위치의 원소 포함 x)만큼의 원소들을 list4의 iterator(list4.begin()) 위치 앞으로 이동
list4.splice(list4.begin(), list5, list5.begin(), iter12);

// 오름차순 정렬한다.
list4.sort();
// 함수 기준으로 정렬한다. (현재 오름차순)
//list4.sort(descending);
list5.sort(ascending);
// 두 컨테이너 모두 오름차순 정렬되어 있다는 가정하에(정렬되어 있지 않다면 assert 발생) list5의 모든 원소들을 list4로 이동한다. 
// 모든 원소들은 정렬기준에 어긋나지 않게 정렬되어 있다.
list4.merge(list5);
// 두 컨테이너 모두 함수기준으로 정렬되어 있다는 가정하에(정렬되어 있지 않다면 assert 발생) list5의 모든 원소들을 list4로 이동한다. 
// 모든 원소들은 정렬기준에 어긋나지 않게 정렬되어 있다. 
list4.merge(list5, ascending);
// 모든 원소들의 순서를 반대로 뒤바꾼다
list4.reverse();
```
