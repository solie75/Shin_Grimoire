Actor 를 생성하고, 생성된 Actor 를 랜덤하게 10번 이동.

- 필수
	1. (0,0) 에서 스폰 되며 spawn 될 때 로그 출력
	2. 게임 캐릭터가 랜덤하게 10 회 이동 -> 이때 변화된 좌표를 출력(UE_LOG 활용)
	3.  step 함수 :  한번에 이동할 x, y 값 반환
	4. move 함수 0,0 에서 10회 랜덤 이동 -> 좌표 출력; 이동 시 step 함수 활용.

- 순서
	1. Top Down 프로젝트 c++ 로 생성.
	2. 바닥 객체 에 Material 추가. (Tiling 으로 텍스쳐 비율 조절)
	3. Actor 생성. (Player)
	4. 플레이 시작 시 UI_LOG, AddOnScreenDebugMessage 로 좌표 출력.
	5. IA 와 IMC 생성 (space key 누르면 Move 함수 호출) 및 바인딩.
	6. move 함수와 step 함수에서 이동 구현

# 주요 코드

```c++
// 프로젝트 player 클래스
class UInputMappingContext;
class UInputAction;

UCLASS(Blueprintable)
class AUE_Assignment1Character : public ACharacter
{
	GENERATED_BODY()

public:
	AUE_Assignment1Character();

protected:
	virtual void BeginPlay() override;
	
public:
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override;

	// Called every frame.
	virtual void Tick(float DeltaSeconds) override;

	/** Returns TopDownCameraComponent subobject **/
	FORCEINLINE class UCameraComponent* GetTopDownCameraComponent() const { return TopDownCameraComponent; }
	/** Returns CameraBoom subobject **/
	FORCEINLINE class USpringArmComponent* GetCameraBoom() const { return CameraBoom; }

	UFUNCTION()
	void Move(); // ... a)

	UFUNCTION()
	FIntPoint Step(); // ... a)

private:
	/** Top down camera */
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, meta = (AllowPrivateAccess = "true"))
	class UCameraComponent* TopDownCameraComponent;

	/** Camera boom positioning the camera above the character */
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = Camera, meta = (AllowPrivateAccess = "true"))
	class USpringArmComponent* CameraBoom;

	UPROPERTY(EditAnywhere, Category = "Input")
	class UInputMappingContext* IMC; // ...b)

	UPROPERTY(EditAnywhere, Category = "Input")
	class UInputAction* IA_Teleport; // ...b)

	//UPROPERTY(VisibleAnywhere, Category = "")
	float MovedDistance = 0.f;

	int MovingCount = 0;

	UFUNCTION()
	int CreateEvent();

	void PlayExplosionEffect(FVector Location);
public:
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Effects")
	UParticleSystem* ExplosionParticle;
};


```