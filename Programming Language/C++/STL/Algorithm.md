# min_element

- 주어진 범위에서 가장 작은 값의 iterator를  반환한다.
- 범위가 비어있다면 last 를 반환한다.
- 최소값이 배열에 여러개 있을 때 가장 앞의 최소값의 iterator 를 반환한다.
```c++
template<class ForwardIt> 
ForwardIt min_element(ForwardIt first, ForwardIt last);

// 사용자 정의 비교 함수
template<class ForwardIt, class Compare>
ForwardIt min_element(ForwardIt first, ForwardIt last, Compare comp);
```

# lexicographical_compare

```c++
template< class InputIt1, class InputIt2, class Compare >  
bool lexicographical_compare( InputIt1 first1, InputIt1 last1,  
                              InputIt2 first2, InputIt2 last2,  
                              Compare comp );
```

- 첫 번째 범위  \[first1, last1] 가 사전식 정렬에 대해 두 번째 범위 \[first2, last2] 보다 작은지 확인한다.
- 첫 번째 범위가 두 번째 범위에 비해 사전식 정력에 대해 작다면 true 를 그렇지 않다면 false 를 반환한다.


# sort

범위 내의 요소들을 오름차순 또는 내림차순 또는 사용자 지정 비교 기준에 따라 정렬
introsort (quick sort, heap sort, insert sort 혼합) 를 사용하여 평균 O(n log n) 시간에 동작한다.

```c++
vector<int> vec = 3, 1, 4, 1, 5};
sort(vec.begin(), vec.end()); // 오름차순 정렬
// vec == {1, 1, 3, 4, 5}
sort(vec.begin(), vec.end(), greater<int>()); // 내림차순 정렬
sort(vec.begin(), vec.end(), [](... )); // 사용자 지정 비교 기준 따라 정렬.
```

# partial_sort

