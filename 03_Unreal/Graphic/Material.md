# Tiling

- Material 의 Texture 를 표면에 반복적으로 배치하는 과정을 의미한다. 

- Tiling 노드에 입력하는 값으로 Texture 가 표면에 몇 번 반복되는지 결정한다.
	- 값이 1 이면 텍스쳐가 1번만 매핑된다.
	- 값이 2 이면 텍스쳐가 UV 영역에서 2x2 로 반복된다.
	- 값이 0.5 면 텍스쳐가 더 크게 보이고 덜 반복된다.

![Tiling 입력 값에 따른 비교](/Image/Unreal/Tiling_Compare_for_Value.png)
두 벽에 같은 Material 을 입력할 때 왼쪽은 Tiling 값을 1, 오른 쪽은 tiling 값을 5 로 준 것이다.

- UV 조정
	- TextureCoordinate 노드 를 사용해서 UV 좌표 조작.
	- UTiling 과 VTiling 값을 사용해 텍스쳐의 X, Y 방향 <span style="background-color: violet;">반복 수</span> 를 설정할 수 있다.
		- Tiling 노드의 입력 값을 1 로 고정하고 UTiling = 4, VTiling = 4 로 설정하면 텍스쳐가 x 및 y 축으로 4번 반복된다.
	- Tiling 노드의 입력 값을 5 로 고정한 상태에서, 왼쪽 벽은 (UTiling = 1.0, VTiling =  0.5) 을 입력하고, 오른쪽 벽은 (UTiling = 0.5, VTiling =  1.0) 을 입력한 결과이다.
![UV 입력 값에 따른 Tiling 비교](/Image/Unreal/Tiling_Compare_for_UV.png)

# Dynamic_Mateiral Instance

- UMaterialInstanceDynamic
	- 게임 실행 중에 색상, 텍스쳐, 숫자 값 등 변경 가능 하도록 하는 Material Instance
```c++
#include "Materials/MaterialInstanceDynamic.h"

UMaterialInstanceDynamic* DynamicManterial = UMaterialInstanceDynamic::Create(DriginalMaterial, this)
```
- CreateAndSetMaterialInstanceDynamic()
	- 기존의 Static Material 을 Dynamic Material 로 변환 및 해당 메쉬에 설정. Create 함수 호출 없이 기존 머터리얼을 즉시 변환하여 적용.
```c++
UMaterialInstanceDynamic* CreateAndSetMaterialInstanceDynamic(int32 ElementIndex);
```
- SetVectorParameterValue()
	- UMaterialInstanceDynamic 의 Vector 파라미터 값 변경.
	- 보통 색상 값 설정.
	- ParameterName 은 머터리얼에서 설정한 Vector Parameter 이름과 일치해야한다.
```c++
void SetVectorParameterValue(FName ParameterName, FLinearColor Value);
```
