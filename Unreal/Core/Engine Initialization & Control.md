# FCommandLine::Parse

```c++
FCommandLine::Prse(FCommandLine::Get(), Tokens, Switches);
```

^b58b28

- 현재 실행된 명령줄 (Get) 을 가져와서, 일반 단어는 Tokens에 담고, 옵션 설정은 Switches에 담으라는 뜻.
- 각 Parameters 의미
	- FCommandLine::Get()
		- 현재 이 프로그램을 실행할 때 입력한 모든 명령어 문자열 원본을 가져온다.
		- 만약 윈도우 실행창이나 바로가기 설정에서 'MyGameServer.exe MyMap -log -port7777' 과 같이 실행했다면 Get(0 은 해당 문자열 전체를 반환한다.)
	- Tokens
		- TArray\<FString>
		- 명령어 중에서 대시(-) 나 슬레시(\\) 와 같은 기호로 시작하지 않는 일반적인 단어들이 여기에 담긴다.
		- 윗 예시의 경우 "MyGameServer.exe", "MyMap" 이 Tokens 배열에 들어간다.
	- Switchs
		- TArray<\FString>
		- 명령어 중에서 대시(-) 나 슬래시(\\) 로 시작하는 옵션들이 여기에 담긴다. (보통 저장될 때 - 기호는 떼고 저장된다.)
		- 윗 예시의 경우 "log", "port=7777" 등이 Switchs 배열에 들어간다.