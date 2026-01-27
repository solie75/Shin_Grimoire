
# vector

- 동적 배열, 연속적인 메모리 블록 사용 (랜덤 접근이 빠름),
- 원소 추가 및 삭제 에 대해 마지막 원소 기준으로 가장 효율적.
- 중간 삽입 및 이동은 비효율적.
- 함수
	- puch_back(value)
	- pop_back()
	- size()
	- capacity()
	- reserve(size_t)
	- at(index)
	- front()
	- back()
	- clear()
	- insert(iterator, value)
	- erase(iterator)
	- data()

# list

- 이중 링크드 리스트, 앞 / 뒤 탐색 및 삭제가 효율적.
- 메모리가 비연속적 (랜덤 접근이 느림)
- 순차적 데이터 추가 / 삭제에 좋다.
- 함수
	- push_back(value)
	- push_front(value)
	- pop_back()
	- pop_front()
	- insert(iterator, value)
	- erase(iterator)

# deque

- 양 방향으로 동적 배열
- 임의 접근 가능, 양쪽 끝에서의 삽입 / 삭제가 빠르다.
- 중간 삽입/ 삭제는 비효율적
- 함수
	- push_back(value)
	- push_front(value)
	- pop_back()
	- pop_front()
	- at(index)
	- front()
	- back()
	- clear()
	- insert(iterator, value)
	- erase(iterator)

# iterator

- 선언 : 컨테이너<자료형>::iterator
- reverse_iterator : 컨테이너를 역순으로 순회 (rbegin(), rend())
```c++
 vector<int> vec = { 1, 2, 3, 4, 5, 6, 7 };
 vector<int>::reverse_iterator reIter = vec.rbegin();
 for (; reIter != vec.rend(); ++reIter)
 {
     cout << *reIter << " ";
 }
 cout << '\n';

 vector<int>::iterator iter = vec.end();
 for (; iter != vec.begin();)
 {
     --iter;
     cout << *iter << " ";
 }
```
- const_iterator : 컨테이너의 데이터 수정불가(cbegin(), cend()).
```c++
vector<int>::const_iterator constIter = vec.begin();
*constIter = 3; // 오류! constIter 는 변경 가능해야 한다.
constIter = vec.cbegin();
vector<int>::iterator normalIter = vec.cbegin(); // 오류!
```

- distance() : 
- advance() : iterator 이동
# 정렬

- sort
- partial_sort
- stable_sort
- nth_element

# 탐색

- find
- binary_search
- lower_bound, upper_bound

# 수치

- accumulate
- inner_product
- adjacent_difference

# 수정

- fill
- replace
- remove_if
- unique

# map

- 키와 값을 한쌍으로 묶어서 관리
- 키는 유일, (검색, 삽입, 삭제) 가 평균 O(log N)
- 함수
	- insert(pair)
	- erase(key)
	- find(key) : 해당하는 iterator 반환, 없으면 end() 리턴
	- operator\[key]
	- size()
# Set

- 키만 저장.
- 중복 불가능.
- 자동 정렬.
- 함수
	- insert(value)
	- erase(value)
	- find(value)
	- size()
	- lower_bound(value)
	- upper_bound(value)

# multimap

- 키 중복 허용
- 함수
	- insert(pair)
	- erase(key)
	- find(key)
	- equal_range(key) : 동일한 키의 요소 범위를 반환

```c++
multimap<int, string> mm;
mm.insert({ 1, "AA1" });
mm.insert({ 1, "AA2" });
mm.insert({ 2, "BB1" });

pair<multimap<int, string>::iterator, multimap<int, string>::iterator> range = mm.equal_range(1);
for (multimap<int, string>::iterator iter = range.first; iter != range.second; ++iter)
{
	cout << iter->second << " ";
}

// 결과
AA1 AA2
```
# multiset

- 키 중복 허용
	- insert(value)
	- eraser(value)
	- find(value)

# 레드-블랙 트리

# priority_queue

- 내부적으로 Heap 구조
- 삽입, 삭제는 O(log N), top() (가장 우선순위 높은 원소 조회) O(1)
- 가장 큰, 작은 원소를 즉시 추출하는 문제에 최적
- 우선순위의 기준. ( 최대 요소가 우선이면 Max  Heap, 최소 요소가 우선이면 Min Heap(greater<자료형>), 사용자 정의 비교 함수 (람다) 가능)

# 순열

- next_permutation : 사전 순으로 다음 순열
- prev_permutation : 사전순으로 이전 순열

# n 번째 값

- nth_element

# partial_sum

- 처음부터 각 인덱스 까지의 합을 각 인덱스에 저장.

# unique

- 이전에 정렬 필수
- 연속된 중복 원소 대신 그 이후의 값을 대체
	- 이를 반복하여 가장 큰(우선순위) 의 값을 대체하면 그 iterator 반환

# accumulate

- accumulate(v.begin(), v.end(), 0) : 범위의 원소 총합
- accumulate(v.begin(), v.end(), 1, multiplies\<int>()) : 범위의 총 곱

# lower_bound, upper_bound

# transform

- 단항 연산
```c++
vector<int> v1 = {1, 2, 3, 4, 5};
vector<int> v2(5);
transform(v1.begin(), v1.end(), v2.begin(), [](int _i){return pow(_i,2)}); // 각 요소 제곱
// v2 는 1, 4, 9, 16, 25;
```


- 이항연산

```c++
vector<int> v1 = {1, 2, 3, 4, 5}; 
vector<int> v2 = {6, 7, 8, 9, 10}; 
vector<int> v3(5); 
transform(v1.begin(), v1.end(), v2.begin(), v3.begin(), plus<int>());
// v3 는 7 9 11 13 15
```

# 최대값, 최소값

- max_element
- min_element
- minmax_element

# 개수

- count
- count_if()

# 코테 Tip

- 단일 헤더 파일
	- \#include \<bits/stdc++.h>
	- GCC 전용 헤더로 C++ 표준 라이브러리를 모두 포함 한다.
- 입출력 효율
	- ios::sync_with_stdio(false)
	- cin.tie(nullptr) , cin.tie(0)
	- 위의 두 줄의 추가로 c 표준 입력(stdio) 와 c++ 스트림을 분리하여 동작한다. -> 버퍼링 효율이 좋아진다.
- 시간 복잡도 계산
	- 입력크기와 시간 제한을 확인하여 사용할 자료구조나 알고리즘을 결정한다.
	- 예외 케이스 고려
		- 빈 입력, 동일 원소만 존재하는 경우
		- 음수, 0, 최댓값/최솟값 경계에서의 동작
		- 순영, 정렬 문제에서 이미 정렬, 혹은 역정렬 상태로 입력