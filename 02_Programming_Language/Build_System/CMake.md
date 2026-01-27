# What is CMake

CMake 는 소프트웨어 프로젝트의 빌드 과정을 관리하는 데 도움을 주는 크로스 플랫폼 빌드 시스템이다.

c++ 코드는 자바나 파이썬과 달리, OS 마다 빌드하는 방법이 다르다.
- Windows 는 .sln, .vcxproj 을 사용하여 빌드한다.
- Linux 는 주로 Makefile 이라는 것을 사용하여 빌드한다.
- Max 은 Xcode (.xcodeproj) 을 사용하여 빌드한다.

만약 크로스 플랫폼을 지향하는 개발자라면 윈도우용, 리눅스용, 맥용 프로젝트 파일을 다로 만들어서 제공하는 것은 매우 힘들다.

따라서 개발자는 CMakeLists.txt 라는 텍스트 파일 하나만 작성한다 (이것이 '공통 설계도' 이다.) 그리고 CMake 를 실행하면 지금 컴퓨터 환경에 맞는 빌드 파일로 변환해준다. 즉, Windows 에서 실행하면 .sln 파일을 생성하고, Linux 에서 실행하면 Makefile 을 생성, Mac 에서 실행하면 Xcode 프로젝트 파일을 생성한다.

즉, 소스 코드는 하나인데 여러 OS 에서 빌드해야 할 때 사용하는 필수 도구이다.

크로스 플랫폼을 지향하는 소스코드는 CMakeLists.txt 를 배포하게 되고 사용자는 CMake 를 설치하고 실행하여 해당 소스 코드를 읽게 한다. 그러면 사용자 의 OS 를 구별하여 그에 맞는 프로젝트 파일을 생성한다.


*강의에서 설명*
CMake is a cross-platform build system that helps manage the build proess of software projects. It generates native build files and workspaces for various platforms, and it simplifies and automates the process of compiling and linking your code across different environments.