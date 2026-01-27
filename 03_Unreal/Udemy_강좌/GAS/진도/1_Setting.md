Editor Preferences
User Interface -> Asset Eidtor Open Location 을 Main Window 로 세팅.
-> 에셋이 언리얼 에디터의 Tab 으로 열림

Editor Preferences
Accessor -> Source Code Editor 를 Rider Uproject 로 설정.

Editor Preferences
Hot Reload Automatically Compile Newly Added C++ Classes 를 Disable 로 설정.

Editor Preferences
Generl -> Enable Live Coding 을 Disable 로 설정.

Plugins 에서 Rider Integration 을 Enable 로 설정.

# Rider 의 Rum 과 Debug 차이

Debug 모드에서 Breakpoints, Call Stack, Variable Inspection 이 가능하다.
Run 모드는 단순 실행 또는 테스트를 위해 사용되고 , Debug 모드는 버그 추적, 문제 분석용으로 사용된다. 
Run 모드는 보통 Development 로, Debug 는 DebugGame, DebugGame Editor 로 사용된다.

Target 은 Editor 를 킬것인가 바로 게임을 킬 것인가의 차이.

사용 경우

| 작업       | Configuration | Target   | 버튼       |
| -------- | ------------- | -------- | -------- |
| 개발 중 디버깅 | `DebugGame`   | `Editor` | 🐞 Debug |
| 빠르게 테스트  | `Development` | `Editor` | ▶️ Run   |
| 최종 빌드    | `Shipping`    | `Game`   | 패키징      |
# Asset Migrate 와 Move

Asset 을 이동시킬 때 프로젝트 끼리의 이동을 Migrate 을 이용하고 한 프로젝트에서 이동할 때에는 언리얼 에디터의 Content Browser 상의 Move Here 로 이동해야 각 에셋 간의 참조가 깨지지 않는다.

# Setting

Rider 의 기본 세팅 알아보기
GameSampleAnimation 에서 기본 에셋 가져오기.
Aura 정상 컴파일 시키기

# Set ABP

- AnimInstance 생성
- Main State Machine 과 Defualt Slot 생성.
- [Default Slot 이란?](5_Animation_Blueprint.md#Animation_Slot)

# Animation 클래스 구조와 Owner 관리

![GAS_Project_Character_And_Animation_Basic_Structure](/Image/Unreal/GAS_Project_Character_And_Animation_Basic_Structure.png)

- CharacterAnimInstance::NativeUpdateAnimation() : 매 프레임 호출되는 Tick 함수로 ABP 에서 사용되는 변수들 계산 및 업데이트

# Goblin Monster 용 ABP 생성

- ABP_Monster (Template) 생성.
ABP Template 에서 Main State -> Default -> Output Pose
Main State 의 Locomotion 에서 Blendspace Player -> Output Animation Pose
[BlendSpace 란?](https://dev.epicgames.com/documentation/en-us/unreal-engine/blend-spaces-in-animation-blueprints-in-unreal-engine#:~:text=Aim%20Offset.-,Blend%20Space%20Player,-Blendspace%20Players%20are) Blend Space 에셋을 재생하고 제어하는 노드. Blend Space 또는 Blend Space 1D 에셋을 할당하면 입력되는 값( X축, Y축) 을 기준으로 애니메이션들을 부드럽게 혼합하여 출력.

- ABP_Goblin 생성.
	- 해당 ABP 의  Asset Override Editor 탭에서 BlendSpace Player 에 Montage 추가.

# Player Input

IA_Move, IA_Look, IMC_Player 추가.