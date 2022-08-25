---
layout: post
title:  "C/C++ Container : 04. Set"
summary: "컨테이너 세트 정리"
author: Eussy
date: '2022-08-01 20:00:00 +0530'
category: '01_C/C++'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: set, container, c, c++, stl
usemathjax: true
permalink: /blog/01_C_C++/Container_04_set
---

## 세트(set)


### 세트(set)란?
세트는 값의 중복을 허용하지 않는 컨테이너로, 자동으로 정렬을 해준다는 특징이 있다. 디폴트로 오름차순 정렬이 설정되어 있고, 이는 변경이 가능하다.
중복을 허용하는 멀티세트(multiset) 또한 존재한다.


### 세트의 필요 선언
---
```c++
#include <set>
```
---


### 세트의 선언 방식과 생성, 소멸

```c++
// 원소 없이 빈 세트 생성, 디폴트로 오름차순 정렬
set<int> set1;
multiset<int> multiset1;
// 원소 없이 빈 세트 생성, 함수 기준(내림차순)으로 정렬	(less안의 타입을 넣냐 안넣냐에 따라서 set1, multiset1과의 타입이 같으냐 안같으냐가 결정)
set<int, less<int>> set2;
multiset<int, less<int>> multiset2;
// 같은 타입으로 생성된 set1을 복사하여 세트 생성
set<int> set4(set1);
multiset<int> multiset4(multiset1);
// iterator 범위로 초기화하여 세트 생성
set<int> set5(set1.begin(), set1.end());
multiset<int> multiset5(multiset1.begin(), multiset1.end());
// 해제자
set5.~set();
multiset5.~multiset();
```


### 세트의 메모리, 크기와 관련된 함수들

```c++
// 덱의 크기
set4.size();
multiset4.size();
// 덱가 비어있는지 확인
set4.empty();
multiset4.empty();
// 가질 수 있는 최대 원소의 개수 : 진짜 메모리에서 계속해서 가질 수 있는 어마어마하게 큰 수이다. capacity와 다름
set4.max_size();		// 4654132154
multiset4.max_size();
```


### 세트의 할당 방법

```c++
// set3의 모든 원소들을 set2에 할당 (set3가 원소가 없다면 모든 원소 사라짐)
set1 = set4;
multiset1 = multiset4;
// set2와 set3의 데이터를 교체한다.
set2.swap(set4);
multiset2.swap(multiset4);
```


### 세트에서 지원하는 비교 연산자

```c++
bool right = false;
right = set2 == set4;	// false	: capacity는 달라도 상관 X, size는 상관 O
right = set2 != set4;	// true
right = set2 < set4;	// false
right = set2 > set4;	// true
right = multiset2 == multiset4;	// false	: capacity는 달라도 상관 X, size는 상관 O
right = multiset2 != multiset4;	// true
right = multiset2 < multiset4;	// false
right = multiset2 > multiset4;	// true
```


### 세트의 인덱싱

세트는 인덱싱이 존재하지 않는다.


### 세트의 반복자(iterator, reverse_iterator)

```c++
set<int>::iterator iter1, iter2;
set<int>::reverse_iterator iter3, iter4;
// 첫번째 원소를 가리키는 iterator를 반환한다.
iter1 = set1.begin();
// 마지막 원소의 주소 + 1 가리키는 iterator를 반환한다.
iter2 = set1.end();
// 마지막 원소를 가리키는 reverse_iterator를 반환한다.
iter3 = set1.rbegin();
// 첫번째 원소의 주소 - 1 가리키는 reverse_iterator를 반환한다.
iter4 = set1.rend();
multiset<int>::iterator iter5, iter6;
multiset<int>::reverse_iterator iter7, iter8;
// 첫번째 원소를 가리키는 iterator를 반환한다.
iter5 = multiset1.begin();
// 마지막 원소의 주소 + 1을 가리키는 iterator를 반환한다.
iter6 = multiset1.end();
// 마지막 원소를 가리키는 reverse_iterator를 반환한다.
iter7 = multiset1.rbegin();
// 첫번째 원소의 주소 - 1을 가리키는 reverse_iterator를 반환한다.
iter8 = multiset1.rend();
```


### 세트의 삽입

