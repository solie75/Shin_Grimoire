![[Pasted image 20250218223134.png]]




3. Animation Blueprint (ABP) 생성
	1. Event Graph
		1. State Machine( name State Controller)
			1. On State Entry
		2. 

# Animation Blueprint ABP

##### Rules

4. 
### Event Graph

##### State Machine

- State Machine 은 여러 개의 상태를 관리하고 상태 전환을 제어하는 시스템이다.

- State : 캐릭터가 현재 어떤 애니메이션 상태에 있는지 정의 하는 개별적인 단위. 캐릭터가 게임 내에서 행동을 바꾸면, State Machine 은 현재 상태(State) 를 다른 상태로 변경하면서 애니메이션을 전환한다. 각 State 는 특정 애니메이션을 담당한다. 
	- Details
		- State
			- On State Entry
				- 애니메이션 상태(State) 가 활성화 될 때 또는 전환 될 때 한 번만 호출한 이벤트(특정 동작 수행)를 설정한다.
				- 특정 변수 값을 변경, 사운드/이펙트 실행, 게임플레이 이벤트 호출 등
				- 일반적으로 캐릭터가 특정 애니메이션에 진입할 때 트리거되는 이벤트를 설정할 때 사용된다.

# ETC file
### Chooser

플러그인 에서 'Chooser' 추가.
특정 조건에 따라서 다양한 값을 선택하는 기능을 제공.
기본적인 블루프린트 또는 c++ 조건문을 사용하지 않고도 더 직관적이고 유연한 방식으로 값을 선택할 수 있다.
메타데이터 기반으로 의사 결정을 자동화 할 때 강력한 도구로 활용된다.

- 기능
	- 조건 기반 선택
		- 특정 조건을 선택하고 그에 따라 특정 값 반환 : 캐릭터의 위치에 따라 다른 애니메이션 선택
	- Blend 및 Randomization 지원 
		- 단순한 값 선택 뿐 아니라, 여러 개의 후보 중에서 확률적으로 선택하는 기능도 지원.
- 종류
	- Chooser Table
		- 조건에 따라 특정 데이터를 반환하는 테이블 기반 시스템
			- ex. 캐릭터의 속성등에 따라 다른 애니메이션 블렌드 설정을 선택할 수 있다.
	- State Chooser
		- 특정 조건에 따라 애니메이션상태를 결정.
			- ex. 플레이어의 이동 속도, 점프 여부 등을 기반으로 애니메이션 선택
	- Task Chooser
		- AI 또는 게임 플레이 로직에서 특정 작업 (Task) 을 선택하는 시스템
			- ex. NPC의 현재 상태에 따라 '공격', '방어', '도망' 중 하나를 선택하는 AI 로직 생성.

- 생성
	- 우클릭 -> Animation -> Chooser -> Create Chooser Table
		- Chooser Type (반환 값) 으로 Animation Chooser 선택
			- Anim Class 로 ABP 클래스 지정 : Animation Chooser 는 특정 애니메이션 블루 프린트 와 연동하여 해당 클레스에서 사용할 애니메이션을 자동으로 선택하는 역할을 한다. 따라서 어떤 ABP 에서 사용할 Chooser 인지 ABP 를 지정해 주어야 한다.

- 사용
	- Row 에는 반환할 에셋 또는 값을, Colume 에는 조건이 되는 값을 저장한다.
		- 이때 Column 으로 생성된 변수에 지정된 ABP 의 Valuable 을 지정할 수 있다.
			- 하나의 Column 에서 여러 개의 설정을 동시 지정할 수 있다.
	- Table
		- Result
			- Add Row
				- Add FallbackResult
					- 기본 값(Default Value) 역할
					- Chooser 에서 모든 조건이 불일치 할 때 반환되는 값.
				- Add Asset
					- 특정 Asset 을 직접 선택하여 반환.
					- Chooser 의 조건이 충족되었을 때 고정된 에셋을 반환.
				- Add Asset (Soft Reference)
					- Soft Reference (소프트 참조) 방식으로 에셋을 선택하여 반환.
						- Soft Reference 는 메모리를 즉시 로드하지 않고 필요할 때만 로드하는 방식. ( 성능 최적화에 도움이 된다.)
						- ex
							- 애니메이션이나 오디오 등 필요할 때만 로드해야 하는 리소스에 대해서 생성.
							- 대형 오픈월드 게임에서 필요할 때만 특정 애니메이션 불러오도록 설정 가능
							- 대량의 에셋이 사용되는 경우, 성능 최적화를 위해 사용.
								- 특정 시역에서만 사용되는 에셋을 필요할 때만 로드하도록 설정가능.
				- Evaluate Chooser
					- 다른 Chooser 를 실행하고 그 결과를 반환
					- Chooser 내에서 또 다른 Chooser 를 실행하여 계층적인 (하나의 기준이 아니라 여러 개의 조건을 순차적으로 평가하는) 선택 가능.
					- ex. 무기 종류 -> 캐릭터 상태 -> 속도에 따라 다르게 선택하는 경우.
				- Add Nested Chooser
					- 현재 Chooser 내부에 또 다른 Chooser 를 포함하여 사용.
					- Evaluate Chooser 는 외부 Chooser 를 호출하는 반면, Nested Chooser 는 현재 Chooser 내부에서만 동작하는 추가 Chooser 이다.
					- 단일 Chooser 에서 처리할 수 없는 복잡한 로직이 필요할 때 사용.
				- Add Lookup Proxy
					- 다른 Data Source 에서 값을 가져와서 사용할 수 있도록 연결하는 기능.
					- Blueprint 나 c++ 에서 정의된 특정 변수나 데이터 테이블을 참조하여 Chooser 에서 사용 가능.
					- ex. 특정 게임플레이 변수 ( 현재 hp, 캐릭터 클래스 등)에 따라 애니메이션을 선택.
					- 