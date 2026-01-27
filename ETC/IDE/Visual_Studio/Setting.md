# Show_Open_Tab

열린 탭의 표시 방법.
Tools -> Options -> Enviroment ->Tabs and Windows -> Show tabs in multiple rows 를 enable 하면 열린 모든 tab 들이 전부 표현된다. / disable 하면 한 줄로 표현되고 일정 수가 넘어가면 나머지는 드롭 다운 형식으로 표현된다.

# 관리자_권한으로_실행_지정

프로젝트 Properties -> Linker -> Manifest File -> UAC Execution Level 을 requireAdministrator 로 지정.

위의 과정은 visual studio 프로젝트의 실행 파일에 관리자 권한이 필요하도록 지정한다. Application manifest 에 requireAdministrator UAC 레벨을 추가하여 프로그램이 실행될 때 관리자 권한을 요청하도록 한다.

\* UAV(User Account Control) 은 Windows 에서 사용자의 동의 없이 관리자 권한으로 애플리케이션이 실행되지 않도록 보호하는 보안 메커니즘이다.

# Tool Bar Customize

### **1. 툴바 너비 수정 방법**

1. 상단 툴바의 빈 공간이나 아이콘 위에서 **마우스 우클릭**을 합니다.
2. 가장 아래에 있는 **`사용자 지정(Customize)...`**을 클릭합니다. 3. 새로 뜨는 창에서 **`명령(Commands)`** 탭을 선택합니다.
3. **`도구 모음(Toolbar)`** 라디오 버튼을 선택하고, 옆의 드롭다운 목록에서 **`표준(Standard)`**을 선택합니다. _(보통 저 위치에 있는 툴바가 '표준' 툴바입니다.)_
4. 아래 목록(컨트롤)에서 **`솔루션 구성(Solution Configurations)`**을 찾아 선택합니다.
5. 오른쪽에 있는 **`선택 사항 수정(Modify Selection)`** 버튼을 클릭합니다.
6. 메뉴가 열리면 **`너비(Width)`** 항목을 찾습니다.
    - 기본값이 `65` 정도로 되어 있을 텐데, 이를 **`150`**이나 **`200`** 정도로 넉넉하게 입력하고 엔터를 칩니다.
7. `닫기`를 누르면 즉시 적용됩니다.