```c++
set<int>::iterator iter9, iter10;
// 삽입한 위치 iterator와 삽입 여부를 알려주는 pair 반환	(pair.first, pair.second로 접근)
pair<set<int>::iterator, bool> suc = set2.insert(1);
iter9 = suc.first;
right = suc.second;
// 복사본(2)를 삽입 후 그 위치의 iterator 반환 iterator는 위치부터 키를 검색할 용도로 사용한다.
iter10 = set2.insert(set2.begin(), 2);
// iterator 위치에 begin(set1.begin())~end(set2.end()) 범위의 모든 원소들을 복사하여 삽입, 반환 값은 x
set4.insert(set2.begin(), set2.end());
// initializer_list를 삽입, 반환 값은 x
set2.insert({ 4, 7, 8, 1, 2 });

multiset<int>::iterator iter11, iter12;
// 삽입한 위치의 iterator 반환
iter11 = multiset2.insert(1);
// iterator 위치에 원소의 복사본(2)를 삽입 후 그 위치의 iterator 반환
iter12 = multiset2.insert(multiset2.begin(), 2);
// iterator 위치에 begin(set1.begin())~end(set2.end()) 범위의 모든 원소들을 복사하여 삽입, 반환 값은 x
multiset4.insert(multiset2.begin(), multiset2.end());
// initializer_list를 삽입, 반환 값은 x
multiset2.insert({ 4, 7, 8, 1, 2 });
// **emplace() : () 안에 있는 데이터를 컨테이너 안에서 생성자를 통해 객체를 생성 후 넣는다. (복사비용이 X) but 포인터를 넣을때는 의미 X
// **emplace_hint() : emplace에서 넣을 iterator의 위치 힌트를 준다. (연구 필요)
{
	set<TItem> emSet;
	multiset<TItem> emMultiSet;
	// 1. 객체를 생성자를 통해서 생성하고 생성된 객체를 복사해서 컨테이너 안에 넣는다.
	{
		TItem item(1);
		emSet.insert(item);
		emMultiSet.insert(item);
	}
	// 2. emplace 로 넣는 값을 컨테이너 안에서 직접 생성자 호출해서 통해서 생성해 넣는다 (복사를 한 것이 아니라 복사비용X)
	{
		emSet.emplace(3);
		emMultiSet.emplace(3);
	}
	// 3. 두 번 생성해서 넣는다. 
	{
		TItem item(2);
		emSet.emplace(item);
		emMultiSet.emplace(item);
	}
}
```


### 세트의 제거

```c++
set<int>::iterator iter13, iter14;
// iterator 위치의 원소를 삭제하고 그 다음 원소 위치의 iterator 반환
iter13 = set2.erase(set2.begin());
// 해당 범위(set2.begin() ~ iter9 => 마지막 위치(iter13)의 원소는 포함x)의 모든 원소를 제거 한 후 그 다음 원소 위치의 iterator 반환
++iter13;
++iter13;
iter14 = set2.erase(set2.begin(), iter13);
// value에 해당하는 원소를 삭제하고 제거된 원소의 개수를 반환한다.
int eraseCnt = set2.erase(2);
// 세트의 모든 원소를 제거하고 빈 컨테이너로 만든다.
set2.clear();
multiset<int>::iterator iter15, iter16;
// iterator 위치의 원소를 삭제하고 그 다음 원소 위치의 iterator 반환
iter15 = multiset2.erase(multiset2.begin());
// 해당 범위(set2.begin() ~ iter9 => 마지막 위치(iter9)의 원소는 포함x)의 모든 원소를 제거 한 후 그 다음 원소 위치의 iterator 반환
++iter15;
++iter15;
iter16 = multiset2.erase(multiset2.begin(), iter15);
// value에 해당하는 원소를 삭제하고 제거된 원소의 개수를 반환한다.
eraseCnt = multiset2.erase(2);
// 세트의 모든 원소를 제거하고 빈 컨테이너로 만든다.
multiset2.clear();
```

### 세트에서 값 찾기

```c++
// c++20 에서는 contains()가 생김 있으면 true, 없으면 false
bool isContain = set4.contains(2);
// key가 존재하면 그 위치의 iterator 반환, 없으면 end()
set<int>::iterator iter17 = set4.find(4);
multiset<int>::iterator iter18 = multiset4.find(4);
```
