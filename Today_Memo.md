
# Unreal Engine 코드 빌드하기

- Epic 에서 GitHub 연동.
- Unreal Engine github 클론.

- 폴더의 주소창에 cmd 를 Enter 치면 해당 폴더 경로 기준으로 터미널을 열 수 있다.

- 엔진의 바이너리 콘텐츠 를 다운로드하고 필수 구성 요소를 설치하며 언리얼 파일 연결을 설정하기 위해 Batch File 일부를 실행해야 한다.
	- Setup.bat 실행.
	- GenerateProjectFiles.bat 실행.
	-> sln 파일 생성됨.

- sln 파일을 통해 언리얼 엔진 오픈
	- 구성을 Development Editor, 플랫폼을 Win64 로 설정.
	- 프로젝트 (UE5) 우클릭 -> 'Set as Startup Project' 로 설정.
	- 프로젝트 (UE5) 우클릭 -> 'Build' 시작.


# Template Project

- Template project 의 uproject 우클릭 -> Switch Unreal Engine Version 선택 -> 위에서 빌드한 엔진 을 선택.
	- 이때 에픽게임즈 런처로 설치한 binary build 가 아니라 직접 빌드한 Source Build 를 설정한다. 이때 Source Build 는 Binary 가 아니라 폴더 경로 또는 GUID로 인식된다.
- 생성된 TemplateProject 의 Sln 로 VS 를 연다
	- Configuration 을 Development Editor로, Platform 을 Win64 로 지정한다.
	- 게임 프로젝트만 빌드한다 (Unreal Engine 제외)
	- Local Windows Debugger 실행.
- Template Project 의 Unreal Editor 의 Multiplayer Option 에서 Net Mode 를 Play as Client 로, Player Num 을 2로 설정한다.

# Integrating GameLift

