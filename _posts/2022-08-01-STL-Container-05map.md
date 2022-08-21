---
layout: post
title:  "C/C++ Container : 05. Map"
summary: "컨테이너 맵 정리"
author: Eussy
date: '2022-08-01 20:30:00 +0530'
category: '01_C/C++'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: map, container, c, c++, stl
usemathjax: true
permalink: /blog/01_C_C++/Container_05_map
---

## 맵(map)


### 맵(map)란?
맵은 키(key)와 값(value) 두 가지 자료를 가지고 있는 컨테이너로 키(key)를 중심으로 자동으로 정렬해준다. 
또한 키의 중복을 허용하지 않는다. 이미 있는 키의 자료를 삽입하게되면 그 값은 사라진다.
특징으로는 오름차순 정렬이 디폴트 값으로 설정되어있고 pair<키, 값> 형태로 값을 반환해준다.


### 맵의 필요 선언
---
```c++
#include <map>
```
---


### 맵의 선언 방식과 생성, 소멸

```c++
// <Key, Value> 원소 없이 빈 set 생성, 디폴트로 오름차순 정렬
map<int, string> map1;
multimap<int, string> multimap1;
//// <Key, Value> 원소 없이 빈 set 생성, 함수 기준(내림차순)으로 정렬	(less안의 타입을 넣냐 안넣냐에 따라서 map1, multimap1과의 타입이 같으냐 안같으냐가 결정)
map<int, string, less<int>> map2;
multimap<int, string, less<int>> multimap2;
//// 같은 타입으로 생성된 map1을 복사하여 set 생성
map<int, string> map3(map1);
multimap<int, string> multimap3(multimap1);
//// iterator 범위로 초기화하여 map 생성
map<int, std::string> map4(map1.begin(), map1.end());
multimap<int, string> multimap4(multimap1.begin(), multimap1.end());
```


### 맵의 메모리, 크기와 관련된 함수들

```c++
// 덱의 크기
map3.size();
multimap3.size();
// 덱가 비어있는지 확인
map3.empty();
multimap3.empty();
// 가질 수 있는 최대 원소의 개수 : 진짜 메모리에서 계속해서 가질 수 있는 어마어마하게 큰 수이다.
map3.max_size();		// 엄청나게 큰 값
multimap3.max_size();	// 엄청나게 큰 값
```


### 맵의 할당 방법

```c++
// set3의 모든 원소들을 map2에 할당 (set3가 원소가 없다면 모든 원소 사라짐)
map1 = map3;
multimap1 = multimap3;
// map2와 set3의 데이터를 교체한다.
map2.swap(map3);
multimap2.swap(multimap3);
```


### 맵에서 지원하는 비교 연산자

```c++
bool right = false;
right = map2 == map3;	// false	: capacity는 달라도 상관 X, size는 상관 O
right = map2 != map3;	// true
right = map2 < map3;	// false
right = map2 > map3;	// true
right = multimap2 == multimap3;	// false	: capacity는 달라도 상관 X, size는 상관 O
right = multimap2 != multimap3;	// true
right = multimap2 < multimap3;	// false
right = multimap2 > multimap3;	// true
```


### 맵의 인덱싱
```c++
// key로 key를 가진 원소의 값을 레퍼런스로 반환(string)한다. 
// 만약 key가 존재하지 않는다면 value가 기본 값과 함께 삽입된다.
// 이런식으로 값을 넣는 것은 사용하지 말도록, why? 키가 중복된다면 덮어써버린다!
map2[2] = "1입니다.";
// key로 동일한 key를 가지는 원소의 개수를 반환한다.
int count = multimap2.count(2);
```


### 맵의 반복자(iterator, reverse_iterator)

```c++
map<int, string>::iterator iter1, iter2;
map<int, string>::reverse_iterator iter3, iter4;
// 첫번째 원소를 가리키는 iterator를 반환한다.
iter1 = map1.begin();
// Key 값
iter1->first;
// Value 값
iter2->second;
// 마지막 원소의 주소 + 1 가리키는 iterator를 반환한다.
iter2 = map1.end();
// 마지막 원소를 가리키는 reverse_iterator를 반환한다.
iter3 = map1.rbegin();
// 첫번째 원소의 주소 - 1 가리키는 reverse_iterator를 반환한다.
iter4 = map1.rend();
multimap<int, string>::iterator iter5, iter6;
multimap<int, string>::reverse_iterator iter7, iter8;
// 첫번째 원소를 가리키는 iterator를 반환한다.
iter5 = multimap1.begin();
// 마지막 원소의 주소 + 1을 가리키는 iterator를 반환한다.
iter6 = multimap1.end();
// 마지막 원소를 가리키는 위치의 reverse_iterator를 반환한다.
iter7 = multimap1.rbegin();
// 첫번째 원소의 주소 - 1을 가리키는 위치의 reverse_iterator를 반환한다.
iter8 = multimap1.rend();
```


### 맵의 삽입

