
### ① UI 및 PC (입력 단계)

- **`OnCustomGameButtonClicked`**: 사용자가 버튼을 누르면 로그를 찍고, 현재 플레이어 컨트롤러(`AGS_MainMenuPC`)에게 방 생성을 요청합니다.
    
- **`HandleCustomGameRequest`**: 로딩 화면을 띄워 사용자가 중복 클릭하지 않게 방어하고, 실제 로직이 담긴 `GameInstance`로 처리를 넘깁니다.
    

### ② GameInstance (HTTP 요청 단계)

- **`StartGameSession`**: 이 코드의 핵심입니다.
    
    - 현재 플레이어의 `UniqueId`(고유 식별자)를 가져와서 누가 방을 만드는지 파악합니다.
        
    - **HTTP POST** 방식을 사용해 외부 주소(`BackendUrl`)로 데이터를 보냅니다.
        
    - 보내는 데이터: `AliasId`(어떤 플릿 그룹을 쓸지)와 `PlayerId`(방장 ID).
        

### ③ 비동기 응답 처리 (예정)

- `OnCreateSessionResponse`: 아직 코드는 없지만, 람다가 방을 만들고 IP 주소를 돌려주면 그 주소로 `ClientTravel`을 호출해 서버에 접속하는 로직이 들어갈 자리입니다.


