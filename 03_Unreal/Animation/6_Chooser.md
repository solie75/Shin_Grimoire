# Create Chooser Table

- Chooser Type : Generic Chooser 
	- Result Type (반환할 데이터 유형)
		- Object Of Type (특정 UObject* 반환)
		- Subclass Of (특정 TSubObjectOf\<UObject> 반환)
	- Result Class (반환할 클래스 타입)
		- Object Of Type : UObject* 타입 선택
		- Subclass Of 이면 클래스 타입 선택
	- Parameters (입력값 설정)
		- class Parameter-> UObject* 타입의 클래스
		- Struct parameter -> FVector, FTransform 같은 구조체
		- Class -> Subclass o0f 선택시 지정한 클래스
		- Direction -> In, Out, InOut
- Chooser Type : Animation Chooser
	- Result Type 이 Object Of Type 으로 고정.
	- Result Class 가 UAnimSequence 또는 UBlendSpace 같은 애니메이션 관련 클래스로 제한.

# Get_Function_Return_Value

반환 값을 가지는 함수를 Chooser Table 의 기준 항목으로 가져오기 위해서는 

1. 함수의 반환 값이 정확히 'ReturnValue' 여야 한다. 띄어쓰기 하면 인식되지 않는다.
2. 함수의 Graph 상세 항목에서 Pure 가 Enable 이어야 한다.
3. 함수의 Advanced 상세 항목에서 Thread Safe 가 Enable 이어야 한다.
