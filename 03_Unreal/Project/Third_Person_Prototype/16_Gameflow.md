# Game flow 시작

GameState 에서 하나의 Level 의 상태를 관리하고, GameInstance 에서 게임 전체 데이터를 관리한다.
Level 이 진행되는 조건은 spawn 된 코인을 다먹거나 30 초가 지난 것을 기준으로 한다.

- SpawnVolume 에서 Item 을 생성할 때 함수가 생성한 Actor 를 반환하도록 변경
```c++
// SpawnVolume.cpp
AActor* ASpawnVolume::SpawningItem(TSubclassOf<AActor> ItemClass)
{
	if (!ItemClass)
	{
		return nullptr;
	}

	AActor* SpawnedActor = GetWorld()->SpawnActor<AActor>(
		ItemClass,
		GetRandomPointInVolume(),
		FRotator::ZeroRotator
	);

	return SpawnedActor;
}

AActor* ASpawnVolume::SpawnRandomItem()
{
	if (FItemSpawnRow* SelectedRow = GetRandomItem())
	{
		if (UClass* ActualClass = SelectedRow->ItemClass.Get())
		{
			SpawningItem(ActualClass);
		}
	}
	return nullptr;
}
```

- GameInstance
```c++
// SpartaGameInstance.h
UCLASS()
class SPARTAPROJECT_API USpartaGameInstance : public UGameInstance
{
	GENERATED_BODY()
public:
	USpartaGameInstance();

	UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "GameData")
	int32 TotalScore;
	UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "GameData")
	int32 CurrentLevelIndex;
	
	UFUNCTION(BlueprintCallable, Category = "GameData")
	void AddToScore(int32 Amount);	
};
```

```c++
// SpartaGameInstance.cpp
USpartaGameInstance::USpartaGameInstance()
{
	TotalScore = 0;
	CurrentLevelIndex = 0;
}

void USpartaGameInstance::AddToScore(int32 Amount)
{
	TotalScore += Amount;
	UE_LOG(LogTemp, Warning, TEXT("Total Score Updated: %d"), TotalScore);
}
```

- GameState

```c++
UCLASS()
class SPARTAPROJECT_API ASpartaGameState : public AGameState
{
	GENERATED_BODY()
public:
	ASpartaGameState();

public:
	// 현재 Level 의 Score;
	UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "Score")
	int32 Score;

	// 현재 Level 에서 스폰된 코인 개수
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Coin")
	int32 SpawnedCoinCount;
	// 플레이어가 현재 Level 에서 습득한 코인 개수
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Coin")
	int32 CollectedCoinCount;

	FTimerHandle LevelTimerHandle;
	// Level 지속 시간
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Level")
	float LevelDuration;
	// 현재 Level 의 인덱스
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Level")
	int32 CurrentLevelIndex;
	// 최대 레벨
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Level")
	int32 MaxLevelIndex;
	// 레벨 리스트
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Level")
	TArray<FName>LevelMapNames;

public:
	virtual void BeginPlay() override;

	UFUNCTION(BlueprintPure, Category = "Score")
	int32 GetScore();

	UFUNCTION(BlueprintCallable, Category = "Score")
	void AddScore(int32 Amount);
	
	UFUNCTION(BlueprintCallable, Category = "Level")
	void OnGameOver();

	void StartLevel();
	void OnLevelTimeUP();
	void EndLevel();
	void OnCoinCollected();
};
```

