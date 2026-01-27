# Details

### Root Motion

애니메이션이 가진 Root Bone (root or pelvis) 의 이동 회전 정보를 캐릭터 이동으로 추출하는 기능.

즉 애니메이션 클립 자체만으로도 캐릭터를 앞으로 나아가게 하거나 회전하게 할 수 있다.

- EnableRootMotion
	- Sequence 내 Root Bone 의 Transdform 데이터를 추출할지 여부를 결정. Disable 이면 Root 는 Animation 내내 월드에서 고정된 채로 재생된다.
	- Root Motion Root Lock
		- 추출된 Root Motion을 월드에 적용할 때 '어느 기준점' 을 고정할 지 정한다.
		- Anim First Frame : 애니메이션 첫 프레임 위치를 기준.
		- Zero :  애니메이션 임포트 원점 (0, 0, 0) 기준.
		- RefPose : Skeleton 의 참조 포즈 기준.
		- enable 이면 Montage 를 느리게 재생해도 보폭거리가 자동으로 줄어든다
		- disable 이면 재생 속도와 관계없이 원본 보폭 그대로 적용.
		- 