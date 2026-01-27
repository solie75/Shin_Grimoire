# precision

출력할 수의 자릿수 단위를 지정.
```c++
double a = 10.0;
cout << a / 3 << "\n"; // 3.33333 출력
cout.precision(10); 
cout << a / 3 << "\n"; // 3.333333333 출력
```
소수점 뒤가 아닌 모든 정수 부분, 소수 부분의 숫자 개수 합이다.

# fixed

출력할 소수 부분 자릿수 단위 지정.

```c++
double a = 10.0;
cout << a / 3 << "\n"; // 3.33333 출력
cout.precision(10); 
cout << a / 3 << "\n"; // 3.333333333 출력
cout << fixed;
cout << a / 3 << "\n"; // 3.3333333333 출력
```

# unsetf()

특정 형식 플래그(format flags) 를 해제한다.
unsetf(ios::fixed)
출력할 수의 자릿수를 소수로 고정된 것을 정수 부분 + 소수 부분  으로 변경

```c++
double a = 10.0;
cout << a / 3 << "\n"; // 3.33333 출력
cout.precision(10); 
cout << a / 3 << "\n"; // 3.333333333 출력
cout << fixed;
cout << a / 3 << "\n"; // 3.3333333333 출력
cout.unsetf(ios::fixed);
cout << a / 3 << "\n"; // 3.333333333 출력
```