
# Concept

### FString vs FCString

^3f35f6

FString 은 std::string 과 비슷하다.
- 문자열 객체(변수)로 데이터를 직접 가진다.
- 메모리를 자동 관리 한다. 글자가 길어지면 알아서 늘리고 필요 없으면 지운다.
- Split, Append, Printf 등 자체 기능을 내장하고 있다.

FCString 은 C언어 부터 내려온 저수준 문자열 함수(strcpy, strlen, atoi)들을 언리얼 엔진에서 쓸 수 있게 Wrapping 해 놓은 Static 라이브러리이다.
- 데이터를 가지지 않는다. 단순한 함수의 집합일 뿐, 변수로 선언하여 값을 담을 수 없다.
- C 스타일 : 문자열 포인터(TCHAR*) 를 인자로 받아서 처리한다.
- 문자열을 숫자로 바꾸거나(Atoi), 메모리를 복사하는 등 기초적인 작업을 수행한다.

FString 은 그 내부에 TCHAR* 이 들어있다. FCString 은 옛날 방식이라 FString 외부를 이해하지 못하기 때문에 * 연사자를 통해서 FString 의 내부에 있는 TCHAR* 를 주어야 한다.

```c++
FString MyString = "1234"; // FString 객체

MyString.Split(...);

FCString::Atoi(*MyString); // * 로 내용물(TCHAR*) 만 전달해야한다.
```

# Function

### FString::Split()

- 하나의 문자열을 특정 기준(구분자)을 중심으로 두 조각(왼쪽, 오른쪽) 으로 쪼개는 함수
```c++
FString::Split(const FString& InS, FString* LeftS, FString* RightS)
```

- Ins : 구분자
- LeftS, RightS : 결과를 담을 주소
- 해당 함수는 인자로 준 주소에 결과물을 저장하고, 구분자를 찾아서 성공적으로 쪼갰는지에 따라 true, 나 false 를 반환한다. (구분자를 못찾은 경우 false 반환)

```c++
FString ExampleText = "asdf=1234";
FString Key;
FString Value;
if(Switch.Split(TEXT"=", &Key, &Value));
{
	// 쪼개기 성공
	// Key 에는 asdf 가 저장되고
	// Value 에는 1234가 저장된다.
}
```

### FCString::Atoi