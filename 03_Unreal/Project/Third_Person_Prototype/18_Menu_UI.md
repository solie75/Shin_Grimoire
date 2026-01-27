프로젝트의 전 후 에 쓰일 메뉴 Level 을 생성하고 메뉴 UI 를 추가한다.

UI 에서 Button 항목을 활용 
	-> Is Variable 로 C++ 에서 인식 할 수 있도록 한다.
	-> Events 에서 On Clicked 를 활성화.

Menu UI 는 PlayerController 에서 관리 하도록 한다.

```c++
// SpartaProject.h
UFUNCTION(BlueprintCallable, Category = "Menu")
void ShowMainMenu(bool bIsRestart);

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Menu")
TSubclassOf<UUserWidget> MainMenuWidgetClass;

UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = "Menu")
UUserWidget* MainMenuWidgetInstance;
```

```c++
// SpartaProject.cpp
void ASpartaPlayerController::ShowMainMenu(bool bIsRestart)
{
	// 기존의 게임 플레이 와 메뉴 UI 인스턴스 삭제
	if (HUDWidgetInstance)
	{
		HUDWidgetInstance->RemoveFromParent();
		HUDWidgetInstance = nullptr;
	}

	if (MainMenuWidgetInstance)
	{
		MainMenuWidgetInstance->RemoveFromParent();
		MainMenuWidgetInstance = nullptr;
	}

	// 메인 메뉴 인스턴스 생성.
	if (MainMenuWidgetClass)
	{
		MainMenuWidgetInstance = CreateWidget<UUserWidget>(this, MainMenuWidgetClass);
		if (MainMenuWidgetInstance)
		{
			MainMenuWidgetInstance->AddToViewport();

			bShowMouseCursor = true;
			SetInputMode(FInputModeUIOnly());
		}

	// 현재의 게임 상태(처음 시작인지 게임을 완료한 뒤인지)
		if (UTextBlock* ButtonText = Cast<UTextBlock>(MainMenuWidgetInstance->GetWidgetFromName(TEXT("StartButtonText"))))
		{
			if (bIsRestart)
			{
				ButtonText->SetText(FText::FromString(TEXT("Restart")));
			}
			else
			{
				ButtonText->SetText(FText::FromString(TEXT("Start")));
			}
		}
	}
}
```