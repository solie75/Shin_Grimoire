# Show_Open_Tab

열린 탭의 표시 방법.
Tools -> Options -> Enviroment ->Tabs and Windows -> Show tabs in multiple rows 를 enable 하면 열린 모든 tab 들이 전부 표현된다. / disable 하면 한 줄로 표현되고 일정 수가 넘어가면 나머지는 드롭 다운 형식으로 표현된다.

# 관리자_권한으로_실행_지정

프로젝트 Properties -> Linker -> Manifest File -> UAC Execution Level 을 requireAdministrator 로 지정.

위의 과정은 visual studio 프로젝트의 실행 파일에 관리자 권한이 필요하도록 지정한다. Application manifest 에 requireAdministrator UAC 레벨을 추가하여 프로그램이 실행될 때 관리자 권한을 요청하도록 한다.

\* UAV(User Account Control) 은 Windows 에서 사용자의 동의 없이 관리자 권한으로 애플리케이션이 실행되지 않도록 보호하는 보안 메커니즘이다.