# isdigit

```c++
int isdigit(int c);
```

단일문자가 숫자인지 확인하는데 사용
c 가 0-9 라면 true(1) 를 반환, 아니라면 false(0) 을 반환

# toupper(), tolower()

- toupper() : 소문자를 대문자로 변환
- tolower() : 대문자를 소문자로 변환

```c++
int toupper(int c);
int tolower(int c);
```

# distance()

두 iterator 사이의 요소 개수를 구한다.
```c++
 vector<int> vec{ 2, 6, 3, 2, 4, 1, 5 };
 vector<int>::iterator iter = vec.begin();
 vector<int>::iterator iter2 = vec.end();

 cout << distance(iter, iter2) << "\n";
 
 // 결과
 7
```

# advance()

iterator 를 주어진 수 만큼 이동시킨다.

```c++
vector<int> vec{ 2, 6, 3, 2, 4, 1, 5 };
vector<int>::iterator iter = vec.begin();
advance(iter, 4);
cout << *iter << "\n";

// 결과
4
```

# max_element(), min_element(), minmax_element()

최대값 최소값 탐색

```c++
vector<int> v = { 4, 1, 6, 3, 8, 2 };

vector<int>::iterator iterMax = max_element(v.begin(), v.end());

vector<int>::iterator iterMin = min_element(v.begin(), v.end());

cout << "min : " << *iterMin << ", max : " << *iterMax << "\n";
// 결과 min : 1, max : 8

pair<vector<int>::iterator, vector<int>::iterator> MinMaxPair = minmax_element(v.begin(), v.end());

cout << "min : " << *MinMaxPair.first << ", max : " << *MinMaxPair.second;
// 결과 min : 1, max : 8
```

# count(), count_if()

특정 조건 만족하는 원소 개수 구하기

```c++
int main()
{
    vector<int> vec{ 2, 6, 3, 2, 4, 1, 5 };

    int Two_Count =  count(vec.begin(), vec.end(), 2);

    cout << Two_Count << "\n";

    int Even_Count = count_if(vec.begin(), vec.end(), [](int _i) { return _i % 2 == 0; });

    cout << Even_Count << "\n";
}

// 결과
2
4
```