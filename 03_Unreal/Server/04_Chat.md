Listen Server 를 이용하여 RPC 를 호출해 채팅을 구현.

1. Level 생성. (name LvChat)
2. GameModeBase 상속 받는 BP 생성 (name BP_ChatMode)
3. PlayerController 상속 받는 BP 생성 (name BP_Controller)
4. World Setting 에서 GameMode Override 에 BP_GameMode, Palyer Controller Class 에 BP_Controller 할당.
5. Change Play Mode and Play Settings -> Multiplayer Oprions 에서 Numbers of Players 를 2 로 설정, Net Mode 를 Play As Listen Server 로 설정.
6. Eidt -> Project Settings -> Maps & Modes 에서 Default GameMode 와 Global Default Server Game Mode 를 BP_CharMode 로 설정. Editor Startup Map 과 Game Default Map 을 LvChat 으로 설정.
7. UI 생성
	1. User Interface -> Widget Blueprint (name Widget_ChatWindow)
	2. 화면 전체를 덮는 HUD 로 만들기
		1. Open Level Print -> Create Widget -> Add to Viewport (UserWidet 을 Viewport 에 추가한다. 즉 게임 화면 위에 UI 요소 ( Hp 바, 미니맵, 메뉴 등) 을 띄우는 데 사용된다.)
			1. Create Widget 에서 Class 에서 Widget Chat Window 선택

![Chat Widget](/Image/Unreal/Chat_Widget.png)
	1. Canvas -> EditableTextbox 를 할당
	2. 언리얼 엔진의 UMG ( Unreal Motion Graphics ) Widget Blueprint 에서 Is Variable 항목은 특정 위젯 요소 를 블루 프린트에서 변수로 사용할 수 있도록 설정하는 옵션이다.해당 항목을 Enable 하게 되면 해당 위젝이 변수로 생성되며, 블루프린트의 그래프에서 접근이 가능해진다.
	3. OnTextCommitted 를 추가해서 이벤트 생성.
		1. OnTextCommitted 이벤트는 사용자가 텍스트 입력을 완료했을 대 호출된다.
	
7. Widget_Chat_Window 에서 Event Dispatchers 추가 (name SetMessageToUserController).
	-  입력 값으로 string 추가 (name Msg)
![Widget Char Widnow](/Image/Unreal/Widget_Chat_Window.png)
	1. Widget 에 입력된 명령이  Enter 버튼일 때
	2. SetMessageToUserController 를 호출하고 Widget 에 입력된 Text 를 대입한다.
	3. Widget 의 입력 칸을 빈칸으로 만든다.