```c++
map<int, string>::iterator iter9, iter10;
// 원소를 만들어 넣는 방법은 {}과 make_pair이 있다.
// 삽입한 위치 iterator와 삽입 여부를 알려주는 pair 반환	(pair.first, pair.second로 접근)
pair<map<int, string>::iterator, bool> suc = map2.insert({ 1, "1입니다" });
pair<map<int, string>::iterator, bool> suc3 = map2.insert(make_pair(1, "1입니다"));
iter9 = suc.first;
right = suc.second;
// 원소의 복사본(2, "2입니다.")를 삽입 후 그 위치의 iterator 반환 iterator는 위치부터 키를 검색할 용도로 사용한다.
iter10 = map2.insert(map2.begin(), { 2, "2입니다" });
// iterator 위치에 begin(map1.begin())~end(map2.end()) 범위의 모든 원소들을 복사하여 삽입, 반환 값은 x
map3.insert(map2.begin(), map2.end());
// initializer_list를 삽입, 반환 값은 x
map2.insert({ {4, "4입니다."}, {7, "7입니다."}, {8, "8입니다."}, {1, "1입니다."}, {2, "2입니다."} });

multimap<int, string>::iterator iter11, iter12;
// 원소를 만들어 넣는 방법은 {}과 make_pair이 있다.
// 삽입한 위치의 iterator 반환	(multimap은 pair로 반환 X)
iter11 = multimap2.insert({ 1, "1입니다" });
iter11 = multimap2.insert(make_pair(1, "1입니다"));
// iterator 위치에 원소의 복사본(2, "2입니다")를 삽입 후 그 위치의 iterator 반환
iter12 = multimap2.insert(multimap2.begin(), { 2, "2입니다" });
// iterator 위치에 begin(map1.begin())~end(map2.end()) 범위의 모든 원소들을 복사하여 삽입, 반환 값은 x
multimap3.insert(multimap2.begin(), multimap2.end());
// initializer_list를 삽입, 반환 값은 x
multimap2.insert({ {4, "4입니다."}, {7, "7입니다."}, {8, "8입니다."}, {1, "1입니다."}, {2, "2입니다."} });

// **emplace() : () 안에 있는 데이터를 컨테이너 안에서 생성자를 통해 객체를 생성 후 넣는다. (복사비용이 X) but 포인터를 넣을때는 의미 X
// **emplace_hint() : emplace에서 넣을 iterator의 위치 힌트를 준다. (연구 필요)

map<int, TItem> emMap;
multimap<int, TItem> emMultiMap;

// 1. 객체를 생성자를 통해서 생성하고 생성된 객체를 복사해서 컨테이너 안에 넣는다.
TItem item(1);
emMap.insert({ 1, item });
emMultiMap.insert({ 2, item });

// 2. emplace 로 넣는 값을 컨테이너 안에서 직접 생성자 호출해서 통해서 생성해 넣는다 (복사를 한 것이 아니라 복사비용X)
TItem item2(2);
emMap.emplace(3, 3);
emMultiMap.emplace(3, 3);

// 3. 두 번 생성해서 넣는다. 
TItem item3(2);
emMap.emplace(2, item);
emMultiMap.emplace(2, item);
```


### 맵의 제거

```c++
map<int, string>::iterator iter13, iter14;
// iterator 위치의 원소를 삭제하고 그 다음 원소 위치의 iterator 반환
iter13 = map2.erase(map2.begin());
// 해당 범위(map2.begin() ~ iter9 => 마지막 위치(iter13)의 원소는 포함x)의 모든 원소를 제거 한 후 그 다음 원소 위치의 iterator 반환
++iter13;
++iter13;
iter14 = map2.erase(map2.begin(), iter13);
// value에 해당하는 원소를 삭제하고 제거된 원소의 개수를 반환한다.
int eraseCnt = map2.erase(2);
// 세트의 모든 원소를 제거하고 빈 컨테이너로 만든다.
map2.clear();

multimap<int, string>::iterator iter15, iter16;
// iterator 위치의 원소를 삭제하고 그 다음 원소 위치의 iterator 반환
iter15 = multimap2.erase(multimap2.begin());
// 해당 범위(map2.begin() ~ iter9 => 마지막 위치(iter9)의 원소는 포함x)의 모든 원소를 제거 한 후 그 다음 원소 위치의 iterator 반환
++iter15;
++iter15;
iter16 = multimap2.erase(multimap2.begin(), iter15);
// value에 해당하는 원소를 삭제하고 제거된 원소의 개수를 반환한다.
eraseCnt = multimap2.erase(2);
// 세트의 모든 원소를 제거하고 빈 컨테이너로 만든다.
multimap2.clear();
```

### 맵에서 값 찾기

```c++
// c++20 에서는 contains()가 생김 있으면 true, 없으면 false
bool isContain = map3.contains(2);
// key가 존재하면 그 위치의 iterator 반환, 없으면 end()
map<int, string>::iterator iter17 = map3.find(4);
multimap<int, string>::iterator iter18 = multimap3.find(4);
```
