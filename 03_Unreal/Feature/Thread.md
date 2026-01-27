# Main Thread

- 게임의 기본 실행 흐름을 담당하는 주 스레드. UI 업데이트, 게임 로직, 물리 연산, 렌더링 등 핵심 작업을 실행한다.
	- 하지만 모든 연산이 메인 스레드에서 실행되면 CPU 부하가 증가하여 성능이 저하될 수 있다.
- 주요 실행 작업
	- Game Loop
		- 프레임 업데이트, 입력 처리, 렌더링 등
	- User Interface Update
		- HUD, 메뉴, 버튼 클릭 이벤트 등.
	- Physics Calculation
		- 캐릭터 충돌 감지, 중력 적용 등.
	- Animation Event Handling
		- Play Sound, Spawn Actor, Trigger Animation Montage 등.
	- AI Logic, Behavior Trees
		- AI 상태 변화, 네비게이션 경로 계산 등.


# Multi-threading

- 여러 개의 작업을 동시에 실행할 수 있도록 보조 스레드를 활용하는 방식. 게임의 성능을 최적화 하고 CPU 자원을 효율적으로 활용한다.
	- 게임이 실행 중일 때 CPU 의 여러 코어를 활용하여 작업을 분산.
	- 메인 스레드의 부하를 줄이고 게임의 성능을 향상시킨다.

- 주요 실행 작업
	- AI Pathfinding
		- Nav Mesh 연산
	-  Async Loading (로딩 화면 처리)
		- 맵, 오브젝트, 리소스 로딩을 별도 스레드에서 실행하여 프레임 드롭 방지.
	- Animation Calculation
		- BlueprintThreadSafeUpdateAnimation 에서 실행 가능
	- Heavy Computation (데이터 연산)
		-  수천 개의 오브젝트 충돌 연산, 대규모 행렬 연산 등


# Thread 데이터 취합

- 게임 엔진은 프레임 단위로 데이터를 동기화 하여 일관된 화면을 렌더링한다.
- Main Thread 는 한 프레임 내에서 처리될 데이터의 기준을 정하고, Multi-thread 는 그 데이터를 가공하여 제공 하는 역할을 한다.
- Multi-thread 연산 결과가 제때 도착하지 못하면 이전 프레임의 데이터를 사용하여 게임이 끊기지 않도록 한다.

### Main Thread 와 Mulri Thread 의 동기화 (Synchronization)

1. 프레임 단위 동기화
	1. 각 프레임에서 데이터를 취합하여 '그릴 수 있는 가장 최신 상태' 를 만든 후 렌더링한다.
		1. ex. 애니메이션이 멀티스레드에서 계산되고 있는데, 해당 결과가 이번 프레임에 반영되지 않으면 이전 프레임의 애니메이션 상태를 사용하여 부드러운 전환을 유지한다.
2. 데이터 락킹 (Data Locking)
	1. 멀티스레드에서 작업하는 동안, 다른 스레드가 동일한 데이터를 수정하면 충돌이 발생할 수 있다. 이를 방지하기 위해 Lock 이나 Read Write Separatio  기법을 사용한다.
3. Double Buffering
	1. 게임에서는 최신 데이터를 안전하게 반영하기 위해 버퍼를 두 개(현재 프레임 버퍼, 이전 프레임 버퍼) 를 사용한다
	2. 새로운 데이터가 준비되지 않았다면 이전 프레임의 데이터를 사용하여 일관성을 유지한다.

### 게임에서 한 프레임이 그려지는 과정

- Game Thread
	1. 입력 처리
	2. 게임 로직 업데이트
	3. 물리 연산 실행
	4. 애니메이션 처리 요청 (Update Animation State)
	5. 멀티스레드로 애니메이션 연산, AI 연산 요청

-> AI 계산, 애니메이션 보간

- 멀티스레드 실행
	6. 멀티스레드 연산 완료 후 결과를 메인 스레드로 전달 (Sync)
	7. 렌더링 데이터를 생성 (Render Setup)

-> 렌더링 요청 전달

- 렌더 스레드 실행
	1. GPU 에서 최종 화면을 렌더링
# 싱글 vs 멀티

### Single Play

- 로컬에서 모든 데이터를 처리하므로 프레임 동기화만 잘 맞추면 된다.
- 멀티 스레드가 한 프레임 내에서 연산을 마치지 못하면 이전 프레임 데이터를 사용하여 부드러운 전환을 유지한다.

### Multi Play

- 서버와 클라이언트가 각자 독립적으로 게임을 실행하며, 일정 간격으로 데이터를 동기화 해야 한다.
- 서버가 최신 데이터를 보내줘도, 클라이언트에서 해당 데이터를 사용할 프레임이 다를 수 있다.
	- -> Prediction (예측) 과 Correction (보정) 기법 필요.

# Animation 에서 Threading

Unreal Engine 의 Animation Sample Project 에서 Animation 은 다음의 두 함수에서 Update 된다.

1. BlueprintAnimationUpdate (이벤트)
	1. 메인 스레드에서 실행해야 하는 작업(게임 이벤트 처리)를 안전하게 실행할 수 있도록 한다.
		1. 사운드 재생, UI 업데이트 , 캐릭터 애니메이션 상태 변경.
2. BlueprintThreadSaffeUpdateAnimation (ABP override 함수)
	1. 멀티스레드에서 실행 가능한 연산만 처리해야 한다.
		1. Speed, Direction, IsJumping, IsCrouching 등의 애니메이션 관련 변수 업데이트는 멀티 스레드에서 실행 가능하다. 하지만 Play Sound (사운드 재생), Spawn Actor, UI 업데이트 와 같은 작업은 크래시 위험이 있기 때문에 멀티스레드에서 호출이 불가능하다.

- 엔진은 위의 두 함수를 번갈아 호출한다 왜일까?
	- 멀티 스레딩 환경에서 안전성을 보장하기 위해서
	- 게임의 최적화를 위해 일부 연산을 멀티스레드에서 실행 가능하도록 하기 위해서

# Thread_와_UObject

Work Thread 는 멀티 스레딩 환경에서 특정 작업을 백그라운드에서 수행하는 보조 스레드를 의미한다.
UObject 는 기본적으로 Main Thread에서만 안전하게 접근 가능하며, Worker Thread 에서 직접 접근하면 충돌이나 예기치 않은 동작이 발생할 수 있다. 
	언리얼 엔진의 UObject 시스템은 기본적으로 Garbage Colliector(GC) 를 통해 객체의 수명을 관리한다. 하지만 GC 는 Main Thread 에서만 실행되기 때문에 Worker Thread 에서 UObject 를 참조하면, 객체가 삭제되었을 때 크래시가 발생할 수 있다. 이에 Multi Thread 에서 UObject 에 관련된 작업을 하기 위해서는 작업 시작 전에 해당 UObject 가 유효한지 검사해야 한다.
따라서 UObject 유효설을 미리 Main Thread  에서 저장해 두고 Worker Thread 에서는 캐시된 데이터를 사용하는 방식이 필요하다.