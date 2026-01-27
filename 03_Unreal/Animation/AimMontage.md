### Section_활용

출력되는 Section 순서 지정.
![AnimMontage 섹션 순서 지정.](/Image/Unreal/AnimMontage_Section순서지정.png)
1. Animation Sequence 우클릭 -> New Montage Section
2. 각 Section 옆에 흰색 박스를 클릭 -> 다음에 재생될 Section 선택
예를 들어 EquipStop 부분을 그냥 넘어가고 싶다면 Default Section 의 우측 흰색 박스에서 Next Section - Draw 를 선택하면 된다.

##### Animation Sequence 의 속도 조절

Animation Sequnce 선택 -> Details -> PlayRate 에서 조정
1.0 이 기본 속도. 2.0 이면 두 배 빠르게 재생.

### Blend In & Blend Out


# Current Montage 와 Stop Montage

### StopMontage 에 CurrentMontage 를 넣는 것과 AnimMontage 를 특정 하는 것의 차이.

1. Montage_Stop(BlendOutTime, CurrentMontage)
	1. 현재 재생 중이 몽타주를 멈춤.
	2. `CurrentMontage`는 내부적으로 `AnimInstance->GetCurrentActiveMontage()`로 얻을 수 있으며,  재생 중인 것이 없으면 `nullptr`이기 때문에 아무 일도 안 일어남.
	3. 다수의 몽타주가 블렌딩 중일 경우 정확한 제저가 어렵다.
2. Montage_Stop(BlendOutTime, SlecificMontage);
	1. 명시적으로 특정한 UAnimMontage* 가 재생 중인 경우에만 그걸 멈춘다.
	2. 다른 몽타주가 재생 중이어도. 지정한 그 몽타주만 멈추려고 시도한다.

- 여러 개의 슬롯에서 애니메이션이 동시에 재생 중일 때, Montage_Stop(BlendOutTime, CurrentMontage) 를 호출하면 어떤 슬롯의 애니메이션이 멈추는가?
	- Montage_Stop(BlendOutTime, CurrentMontage) 는 슬롯과 무관하게,  GetCurrentActiveMontage() 로 가져온 "가장 마지막으로 재생된 몽타주 하나만" 멈춘다.

- Slot 이름 기반으로 정지
	- Montage_StopGroupByName(0.25f, FName("UpperBody"));
