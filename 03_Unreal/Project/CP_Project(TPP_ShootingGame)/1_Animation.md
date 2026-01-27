- Animation Sample Project 를 분석하여 CP_Project 에 적용.
## Event Graph

- Character 을 지정. 
	- (CP_AnimInstance 에서 진행) ([Event BlueprintInitialize Animation 이벤트 함수보다 Animation Instance 의 NativeInitializeAnimation 가 더 빠르게 호출되기 때문](#캐릭터지정))
- Event Blueprint Update Animation
	- 메인 게임 스레드에서 실행된다.
	1. Update CVar Driven Variables (설정값 업데이트)
		1. [기본 콘솔 값(CVar) 태그](https://framedsc.com/GeneralGuides/ue5_commands.htm)
		2. 커스텀 콘솔 값 세팅 : Project Settings -> Data Driven CVars -> CVars Array 에 원하는 콘솔 값 추가.
		3. 프로젝트에서는 설정 값이 주어지지 않도록 한다.
	2. Update Logic
		1. Update Trajectory
			1. Trajectory History 업데이트 & Future Prediction 을 생성.
			2. 생성된 Trajectory 로 시간에 따라 중력을 적용 및 충돌 체크
			3. 각기 다른 시간 지점에 예상되는 속도 값을 지정.
		2. Update Essential Values
			1. Cache Actor(Capsule) Transform
			2. Cache Offset Root Bone Transform
			3. Cache Acceleration
			4. Cache Velocity
		3. Update States

- BlueprintThreadSafeUpdateAnimation
	- Animation Blueprint 의 Override 함수로 Update Logic 을 호출한다.
	- 해당 함수는 멀티 쓰레드에서 실행될 수 있도록 보장된다.
	- [Event Blueprint Update Animation 과 BlueprintThreadSafeUpdateAnimation 의 차이]

- SetupIput
	- Add Mapping Context
	- CP_Player controller -> BeginPlay() 에서 실행하고 있음.

# AnimGraph

- 순서도
Motion Matching  + (State Controller(State Machine) + Blend Stack) -> Apply Mesh Space Additive(Simple Additive Lean) -> Apply Mesh Space Additive(Simple Aim Offset) -> Slot 'DefaultSlot' -> Offset Root Bone -> Local To Component ->Foot Placement -> Leg IK -> Component To Local ->Pose History -> Output Pose

1. Motion Matching
2. State Controller
	1. Set Blend Stack Anim from Chooser
		1. S_BlendStackInputs (구조체)
			1. Animation Asset
			2. Loop (boolen)
			3. Start Time
			4. Blend Time
			5. [Blend Profile](7_Motion_Matching.md#Blend_Profile)
			6. Tags
	2. Animation for StateMathine Chooser Table
		- nest 구조
		1. stand Stopped -> Stand Idle Loops -> (Stand Idle Loop, Stand Idle Break)
		2. 







State 와 State Machine State 

ShouldTurnInPlace 가만히 서서 회전이 가능하도록 할 것인지에 대한 함수. -> 우클릭으로만 시야를 돌렸을 때 제자리에서 회전한다. -> aim 중에도 가능하게 할 수도 있지 않을까. -> FPS 니까




##  Pre-Event Graph 란


# 우선 순위

1. CP_Instance 에 Character 지정.
```c++
// CP_PlayerAnimInstance.h
	UPROPERTY(BlueprintReadOnly, Category = "Character")
	ACharacter* OwnerCharacter;

	virtual void NativeInitializeAnimation() override;
	
```


 
3. pose Search 플러그인
	1. Pose Search Trajectory Data Type : 
		1. 캐릭터의 현재 위치와 방향을 기준으로 미래 이동 경로를 저장하는 데이터 타입. (즉, 특정 시간 후 캐릭터가 어디에 있을지, 어떤 방향을 향할지를 저장하는데 데이터 구조.)
		2. Pose Search 알고리즘이 가장 적절한 애니메이션을 선택. Current State 와 Future State 를 비교. Motion Matching 시스템과 연동되어 부드러운 애니메이션 전환 제공.
		3. details
			1. Trajectory Mode : Trajectory  데이터를 어떻게 사용할지 결정.
			2. Predictio Time Horizon : 몇 초 후의 이동 궤적을 예측할 지 설정.
			3. Trajectory Samples : Trajectory 를 계산할 샘플 개수( 샘플이 많은 수록 부드러운 예츨 가능) (3 ~ 10, 기본값 : 5)
			4. Trajectory Position Weight : 위치 기반 매칭 가중치 (높을 수록 위치 중심으로 애니메이션 선택)
			5. Trajectory Direction Weight : 방향 기반 매칭 가중치 (높을 수록 방향 중심으로 애니메이션 선택)
			6. Enable Velocity Matching : 속도 일치 여부 (활성화하면 속도도 고려하여 애니메이션 선택)
			7. Velocity Matching Weight : 속도 일치 가중치 (높을 수록 속도가 유사한 애니메이션 선택)
4. Trajectory 플러그인인
	1. Pose Search Generate Trajcectory (for Character)
		1. In History Sampling Interval ( 과거 샘플링 간격)
			1. 과거 데이터에서 샘플을 얼마나 자주 가져올지 설정한다.
			2. 캐릭터의 이동 궤적을 과거에서부터 특정 간격으로 샘플링.
			3. 빠르게 움직이면 낮은 값 (0.1 ~ 0.2), 천천히 움직이면 높은값 0.3 ~ 0.5
		2. In Trajectory History Count
			1. 과거에서 몇 개의 궤적 샘플을 유지할 지 결정
			2. 즉, 과거 몇 초 동안의 데이터를 저장할지를 결정.
			3. 빠르게 움직이는 캐릭터 -> 샘플 개수를 늘려서 더 정확한 히스토리를 유지
		3. In Prediction Sampling Interval (예측 샘플링 간격)
			1. 미래 궤적을 계산할 때 샘플을 얼마나 자주 가져올지 결정.
			2. 높은 값일 수록 미래 예측 간격이 커지고, 낮은 값일수록 더 세밀하게 예측.
			3. 빠르게 움직이는 캐릭터 -> 낮은 값(0.1 ~ 0.2), 천천히 움직이는 캐릭터 -> 높은 값 ( 0.3 ~ 0.5)
		4. In Trajectory Prediction Count (미래 샘플 개수)
			1. 미래 예측을 몇 개의 샘플로 수행할지 결정.
			2. 즉, 미리 몇 초 동안의 데이터를 유지할지를 결정한다.
			3. 빠르게 움직이는 캐릭터 -> 샘플 개수를 늘려서 더 정확한 예측 유지/ 천천히 움직이는 캐릭터 -> 적은 샘츨 개수로도 충분
	2. Pose Search Trajectory World Collision Results
		1. Pose Search 시스템에서 Trajectory 와 World 의 충돌 결과를 저장하는 데이터를 의미한다.
		2. 즉, 캐릭터의 예상 이동 궤적이 월드(환경)와 어떻게 충돌하는지를 나타내는 값
		3. 캐릭터가 장애물과 충돌하는 경우, 올바른 애니메이션을 선택하는 데 도움을 주는 데이터이다.
		4. ex. 캐릭터가 벽에 가까이 있다면 "걷는 애니메이션 " 대신 "벽을 타는 애니메이션" 이 필요할 수 있다. / 점프 중일 때, 천장과 충돌하면 '충돌 후 착지하는 애니메이션' 으로 전환할 필요가 있다.
		5. 데이터 구조
			1. TArray\<FVector> CollisionPoints;  // Trajectory 의 충돌지점.
			2. TArray\<FVector> CollisionNormals; // 충돌한 물체의 표면 법선.
			3. bool bHasCollision; // 충돌 발생 여부
		6. HandleTrajectory World Collisions 함수
			1. Pose Search 시스템에서 캐릭터의 Trajactory 이 환경(월드) 와 충돌하는지를 감지하고 처리하는 기능 제공.
			2. Floor Collisions Offset : 충돌 감지를 시작할 오프셋 (캐릭터의 위치에서 얼마나 떨어진 곳에서 감지할지)
			3. Trace Channel : 충돌을 감지할 트레이스 채널(ex. 월드 정적 오브젝트, 물리 객체 등)
			4. Actors to Ignore : 충돌 감지에서 제외할 액터 목록
			5. Draw Debug Type : 디버그 선을 그릴지 여부
			6. Ignore Self : 자기 자신 을 충돌 감지에서 제외할지 여부
			7. Max Obstacle Height : 감지할 최대 장애물 높이 (너무 낮거나 높은 물체는 무시)
			8. Trace Color : 트레이스의 기본 색상
			9. Trace Hit Color : 충돌 감지된 경우 트레이스 색상
			10. Draw Time :디버그 선을 그릴 지속 시간.
5. Animation Warping 플러그인
6. 
# 함수

TryGetPawnOwner() : 애니메이션을 재생하는 주체가 누구인지 가져올 때 사용된다.


# 질문

- Pre-Event Graph 에 대해서 물어보기

### 조건

- enum
Gait
MovementMode : 땅인지 바닥인지
	MovementModeLastFrame
MovementState : idle 인지 Moving 인지.
	MovementStateLastFrame
Stance

StateMachineState


- structure
CharacterInputState -> WnasToStrafe로 Default 설정.



 ##### 캐릭터지정
 Character 의 Possessed 이벤트에서 Controller 에 IMC 를 매핑 하는 것보다. GameMode 의 Controller 의 BeginPlay() 가 더 빨리 호출된다.
	- 전반적이 순서
		1. GameMode 가 PlayerController 생성
			1. GameModeBase::SpawnPlayerController() 호출
			2. APlayerController 객체 생성
			3. PlayerController::BeginPlay() 가 실행된다.
		2. GameMode 가 Player 의 Character 생성
			4. GameModeBase::SpawnDefaultPawnFor() 호출
			5. 캐릭터 생성
		3. PlayerController 가 캐릭터를 Possess 한다.
			1. PlayerController::Posess(ACharacter*) 호출
			2. Character::Event Possessed 실행됨.


# 주의

MovementComponent 에 접근할 때 Pawn Movement Component 인지, Character Movement Component 인지 주의 해야 한다.


캐릭터의 Movement Mode 를 caching 하고 이전 프레임의 값을 저장. Character 의 Movement Component 의 Movement Mode 를 커스텀한 E_MovementMode 에 정리해서 연결한다.