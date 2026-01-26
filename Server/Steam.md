Steam Online Subsystem(OSS) 의 설정.

- bUseSteamNetworking
	- 언리얼 엔진의 기본 네트워크 레이어 대신 **Steam의 전용 네트워크 스택**을 사용할지 여부입니다.
	- 일반적인 IP 기반 통신(소켓)은 공유기 설정(NAT) 때문에 외부 사용자가 접속하기 어려운 경우가 많습니다. 이 옵션을 `true`로 하면 Steam의 인프라를 사용하여 복잡한 네트워크 설정 없이도 플레이어 간 연결을 도와줍니다.
- `bAllowP2PPacketRelay`
	- 플레이어 간 직접 연결(Direct Connect)이 불가능할 때 **Steam 서버를 중계기(Relay)로 사용할지** 결정합니다.
	- 두 플레이어의 방화벽이 너무 강력해서 직접 통신이 안 될 때, Steam 릴레이 서버가 중간에서 데이터를 받아서 전달해 줍니다.
- bInitServerOnClient
	- 클라이언트(플레이어) 실행 시 **게임 서버용 Steam API 인터페이스도 함께 초기화**할지 결정합니다.
	- **왜 중요한가 (질문의 핵심):** * 언리얼의 `CreateSession`은 내부적으로 Steam의 "Game Server API"를 사용합니다.