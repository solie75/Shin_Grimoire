### CreateDefaultSubObject

```c++
/**
 * Create a component or subobject that will be instanced inside all instances of this class.
 * @param	TReturnType					Class of return type, all overrides must be of this type
 * @param	SubobjectName				Name of the new component
 * @param	bTransient					True if the component is being assigned to a transient property. This does not make the component itself transient, but does stop it from inheriting parent defaults
 */
template<class TReturnType>
TReturnType* CreateDefaultSubobject(FName SubobjectName, bool bTransient = false)
{
	UClass* ReturnType = TReturnType::StaticClass();
	return static_cast<TReturnType*>(CreateDefaultSubobject(SubobjectName, ReturnType, ReturnType, /*bIsRequired =*/ true, bTransient)); // Call UObject::CreateDefaultSubobject()
}

/**
 * Create a component or subobject, allows creating a child class and returning the parent class.
 * @param	TReturnType					Class of return type, all overrides must be of this type
 * @param	TClassToConstructByDefault	Class of object to actually construct, must be a subclass of TReturnType
 * @param	SubobjectName				Name of the new component
 * @param	bTransient					True if the component is being assigned to a transient property. This does not make the component itself transient, but does stop it from inheriting parent defaults
 */
template<class TReturnType, class TClassToConstructByDefault>
TReturnType* CreateDefaultSubobject(FName SubobjectName, bool bTransient = false)
{
	return static_cast<TReturnType*>(CreateDefaultSubobject(SubobjectName, TReturnType::StaticClass(), TClassToConstructByDefault::StaticClass(), /*bIsRequired =*/ true, bTransient)); 
}
```

```c++
// obj.cpp
UObject* UObject::CreateDefaultSubobject(FName SubobjectFName, UClass* ReturnType, UClass* ClassToCreateByDefault, bool bIsRequired, bool bIsTransient)
{
	FObjectInitializer* CurrentInitializer = FUObjectThreadContext::Get().TopInitializer();
	UE_CLOG(!CurrentInitializer, LogObj, Fatal, TEXT("No object initializer found during construction."));
	UE_CLOG(CurrentInitializer->Obj != this, LogObj, Fatal, TEXT("Using incorrect object initializer."));
	return CurrentInitializer->CreateDefaultSubobject(this, SubobjectFName, ReturnType, ClassToCreateByDefault, bIsRequired, bIsTransient); // Call FObjectInitializer::CreateDefaultSubobject();
}
```

```c++
// UObjectGlobals.cpp
UObject* FObjectInitializer::CreateDefaultSubobject(UObject* Outer, FName SubobjectFName, const UClass* ReturnType, const UClass* ClassToCreateByDefault, bool bIsRequired, bool bIsTransient) const
{ 
	...
	UObject* Result = nullptr;
	FOverrides::FOverrideDetails ComponentOverride = SubobjectOverrides.Get(...);
	const UClass* OverrideClass = ComponentOverride.Class;

	if(OverrideClass)
	{
		...
		UObject* Template = OverrideClass->GetDefaultObject(); // force the CDO (class default object) to be created if it hasn't already
		...
		FStaticConstructObjectParameters Params(OverrideClass);
		...
		Result = StaticConstructObject_Internal(Params);
		...
		Outer->GetClass()->AddDefaultSubobject(Result, ReturnType);
	}

	return Result;
}
```

