---
layout: post
title:  "C/C++ Container : 06. Unordered_Map"
summary: "컨테이너 해쉬맵 정리"
author: Eussy
date: '2022-08-01 21:00:00 +0530'
category: '01_C/C++'
tags: Eussy
thumbnail: /assets/img/posts/code.jpg
keywords: unordered_map, container, c, c++, stl
usemathjax: true
permalink: /blog/01_C_C++/Container_06_unordered_map
---

## 해쉬맵(unordered_map)


### 해쉬맵(unordered_map)란?
해쉬맵은 맵에서 키(key) 관리를 해쉬(hash) 방식으로 진행한다.
해쉬맵 또한 멀티해쉬맵을 허용한다.


### 해쉬맵의 필요 선언
---
```c++
#include <unordered_map>
```
---


### 해쉬맵의 선언 방식과 생성, 소멸

```c++
// <Key, Value> 원소 없이 빈 set 생성, 디폴트로 해쉬 방식
unordered_map<int, string> uord_map1;
unordered_multimap<int, string> unord_mult1;
// <Key, Value> 원소 없이 빈 set 생성 (****해쉬를 바꿀 수 있는지는 연구 필요)
unordered_map<int, string> uord_map2;
unordered_multimap<int, string> unord_mult2;
// 같은 타입으로 생성된 uord_map1을 복사하여 set 생성
unordered_map<int, string> uord_map3(uord_map1);
unordered_multimap<int, string> unord_mult3(unord_mult1);
// iterator 범위로 초기화하여 unordered_map 생성
unordered_map<int, string> uord_map4(uord_map1.begin(), uord_map1.end());
unordered_multimap<int, string> unord_mult4(unord_mult1.begin(), unord_mult1.end());
```


### 해쉬맵의 메모리, 크기와 관련된 함수들

```c++
// 크기
uord_map3.size();
unord_mult3.size();
// 비어있는지 확인
uord_map3.empty();
unord_mult3.empty();
// 가질 수 있는 최대 원소의 개수 : 진짜 메모리에서 계속해서 가질 수 있는 어마어마하게 큰 수이다.
uord_map3.max_size();		// 엄청나게 큰 값
unord_mult3.max_size();	// 엄청나게 큰 값
```


### 해쉬맵의 할당 방법

```c++
// set3의 모든 원소들을 uord_map2에 할당 (set3가 원소가 없다면 모든 원소 사라짐)
uord_map1 = uord_map3;
unord_mult1 = unord_mult3;
// uord_map2와 set3의 데이터를 교체한다.
uord_map2.swap(uord_map3);
unord_mult2.swap(unord_mult3);
```


### 해쉬맵에서 지원하는 비교 연산자

```c++
// > < 와 같은 연산자 존재 X
bool right = false;
right = uord_map2 == uord_map3;	// false	: capacity는 달라도 상관 X, size는 상관 O
right = uord_map2 != uord_map3;	// true
right = unord_mult2 == unord_mult3;	// false	: capacity는 달라도 상관 X, size는 상관 O
right = unord_mult2 != unord_mult3;	// true
```


### 해쉬맵의 인덱싱
```c++
// key로 key를 가진 원소의 값을 레퍼런스로 반환(string)한다. 
// 만약 key가 존재하지 않는다면 value가 기본 값과 함께 삽입된다.
// 이런식으로 값을 넣는 것은 사용하지 말도록, why? 키가 중복된다면 덮어써버린다!
uord_map2[2] = "1입니다.";
// key로 동일한 key를 가지는 원소의 개수를 반환한다.
count = uord_map2.count(2);
count = unord_mult2.count(2);
```


### 해쉬맵의 반복자(iterator, reverse_iterator)

```c++
// rbegin(), rend()와 같은 reverse_iterator가 존재 X
unordered_map<int, string>::iterator iter1, iter2;
// 첫번째 원소를 가리키는 iterator를 반환한다.
iter1 = uord_map1.begin();
// 마지막 원소의 주소 + 1 가리키는 iterator를 반환한다.
iter2 = uord_map1.end();
unordered_multimap<int, string>::iterator iter5, iter6;
// 첫번째 원소를 가리키는 iterator를 반환한다.
iter5 = unord_mult1.begin();
// 마지막 원소의 주소 + 1을 가리키는 iterator를 반환한다.
iter6 = unord_mult1.end();
```


### 해쉬맵의 삽입

