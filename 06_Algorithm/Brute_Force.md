가능한 모든 경우의 수를 전부 시도해 보는 것.
자료의 크기가 작거나, 최적화된 알고리즘을 떠올리기 어려울 때, 검증을 위해 사용된다.

예를 들어 n개의 원소에 대해
	부분 집합을 전부 구하는 경우 2^n;
	순열 (모든 원소를 나열하는 경우)의 브루트 포스 : n!
	이중, 삼중 반복문 등을 이용한 모든 '조합' 탐색.\
등이 있다.

# 방법

1. 중첩 반복문

2. bitmasking

```c++
int main()
{
	vector<int> vec = {1, 2, 3};
	int size = vec.size();

	for (int i = 0; i < (1 << size); i++)
	{
		cout << "{ ";
		for (int j = 0; j < size; j++)
		{
			if (i & (1 << j))
			{
				cout << vec[j] << " ";
			}
		}
		cout << "}\n";
	}
}
```

1. [next_permutation](Algorithm.md#next_permutation)

2. 백트래킹.


# 예시

- 완전 탐색
	1. a + b^2 + c^3 = n
	2. a, b, c 는
		1. 중복 가능
		2. 1 이상 100 이하 자연수

를 만족하는 a, b, c 를 구하는 문제.

```c++
int main()
{
	int n;
	cin >> n;

	vector<vector<int>> vec;

	for (int i = 0; i <= n; i++)
	{
		for (int j = 0; i + pow(j, 2) <= n; j++)
		{
			for (int k = 0; i + pow(j, 2) + pow(k, 3) <= n; k++)
			{
				if ((i + pow(j, 2) + pow(k, 3)) == n)
				{
					vector<int> temp;
					temp.push_back(i);
					temp.push_back(j);
					temp.push_back(k);
					vec.push_back(temp);
				}
			}
		}
	}

	int c = 0;
}
```

- 2차원 평면
2차원 격자(가령 n x m) 위에서 가능한 모든 경로를 탐색하는 문제

```c++
int PathCount = 0;

int solution(int _rowIndex, int _colIndex, int _rowSize, int _colSize)
{
	if (_rowIndex > _rowSize || _colIndex > _colSize)
	{
		return;
	}

	if (_rowIndex == _rowSize - 1 && _colIndex == _colSize - 1)
	{
		PathCount++;
		return;
	}

	solution(_rowIndex + 1, _colIndex, _rowSize, _colSize);
	solution(_rowIndex, _colIndex + 1, _rowSize, _colSize);
}

int main()
{
	cout << "(0, 0) 에서 (10, 10) 까지 가는 경우의 수 : " << solution(0, 0, 10, 10);
}
```