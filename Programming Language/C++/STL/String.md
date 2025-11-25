# define

```c++
using string = std::basic_string<char, std::char_traits<char>, std::allocator<char>>;
```
- string 은 char 을 저장한다 -> string 은 char 배열이다.
# operator \[ ]

```c++
int main()
{
	string str = "abcdefg";
	str[3] = '?';

	cout << str;
	return 0;
}
```

```c++
// 결과
abc?efg
```

# pop_back

```c++
int main()
{
	string str = "abcdefg";
	cout << str << "\n";
	str.pop_back();
	cout << str << "\n";
	return 0;
}
```

```c++
// 결과
abcdefg
abcdef
```

# push_back

```c++
int main()
{
	string str = "abcdefg";
	cout << str << "\n";
	str.push_back('?');
	cout << str << "\n";
	str.push_back('zzz');
	cout << str << "\n";
	return 0;
}
```

```c++
// 결과
abcdefg
abcdefg?
abcdefg?z
```

- push_back 은 한 번에 한 문자만 추가할 수 있다. (문자열 추가는 append)

# append

```c++
int main()
{
	string str = "abcdefg";
	cout << str << "\n";
	str.append(" namd : Jack");
	cout << str << "\n";
	return 0;
}
```

```c++
abcdefg
abcdefg namd : Jack
```

# replace

```c++
int main()
{
	string str = "abcdefghijk";
	cout << str << "\n";
	str.replace(3, 5, "zzz");
	cout << str << "\n";
	str.replace(str.begin() + 3, str.end() - 2, "???");
	cout << str << "\n";
	return 0;
}
```

```c++
// 결과
abcdefghijk
abczzzijk
abc???jk
```

- replace(int \_start, int \_length, string \_input)
	- \_start 인덱스 부터 \_length 만큼 을 \_input 으로 변경한다.
- replace(iterator \_start, iterator \_end, string \_input)
	- \_start 부터 \_end 까지 \_input 으로 변경한다.

# substr

문자열의 일부를 리턴한다.
```c++
basic_string substr(size_type pos = 0, size_type count = npos) const;
```
pos 부터 count 길이 만큼의 문자열을 리턴한다. count 의 길이가 문자열보다 길다면 그 이상을 반환하지 않고 문자열의 끝까지만 리턴한다.
count 로 npos 를 전달한다면, 자동으로 pos부터 원리 문자열의 끝까지 리턴한다.
pos 가 string 의 범위 밖이라면 오류를 발생시킨다.

- 예시
```c++
int main()
{
    string str = "0123456789abcdefghij";

    string sub1 = str.substr(10);
    cout << sub1 << "\n";

    string sub2 = str.substr(5, 3);
    cout << sub2 << "\n";

    string sub3 = str.substr(str.size() - 3, 50);
    cout << sub3 << "\n";
}
```

```c++
// 결과
abcdefghij
567
hij
```

# append

