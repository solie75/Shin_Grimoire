# Set Blend Stack Anim From Chooser

# Blend_Profile

언리얼 엔진에서 Animation Blending 시 특정 Bone 마다 서로 다른 블렌드 속도를 적용할 수 있는 기능.
기본적으로 Animation Blending 은 전체 캐릭터의 모든 뼈에 균일한 방식으로 적용되지만, Blend Profile 을 사용하면 특정 뼈마다 블렌드 속도를 다르게 조절할 수 있다.
예를 들어, 발을 지면에 고정한 채 상체만 자연스럽게 블렌딩하고 싶은 경우. 전체 몸통보다 머리의 블렌드 속도를 다르게 하고 싶은 경우.

- 작동 방식
	- 각 Bone 에 가중치(Weight) 를 부여하여 블렌딩 속도를 조절한다. Weight 값이 높을 수록 블렌드가 더 빠르게 적용. Weight 값이 낮을 수록 블렌드가 더 부드럽게 적용.
	- weight 가 1.0 이면 일반적인 블렌드 속도, 0.0 이면 블렌드 없음(고정됨)
	- 즉, weight 값이 0에 가까울수록 해당 뼈는 원래 애니메이션에 더 가깝게 유지되고, Weight 값이 클수록 블렌드가 빠르게 적용된다.
- 설정
	- Anim Editor -> Skeleton 에셋 -> Blend Profile 생성 -> 각 Bone 에 대해 Weight 값 조정.
	- 저장 후, 애니메이션 블렌드에 적용.