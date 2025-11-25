# inner_product

```c++
_NODISCARD _CONSTEXPR20 _Ty inner_product(
    _InIt1 _First1, _InIt1 _Last1, _InIt2 _First2, _Ty _Val, _BinOp1 _Reduce_op, _BinOp2 _Transform_op);
_NODISCARD _CONSTEXPR20 _Ty inner_product(const _InIt1 _First1, const _InIt1 _Last1, const _InIt2 _First2, _Ty _Val);
```
- \_First1, \_Last1 : 첫 번째 범위의 시작과 끝을 나타내는 반복자.
- \_First2 : 두 번째 범위의 시작을 나타내는 반복자.

```c++
std::vector<int> vec1 = {1, 2, 3};
std::vector<int> vec2 = {4, 5, 6};
int dot = std::inner_product(vec1.begin(), vec1.end(), vec2.begin(), 0); 
// dot = 1*4 + 2*5 + 3*6 = 32
```
# accumulate

범위 내의 모든 요소를 누적하여 하나의 값을 계산한다.

```c++
std::vector<int> vec = {1, 2, 3, 4, 5};
int sum = std::accumulate(vec.begin(), vec.end(), 0); 
// sum 은 15
```

범위 내 모든 요소 곱셈

초기값을 시작 값으로 하여 배열 요소를 하나씩 곱한다.
```c++
vector<int> vec1{1, 2, 3, 4, 5}
int sum1 = accumulate(vec1.begin(), vec2.begin(), 1, mulriplies<int>());
// sum1 = 120
// 인자 중에 1 은 초기값.
```

사용자 정의 계산

```c++
vector<int> vec{ 1, 1, 2, 3, 3, 4, 5 };

int sum = accumulate(vec.begin(), vec.end(), 0, [](int _a, int _b) {
	return _a + _b + 2;
});
// 누적하여 더할 때마다 추가로 2를 더한다.
```
# adjacent_difference

범위 내에서 인접한 요소들 사이의 차이를 계산하여 다른 범위에 저장.
첫 번째 요소는 그대로 복사된다. 이후의 각 요소는 바로 이전 요소와의 차이가 계산된다.

```c++
vector<int> vec{ 1, 3, 6, 10 };
vector<int> diff(vec.size());
adjacent_difference(vec.begin(), vec.end(), diff.begin());
// diff 는 {1, 2, 3, 4} 이다.
```

# partial_sum


```c++
template< class InputIt, class OutputIt >
OutputIt partial_sum( InputIt first, InputIt last, OutputIt d_first );
```
지정된 배열의 시작을 InputIt fisrt 에 끝을 inputIt Last에 전달한다.
InputIt 와 같은 value type 으로 누적합을 위한 배열을 만들고 해당 배열의 시작점을 d_first 에 전달한다.
InputIt 배열의 각 인덱스별 누적합을 OutputIt 배열의 같은 인덱스에 저장한다.

```c++
vector<int> v = { 1, 2, 3, 4, 5 };
vector<int> pSum(5);
partial_sum(v.begin(), v.end(), pSum.begin());

for (int i : pSum)
{
	cout << i << "\n";
}

// 결과
1
3
6
10
15
```

이를 활용해 구간합을 구할 수 있다

pSum\[3] - pSum\[1] 는 v\[2] 부터 v\[3] 까지의 합이다.

