선입선출 구조를 따르는 컨테이너. 먼저 들어온 요소가 먼저 나가는 방식.

- 함수
```c++
queue<int> que;

que.push(1);
que.push(2);
que.push(3);

que.front();
que.back();
que.pop();
```

1. push
2. front : 첫번째 인덱스
3. back : 마지막 인덱스
4. pop : front 삭제
5. size
6. empty

# priority_queue

heap 기반 우선 순위 큐 컨테이너. 기본적으로 최대 힙 으로 동작하여 가장 큰 값이 먼저 반환된다.

- 최대 힙
```c++

priority_queue<int> pq;

pq.push(10);
pq.push(20);
pq.push(30);
pq.push(5);

while(!pq.empty())
{
	cout << pq.top() << " ";
	pq.pop();
} // 결과 30 20 10 5
```

- 최소 힙
```c++
priority_queue<int, vector<int>, greater<int>> reversePq;

reversePq.push(10);
reversePq.push(20);
reversePq.push(30);
reversePq.push(5);

while (!reversePq.empty())
{
	cout << reversePq.top() << " ";
	reversePq.pop();
} // 결과 5 10 20 30
```

- 사용자 정의 비교 연산자 사용
```c++
struct Compare {
	bool operator ()(int _a, int _b)
	{
		return _a > _b;
	}
};

int main()
{
	priority_queue<int, vector<int>, Compare>cumtomPq;

	cumtomPq.push(10);
	cumtomPq.push(20);
	cumtomPq.push(30);
	cumtomPq.push(5);
	
	while (!cumtomPq.empty())
	{
		cout << cumtomPq.top() << " ";
		cumtomPq.pop();
	}
	// 결과 5 10 20 30
}
```

- 함수

7. push : 삽입
8. pop : top 삭제
9. top : 첫번째 인덱스
10. empty : 비었는지 확인.