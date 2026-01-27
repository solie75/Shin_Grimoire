# Prefix, Postfix

- Prefix (++i)
	- 변수의 값을 증가시키고 증가된 값을 반환.
	- <span style="color: yellow;">복사본을 만들지 않고</span>   변수 값을 직접 증가 시킨다.
- Postfix (i++)
	- 변수의 현재 값을 먼저 반환 한 뒤, 값을 증가.
	- 단계
		1. 변수의 복사본 생성.
		2. 변수 값 증가
		3. 복사본 반환
	- <span style="color: yellow;">복사본 생성</span> 이라는 연산이 필요하다.

```c++
int i = 10;
cout << i << "\n";
cout << ++i << "\n";
cout << i++ << "\n";
cout << i << "\n";
```

```c++
// 결과
10
11
11
12
```

