# Oritent_Rotation_to_Movement

캐릭터가 이동하는 방향을 기준으로 자동으로 회전하도록 하는 기능.

- ture : 이동하는 방향(이동 벡터 Vector)을 바라보도록 캐릭터의 회전을 자동으로 조정
	- 컨트롤러 방향과 상관없이, 이동 방향대로 캐릭터가 돌아간다.
- false : 캐릭터의 회전은 Controller 의 입력에 따라 결정.
	- 캐릭터의 회전은 Controller 의 방향(FRotator) 을 따라가고, 이동 방향과 무관하낟.
	- 보통의 플레이어 에서 마우스로 카메라를 조작할 때 사용된다.

```c++
GetCharacterMovement()->bOrientRotationToMovement = true; // 이동 방향으로 회전 
GetCharacterMovement()->RotationRate = FRotator(0.f, 720.f, 0.f); // 회전 속도 설정 
bUseControllerRotationYaw = false; // 컨트롤러 회전 비활성화
```

- bOrientRotationToMovement = true 와 bUseControllerRotationYaw = true 를 함께 사용하면 컨트롤러와 이동방향이 서로 충돌할 수 있다.
- RotationRate 가 낮으면 회전이 부드럽고, 높으면 빠르게 회전한다. 0.f 로 설정하면 회전이 안된다.