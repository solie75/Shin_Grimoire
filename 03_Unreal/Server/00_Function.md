# Get_Remote_Role_Node

- 서버 또는 클라이언트에서 실행 중인 액터가, remote 에서 어떤 역할을 가지는지 반환하는 함수.
- ENetRole 타입 반환.
	- ROLE_None : 네트워크 역할이 없음 (싱글 플레이어 상태)
	- ROLE_SimulteProxy : 리모트에서 시뮬레이션하는 복제된 엑터 (클라이언트가 조작할 수 없음. 일반 NPC)
	- ROLE_AutonomousProxy : 리모트에서 직접 제어하는 액터 (클라이언트에서 직접 제어. 보통 플레이어 캐릭터)
	- ROLE_Authority : 서버에서 실행되는 액터 (서버가 직접 관리하는 객체)

# Get_Local_Role_Node

- 현재 로컬에서 실행 중인 액터의 네트워크 역할을 가져오는 기능을 한다.
- 블루프린트에서 네트워크 환경을 파악할 때 사용된다.
- ENetRole 값 반환.


# Has_Authority_Node

- 현재 실행 중인 Actor 가 서버와 클라이언트 중 어디에서 실행되고 있는지를 확인하는 노드.
- 반환 값이 Ture 이면 서버에서 실행 중.
- 반환 값이 False 이면 클라이언트에서 실행 중.
