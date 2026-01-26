
1. 현재 프로젝트에서 API Gateway 의 Method 를 호출하는 함수를 찾을 것.
- StartGameSession
```c++
...
local player controller 와 Player State 에서 Player id 를 추출 후 하드 코딩된 lambda url 을 통해 POST Method 실행.

Request->OnProcessRequestComplete().BindUObject(this, &UGS_GameInstance::OnCreateSessionResponse);  
Request->ProcessRequest();
```