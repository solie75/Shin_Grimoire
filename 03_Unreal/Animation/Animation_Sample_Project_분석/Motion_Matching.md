# 설명

1. 포즈 검색 데이터 베이스 (포즈와 궤적 데이터를 정의하는 포즈 검색 스키마 자산을 사용한다.)
2. 포즈 히스토리 노드 (포즈 + 궤적데이터 를 기반으로 애니메이션 데이터 조회 및 매칭.

해당 설정에서 Pose Search Database asset 이 Update Motion Matching 함수에서 설정.
Blend Time float input 을 사용하면 동적으로 blend 시간을 변경할 수 있다.
이는 서로 다른 종류의 애니메이션이 서로 다른 속도로 블렌딩 되어야 할 때 유용.
Get_MMBlendTime 으로 BlendTime 입력 설정.
Update MotionMatching function : Chooser asset 을 evaluate 하여 array of motion matching databae 출력.
-> locomotion data 를 여러 dtabases 로 분리시키고 Chooser 는 motion matching Node 가 프레임마다 검색할 Animation Data 를 관련된 것들로 필터링할 수 있게 한다.
Update_MotionMatching_PostSelection() : Motion Matcing 결과를 기반으로 추가 로직을 수행할 수 있게 한다.
선택된 애니메이션이 포함된 DataBase 를 캐싱 -> 해당 애니메이션이 가진 Tag 를 가져와 선택 로직에 사용할 수 있도록 함.
Tag 는 스레드 안전성 문제로 Event Graph에서 가져온다. (CurrentDatabaseTags 참조)
Motion Matching 내부의 Blend Stack Graph 에서 방향 왜곡 및 조향 처리 담당.

# 구현

1. Pose Search plugIn 추가.

2. Chooser plugin
	1. Dense 와 Sparse 의 차이는...? 어떻게 다르게 작동하는지는 모르겠으나 하나의 PSD(Pose Search Database) 에 Dense 가 Sparse 보다 많은 Animation Sequence Asset 을 가지고 있다. (거리에 따른 애니메이션 축약에 관해 사용된다고 설명된다.)
	2. LOD 를 결정할 Chooser생성, 각 LOD 에 해당 하는 Chooser 생성.
		1. Evaluate Nest 로 각 LOD 에 해당 하는 Chooser 를 추가.
		2. PA_CharacterAnimInstance 에서 LOD 변수 추가.
		3. 지금은 LOD 0 단계(가장 자세한 단계)의 Chooser 를 우선 구현한다.

3. Update MotionMatching function 추가.
	1. 추가 : motion matching Node -> details -> Functions -> Motion Matching -> On Update -> + -> rename to Update_MotionMatching.
	2. 설명 : 매 프레임마다 Motion Matching Node 실행 -> Motion matchign Update Function 실행. Chooser asset 을 evaluate 하여 현재 사용해야할 Array of Pose Search Database asset 을 반환.
		1. 이때 배열을 반환하는 이유 : 달리면서 조준하는 경우. 하반신은 Run Animation 이 필요하고 상반신은 Aim 애니메이션이 필요하기 때문에 evaluate Chooser 에서 두 개의 PSD 를 반환해야 한다.

4. Chooser 에서 기준점이 될 Enum 생성.
	1. Movement Mode, Movement State, Stance, Gait
	2. ABP 의 Variable 에 위의 Enum 들을 변수로 추가.
5. Chooser 에 Nest 를 추가하고 위의 Enum 을 판단 기준으로 추가.
	1. Nest 에 맞게 Enum 값을 지정.
![Chooser Image](/Image/Unreal/ChooserLOD1_in_PA_proj.png)
6. PSD 에 사용될 Schema 생성.