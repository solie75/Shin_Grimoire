1. Create Animation Blueprint Template
![[Image/GAS_Project/GASproj_CreateABT.png]]

2. Motion Matching 의 Update Function 에서 Evaluate Chooser 가 한 개의 고유 Chooser 를 미리 지정해 놓고 있어야 한다. 
	- 생각 1 : Motion Matching 에 대한 Update 함수를 각 캐릭터 별로 만들어 놓고 각 캐릭터 ABP 단에서 Motion Matching 에 바인딩 해준다. -> Evaluate Chooser 는 Chooser 가 요구하는 ABP 클래스를 입력 값으로 넣어 주어야 하는데 ABT 에서는 Cast ABP 조차 불가능(ABT 는 런타임 로직 불가 )하기 때문에 Evaluate Chooser 자체를 ABT 가 아닌 ABP 에서 진행해야 한다 
		- 그렇다면 각 ABP 에서 Evaluate Chooser 의 결과 값을 Seeker AnimInstance 의 변수 값으로 캐싱하고 ABT 에서 해당 변수 값을 가져다가 사용하는 방법. -> X (하나의 Tick 에 결과 값을 너무 여러번 거치게 된다.)
	- 생각 2 : Update 함수를 c++ 단에서 구현
		- Evaluate 에 ABP 를 입력해 주어야 하는데 c++ 에서는 불가능하다.
		- 왜 ABP 를 입력해 주어야 할까? -> Flag 가 되는 변수 또는 함수가 ABP 에 있기 때문에 ->그러면 입력 값을 Custom Struct 를 만들어 지정하고 해당 Struct 에 Flag 가 되는 변수와 함수를 집어 넣는다면? ->
		- 애시당초에 ABP 를 입력하지 않고 GS_SeekerAnim 을 입력하면 되는거 아닌가..? 캐릭터 마다 Chooser 의 flag 값이 다를 일이 있을까? 
			- Motino Matching Update 를 



- 계획
1. ABT 에서 주요 ABP 코드 구현 ( Motion Matching, Foot Root Motion)
2. 각 ABP 에서 ABT 에 대해 Licked Anim Graph 로 접근.
3. ABT 의 결과 값 포즈를 가져온다 -> 기본 Locomotion 을 모듈화 성공.

문제 1
1. ABT 의 Motion Matcing 의 Update 함수에서 Evaluate Chooser 에 대하여 
	1. Evaluate 의 Chooser 를 지정하는 문제.
	2. Chooser 에 입력될 ABP 의 Cast 가 불가능한 문제.

해결 

1. 문제 1-2 에 대해 : Chooser 입력 값을 캐릭터에서 Struct로 생성하여 넘긴다.
	1. `FMyChooserInputStruct` Struct 생성
	2. `ABT`에 해당 Struct 값을 저장할 변수 선언
	3. `ABP` 또는 캐릭터에서 Struct 값을 생성해 주입
	4. 흐름 : CustomStruct.h 생성 -> GS_SeekerAnimInstance 에서 CustomStruct 를 멤버 변수로 선언  + Chooser 의 Input 타입을 CustomStruct 으로 설정.
2. 문제 1-1 에 대해 : Evaluate Proxy + Proxy Table + Chooser Table 의 조합 방식을 사용한다.

문제 2
1. CustomStruct 를 인자로 받은 Chooser 는 함수를 Flag 로 사용하는데 그럼 함수들을 Struct 에 구현해야하나?
2. 결국 CustomStruct 도 특정 ABP 에 속한 멤버 함수인데 ABT 에 전달하기에 Chooser 와 실제 멤버 변수인 CustomStruct 간의 연결에 문제가 없는가?
3. USTRUCT 에서는 UFUNCTION 을 선언할 수 없다. Chooser 에서는s UFUNCTIO 의 BlueprintPure 와 meta=(BlueprintThreadSafe ="true") 가 필요한데 어떻게 해결할 것인가?

해결
1.  문제 2-3 에 대해 : USTRUCT 대신 UOBJECT 를 사용한다-> 이러면 GS_SeekerAnimInstance 를 주는것과 다를게 뭐지..?: