- 순서를 유지하면서 중복을 허용하지 않는 고유한 값들의 컨테이너.
- set 에 추가되는 모든 요소는 자동으로 정렬된다.
- 중복 삽입시 무시된다.
- 내부적으로 이진 검색 트리로 구현되어 있어 삽입, 삭제, 탐색의 평균 시간 복잡도가 $O(log(n))$ 이다.
- 모든 요소는 읽기 전용으로 요소의 값 자체의 변경을 허용하지 않는다. 값을 변경하려면 요소를 삭제한 후 새 값을 삽입해야 한다.

```c++
set<int> s;
s.insert(5);
s.insert(2);
s.insert(9);

for (int x : s)
{
	cout << x << "\n";
}

if (s.find(2) != s.end())
{
	cout << "2 found" << "\n";
}

s.erase(2);

cout << "Size of set" << s.size() << "\n";

for (int x : s)
{
	cout << x << "\n";
}
```

```c++
2
5
9
2 found
Size of set2
5
9
```

- insert() : 요소 삽입, 중복 요소는 삽임 x
- erase() : 특정 값을 삭제
- find() : 특정 값을 탐색. 찾으면 해당 값의 반복자 반환하고 못찾으면 end() 를 반환.
- size() : 요소 개수 반환
- clear() : 모든 요소 삭제
- count() : 특정 값의 개수 반환 -> set 에서는 값이 하나 뿐이므로 0 또는 1을 반환.
- lower_bound(value) : 지정된 값 이상의 첫 번 째 요소를 반환
- upper_bound(value) : 지정된 값보다 큰 첫 번째 요소를 반환


# Multiset

동일 값(키) 중복을 허용한다.

```c++
multiset<int> MS;

MS.insert(10);
MS.insert(20);
MS.insert(10);
MS.insert(30);
MS.insert(10);

// MS 는 순서대로 10, 10, 10, 20, 30
int Temp = MS.count(10); // Temp 는 3
MS.erase(MS.find(10));
// MS 는 순서대로 10, 10, 20, 30
```

- insert
- count
- find
- lower_bound(), upper_bound()