1. [Integrate Amazon GameLift Servers into an Unreal Engine project](https://docs.aws.amazon.com/gameliftservers/latest/developerguide/integration-engines-setup-unreal.html)
	1. CMake 설치 -> Add CMake to the PATH encironment variable 체크
	2. Python 설치
		1. Use Admin Pribileges when installing py.exe 체크
		2. Add python.exe to PATH 체크
		3. C 드라이브에 폴더 생성 $\to$  폴더 명 Python
		4. Customize Installation 에서 Path 를 Python 폴더로 지정.
		5. 시스템 환경 변수 편집에서 Python 폴더와 Python/Scripts 를 추가.
	3. Git 설치


# Server Build Target

- What is Build Target

빌드 타겟이란, 빌드 과정에서 생성하고자 하는 특정 결과물이나 산출물을 의미한다. 이는 원하는 상태를 달성하기 위해 특정 순서대로 실행되어야 하는 일련의 작업들을 그룹화 한 것이다.
예를 들어 소프트웨어 프로젝트에서 일반적인 Build Target 에는 Executable 파일 생성, Library 컴파일, documentation 생성 등이 포함 될 수 있다.
언리얼 엔진의 빌드 도구인 UBT (Unreal Build Tool) 가 이 타깃 설정을 읽고, 수많은 엔진 모듈 중 무엇을 포함하고 (Link), 무엇을 제외할 지 결정한다.

```c#
using UnrealBuildTool;
using System.Collections.Generic;

public class FPSTemplateTarget : TargetRules
{
	public FPSTemplateTarget(TargetInfo Target) : base(Target)
	{
		Type = TargetType.Game;
		DefaultBuildSettings = BuildSettingsVersion.V5;
		IncludeOrderVersion = EngineIncludeOrderVersion.Unreal5_4;
		ExtraModuleNames.Add("FPSTemplate");
	}
}
```

template project 의 target 클래스인 FPSTemplateTarget 클래스는 TargetRules 클래스를 상속 받았다.
TargetRules 는 UBT 에게 해당 실행 파일을 어떻게 만들어야 하는지 알려주는 클래스이다.
TargetRules 는 프로젝트 전체의 빌드 환경 설정을 담당한다. cpp 가 컴파일 되게 전에 컴파일러에게 거시적인 규칙을 정해주는 것이다.
1. Type 은 빌드 결과물의 정체성을 결정한다.
	1. TargetType.Game : 게임 클라이언트 (에디터 기능(디버깅 툴, 에디터 UI) 이 모두 제외), cook 된 콘텐츠만 실행할 수 있는 최적화된 상태.
	2. TargetType.Editor : 개발용 (언리얼 에디터)
	3. TargetType.Server : 데이케이티드 서버 (그래픽 제외)
2. DefaultBuildSettings : 언리얼 엔진 버전에 맞는 컴파일러 설정과 C++ 표준을 정의한다.
3. IncludeOrderVersion : 헤더파일 포함 순서와 규칙을 정한다. 
	1. Unreal 5_4 버전의 최적화된 포함 규칙을 사용한다.
4. ExtraModulenames : 언리얼은 모든 c++ 코드를 모듈 단위로 관리한다. 캐릭터, 게임 모드 등의 c++ 클래스들리 들어있는 FPSTemplate 라는 이름의 모듈(폴더)을 이 실행 파일에 포함시키라는 명령이다.


# Add Server Build Target

1. 프로젝트 의 Source 폴더 우클릭 -> Add File -> FPSTemplateServer.Target.cs 로 생성.
2. 다른 Target.cs 파일을 복사 붙여 넣기 한 뒤 class 이름을 FPSTemplateServer 로 변경하고 Type 을 TargetType.Server 로 변경 해준다.
3. solution configuration 를 Development Server 로 변경 $\to$ 프로젝트 빌드
# GameLift Server SDK

[참조 1 : OpenSSL Build and Install](https://github.com/openssl/openssl?tab=readme-ov-file#build-and-install)
[참조 2 : OpenSSL Note for Windows](https://github.com/openssl/openssl/blob/master/NOTES-WINDOWS.md)

1. [Perl 설치](https://strawberryperl.com/)
2. [NASM 설치](https://www.nasm.us/)
	1. Perl 에도 NASM 이 설치 되어 있을 수 있다. Perl 의 NASM 이 아니라 직접 다운 받은 NASM 을 사용하고 싶다면 '시스템 환경 변수 편집' 에서 NASM 의 순서를 Perl 보다 위로 하면 된다.
3. CMD 의 환경 변수 (Path) 에 컴파일러와 빌드 도구의 경로를 등록(injection).
	1.  Nmake.exe 를 위한 환경 변수를 세팅한다.
		1.  Visual Studio Developer Command Prompt 를 관리자 권한으로 실행한 CMD 에서 사용한다. 
		2. VsDevCmd 의 path 를 찾는다.
		3. 해당 Path 에 architecture 를 골라 뒤에 붙여 실행한다.
```
C:\Windows\System32>"C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\Tools\VsDevCmd.bat" -arch=amd64
**********************************************************************
** Visual Studio 2022 Developer Command Prompt v17.13.3
** Copyright (c) 2022 Microsoft Corporation
**********************************************************************
```
4. [OpenSSL 설치](https://github.com/openssl/openssl)
	1. c 드라이브에 'OpenSSL_3.2.4' 폴더 추가 (다운 받을 버전)
	2. 해당 폴더에 OpenSSl 를 clone
	3. OpenSSL 에서 MakeFile 생성.
```cmd
C:\OpenSSL_3.2.4\openssl>perl Configure VC-WIN64A
Configuring OpenSSL version 4.0.0-dev for target VC-WIN64A
Using os-specific seed configuration
Created configdata.pm
Running configdata.pm
Created makefile.in
Created makefile
Created include\openssl\configuration.h

**********************************************************************
***                                                                ***
***   OpenSSL has been successfully configured                     ***
***                                                                ***
***   If you encounter a problem while building, please open an    ***
***   issue on GitHub <https://github.com/openssl/openssl/issues>  ***
***   and include the output from the following command:           ***
***                                                                ***
***       perl configdata.pm --dump                                ***
***                                                                ***
***   (If you are new to OpenSSL, you might want to consult the    ***
***   'Troubleshooting' section in the INSTALL.md file first)      ***
***                                                                ***
**********************************************************************
```

5. nmake 실행
	1. "x64 Native Tools Command Prompt" 실행
	2. openssl 폴더로 이동
	3. nmake 실행
		1. 이를 통해 libcrypto-4-x64.dll 과 libssl-4-x64.dll 이 생성된다. 해당 파일들을 복사하여 openssl 폴더 외부에 보관한다.
### OpenSSL

- SSl 은 Secure Sockets Layer 의 약자이다.
- 전 세계적으로 가장 많이 쓰이는 오픈소스 암호화 라이브러리. 인터넷 상의 데이터를 남들이 품쳐보지 못하게 암호로 바꾸거나, 암호를 다시 푸는 기능을 담당한다.
- 게임 서버 (Unreal Dedicated Server) 와 AWS GameLift 클라우드 서비스가 서로 정보를 주고 받을 때, 해커가 중간에서 가로채지 못하도록 모든 통신 내용을 암호화 해야한다. 
- GameLift Server SDK 가 내부적으로 이 OpenSSL 을 사용하여 통신을 수행한다. 따라서 SDK 를 쓰려면 OpenSSL 이 먼저 설치되어(빌드되어) 있어야 하는 것이다.

### GameLift Server SDK

- SDK 는 Standard Development Kit 의 약자이다.
- AWS 에서 제공하는 C++ 라이브러리 이다. 언리얼 엔진이 GameLift 서비스에 명령을 보내거나 상태를 보고할 수 있게 해주는 도구 모음이다.
- 언리얼 엔진 자체에는 현재 게임이 시작되었다는 것을 나타낼 기능이 없다. 따라서 SDK 를 프로젝트에 포함시켜 다음의 C++ 를 사용해야 한다.
	- InitSDK() : 게임 서버(Dedicated Server)**가 켜져서 통신 준비가 끝났음을 GameLift에 알림
	- ActivateGameSession() : GameLift 에게 게임 한 판이 시작됐음을 알림.
	- AcceptPlayerSession() : 특정 플레이어가 입장해서 게임 한 판의 그룹에 참가 시키는 것을 알림.


# 