```c++
// SpartaGameState.cpp
ASpartaGameState::ASpartaGameState()
{
	Score = 0;
	SpawnedCoinCount = 0;
	CollectedCoinCount = 0;
	LevelDuration = 30.f;
	CurrentLevelIndex = 0;
	MaxLevelIndex = 3;
}

int32 ASpartaGameState::GetScore()
{
	return Score;
}

void ASpartaGameState::AddScore(int32 Amount)
{
// GameInstance 의 Score 에 현재 Level 에 획득한 Score 추가가
	if (UGameInstance* GameInstance = GetGameInstance())
	{
		USpartaGameInstance* SpartaGameInstance = Cast<USpartaGameInstance>(GameInstance);
		if(SpartaGameInstance)
		{
			SpartaGameInstance->AddToScore(Amount);
		}
	}
}

void ASpartaGameState::BeginPlay()
{
	Super::BeginPlay();

	StartLevel();
}

void ASpartaGameState::StartLevel()
{
// Instance 의 CurrentLevelIndex 를 가져온다.
	if (UGameInstance* GameInstance = GetGameInstance())
	{
		USpartaGameInstance* SpartaGameInstance = Cast<USpartaGameInstance>(GameInstance);
		if (SpartaGameInstance)
		{
			CurrentLevelIndex = SpartaGameInstance->CurrentLevelIndex;
		}
	}

	SpawnedCoinCount = 0;
	CollectedCoinCount = 0;

// SpawnVolume 을 가져와 해당 Spawn Volume 에서 생성되는 코인의 개수를 저장한다.
	TArray<AActor*> FoundVolumes;
	UGameplayStatics::GetAllActorsOfClass(GetWorld(), ASpawnVolume::StaticClass(), FoundVolumes);
	
	const int32 ItemToSpawn = 40;

	for (int32 i = 0; i < ItemToSpawn; i++)
	{
		if (FoundVolumes.Num() > 0)
		{
			ASpawnVolume* SpawnVolume = Cast<ASpawnVolume>(FoundVolumes[0]);
			if (SpawnVolume)
			{
				AActor* SpawnedActor = SpawnVolume->SpawnRandomItem();
				if (SpawnedActor && SpawnedActor->IsA(ACoinItem::StaticClass()))
				{
					SpawnedCoinCount++;
				}
			}
		}
	}
	// 시간이 초과 하면 다음 레벨로 넘어간다.
	GetWorldTimerManager().SetTimer(
		LevelTimerHandle,
		this,
		&ASpartaGameState::OnLevelTimeUP,
		LevelDuration,
		false
	);

	UE_LOG(LogTemp, Warning, TEXT("Level %d Start!, Spawned %d coin"), 
		CurrentLevelIndex + 1,
		SpawnedCoinCount)
}

void ASpartaGameState::OnLevelTimeUP()
{
	EndLevel();
}

void ASpartaGameState::EndLevel()
{
	GetWorldTimerManager().ClearTimer(LevelTimerHandle); // 타이머 초기화
	// 다음의 레벨을 Instance 에 저장.
	if (UGameInstance* GameInstance = GetGameInstance())
	{
		USpartaGameInstance* SpartaGameInstance = Cast<USpartaGameInstance>(GameInstance);
		if (SpartaGameInstance)
		{
			AddScore(Score);
			CurrentLevelIndex++;
			SpartaGameInstance->CurrentLevelIndex = CurrentLevelIndex;
		}
	}

	if (CurrentLevelIndex > MaxLevelIndex)
	{
		OnGameOver();
		return;
	}
	// 새로운 Level 열기.
	if (LevelMapNames.IsValidIndex(CurrentLevelIndex))
	{
		UGameplayStatics::OpenLevel(GetWorld(), LevelMapNames[CurrentLevelIndex]);
	}
	else
	{
		OnGameOver();
	}
}

void ASpartaGameState::OnGameOver()
{
	UE_LOG(LogTemp, Warning, TEXT("Game Over"));
}

void ASpartaGameState::OnCoinCollected()
{
	// coin item 을 얻을 때마다 갱신.
	CollectedCoinCount++;
	UE_LOG(LogTemp, Warning, TEXT("Coin Collected: %d / %d"),
		CollectedCoinCount,
		SpawnedCoinCount);

	if (SpawnedCoinCount > 0 && CollectedCoinCount >= SpawnedCoinCount)
	{
		EndLevel();
	}
}
```

# Q. 

1. Game flow 를 어디에 구현하는 것이 좋은가.

GameState 에 Game flow 를 구현
GameMode - 서버 전용 로직, 게임 규칙,