8. BP_Controller 에서 Widget과 Event Dispatcher (Set Manager to User Controller) 를 바인딩한다.
![[PlayerController_With_ChatProject.png]]
	1. [Get Remote Role 노드](00_Function.md#Get_Remote_Role_Node)로 현재 ENetRole 이 None 이 아니라면 즉, 싱글플레이 상태가 아니라면 0.5 초 후에 다음 실행.
	2. Get All Widgets of class 를 통해서 현재 Viewport 에 추가된 특정 위젯 클래스를 가진 모든 위젯 인스턴스를 가져온다.
		1. Widget Class 로는 Widget Chat Window 를 선택하고 
		2. Top Level Only 을 Enable 하여 최상위 부모 위젯(Add to Viewport() 로 직접 추가된 위젯들) 만 가져온다.
		3. Widget Chat Window 로 캐스트 한 뒤 Bind Event ro Set Message to User Controller 노드와 Create Event 노드로 'Set Message to User Controller' event dispatcher 가 발동 되었을 때 처리하는 이벤트를 바인딩 한다. (아직 이벤트를 만들지 않았음으로 None 으로 둔다.)

9. Widget 에 Enter 를 눌렀을 때 호출될 이벤트 를 BP_Controller 과 BP_GameMode에 추가.
	1. BP_Controller 에서
		- Get Broadcast : 현재의 Controller 가 서버에 있다면 전달받은 string 을 print 한다. (Listen Server 는 서버이자 클라이언트이기 때문에 서버가 브로드캐스트한 이벤트를 자기 자신도 수신하게 된다. 따라서 서버에서만 실행되어야 하는 로직을 Has Authority 노드로 구분해준다.)
![[CustomEvent_In_ChatProject1.png]]
	2. BP_GameMode 에서
		- GotMessagefromClient : BP_Controller 의 GotBroadcast 를 호출하고 string 을 전달한다.
![GotMessageFromClient](/Image/Unreal/CustomEvent_In_ChatProject2.png)
	3. BP_Controller 에서 
		- OnSendMsgToServer : 입력 받은 메시지 string 에 User ID 를 붙여서 GameMode 의 GotMessagefromClient 이벤트로 전달한다.
![[CustomEvent_In_ChatProject3.png]]
	4. BP_Controller 에서 사용자 아이디 저장할 이벤트 추가.
		- Event Dispatcher 추가 (name OnLogin.)
		- Event 추가 (OnLoginWithID)
			- Run on Server 설정. enable Reliable

10. Level Blueprint 에서 서버라면 유저 아니디를 Host 로 전송하고 Client 라면 Guest 로 전송.
![ChatLevelBlueprint](/Image/Unreal/ChatLevelBlueprint.png)
![OnsendMsgToServer 를 바인딩.](/Image/Unreal/BindOnSendMsgToServer.png)


# Listen Server 의 생성 부터 출력 까지의 흐름

### 생성

- 호스트의 컴퓨터에서 호스트가 Listen Server 를 실행 시키면

1. GameInstance 생성 (서버)
2. GameInstance 생성 (클라이언트)
3. Level 로드 (서버)
4. GameMode 생성 (서버)
5. Game State 생성 (서버)
6. Game State 복사 (클라이언트)
7. Player Controller 생성 (서버)
8. Player Controller 생성 (생성)
9. Player State 생성 (서버)
10. Player State 복사 (클라이언트)
11. Pawn/ Character 생성 (서버)
12. Pawn / Character 복사 (클라이언트)
13. BeginPlay 생성 (서버)

- 추가로 게스트가 접속 하면

1. GameInstance 생성 (게스트 클라)
2. 게스트의 PlayerController 생성 (서버)
3. GameState 복사 (게스트 클라)
4. 게스트의 Pawn/ Character 생성 (서버)
5. Pawn/ Character 복사 (게스트 클라)
6. BeginPlay() 실행 (게스트 클라)

### 초기화

1. Level 의 Event BeginPlay -> Create Widget -> Add To View port -> Branch (Is Server) -> On Login with ID
2. Player Controller 의 Call On User Login 호출
3. Widget 의 OnLoginEvent -> 초기의 Widget 의 입력 값 설정.

### 출력

1. Widget 에 입력 후 Enter
2. Widget의 SetMessageToUserController (Event Dispatchers)에 입력된 string 전달
3. Player Controller 의 OnSendMsgToServer 호출
4. Player Mode 의 GotMessageFromClient 호출
5. Player Controller 이 GotBroadcast 호출 -> Print String 호출


# Replicates 옵션

Event  또는 Function 의 Replicates 옵션은 네트워크 멀티플레이 환경에서 해당 이벤트가 어디에서 실행되는지를 결정한다.

1. Not Replicated : 서버, 클라 상관 없이 호출된 곳에서 그대로 실행된다.
2. Run On Server : 클라이언트에서 호출하면 서버에서 실행된다. (클라이언트에서 공격버튼을 누르면 서버에서 공격 판정을 수행한다.)
3. Run on Owing Client : 서버 에서 호출하면 특정 클라이언트에서 실행된다.
4. Multicast : 서버에서 호출하면 서버 와 모든 클라이언트에서 실행된다.