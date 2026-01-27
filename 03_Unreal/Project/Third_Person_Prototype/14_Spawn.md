- Spawn Volume 생성, AActor 상속
```c++
// SpawnVolume.h
public:
	UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "Spawning")
	USceneComponent* Scene;

	UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "Spawning")
	UBoxComponent* SpawningBox;

public:	
	UFUNCTION(BlueprintCallable, Category = "Spawning")
	void SpawningItem(TSubclassOf<AActor> ItemClass);

	UFUNCTION(BlueprintCallable, Category = "Spawning")
	FVector GetRandomPointInVolume() const;
```

```c++
// SpawnVolume.cpp
ASpawnVolume::ASpawnVolume()
{
 	...
	Scene = CreateDefaultSubobject<USceneComponent>(TEXT("Scene"));
	SetRootComponent(Scene);

	SpawningBox = CreateDefaultSubobject<UBoxComponent>(TEXT("SpawningBox"));
	SpawningBox->SetupAttachment(Scene);
}

void ASpawnVolume::SpawningItem(TSubclassOf<AActor> ItemClass)
{
	if (!ItemClass)
	{
		return;
	}

	GetWorld()->SpawnActor<AActor>(
		ItemClass,
		GetRandomPointInVolume(),
		FRotator::ZeroRotator
	);
}

FVector ASpawnVolume::GetRandomPointInVolume() const
{
	FVector BoxExtent = SpawningBox->GetScaledBoxExtent();
	FVector BoxOrigin = SpawningBox->GetComponentLocation();

	return BoxOrigin + FVector(
		FMath::FRandRange(-BoxExtent.X, BoxExtent.X),
		FMath::FRandRange(-BoxExtent.Y, BoxExtent.Y),
		FMath::FRandRange(-BoxExtent.Z, BoxExtent.Z)
	);
}
```


# Data Table

- new c++ class -> none 상속 -> ItemSpawnRow 로 명명.

```c++
// ItemSpawnRow.h
#pragma once

#include "CoreMinimal.h"
#include "Engine/DataTable.h"
#include "ItemSpawnRow.generated.h"

USTRUCT(BlueprintType)
struct FItemSpawnRow : public FTableRowBase
{
	GENERATED_BODY();
public : 
// 다음 세 가지 변수는 DataTable 의 요소가 된다.
	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	FName ItemName;
	
// 생성될 아이템 클래스
	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	TSubclassOf<AActor> ItemClass;

// 생성될 확률
	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	float SpawnChance;
};

```

```c++
// ItemSpawnRow.cpp

#include "ItemSpawnRow.h"

// 소스파일에서 아무것도 구현하지 않는다.
```

- Unreal Editor -> r click -> Miscellaneous -> Data Table -> Pick Row Structure 에서 ItemSpawnRow 선택 -> Item Spawn Table 로 명명

Row Namw 은 Key 와 같아 고유해야 한다.
아이템 스폰 확률 총합이 100이 되도록 분배하여 설정해 준다.
![DataTable](/Image/Unreal/ItemSpawnTable_DataTable.png)

- SpawnVolume 에서 DataTable 을 받아 랜덤하게 Item Spawn

```c++
// SpawnVolume.h
#include "ItemSpawnRow.h"

UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Spawning")
UDataTable* ItemDataTable;

UFUNCTION(BlueprintCallable, Category = "Spawning")
void SpawnRandomItem();

FItemSpawnRow* GetRandomItem() const;
```

```c++
// SpawnVolume.cpp
ASpawnVolume::ASpawnVolume()
{
 	...
	ItemDataTable = nullptr;
}

FItemSpawnRow* ASpawnVolume::GetRandomItem() const
{
	if (!ItemDataTable)
	{
		return nullptr;
	}

	TArray<FItemSpawnRow*> AllRows;
	static const FString ContextString(TEXT("ItemSpawnContext"));
	ItemDataTable->GetAllRows(ContextString, AllRows); // ContextString 은 데이터 테이블에서 데이터를 가져올 때 오류 메시지에 사용되는 디버그 컨텍스트 문자열이다.어떤 상황에서 오류가 발생했는지 알려주는 역할을 한다.

	if (AllRows.IsEmpty())
	{
		return nullptr;
	}

	// 전체 확률을 더하고 0과 전체 확률 사이에 랜덤 값을 생성.
	float TotalChance = 0.0f;
	for (const FItemSpawnRow* Row : AllRows)
	{
		if (Row)
		{
			TotalChance += Row->SpawnChance;
		}
	}
	const float RandValue = FMath::FRandRange(0.0f, TotalChance);

	// spawn 확률을 처음 부터 더하며 정해진 랜덤 값을 초과 하면 해당 아이템을 Spawn한다.(가중치 랜덤)
	float AccumulateChance = 0.0f;
	for (FItemSpawnRow* Row : AllRows)
	{
		AccumulateChance += Row->SpawnChance;
		if (RandValue < AccumulateChance)
		{
			return Row;
		}
	}

	return nullptr;
}

void ASpawnVolume::SpawnRandomItem()
{
	if (FItemSpawnRow* SelectedRow = GetRandomItem())
	{
		if (UClass* ActualClass = SelectedRow->ItemClass.Get()) // 여기에서 Get() 은 TSubclassOf<AActor> 를 가져온다.
		{
			SpawningItem(ActualClass);
		}
	}
}
```

