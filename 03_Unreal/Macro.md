# UE_LOG

- form
```c++
/**
 * A macro that logs a formatted message if the log category is active at the requested verbosity level.
 *
 * @param CategoryName   Name of the log category as provided to DEFINE_LOG_CATEGORY.
 * @param Verbosity      Verbosity level of this message. See ELogVerbosity.
 * @param Format         Format string literal in the style of printf.
 */
#define UE_LOG(CategoryName, Verbosity, Format, ...) \
```

- Custom LogTemp Category
```c++
// header file
DECLARE_LOG_CATEGORY_EXTERN(로그카테고리 이름, Waring, All);
```

```c++
DEFINE_LOG_CATEGORY(로그카테고리 이름);

...

UE_LOG(로그카테고리 이름, Warning, TEXT("로그에 출력할 내용"))
```

- Log Verbosity
	- Fetal
		- 빨간색 배경 + 흰색 텍스트
		- 프로그램 실행 주 치명적 오류로 인한 강제 종료가 필요할 시
	- Error
		- 빨간색 텍스트
		- 해결이 필요한 심각한 오류
	- Warning
		- 노락색 텍스트
		- 주의가 필요한 상황
	- Display
		- 흰색 텍스트
		- 기본 출력
	- Log
		- 회색 텍스트
		- 디버깅 관련된 일반 로그
	- Verbose
		- 짙은 회색 텍스트
		- 자세한 디버깅 정보 출력
	- VeryVerbose
		- 짙은 회색 텍스트
		- 최대한의 디버깅 정보 출력. 일반저그올 개발 중 특정 문제를 깊이 분석할 때 사용.

# DECLARE_LOG_CATEGORY_EXTERN

로그 카테고리 이름으로 된 구조체를 만들고, 그 구조체 변수를 extern으로 선언한다.

```c++
#define DECLARE_LOG_CATEGORY_EXTERN(CategoryName, DefaultVerbosity, CompileTimeVerbosity) \
    extern struct FLogCategory##CategoryName : public FLogCategory<ELogVerbosity::DefaultVerbosity, ELogVerbosity::CompileTimeVerbosity> \
    { \
       FORCEINLINE FLogCategory##CategoryName() : FLogCategory(TEXT(#CategoryName)) {} \
    } CategoryName;
```

##### 요소

- exturn
	- 어딘가에 정의되어 있으니, 이 헤더를 포함(include)하는 모든 파일은 이 변수를 가져다 써라"**라고 선언하는 것
	- 메모리에 실제 공간을 할당하지 않고 이름만 알리는 것입니다. (실제 할당은 `.cpp` 파일에서 `DEFINE_LOG_CATEGORY`로 한다.
- struct FLogCategory##CategoryName
	- `##` (Token Pasting): 매크로 인자로 들어온 `CategoryName`을 앞의 글자와 합친다.
- : public FLogVerbosity<...>
	- 언리얼 기본 로그 클래스인 FLogCategory 를 상속 받는다. 이때 템플릿 인자로 기본 로그 레벨과 컴파일 타임 로그 레벨을 넘겨준다.
- TEXT(#CategoryName)
	- #(String) : 매크로 인자를 문자열로 바꾼다. 
- CategoryName
	- 마지막으로, 위에서 정의한 구조체의 인스턴스(변수)를 선언한다. 이 변수 이름이 UE_LOG(에서 사용하는 첫 번째 인자가 된다.)

##### Parameter

- CategoryName
	- 로그 카테고리 이름 (코드에서 식별자로 사용된다.)
- DefaultVerbosity
	- 런타임(실행 중) 에 적용될 기본 로그 상세도. (Error, Warning, Log 등)
	- 이 레벨 보다 낮은 중요도의 로그는 출력되지 않는다.
- CompileTimeVerbosity
	- 컴파일 타임에 적용될 최대 상세도
	- 이 레벨보다 더 자세한 로그(VeryVerbose) 는 아예 코드에서 삭제(컴파일제외) 도니다. 최적화를 위해 사용.

##### 실제 사용 예시

```c++
DECLARE_LOG_CATEGORY_EXTERN(GameServerLog, Log, All);
```

1. GameServerLog 라는 이름의 로그 카테고리를 만든다.
2. Log 레벨로 설정한다.
3. 컴파일 레벨을 ALL 로 설정한다.
	1. 코드에 있는 모든 로그 (VeryVerbose 포함)가 컴파일에 포함된다.
4. Extern 선언
	1. 이 코드가 헤더 파일에 있다면 해당 헤더를 include 하는 모든 파일에서 GameServerLog 를 사용할 수 있게 된다.

##### 주의

cpp 파일 에 정의를 해줘야 한다.

- 헤더파일 : DECLARE_LOG_CATEGORY_EXTERN(GameServerLog, Log, All);
- 소스파일 : DEFINE_LOG_CATEGORY(GameServerLog);


# 로그

- 로그 상세도
로그 레벨(Log Level)과 로그 상세도(Verbosity)는 같은 말이다.

- 중요도가 높은 순서
	1. - **Fatal (치명적):** "으악!" 게임이 즉시 크래시(강제 종료) 나야 할 정도로 심각한 오류.
	2.  **Error (에러):** "빨간색 경고." 기능이 작동 안 함. 게임은 안 꺼지지만 고쳐야 함.
	3. **Warning (경고):** "노란색 주의." 뭔가 이상하지만 일단 돌아감. (예: 텍스처를 못 찾음)
	4. **Display (표시):** 콘솔 창에 반드시 보여주고 싶은 정보.
	5. **Log (일반):** "흰색/회색." 일반적인 정보. (예: "서버에 접속했습니다", "총알 발사됨")
	6. **Verbose (상세):** 디버깅용 정보. (예: "A 함수 진입", "B 변수 값 변경됨")
	7. **VeryVerbose (매우 상세):** 아주 사소한 정보. (예: 매 프레임마다 위치 좌표 출력)