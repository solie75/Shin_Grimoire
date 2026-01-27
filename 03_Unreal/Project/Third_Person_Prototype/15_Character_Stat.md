# Character Health 에 대한 Damage 및 Heal 적용.

- Player Character 에서 Health 변수외 그에 대한 게터와 세터를 추가한다. 
- 또한 데미지를 받는 함수를 추가한다.
```c++
// SpartaCharacter.h
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Health")
float MaxHealth;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Health")
float CurHealth;

virtual float TakeDamage(
	float DamageAmount,
	struct FDamageEvent const& DamageEvent,
	AController* EventInstigator,
	AActor* DamageCauser
) override;

UFUNCTION(BlueprintPure, Category = "Health")
float GetHealth() const;

UFUNCTION(BlueprintCallable, Category = "Health")
void AddHealth(float HealAmount);

void OnDeath();
```

```c++
// SpartaCharacter.cpp
ASpartaCharacter::ASpartaCharacter()
{
 	...
	MaxHealth = 100.0f;
	CurHealth = MaxHealth;
}

float ASpartaCharacter::TakeDamage(float DamageAmount, FDamageEvent const& DamageEvent, AController* EventInstigator, AActor* DamageCauser)
{
	float ActualDamage = Super::TakeDamage(DamageAmount, DamageEvent, EventInstigator, DamageCauser);
	CurHealth = FMath::Clamp(CurHealth - DamageAmount, 0.0f, MaxHealth);

	UE_LOG(LogTemp, Warning, TEXT("Health decreased to : %f"), CurHealth);

	if (CurHealth < 0.0f)
	{
		OnDeath();
	}

	return ActualDamage;
}

float ASpartaCharacter::GetHealth() const
{
	return CurHealth;
}

void ASpartaCharacter::AddHealth(float HealAmount)
{
	CurHealth = FMath::Clamp(CurHealth + HealAmount, 0.0f, MaxHealth);
	UE_LOG(LogTemp, Warning, TEXT("Health increased to : %f"), CurHealth);
}

void ASpartaCharacter::OnDeath()
{
	// 게임 종료
}
```

- 지뢰 아이템에 데미지 입히는 코드 구현
```c++
// MineItem.cpp
void AMineItem::Explode()
{
	TArray<AActor*> OverlappingActors;
	ExplosionRangeCheckComp->GetOverlappingActors(OverlappingActors);

	for (AActor* Actor : OverlappingActors)
	{
		if (Actor && Actor->ActorHasTag("Player"))
		{
			UGameplayStatics::ApplyDamage(
				Actor,
				ExplosionDamage,
				nullptr,
				this,
				UDamageType::StaticClass()
			);
			break;
		}
	}

	Destroy();
}
```

- 회복 아이템의 회복 코드 구현
```c++
// HealingItem.cpp
void AHealingItem::ActivateItem(AActor* Activator)
{
	if (Activator && Activator->ActorHasTag("Player"))
	{
		if (ASpartaCharacter* PlayerCharacter = Cast<ASpartaCharacter>(Activator))
		{
			PlayerCharacter->AddHealth(HealAmount);
		}
	}

	Super::ActivateItem(Activator);
}
```

# GameState 로 데이터를 전역으로 관리

- new c++ class -> GameState 상속 -> SpartaGameState 로 명명
```c++
// SpartaGameState.h
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/GameState.h"
#include "SpartaGameState.generated.h"
UCLASS()
class SPARTAPROJECT_API ASpartaGameState : public AGameState
{
	GENERATED_BODY()
public:
	ASpartaGameState();
	
public:
	UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "Score")
	int32 Score;

	UFUNCTION(BlueprintPure, Category = "Score")
	int32 GetScore();

	UFUNCTION(BlueprintCallable, Category = "Score")
	void AddScore(int32 Amount);
};

```

```c++
// SpartaGameState.cpp
#include "SpartaGameState.h"

ASpartaGameState::ASpartaGameState(){ Score = 0; }

int32 ASpartaGameState::GetScore(){	return Score; }

void ASpartaGameState::AddScore(int32 Amount){ Score += Amount; }
```

- GameMode 에서 SpartaGameState 를 추가

- CoinItem 에서 Score 추가
```c++
// CoinItem.cpp
void ACoinItem::ActivateItem(AActor* Activator)
{
	if (Activator && Activator->ActorHasTag("Player"))
	{
		if (UWorld* World = GetWorld())
		{
			if (ASpartaGameState* GameState = World->GetGameState<ASpartaGameState>())
			{
				GameState->AddScore(PointValue);
			}
		}
	}
	Super::ActivateItem(Activator);
}
```



TakeDamage
BlueprintPure
UGameplayStatics::ApplyDamage()
UDamageType::StaticClass()
PlayerState - 플레이어마다의 고유한 정보. 멀티에서 잘 사용 된다.
GameState- 전역 정보를 저장.