기존 문자열의 끝에 다른 문자열이나 문자 등을 추가. 필요에 따라 메모리 재할당이 발생할 수 있다. 이때 [reserve](#reserve) 로 미리 용량을 할당하면 성능을 더욱 최적화 할 수 있다.

```c++
string.append(arg);
```

- 변경된 문자열에 대한 참조(\*this) 를 반환한다. 이를 통해 chaining 이 가능하다.

```c++
string str = "aaa";
string str2 = "zzz";
str.append("bbb").append("ccc").append(str2);
cout << str;
```

```c++
// 결과
aaabbbccczzz
```

- 문자 반복 추가
	- 하나의 문자를 여러개 반복해서 추가할 수 있다.
```c++
string str = "aaa";
str.append(3, 'P');
cout << str;
```

```c++
// 결과
aaaPPP
```

- 문자열의 특정 부분 추가
	1. 
```c++
string str = "aaa";
str.append("player", 3);
cout << str;
```

```c++
// 결과
aaapla
```
	2.
```c++
string str = "aaa";
string str2 = "player Weapon";
str.append(str2, 3, 6);
cout << str;
```

```c++
// 결과
aaayer We
```

- + 연산자와의 비교
	- \+ 연산자는 두 문자열을 결합하여 새로운 문자열을 생성하는 것이다. 기존 문자열에 새로운 문자열을 추가하는 식인 append 보다 새롭게 생성하는 + 연산자가 자원을 많이 사용한다.
	- 체이닝이 불가능하다.
	- -> 따라서 반복 작업등은 append 를 사용하는 것이 좋다.
	- append 와 + 의 비교 코드
```c++
int main() {
    const int iterations = 100000;
    std::string base = "Hello";

    // Using append
    auto start = std::chrono::high_resolution_clock::now();
    std::string result1 = base;
    for (int i = 0; i < iterations; ++i) {
        result1.append(" World");
    }
    auto end = std::chrono::high_resolution_clock::now();
    std::cout << "Append Time: "
        << std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count()
        << " ms\n";

    // Using +
    start = std::chrono::high_resolution_clock::now();
    std::string result2 = base;
    for (int i = 0; i < iterations; ++i) {
        result2 = result2 + " World";
    }
    end = std::chrono::high_resolution_clock::now();
    std::cout << "Plus Time: "
        << std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count()
        << " ms\n";

    return 0;
}
```

```c++
// 결과
Append Time: 5 ms
Plus Time: 1524 ms
// 거의 1.5 초 차이가 발생한다.
```

# reserve

문자열이 내부적으로 사용하는 메모리 용량 을 미리 설정하는데 사용된다.

```c++
void string::reserve(size_t new_capacity);
```
new_capacity 가 현재 용량보다 작으면 용량은 변경되지 않는다.

```c++
string str = "PlayerName";
cout << "capacity : " << str.capacity() << ", size : " << str.size() << "\n";

str.reserve(20);
cout << "capacity : " << str.capacity() << ", size : " << str.size();
```

```c++
// 결과
capacity : 15, size : 10
capacity : 31, size : 10
```

- reserve 사용 유무 차이
```c++
 const int iterations = 1000000;

 // Without reserve
 auto start = std::chrono::high_resolution_clock::now();
 std::string s1;
 for (int i = 0; i < iterations; ++i) {
     s1 += "a";
 }
 auto end = std::chrono::high_resolution_clock::now();
 std::cout << "Without reserve: "
     << std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count()
     << " ms\n";

 // With reserve
 start = std::chrono::high_resolution_clock::now();
 std::string s2;
 s2.reserve(iterations); // 미리 충분한 메모리 확보
 for (int i = 0; i < iterations; ++i) {
     s2 += "a";
 }
 end = std::chrono::high_resolution_clock::now();
 std::cout << "With reserve: "
     << std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count()
     << " ms\n";

 return 0;
```

```c++
// 결과
Without reserve: 28 ms
With reserve: 31 ms
```

[high_resolution_clock](Chrono.md#high_resolution_clock)

# Character Classification

|함수 이름|설명|
|---|---|
|`isalnum`|문자가 **알파벳(a-z, A-Z)** 또는 **숫자(0-9)** 인지 확인합니다. (영숫자)|
|`isalpha`|문자가 **알파벳(a-z, A-Z)** 인지 확인합니다.|
|`islower`|문자가 **소문자(a-z)** 인지 확인합니다.|
|`isupper`|문자가 **대문자(A-Z)** 인지 확인합니다.|
|`isdigit`|문자가 **숫자(0-9)** 인지 확인합니다.|
|`isxdigit`|문자가 **16진수 숫자(0-9, a-f, A-F)** 인지 확인합니다.|
|`iscntrl`|문자가 **제어 문자(예: 줄 바꿈, 탭 등)** 인지 확인합니다.|
|`isgraph`|문자가 **화면에 출력 가능한 문자이며 공백이 아닌 문자**인지 확인합니다.|
|`isspace`|문자가 **공백 문자(공백, 탭, 줄 바꿈 등)** 인지 확인합니다.|
|`isblank` _(C++11)_|문자가 **공백(space) 또는 탭(tab)** 인지 확인합니다.|
|`isprint`|문자가 **출력 가능한 문자**인지 확인합니다. (공백 포함)|
|`ispunct`|문자가 **구두점 문자(., !, ?, %, 등)** 인지 확인합니다.|
# Character Manipulation

| 함수 이름     | 설명                                  |
| --------- | ----------------------------------- |
| `tolower` | 대문자를 **소문자로 변환**합니다. (예: 'A' → 'a') |
| `toupper` | 소문자를 **대문자로 변환**합니다. (예: 'a' → 'A') |