- SubObject 를 생성한다. 여기에서 Sub Object 는 UObject 를 상속 받는 클래스를 뜻한다. 
- 첫 번째 선언 에서는 생성하려는 객체의 반환 타입과 concrete 클래스 타입이 동일하다. TReturnType 은 abstract 클래스여도 사용할 수 있다. Unreal Engine 내부에서 해당 abstract 클래스를 구현하는 기본 concrete 클래스를 찾는다. concrete 클래스를 명시적으로 변경하지 않고, 항상 TReturnType 의 기본 구현체를 생성한다.
- 두 번째 선언에서는 TReturnType(반환타임) 과 TClassToConstructByDefault(생성할 객체의 구체적인 클래스 타입) 을 템플릿 인자로 받는다. TClassToConstructByDefault::StaticClass() 로 지정된 클래스를 인스턴스화 한 뒤, 반환 타입(TReturnType*) 으로 업캐스팅 하여 반환한다. abstract 클래스인 TReturnType 을 반환하면서도, 특정 concrete 클래스를 지정하여 생성하는 경우에 유용하다.
- 생성된 객체는 기본적으로 Owner 클래스의 멤버 함수에 저장된다 또한 Outer 객체(일반적으로 생성된 subobject 의 owner 클래스) 의 서브 오브젝트 컬렉션에 등록한다. 이는 UE 내부에서 owner 객체와 해당 객체에 속한 subobject 간의 관리를 위해 사용된다.

### USceneComponent

```c++
UCLASS (ClassGroup=(Utility, Common), BlueprintType, HideCategories=(Trigger, PhysicsVolume),  
       Meta=(BlueprintSpawnableComponent, IgnoreCategoryKeywordsInSubclasses, ShortTooltip="A Scene Component is a component that has a scene transform and can be attached to other scene components."),  
       MinimalAPI)  
class USceneComponent : public UActorComponent  
```
transform 을 가지고 attachment 를 지원한다. 하지만 랜더링 기능과 충돌 기능은 없다. 컴포넌트 상속 구조를 위한 더미용 컴포넌트로 유용하다.

### SetRootComponent

```c++
bool AActor::SetRootComponent(class USceneComponent* NewRootComponent)
{
	...
	RootComponent = NewRootComponent; // AActor::RootComponent
	...
```
특정된 컴포넌트를 루트 컴포넌트로 지정한다. 

### SetUpAttachment

```c++
void USceneComponent::SetupAttachment(class USceneComponent* InParent, FName InSocketName)
{
	...
	SetAttachParent(InParent);
	SetAttachSocketName(InSocketName);
	SetShouldBeAttached(AttachParent != nullptr);
	...
}
```
- 컴포넌트 간에 부모-자식 관계를 형성한다. 부모 컴포넌트이 Transform 이 자식 컴포넌트에 적용된다.
- InParent 는 해당 컴포넌트를 연결할 부모 컴포넌트로 반드시 USceneComponent 타입이어야 한다.
- InSocketName 은 부모 컴포넌트에 정의된 특정 소켓에 해당 컴포넌트를 연결할 수 있게 한다. 기본 값은 NAME_None 이며, 특정 소켓 없이 부모에 직접 연결된다.

### ConstructorHelpers_FObjectFinder

```c++
namespace ConstructorHelpersInternal
{
	template<typename T>
	inline T* FindOrLoadObject( FString& PathName, uint32 LoadFlags )
	{
		...
		UClass* Class = T::StaticClass();
		Class->GetDefaultObject(); // force the CDO to be created if it hasn't already
		T* ObjectPtr = LoadObject<T>(NULL, *PathName, nullptr, LoadFlags);
		if (ObjectPtr)
		{
			ObjectPtr->AddToRoot();
		}
		return ObjectPtr;
	}
}

struct ConstructorHelpers
{
public:
	template<class T>
	struct FObjectFinder : public FGCObject
	{
		TObjectPtr<T> Object;
		...
		FObjectFinder(const TCHAR* ObjectToFind, uint32 InLoadFlags = LOAD_None)
		{
			...
			Object = ConstructorHelpersInternal::FindOrLoadObject<T>(PathName, InLoadFlags);
			...
		}
		
		bool Succeeded() const {return !!Object;}
	}
	...
}
```

주어진 주소 스트링으로 UObjectGlobals::StaticLoadClass 을 호출하여 해당 파일을 Load 한다.
런타임이 아닌 생성자(객체 생성 시점) 에서 에셋을 로드한다.
load 된 파일을 ConstructorHelpers::FObjectFinder 구조체의 TObjectPtr<\T> Object 에 저장한다.
게임 중 유일한 객체에 유일한 에셋을 1회 로딩하는 경우에는 static 으로 선언하지 않겠지만 대부분의 경우 static 으로 선언하여 에셋 로드의 중복을 방지하고 비용이 큰 에셋 로딩 작업을 줄일 수 있도록 한다.