```c++
unordered_map<int, string>::iterator iter9, iter10;
// 원소를 만들어 넣는 방법은 {}과 make_pair이 있다.
// 삽입한 위치 iterator와 삽입 여부를 알려주는 pair 반환	(pair.first, pair.second로 접근)
pair<unordered_map<int, string>::iterator, bool> suc = uord_map2.insert({ 1, "1입니다" });
pair<unordered_map<int, string>::iterator, bool> suc3 = uord_map2.insert(make_pair(1, "1입니다"));
iter9 = suc.first;
right = suc.second;
// 원소의 복사본(2, "2입니다.")를 삽입 후 그 위치의 iterator 반환 iterator는 위치부터 키를 검색할 용도로 사용한다.
iter10 = uord_map2.insert(uord_map2.begin(), { 2, "2입니다" });
// iterator 위치에 begin(uord_map1.begin())~end(uord_map2.end()) 범위의 모든 원소들을 복사하여 삽입, 반환 값은 x
uord_map3.insert(uord_map2.begin(), uord_map2.end());
// initializer_list를 삽입, 반환 값은 x
uord_map2.insert({ {4, "4입니다."}, {7, "7입니다."}, {8, "8입니다."}, {1, "1입니다."}, {2, "2입니다."} });

unordered_multimap<int, string>::iterator iter11, iter12;
// 원소를 만들어 넣는 방법은 {}과 make_pair이 있다.
// 삽입한 위치의 iterator 반환	(unordered_multimap은 pair로 반환 X)
iter11 = unord_mult2.insert({ 1, "1입니다" });
iter11 = unord_mult2.insert(make_pair(1, "1입니다"));
// iterator 위치에 원소의 복사본(2, "2입니다")를 삽입 후 그 위치의 iterator 반환
iter12 = unord_mult2.insert(unord_mult2.begin(), { 2, "2입니다" });
// iterator 위치에 begin(uord_map1.begin())~end(uord_map2.end()) 범위의 모든 원소들을 복사하여 삽입, 반환 값은 x
unord_mult3.insert(unord_mult2.begin(), unord_mult2.end());
// initializer_list를 삽입, 반환 값은 x
unord_mult2.insert({ {4, "4입니다."}, {7, "7입니다."}, {8, "8입니다."}, {1, "1입니다."}, {2, "2입니다."} });

// **emplace() : () 안에 있는 데이터를 컨테이너 안에서 생성자를 통해 객체를 생성 후 넣는다. (복사비용이 X) but 포인터를 넣을때는 의미 X
// **emplace_hint() : emplace에서 넣을 iterator의 위치 힌트를 준다. (연구 필요)

unordered_map<int, TItem> emMap;
unordered_multimap<int, TItem> emMultiMap;

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


### 해쉬맵의 제거

```c++
unordered_map<int, string>::iterator iter13, iter14;

// key값에 해당하는 원소를 삭제하고 제거된 원소의 개수를 반환한다.
int eraseCnt = uord_map2.erase(1);
// 해시 테이블을 버킷 갯수만큼 다시 만들어 빌드
uord_map2.rehash(2);
// 세트의 모든 원소를 제거하고 빈 컨테이너로 만든다.
uord_map2.clear();

unordered_multimap<int, string>::iterator iter15, iter16;
// key에 해당하는 원소를 삭제하고 제거된 원소의 개수를 반환한다.
eraseCnt = unord_mult2.erase(2);
// 해시 테이블을 버킷 갯수만큼 다시 만들어 빌드ㄴ
unord_mult2.rehash(2);
// 세트의 모든 원소를 제거하고 빈 컨테이너로 만든다.
unord_mult2.clear();
```

### 해쉬맵에서 값 찾기

```c++
// c++20 에서는 contains()가 생김 있으면 true, 없으면 false
//bool isContain = uord_map3.contains(2);
// key가 존재하면 그 위치의 iterator 반환, 없으면 end()
unordered_map<int, string>::iterator iter17 = uord_map3.find(4);
unordered_multimap<int, string>::iterator iter18 = unord_mult3.find(4);
// key(4)값에 해당하는 버킷 갯수 (반환 : int)
count = uord_map3.bucket(4);
count = unord_mult3.bucket(4);
// 현재 존재하는 전체 버킷 수 (반환 : int)
count = uord_map3.bucket_count();
count = unord_mult3.bucket_count();
// 버킷의 크기? (반환 : int)
count = uord_map3.bucket_count();
count = unord_mult3.bucket_count();
```
