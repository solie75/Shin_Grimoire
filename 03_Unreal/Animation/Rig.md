https://dev.epicgames.com/documentation/ko-kr/unreal-engine/ik-rig-in-unreal-engine

### IK Goal

- **IK Goal**은 “특정 관절(End Effector)을 IK(역운동학) 방식으로 보정해서 리타게팅하겠다”를 정의하는 설정이다. 
- 역할
	- End Effector 보존
	- Two-Bone IK Solver 사용
- IK Goal 은 자신이 설정된 Chain 에 속한 Bone 들만 계산
- 예를 들어 왼쪽 팔 에 관한 Bone 들에 관하여 IK Goal 은 손부터 어깨까지의 Bone 을 조정하여 각 Bone 의 위치를 자연스럽게 위치시켜야 하기 때문에 Chain 을 Clavicle, UpperArm, LowerArm, Hand 를 각자 Chain 으로 설정하는게 아니라 Clavicle 부터 Hand 까지 하나의 Chain 으로 설정하고 해당 Bone 들이 하나의 IK Goal 에 조정되도록 해줘야 한다.


### Solver

- 종류
	- Full Body IK
		- Details
			- Stiffness (강성)
				- IK solver 가 목표 위치, 회전에 뼈를 얼마큼 Stiff 하게 끌어당길지 조절.
				- 값이 0.0 이면 관절이 완전히 느슨하게 반응한다.
				- 값이 1.0 이면 관절이 목표에 딱 달라붙듯 강하게 반응한다.
				- 상체나 골반처럼 약간 유연하게 움직이길 원한다면 0.2 ~ 0.5
				- 손목 발목처럼 정확도가 중요하면 0.8 ~ 1.0
			- Limits (회전 제한)
				- Free : 제한 없음
				- Locked : 해당 축 회전 완전 고정. IK Solver 가 이 축으로는 어떠한 회전값도 적용 못한다.
				- Limited : 최소/ 최대 각도 범위 내에서만 회전을 허용한다.
				- 팔꿈치 무릎 처럼 특정 축으로만 굽혀져야할 때 -> Limited 로 범위지정.
				- 허리나 목 복잡한 회전을 박고 싶을 때 -> 필요 축 Locked
			- Preferred Angles
				- Use Preferred Angles
					- enable : IK solver 가 회전 결과를 계산할 때, 선호 각도 정보도 참고해서 가능한 원래 rig 의 rest pose 에 가까운 회전을 유지하려고 한다.
					- disable : 선호 각도를 무시하고 순수하게 목표 위치/ 회전에만 집중해 어색한 뒤틀림 발생 가능.
				- Preferred Angles
					- Pole/Free Axis 와 달리 별도의 선호 각도(Preferred Angle) 값을 지정할 수 있다. 뼈가 접혔으 때 기본적으로 어떤 회전값을 가지길 원하는지 축별로 입력
		- Goal Setting -> Details
			- Chain Depth
				- 해당 Goal 이 몇 단게 위 의 Bone 까지 영향을 미칠지 결정한다.
				- 0 이면 End Bone 만 IK 보정.
				- 1 이면 End Bone 의 부모 한 단계(hand_r)->lowerarm_r) 가지 포함.
			- Strength Alpha
				- 해당 Goal 이 IK Solver 에 얼마큼의 Weight 를 줄것인지 다시말해 보정 강도를 0 ~ 1 사이로 조절한다.
				- 0 이면 Goal 무시(원본 애니메이션 유지) 이고.
				- 1 이면 Goal 위치/회전에 100% 일치시키려고 한다.
				- 다중 Goal 이 충돌하지 않게 하려면, 각 Goal 의 Strength Alpha 를 조정하여 우선 순위를 부여한다.
			- Pull Chain Alpha
				- IK Goal 에서 계산된 위치로 "Chain 을 당길 때" 의 강도를 0~1 로 조절.
				- 0 이면 체인을 당기지 않고 Goal 에 가까워 지기만 한다.
				- 1.0 골 지점까지 강하게 당겨서 체인 전 구간이 밀착.
				- 자연스러운 움직임을 위해서는 Strength Alpha 보다 살짝 낮게 설정한다.
				- 단단하게 고정하고 싶다면 높은 값을 설정.
			- Pin Rotation
				- End Effector(End Bone) 의 회전을 목표 회전으로 고정 할지 여부를 설정.
				- enable : IK 는 위치만 보정하고, goal bone 의 회전은 원본 애니메이션 (또는 Goal에 입력된 회전) 그대로 유지
				- disable : IK 가 위치 뿐 아니라 회전까지 목표 값으로 맞춤
				- 손 끝이나 발끝의 자세까지 정확히 맞추고 싶을 때는 disable로, 발바닥이 지면에 살짝 붙은 자세 등 회전 변화가 어색할 때는 enable.

Post-Process AnimBP 가 존재하면 Skeletal 이 변화가 적용되지 않