### GetWorldTimerManager().SetTimer() 

특정 함수를 일정 시간 후에 실행하거나, 반복 실행할 수 있도록 설정한다.
```c++
void SetTimer( 
	FTimerHandle& InOutHandle, 
	UObject* InObj, 
	FName InFunctionName, 
	float InRate, 
	bool InbLoop = false, 
	float InFirstDelay = 0.0f 
);
```
- FTimerHandle& InOutHandle : 타이머 핸들 (타이머 제어용 변수)
- UObject* InObj : 함수를 실행할 객체(this 사용 가능)
- FName InFunctionName : 실행할 함수 이름
- float InRate : 실행 주기 (초 단위)
- bool InbLoop : true (반복실행), false (한 번만 실행);
- float InFirstDelay : 첫 실행까지 대기 시간 (default 0.0f)

- 한번 실행 예시
	- StartTimer() 를 호출한 뒤 3 초 후 PrintMessage 를 한 번만 호출하며 반복하지 않는다.
```c++
void AMyActor::StartTimer() 
{ 
	GetWorldTimerManager().SetTimer(
		TimerHandle, 
		this, 
		FName("PrintMessage"), 
		3.0f, 
		false
	); 
}
void AMyActor::PrintMessage() 
{ 
	UE_LOG(LogTemp, Warning, TEXT("3초 후 실행됨!")); 
}
```

- 반복 실행 예시
	- StartLoopTimer() 호출 이후 2 초마다 PrintMessage 함수 호출.
```c++
void AMyActor::StartLoopTimer()
{ 
	GetWorldTimerManager().SetTimer(
		TimerHandle, 
		this, 
		FName("PrintMessage"), 
		2.0f, 
		true
	); 
}
```

### AddActorWorldOffset()

```c++
void AddActorWorldOffset(
    FVector DeltaLocation, 
    bool bSweep = false, 
    FHitResult* OutSweepHitResult = nullptr, 
    ETeleportType Teleport = ETeleportType::None
);
```

- DeltaLocation : 이동할 오프셋
- bSweep : ture 이면 충돌 감지 (Sweep) 활성화
- OutSweepHitResult : Sweep 이 활성화된 경우 , 충돌 정보를 저장.
- Teleport : ETeleportType::None(기본값) 또는 ETeleportType::TeleportPhysics 사용 가능.
	- TeleportPhysics 를 사용하면 물리 연산 없이 순간 이동 가능.
		- 물리 엔진을 무시하고 액터를 이동시키므로 빠르게 순간이동 가능.

- x 방향으로 100 만큼 이동.
```c++
AddActorWorldOffset(FVector(100.0f, 0.f, 0.f));
```

# Tags.Add(""), ActorHasTag()

Actor 에 tag 를 추가 하고 불러올 수 있다.

```c++
// 태그 추가
(Actor 클래스)->Tags.Add("TagName");

// 태그 불러오기
(Actor 클래스)->ActorHasTag("TagName");
```


# 함수 정리

- CreateDefaultSubObject 

- USceneComponent

- SetRootComponent

- SetUpAttachment

- ConstructorHelpers

- Succeeded

- SetStaticMesh

- SetMaterial

- GetActorLocation

- GetActorScale3D()
	- FVector 반환 (x, y, z) 축의 크기를 나타낸다.
```c++
FVector GetActorScale3D() const;
```

- SetActorScale3D() : 스케일 변경

- AddActorLocalRotation() : Actor 의 Rotation 현재 값에 일정 값을 더하여 설정한다.

- GetWorld()

-  GetTimeSeconds() : 현재 월드의 경과 시간을 반환. 즉, 게임이 시작된 후 현재까지의 시간을 초 단위로 반환.




### 회전과 이동에 관련된 모든 함수들 모음

- GetRelativeRotation
- SetRelativeRotation
- AddActorLocalRotation
- GetActorScale3D
- GetActorLocation
- AddActorWorldOffset
