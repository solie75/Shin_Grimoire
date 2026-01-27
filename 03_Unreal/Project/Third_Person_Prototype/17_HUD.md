Widget Blueprint 생성 ->WBP_HUD 로 명명

Canvas 와 Text 를 사용하여  디자인
![[/Image/Unreal/HUD_WidgetBlueprintCreated.png]]

SpartaPlayerController 에서 HUD 변수 생성.

```c++
// SpartaPlayerController.h
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "HUD")
TSubclassOf<UUserWidget> HUDWidgetClass;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "HUD")
UUserWidget* HUDWidgetInstanace;

UFUNCTION(BlueprintCallable, Category = "HUD")
UUserWidget* GetHUDWidget() const;
```

```c++
// SpartaPlayerController.cpp
void ASpartaPlayerController::BeginPlay()
{
	...
	
	if (HUDWidgetClass)
	{
		HUDWidgetInstanace = CreateWidget<UUserWidget>(this, HUDWidgetClass);
		if (HUDWidgetInstanace)
		{
			HUDWidgetInstanace->AddToViewport();
		}
	}

	ASpartaGameState* SpartaGameState = GetWorld() ? GetWorld()->GetGameState<ASpartaGameState>() : nullptr;
	if(SpartaGameState)
	{
		SpartaGameState->UpdateHUD();
	}
}
```

- 실제 값과 HUD 연동

WBP_HUD 에서 직접적으로 바인딩하는 경우는 다음과 같다.
![[/Image/Unreal/DirectBinding_From_WBP_HUD.png]]

매 프레임 마다 갱신해 줘야 하기 때문에 성능에 취약하다. 따라서 Score 에 변동이 있을 때에만 호출되도록 다른 방식을 사용한다.
갱신을 GameState 에서 진행한다.

```c++
// SpartaGameState.h

	FTimerHandle HUDUpdateTimerHandle;
	void UpdateHUD();


// SpartaGameState.cpp
// 0.1 초 마다 갱신.
void ASpartaGameState::BeginPlay()
{
	Super::BeginPlay();

	StartLevel();

	GetWorldTimerManager().SetTimer(
		HUDUpdateTimerHandle,
		this,
		&ASpartaGameState::UpdateHUD,
		0.1f,
		true
	);
}

void ASpartaGameState::UpdateHUD()
{
	APlayerController* PlayerController = GetWorld()->GetFirstPlayerController();

	if(ASpartaPlayerController* SpartaPlayerController = Cast<ASpartaPlayerController>(PlayerController))
	{
		if (UUserWidget* HUDWidget = SpartaPlayerController->GetHUDWidget())
		{
			if (UTextBlock* TimeText = Cast<UTextBlock>(HUDWidget->GetWidgetFromName(TEXT("Time"))))
			{
				float RemaingTime = GetWorldTimerManager().GetTimerRemaining(LevelTimerHandle);
				TimeText->SetText(FText::FromString(FString::Printf(TEXT("Time: %.1f"), RemaingTime)));
			}

			if (UTextBlock* ScoreText = Cast<UTextBlock>(HUDWidget->GetWidgetFromName(TEXT("Score"))))
			{
				if (UGameInstance* GameInstance = GetGameInstance())
				{
					USpartaGameInstance* SpartaGameInstance = Cast<USpartaGameInstance>(GameInstance);
					if (SpartaGameInstance)
					{
						ScoreText->SetText(FText::FromString(FString::Printf(TEXT("Score: %d"), SpartaGameInstance->TotalScore)));
					}
				}				
			}

			if (UTextBlock* LevelText = Cast<UTextBlock>(HUDWidget->GetWidgetFromName(TEXT("Level"))))
			{
				if (UGameInstance* GameInstance = GetGameInstance())
				{
					USpartaGameInstance* SpartaGameInstance = Cast<USpartaGameInstance>(GameInstance);
					if (SpartaGameInstance)
					{
						LevelText->SetText(FText::FromString(FString::Printf(TEXT("Level: %d"), SpartaGameInstance->CurrentLevelIndex + 1)));
					}
				}
			}
		}
	}
}
```


AddToViewport()