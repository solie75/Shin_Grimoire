시간과 날짜를 처리하기 위한 라이브러리.

- 지원 시간 단위
	- `std::chrono::nanoseconds`: 나노초 (10⁻⁹ 초)
	- `std::chrono::microseconds`: 마이크로초 (10⁻⁶ 초)
	- `std::chrono::milliseconds`: 밀리초 (10⁻³ 초)
	- `std::chrono::seconds`: 초
	- `std::chrono::minutes`: 분
	- `std::chrono::hours`: 시간
- 시계
	- std::chrono::system_clock : 1970/01/01 0시 0분 0초 이후로 흘러간 시간 (시계)
	- std::chrono::system_clock : 마지막 부팅 이후로 흘러간 시간. (타이머)
	- system_clock의 경우 os 시간 동기화 등에 의하여 시간 측정 중에 시간 값이 변할 수 있는것에 반해 steady_clock 은 그럴 가능성이 없다.
- 활용
	- 게임서버에서 아이템과 같은 경우, 소유한 이후에 서버 부팅이 일어날 수 있기 대문에 system_clock() 을 사용하는 것이 좋다.
	- 스킬 사용의 경우 사용 후 재사용 시간 내에 서버 부팅이 일어날 일이 적기 때문에 steady_clock() 을 사용한다.

# now

현재 시점 반환
```c++
static std::chrono::time_point<std::chrono::system_clock> now() noexcept;
static std::chrono::time_point<std::chrono::steady_clock> now() noexcept;
```
high_resolution_clock::now() 도 사용된다.
```c++
// __msvc_chrono.hpp
_EXPORT_STD using high_resolution_clock = steady_clock;
```

- time_Point : 특정 시점을 나타내는 자료형;
- time_Point 간의 차이는 std::chrono::duration 으로 반환된다.
```c++
auto start = std::chrono::steady_clock::now(); // 시작 시간
// 어떤 작업 수행
auto end = std::chrono::steady_clock::now();   // 끝난 시간
std::chrono::duration<double> elapsed = end - start; // 작업 소요 시간
std::cout << "Elapsed time: " << elapsed.count() << " seconds\n";
```

```c++
// 결과
Elapsed time: 2.6e-06 seconds
```

# high_resolution_clock

현재 시스템에서 가능한 최상의 시계로 매핑
고정밀도가 필요한 타이머나 성능 분석에서 매우 유용한다. 하지만 절대 시간보다는 상대 시간(경과 시간)을 측정할 때 더 적합하다.
- high_resolution_clock : 하드웨어 타이머를 호출하기 때문에 비용이 높은 연산이다.