지정됨 범위의 앞부분(\[first, middle) 을 정렬하고, 나머지 부분은 정렬되지 않은 상태로 둔다.

```c++
vector<int> vec{ 3, 4, 6, 2, 7, 3, 1, 5, 9};
partial_sort(vec.begin() + 1, vec.begin() + 5, vec.end());
// 이때 vec 은 {3, 1, 2, 3, 4, 7, 6, 5, 9} 이다.
```

# stable_sort

정렬 시 동일한 값의 요소들 간의 상대적 순서를 보존
sort() 와 같아 보이지만 sort 는 같은 요소들이 상대적 순서가 원래와는 다르게 바뀔 수 있지만 stable_sort는 같은 요소들의 상대적 순서가 유지된다.

# nth_element

범위 내의 요소들을 재배치하여, n 번째 위치에 올 요소가 정확히 그 자리에 오도록 한다. 이때 n 번째 요소보다 작은 값들은 그 앞쪽에, 큰 값들은 그 뒤쪽에 위치하게 된다. 순서가 완전히 정렬되지는 않기 때문에, n 번째 요소만을 찾거나 k 번째 작은 원소를 찾을 때 유용하다.

```c++
void nth_element(RandomAccessIterator first, RandomAccessIterator nth, RandomAccessIterator last);
```
- first : 배열 시작.
- nth : n 번째 위치
- last : 배열의 끝

```c++
vector<int> vec = { 9, 3, 5, 1, 4, 8, 10, 34, 44, 55, 66, 15, 73, 11};
vector<int>::iterator iter = vec.begin() + 2;
nth_element(vec.begin(), iter, vec.end());
```
vec\[3] 에 정확히 vec 에서 3번째로 작은 수가 들어가게 된다 그 앞과 뒤는 정렬됐는지 확실치 않다. iter 는 이제 vec 에서 3번 째로 작은 수를 가리키므로 사용하면 된다.

- 중앙 값 찾기
```c++
int main()
{
	vector<int> vec = { 9, 2, 7, 4, 1, 5, 8};

	int mid = vec.size() / 2;
	nth_element(vec.begin(), vec.begin() + mid, vec.end());

	cout << vec[mid];
}
// 결과
5
```

# binary_search

정렬된 범위 내에서 이진 탐색을 수행하여 특정 값 존재 여부 확인.
bool 값을 반환 값이 존재하면 true
O(log n) 의 시간 복잡도를 가진다.

```c++
vector<int> vec = {1, 2, 3, 4, 5};
bool exists = binary_search(vec.begin(), vec.end(), 4);
```

# lower_bound

정렬된 범위에서 지정된 값 이상의 값 중, 처음으로 나타나는 위치를 찾는다. 중복된 경우 가장 첫번째(왼쪽) 위치를 반환

```c++
vector<int> vec = {1, 3, 3, 5, 9}
vector<int>::iterator iter = lower_bound(vec.begin(), vec.end(), 4);
vector<int>::iterator iter2 = lower_bound(vec.begin(), vec.end(), 3);
// 이때의 iter 는 5, iter2 는 첫 번째 3이다.
```

# upper_bound

정렬된 범위에서 지정된 값보다 큰 첫 번째 요소의 위치를 찾는다.

```c++
vector<int> vec = {1, 3, 3, 5, 9}
vector<int>::iterator iter = upper_bound(vec.begin(), vec.end(), 3);
// iter 는 5;
```

# fill

지정된 범위의 모든 요소를 주어진 값으로 채운다.

```c++
vector<int> vec(5);
fill(vec.begin(), vec.end(), 3); // 모든 요소를 3으로 채운다.
```

# replace

범위 내에서 특정 값과 일치하는 요소들을 새로운 값으로 바꾼다.

```c++
replace(vec.begin(), vec.end(), 2, 99); // 2 를 99 로 교체한다.
```

# remove_if

범위 내에서 지정된 조건을 만족하는 요소들을 제거하기 위해 요소들을 재배치 한다.
실제 컨테이너의 크기를 줄이지 않고, 조건에 맞지 않는 요소들을 앞으로 당겨서 새로운 끝 iterator 를 반환한다.
컨테이너에서 실제로 요소를 삭제하기 위해서는 반환된 iterator 를 이용해 erase 함수를 호출해야 한다.

```c++
vector<int> vec{ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
vector<int>::iterator iter = remove_if(vec.begin(), vec.end(), [](int x) {return x % 2 == 0; });
// 이때 vec4 는 {1, 3, 5, 7, 9, 6, 7, 8, 9, 10} 이다.
vec.erase(iter, vec.end());
// 이때 vec4 는 {1, 3, 5, 7, 9};
```

# unique

미리 정렬을 해주어야 한다.
연속된 중복 요소들을 제거하여 유일한 값들로 재배열한다.
컨테이너의 실제 크기를 줄이지 않고, 중복되지 않은 요소들을 앞쪽으로 모은다.

반환된 iterator 는 논리적 끝을 나타내며 이후의 요소는 불특정한 값일 수 있다. 

```c++
vector<int> vec{ 1, 2, 2, 3, 3, 3, 4, 5, 5 };
vector<int>::iterator iter = unique(vec.begin(), vec.end());
// 이때 vec 은 {1, 2, 3, 4, 5, 3, 4, 5, 5} 이다.
// iter 는 두 번째 3. 즉, vec[5] 를 가리키고 있다.
vec.erase(iter, vec.end());
// 이때 vec 은 {1, 2, 3, 4, 5} 이다.
```

- 스스로 구현한 unique
```c++
vector<int>::iterator unique(vector<int>& _vec)
{
	if (_vec.begin() == _vec.end())
	{
		return _vec.end();
	}

	vector<int>::iterator iter1 = _vec.begin();
	vector<int>::iterator iter2 = _vec.begin() + 1;

	while (iter2 != _vec.end())
	{
		if (*iter1 != *iter2)
		{
			++iter1;
			*iter1 = *iter2;
		}
		++iter2;
	}

	return iter1;
}

int main()
{
	vector<int> vec{ 1, 1, 2, 3, 3, 4, 5, 5 };
	
	vector<int>::iterator tempIter = unique(vec);

	vec.erase(tempIter, vec.end());

	cout << *tempIter << "\n";
}
```

- 모범 코드
```c++
template <typename T>
T custom_unique(T _first, T _last)
{
	if (_first == _last)
	{
		return _last;
	}

	T result = _first;
	while (++_first != _last)
	{
		if (*result != *_first)
		{
			*(++result) = *_first;
		}
	}

	return ++result;
}

int main()
{
	vector<int> vec{ 1, 1, 2, 3, 3, 4, 5, 5 };
	
	vector<int>::iterator tempIter = custom_unique(vec.begin(), vec.end());

	vec.erase(tempIter, vec.end());

	cout << *tempIter << "\n";
}
```

# next_permutation

사전식 순서의 다음 순열을 생성하는 함수. 현재 배열 (또는 문자열) 의 다음 순열을 자동으로 생성한다

```c++
int main()
{
	vector<int> vec = { 1, 2, 3 };

	do
	{
		for (int i = 0; i < vec.size(); i++)
		{
			cout << vec[i] << " "; 
		}
		cout << "\n";
	} while (next_permutation(vec.begin(), vec.end()));
}

// 결과
1 2 3
1 3 2
2 1 3
2 3 1
3 1 2
3 2 1
```
가장 큰 순열을 찾기 전까지 다음 순열을 만든다.
더 큰 순열이 존재하는 지에 대한 bool 값을 반환한다.
마지막 순열 3 2 1 에 도달하면 다시 1 2 3 으로 돌아간다.

# prev_permutation

이전 순열 찾기. 사전식 순서에서 이전 순열을 찾는 함수이다.

```c++
int main()
{
	vector<int> vec = { 3, 2, 1 };

	do
	{
		for (int num : vec)
		{
			cout << num << " ";
		}
		cout << "\n";
	} while (prev_permutation(vec.begin(), vec.end()));
}
// 결과
3 2 1
3 1 2
2 3 1
2 1 3
1 3 2
1 2 3
```

```c++
int main()
{
	vector<int> vec = { 3, 2, 1 };

	do
	{
		for (int num : vec)
		{
			cout << num << " ";
		}
		cout << "\n";
	} while (next_permutation(vec.begin(), vec.end()));
}
// 결과
3 2 1
```
3 2 1 보다 높은 사전식 수열이 없기 때문에 3 2 1 만을 출력한다.
