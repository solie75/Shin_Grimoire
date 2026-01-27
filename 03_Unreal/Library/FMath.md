# VInterpTo()

두 개의 FVector 값 사이를 일정한 속도로 보간(Interpolation) 하는 함수이다.

즉, 현재 위치에서 목표 위치로 일정한 속도로 점진적으로 이동할 때 유용한 함수이다.

보간 속도에 따라 목표에 가까워 질 수록 속도가 점점 감소.

```c++
static FVector VInterpTo(
    FVector Current, 
    FVector Target, 
    float DeltaTime, 
    float InterpSpeed
);
```

- Current : 현재 위치
- Target : 목표 위치
- DeltaTime : 프레임 간 경과 시간( GetWorld()->GetDeltaSeconds())
- InterpSpeed : 보간 속도 ( 높을 수록 빠르게 목표에 도달.)

- Current 에서 Target까지 InterpSpeed 에 따라 보간된 새로운 위치 를 반환한다.

- VInterpToConstant() : VInterpTo() 와 다르게 일정한 속도로 이동한다. 

# IsNearlyZero()

FVector 또는 float 값이 거의 0에 가까운지를 판별하는 함수.
즉, 완전한 0이 아닐 수 있지만 부동 소수점 연산의 오차를 고려하여 0으로 간주할 수 있는 정도인지 확인한다.

```c++
bool IsNearlyZero(float Tolerance = SMALL_NUMBER) const;
```
Tolearance : 0과 비교할 허용 오차 값 (default : 1.e-8 또는 000000001f)

true 를 반환하면 Tolearance 내의 값 이고 false 이면 Tolearance 를 벗어난 것이다.

- IsZero() 는 정확하게 0 인지를 확인하고 IsNearlyZero() 는 부동 소수점 오차를 고려하여 거의 0 인지를 확인한다.

# Clamp()

값이 특정 최소, 최대 범위를 벗어나지 않도록 제한한다.  값이 최소값 보다 작으면 최소값을 반환하고, 값이 최대 값보다 크면 최대값을 반환한다.
```c++
template <typename T>
static T Clamp(const T X, const T Min, const T Max);
```

# Max()

두 인자 중 더 큰 값을 반환한다.
```c++
template <typename T>
static T Max(const T A, const T B);
```