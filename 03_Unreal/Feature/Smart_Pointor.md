# 일반(Row) 포인터의 문제점.

1. Null 포인터 참조  : null 포인터 참조
```c++
int* v1 = new int;
*v1 = 5;
v1 = nullptr;
int v2 = *v1; // 오류 발생 : read access violation
```

1. 포인터 Dangling : 해제 된 포인터 사용.
```c++
int* v1 = new int;
*v1 = 5;
int* v2 = v1;
delete v1;
cout << *v2;
// 결과
-572662307
```

위의 문제를 해결하고자 C++ 에서는 shared_ptr, Unique_ptr 과 같은 스마트 포인터를 제공한다.

# UE 스마트 포인터

##### TSharedPtr\<T>

- shared_ptr 과 유사.
- if( TempSharedPtr.IsValied() ) 또는 If(TempSharedPtr) 로 유효성 체크
- TSharedPtr 을 일반 포인터로 변환 : Get()
```c++
class Test
{
public:
	Test();
	void StartTesting();
}

TSharedPtr<Test> testPtr = MakeShared<Test>();
Test *tempTestPtr = testPtr.Get();
if(tempTestPtr)
{
	tempTestPtr->StartTesting();
}
```
- 다른 Shared 포인터에 공유
	- 같은 타입의 TSharedPtr 로 변환은 제약없이 가능.
```c++
TSharedPtr<Test> anotherTestPtr;
anotherTestPtr = testPtr;
```
- 레퍼런스 카운트 감소(반환)
	- nullptr 로 직접할당
	- Reset() 으로 해당 포인터를 Invalid화
```c++
// 일반 포인터와 같이 반환
testPtr = nullptr;
// Reset() 으로 반환
testPtr.Reset();
```

##### TSharedRef

- TSharedPtr 의 기능, 하지만 nullptr 로 만들 수 없다.
- nullptr 이 될 수 없다. 즉, 언제나 값이 존재하므로 TSharedPtr 보다 안전한다.
```c++
TSharedRef<Test> testRef = MakeShared<Test>();
```
- TSharedPtr 에서 TSharedRef 로 전달.
```c++
TSharedPtr<Test> testPtr = MakeShared<Test>();
TSharedRef<Test> testRef = testPtr.ToSharedRef();
// 이 경우 testPtr 의 레퍼런스 카운트는 2가 된다.
```

##### TWeakPtr

- TSharedPtr 거의 유사한 동작. 하지만 레퍼런스 카운트를 증가시키지 않는다.
```c++
TSharedPtr<Test> testPtr = MakeShared<Test>();
TWeekPtr<Test> testWeekPtr = testPtr;
// 레퍼런스 카운트를 증가시키지 않는다.

if(testWeekPtr.IsValid())
// 레퍼런스 카운트를 알 수 없기 때문에 항상 IsValid() 를 통해 유효성 검사를 해줘야 한다.
```
- 레퍼런스 카운트를 증가시키지 않아서 포인터의 생명주기는 관여 하지않지만 포인터 dangling 상황은 방지.
- 어떤 객체의 생명주기에는 관여하지 않으면서 데이터 모니터링만 하고 싶은때 유용.

##### TUniquePtr

- 레퍼런스를 다른 스마트 포인터와 공유할 수 없다.
```c++
TUniquePtr<Test> testUniquePtr = MakeUnique<Test>();
```
- 유니크 포인터를 다른 유니크 포인터로 옮기는 경우 : MoveTemp() 함수 사용 , MoveTemp 를 통해 주체를 옮기면 기존의 UniquePtr 은 자동으로 nullptr 로 초기화 된다.
```c++
TUniquePtr<Test> testUniquePtr = MakeUnique<Test>();
TUniquePtr<Test> anotherTestUniquePtr = MoveTemp(testUniquePtr);
```

# Hard Reference, Soft Reference

##### Hard Reference
- 직접적인 객체 참조 방식. 참조된 객체는 항상 메모리에 로드된다. Hard Reference 가 많아질 수록 불필요한 리소스가 로드되어 메모리 사용량이 증가할 수 있다.

- UPROPERTY()
	- UPROPERTY() 속성으로 선언된 변수는 Garbage Collector 에 의해 관리된다. 하지만 GC 에 의해 관리되더라도 유효하지 않은 포인터가 될 수 있기 때문에 항시 유효성 검사가 필요하다.
		- 객체가 Destroy() 로 삭제되더라도 그 객체를 가리키는 포인터는 남아 있을 수 있기 때문 (Dangling Pointer 발생가능) 
		- GC 는 일정 주기로 동작하기 때문에 GC 가 실행 되기 전에 유효하지 않은 객체를 잠조하는 경우
		- Async, Timer, Delegate 등의 비동기 작업에서 객체가 삭제될 수 있음.

- Hard Reference 는 주로 항상 로딩되어야 하는 오브젝트 (ex. 플레이어 관련), 로딩 속도가 중요한 경우 에 사용된다.

##### Soft Reference

- 객체를 직접 참조하지 않고, 필요할 때만 로드하는 방식
- TSoftObjectPtr<> 또는 TSoftClassPtr<> 를 사용해서 Soft Reference 관리

- 필요할 때만 로드한다.
- Garbage Collector 의 직접적인 관리 대상이 아니다.
	- Soft Reference 는 직접 객체를 소유하지 않기 때문에, 필요할 때 LoadSynchronous() 나 Asymc 로드 를 사용해야 한다.
- 레벨이 다른 때나 DLC 콘텐츠 로드에 적합하다.
	- ex. 특정 맵에서만 등장하는 무기, 캐릭터 등의 에셋을 Soft Reference 로 설정하면, 해당 맵을 로드할 때만 해당 에셋을 로드할 수 있다.
- 비동기 (Asymchronous) 로딩
	- 로딩 시 블럭 되지 않는다. -> 게임 스레드를 멈추지 않는다.
- 수동으로 로딩을 관리해주어야 한다.
```c++
UPROPERTY()
TSoftObjectPtr<UTexture2D> CharacterTexture = TSofrObjectPtr<UTexture2D>(FSoftObjectPath(TEXT("/Game/Textures/MyTexture")));

if(CharacterTexture.IsValid())
{
	UTextrue2D* LoadedTexture = CharacterTexture.LoadSynchronous();
}
```
- 해제
```c++
FStreamableManager& StreamableManager = UAssetManager::GetStreamableManager();
StreamableManager.Unload(SoftTexture.ToSoftObjectPath());
```
- 스트링 기반 리소스 경로 의존적이라 경로가 바뀌면 따로 관리해 주는 안전 장치가 필요하다.

- 게임플레이와 밀접하게 관련된 오브젝트에 사용금지. 게임플레이와 관계없는 커다란 용량의 그래픽적 요소들 에 사용.