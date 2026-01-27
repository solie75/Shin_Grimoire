### Unreal Replication

Network Framework API 를 상속받아 구현한 구현체

- Actor Replication 
	- 서버에서 특정 Actor 를 클라이언트에 자동으로 동기화 (서버에서 생성한 actor 를 클라이언트에서도 동일하세 생성하고 유지하는 방식)
- Property Replication
	- 서버에서 변경된 특정 변수를 클라이언트에 자동으로 전송.
	- UPROPERTY(Replicated) 또는 UPEROPERTY(ReplicatedUsing=OnRep_FunctionName) 사용하여 특정 변수를 동기화.
- RepNotify
	- 속성이 변경될 때 자동으로 호출
	- ReplicatedUsing-OnRep_FunctionName 을 사용하면 값이 변경될 때 지정한 함수를 자동 호출.
	- 클라이언트에서만 호출. (Health 값 바뀔 때 UI 업데이트, 문 열릴 때 애니메이션 재생, 무기 장전 시 총기 상태 변경 등.)
- RPCs (Remote Procedure Calls, 원격 프로시저 호출)
	- 서버와 클라이언트 간 함수 호출을 가능하게 하는 기능.
	- 네트워크 환경에는 데이터 변경을 클라이언트가 아닌 서버만 가능해야 한다.
	- 유형 : UFUNCTION 을 통해 서버, 클라이언트, 멀티캐스트 에서 동작하는 RPC 를 지정한다.
		- Server : 클라이언트 -> 서버 로 실행; 서버에서 중요한 로직 처리 (공격 요청, 총 발사, 아이템 사용 등)
		- Client : 서버 -> 특정 클라이언트 로 실행; 서버가 특정 클라이언트에게 정보를 보내는 경우 (UI 업데이트, 피드백)
		- Multicast : 서버 -> 모든 클라이언트; 모든 클라이언트에게 특정 액션을 실행할 때 (폭발 효과, 애니메이션 동기화)
	- 전송 속성
		- Reliable
			- 패킷 손실 시 재전송 한다. 반드시 실행되어야 하는 이벤트 (공격 요청, 점수 갱신)
		- Unreliable
			- 패킷 손실 시 무시한다. 손실 되어도 괜찮은 이벤트 (UI 덜 중요한 애니메이션 )

### Event 의 Replicates 속성 옵션

해당 옵션은 Event 가 실행된 위치(서버/ 클라이언트) 와 복제 방식을 설정한다.

- Not Replicated : 네트워크와 관계없이 단순히 로컬에서만 실행.
- Multicast : 서버 -> 모든 클라이언트 호출. 서버가 호출하면 모든 클라이언트에서 실행한다.
- Run on Server : 클라이언트 -> 서버 호출. 클라이언트가 요청하면 서버에서 실행한다.
- Run on Owning Client : 서버 -> 특정 클라이언트. actor 를 소유한 특정 클라이언트의.
# 언리얼 객체 간 통신

언리얼에서 각 다른 객체 간의 통신을 위해 이벤트를 생성하고 이를 호출할 수 있다.

PlayerController 를 상속 받은 BP_Controller 의 커스텀 이벤트 'OnCustomEvent' 가 있다고 할 때, 이를 호출하는 GameMode 또는 Level Blueprint 에서 형변환은 통해 직접 호출할 수 있다.
GetPlayerController -> Cast To BP_Controller -> OnCustomEvent
이 경우, 객체가 없다면 Null Point Exceptin 문제가 생길 수 있다.
이를 방지하기 위해 Event Dispatcher 를 사용한다.

# Event Dispatcher

A 라는 객체에  Z 라는 event dispatcher 를 등록하고 B 라는 객체에 A 의 Z 이벤트를 구독(바인딩.)하는 원리로 작동한다. A 의 Z 가 호출되면 Z 를 구독 중인 다른 이벤트들을 호출한다.

# 서버와 클라이언트의 명령 전달.

![네트워크 상의 언리얼 객체 위치](/Image/Unreal/Unreal_Structure_In_Network_Env.png)

플레이어 컨트롤러를 중심으로 서버와 클라이언트 사이의 언리얼 요소들을 연결한다.

![서버와 클라이언트 간의 연결.](/Image/Unreal/Unreal_Structure_In_Network_Env2.png)