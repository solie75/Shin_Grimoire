1. Gethub -> Settings -> Developer Settings -> Tokens (Classic) -> 토큰 키 생성.
2. 언리얼 -> 계정 -> 앱 및 계정 -> Github 연결
3. https://githubcom/EpicGames 에서 Invitation 메시지에서 'view invitation' 버튼 클릭 -> https://githubcom/EpicGames/UnrealEngine 접근 가능
4. https://githubcom/EpicGames/UnrealEngine http 복사 -> 토큰과 복사한 http 를 결합 (https://토큰@github.com/EpicGames/UnrealEngine.git)
5. CMD 관리자 권한 실행 -> 위에 클론한 폴더로 경로 이동 -> Setup.bat 실행 -> GenerateProjectFiles.bat 실행
6. 빌드
	1. 생성한 UE5.sln -> Development Editor, Win64, UE5 선택 -> 솔루션 탐색기에서 Engine/UE5 r click -> Build
	2. cmd 관리자 권한 실행 -> 언리얼 폴거 -> Engine\Build\BatchFiles\RunUAT.bat BuildGraph -target="Make Installed Build Win64" -script=Engine/Build/InstalledEngineBuild.xml -clean -set:HostPlatformOnly=true -set:WithDDC=false
	3. 빌드가 성공하면 LocalBuilds 폴더 생성됨
		1. 언리얼 폴더 경로\LocalBuilds\Engine\Windows\Engine\Binaries\Win64\UnrealEditor.exe 실행
