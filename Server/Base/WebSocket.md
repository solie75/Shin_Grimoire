웹소켓은 OSI 7계층 중 7계층(Application Layer)에 위치하는 통신 프로토콜이다. 단일 TCP 연결(TCP Connection) 위에서 전이중 통신(Full-Duplex Communication) 을 제공하는 것이 핵심이다.
- 전이중(Full-Duplex) : 데이터의 송신과 수신이 동시에 양방향으로 가능하다.
- 지속적 연결(Persistent Connection) : 한 번 연결이 수립되면 클라이언트나 서버가 연결을 종료하기 전까지 세션이 유지된다.

웹소켓 통신은 크게 3단계로 이루어진다.
1. 핸드셰이크 (Handshake)
	1. 웹소켓은 독자적인 포트를 쓰지 않고 HTTP(80)나 HTTPS(443) 포트를 공유한다. 따라서 최초 접속은 HTTP 요청으로 시작하여 프로토콜을 격상(Upgrate) 시키는 방식을 사용한다.
	2. Client Request (HTTP)
		1. 클라이언트가 서버에 연결 요청을 보낸다. 이때 헤더에 '이 연결을 웹소켓으로 바꾸고 싶다' 라는 정보를 담는다.
			1. Upgrate : websocket
			2. Connection : Upgrate
			3. Sec-WebSocket-Key : 보안 및 유효성 검증을 위한 키 값.
	3. Server Response (HTTP 101)
		1. 서버가 이를 수락하면 HTTP 101 Switching Protocols 응답 코드를 반환한다.
			1. HTTP/1.1 101 Switching Protocols
			2. Upgrade : websocket
			3. Connection : Upgrade
	4. Connection Established
		1. 이 응답이 완료되는 즉시, 해당 TCP 연결을 HTTP 프로토콜에서 WebSocket 프로토콜로 전환된다.
2. 데이터 전송 (Data Transfer)
	1. 연결이 수립된 후에는 더 이상 HTTP 헤더(Header) 를 주고 받지 않는다. 대신 데이터를 프레임(Frame) 이라는 작은 단위로 패킷화 하여 주고 받는다.
	2. 저오버헤드(Low Overhead)
		1. HTTP 는 매 요청마다 수백 바이트의 헤더를 보내야 하지만, 웹소켓 프레임의 헤더는 최소 2바이트로 매우 가볍다.
	3. 실시간 푸시(Server Push)
		1. 클라이언트의 요청이 없어도 서버가 원할 때 즉시 데이터를 클라이언트로 전송할 수 있다.

- HTTP vs WebSocket 기술적 비교

| 특징 (Feature)          | HTTP (RESTful 등)                                   | WebSocket                                            |
| :-------------------- | :------------------------------------------------- | :--------------------------------------------------- |
| **통신 방식**             | **단방향 (Request-Response)**<br>클라이언트가 요청해야만 서버가 응답  | **양방향 (Full-Duplex)**<br>서버와 클라이언트가 동시에 데이터 송수신 가능   |
| **연결 수명 (Lifecycle)** | **비지속적 (Stateless)**<br>요청 처리 후 연결을 끊는 것이 기본*      | **지속적 (Stateful)**<br>최초 핸드셰이크(Handshake) 후 연결 영구 유지 |
| **데이터 전송 트리거**        | 클라이언트의 요청(Request)이 필수                             | 서버가 원할 때 언제든 전송 가능 (**Server Push**)                 |
| **오버헤드 (Overhead)**   | **높음**<br>매 요청마다 헤더(Cookie, User-Agent 등)를 포함하여 전송 | **낮음**<br>최초 접속 후에는 헤더 없이 프레임(Frame) 단위의 경량 데이터만 전송  |
| **프로토콜 스키마**          | `http://` 또는 `https://`                            | `ws://` 또는 `wss://`                                  |
| **GameLift 활용**       | AWS API 호출 (플릿 생성, 상태 조회 등)                        | **게임 서버 프로세스 제어** (세션 시작 명령, 헬스 체크 등 실시간 통신)         |
- GameLift 아키텍쳐에서의 역할
	- GameLift Service SDK Endpoint 가 웹소켓 URL 인 이유는 GameLift 서비스 (백엔드) 와 게임 서버(사용자 PC/ EC2) 간의 제어 흐름(Control Plane) 때문이다.
		1. 초기화 (Init)
			- 게임 서버 프로세스가 켜지면, InitSDK() 함수를 통해 GameLift 서비스의 웹소켓 URL 로 핸드셰이크를 시도한다.
		2. 연결 유지 (Keep-Alive)
			- 연결이 선공하면 게임 서버는 GameLift 서비스와 끊어지지 않는 TCP 파이프 라인을 형성한다.
		3. 이벤트 수신 (OnStartGameSession)
			- 플레이어 매칭이 성사되면, GameLift 서비스는 별도의 HTTP 요청을 기다리지 않고, 열려있는 웹소켓 파이프를 통해 '게임세션 시작해' 라는 메시지(패킷) 을 게임서버로 즉시 push 한다.
			- 게임 서버는 이 메시지를 받는 즉시 콜백 함수를 실행하여 게임 맵을 로딩한다.
		4. 상태 보고(Health Check)
			- 게임 서버는 이 연결을 통해 주기적으로 '나 살아있음' 이라는 핑을 GameLift 에게 보낸다. 몇 번 연속으로 응답이 없다면 GameLift 는 해당 서버를 비정상으로 간주하고 종료시킨다.
	- 즉 GameLift 에서 웹소켓을 사용하는 이유는 서버의 상태를 1ms 단위의 지연없이 실시간으로 관리하고, 외부(매치메이커)의 개입이 있을 때 즉각적으로 게임 서버 프로세스를 제어하기 위함이다. ^f1aace
	- 