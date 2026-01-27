1. State Machine 생성
2. [default slot 생성.]( https://dev.epicgames.com/documentation/ko-kr/unreal-engine/animation-slots-in-unreal-engine)

# State Machine

# Animation_Slot

slot 은 AnimGraph 에서 Montage 를 어느 시점, 어느 파트에서 출력할 지를 정해주는 재생 포인트 이다.
예를 들어 상체만 공격 애니메이션을 재생하고 하체는 그대로 움직이게 하고 싶을 때, 하체와 상체를 다른 Slot 에서 재생하도록 한다.

- Default Slot
	- 아무런 설정을 하지 않았을 때 애니메이션을 재생시키는 슬롯. Montage 를 만들었을 때, 별도로 slot 을 지정하지 않으면 Default Slot 을 사용하게 된다.

- ref : https://dev.epicgames.com/documentation/ko-kr/unreal-engine/animation-slots-in-unreal-engine

- Slot -> Details -> Always Update Source Pose 옵션.
	- 해당 옵션은 Slot 노드에 연결된 Source Pose 를 항상 계산할 것인지를 결정한다.
	- 즉, Montage 가 재생 중이 아니더라도, 해당 Slot 의 Source Pose 를 계속 계산한다.
	- 나중에 몽타주가 재생되면서 이전 포즈와 부드럽게 블렌딩되길 원할 때, 여러 Slot 을 레이어로 블렌딩할 때 등에 사용.

- Montage 나 Sequencer 에서 Slot 을 지정할 수 있다.
	- 여러 개의 Montage 를 하나의 Slot에 바인딩하면? 바인딩된 모든 Montage 들이 앞서 재생된 Montage 를 override 한다. 즉 마지막으로 재생된 Montage 가 출력된다.

# Post_Process_AnimBP

![[Post_Process_AnimBP(AnimGraph).png]]

# Animation Warping

https://dev.epicgames.com/documentation/en-us/unreal-engine/pose-warping-in-unreal-engine#slope%20warping

https://dev.epicgames.com/documentation/en-us/unreal-engine/python-api/class/AnimNode_OrientationWarping?application_version=5.2

https://www.youtube.com/watch?v=j1_SjFUB2ko