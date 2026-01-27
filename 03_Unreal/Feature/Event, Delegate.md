# 콜백 함수의 인자

콜백함수의 매개변수는 바인딩 되는 이벤트에서 요구하는 인자와 정확히 일치해야 한다.
이벤트의 델리게이트 타입을 확인하여 어떤 인자를 요구하는지 알 수 있다.

인자 확인 예시
```c++

// 콜백함수 바인딩
(충돌 컴포넌트)->OnComponentBeginOverlap.AddDynamic(this, &콜백함수)

// PrimitiveComponent.h
UPROPERTY(BlueprintAssignable, Category="Collision")
FComponentBeginOverlapSignature OnComponentBeginOverlap;

DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_SixParams( FComponentBeginOverlapSignature, UPrimitiveComponent, OnComponentBeginOverlap, UPrimitiveComponent*, OverlappedComponent, AActor*, OtherActor, UPrimitiveComponent*, OtherComp, int32, OtherBodyIndex, bool, bFromSweep, const FHitResult &, SweepResult);
/** Delegate for notification of end of overlap with a specific component */
```

