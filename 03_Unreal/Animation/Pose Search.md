q다양한 애니메이션 포즈 데이터를 기반으로 현재 상태와 가장 적절한 포즈를 검색하고 부드러운 애니메이션 전환을 수행하는 시스템이다.
이 기능은 Motion Matching, Procedural Animation, AI 기반의 애니메이션 전환 등에 활용되며, 캐릭터가 자연스럽게 다음 동작을 이어갈 수 있도록 경로 (Trajectory) 를 생성하는 역할을 한다.

# Pose Search Schema

게임에서는 실시간 입력 값과 인게임 상의 상호작용으로 매 프레임에 다음의 애니메이션을 검색해야 하는데 이때 검색에 용이하도록 Locomotion, turn, start/stop, hit reaction 등의 런타임-검색 이 필요한 Animation Sequence 을 데이터베이스 화 하는 것이다. 이때의 데이터 베이스화 는 Animation Sequence 의 각 Frame 을 하나의 Vector 값으로 수치화 하여 관할 수 있도록 만드는 것으로 "미리 벡터화 및 정규화 해둔 대용량 애니메이션 테이블" 을 만드는 것을 말한다.

Pose Search Schema Asset 은 이러한 애니메이션의 데이터 베이스화에 있어 그 방식을 정한다. Schema 가 실제로 지정하는 것은 다음과 같다.

1. Feature Channels  : 어떤 Pose, Trajectory, Phase 값을 어떤 순서로 추출할지 결정한다.
2. Sample Rate : 데이터 베이스를 빌드할 때 각 Animation Sequence 에서 초당 몇 번(Hz) 샘플을 뽑을지 결정하는 값.
3. Weight & Normalization : 채널 별 중요도와 정규화 방식(Data Preprocessor) 을 지정한다. 
	- Feature Channels 은 각각이 Pose, Trajectory, Velocity 와 같은 여러 요소가 있고 각 요소는 Weight 라는 가중치를 가지고 있다.
	- 현재의 Vector 와 현재 Pose Search Database 에 지정된 모든 animation Sequence 의 모든 Frame 에 해당하는 Vector 의 차이를 구한다.
	- 이때 Pose 의 차, Trajectory 의 차, Velocity 의 차 에 각자 가중치를 곱한다.
	- 이후 각 요소를 모두 더하면 Feature Channels 당 값이 하나가 나오게 되고 이때 가장 작은 값을 다음의 Frame 으로 선택하는 것이다.
4. Skeleton Reference : 동일한 Rig 에만 인덱싱 하도록 데이터 호환성을 보장한다.

- DataBase 빌드 에서는 모든 frame 의 sampling 과 채널 결합, 정규화, KD-트리생성 과 같은 무거운 작업을 수행. [KD-트리 란?](https://ko.wikipedia.org/wiki/K-d_%ED%8A%B8%EB%A6%AC)
- 런타임 쿼리 시에는 현재 캐릭터의 벡터화 와 KD-트리가 선정한 후보 벡터와의 차이 계산 을 수핸한다.

#### 설정 요소
- Data Preprocessor
	- Sampling 이 끝난 feature vector 전체를 가중치를 곱하기 전에 어떤 방식으로 스케일 조정할 지 결정한다.
	1. NONE (0) : 모든 feature column 을 그대로 둔다. 정규화 및 가중치 재조정 없음. 이미 모든 채널이 같은 단위 또는 범위를 갖거나, 연구 단계 에서 ' 원본 분포' 를 보고 싶을 때 사용.
	2. NORMALIZE (1) : 각 column 을 표준편차 σ 로 나누어 분산을 1 로 맞춘 뒤, 가중치 벡터 전체를 길이 1 로 재정규화 한다. 실전 기본값으로 단위 차이를 제거하고 비율 만으로 중요도를 조정할 수 있다.
	3. NORMALIZE_ONLY_BY_DEVIATION (2) : σ로 나누지만 가중치 크기는 그대로 둔다. Noramlize 로는 코스트 폭이 너무 낮거나 높게 일정할 때, 가중치 절대값으로 전체 세기를 미세 조정하고 싶을 때 사용한다.
	4. NORMALIZE_WITH_COMMON_SCHEMA (3) : Normalize 와 동일하되, [Normalizetion Set](#Normalize_Set_(_공통_정규화_세트_)_란?) 안의 여러 데이터 베이스를 한꺼번에 모다 σ를 계산해 공통 스케일을 만든다. "Idle DB + Run DB" 와 같이 특성 분포가 다른 여러 DB를 동일 코스트 스케일로 비교해야 할 때.
	- 설정 순서
		1. 처음에는 Normalize + 직관적인 비율 (ex. trajectory 3, pose 1)
		2. [Rewind Debugger 의 코스트 히트맵](#Rewind_Debugger_Cost_Heat-Map_란?)을 보면서 특정 채널만 '빨간색' 이면 Weight 을 감소.
		3. 코스트 분포가 너무 평평 또는 뾰족 하면 NORMALIZE_ONLY_BY_DEVIATION 으로 바꿔 Weight 절대값을 조정. 
		4. 여러 DB 를 동시에 사용하는데 코스트 스케일이 달라서 잘 섞이지 않는다면 NORMALIZE_WITH_COMON_SCHEMA + Normalization Set 시도.

##### Normalize_Set_(_공통_정규화_세트_)_란?

여러 Pose Search Database를 **동일 스키마**로 묶어 한 번에 정규화 통계를 공유하도록 만든 에셋이 **Normalization Set**입니다.스키마의 Data Preprocessor를 **NORMALIZE_WITH_COMMON_SCHEMA**로 설정하면, 빌드 단계에서 Normalization Set에 포함된 모든 DB를 먼저 스캔하여 특성 열마다 전역 평균/표준편차를 구한 뒤 그 값을 각 DB에 적용합니다. 덕분에 로코모션·전투·아이들 DB처럼 값 범위가 서로 다른 클립들도 **동일 코스트 스케일**을 가지게 되어, 숫자가 큰 DB가 검색을 일방적으로 잠식하는 현상을 막을 수 있습니다. 실무에서는 Normalization Set을 만든 뒤 DB들을 등록하고, 각 DB 자산의 **Normalization Set** 슬롯에 해당 세트를 지정한 다음 빌드를 다시 수행합니다.

##### Rewind_Debugger_Cost_Heat-Map_란?

**Rewind Debugger**는 게임플레이를 녹화한 뒤 타임라인을 프레임 단위로 이동하며 디버그 정보를 겹쳐 볼 수 있는 언리얼 편집기 도구입니다. Motion Matching 또는 Pose Search 노드를 선택하면 추가 패널에 **코스트 히트맵**이 표시되는데, 행은 후보 DB 프레임, 열은 피처 채널이며 각 셀의 색이 초록(낮은 코스트)에서 빨강(높은 코스트)까지 변합니다. 이 히트맵을 통해 어떤 채널이 총 코스트에 가장 큰 영향을 주는지 한눈에 파악할 수 있습니다.
1. 녹화 후 Rewind Debugger를 열고 문제 구간을 선택합니다.
2. 특정 채널 열이 진한 빨강이라면 해당 채널 Weight가 과도할 가능성이 높으니 스키마에서 낮춥니다
3. DB를 다시 빌드하고 재생·재검토하여 히트맵이 고르게 녹/노랑이 되도록 조정합니다.
4. Normalization Set과 코스트 히트맵을 함께 활용하면 **DB 간 균형**과 **채널별 영향도**를 동시에 파악할 수 있어 가중치 튜닝이 훨씬 쉽고 안정적으로 이뤄집니다.

##### 두_DB_동시 _검색

### Idle DB + Run DB를 동시에 쓰면 생기는 문제

- **Idle DB**에 저장된 루트 속도는 평균 **10 cm/s** 정도이고,
- **Run DB**에 저장된 루트 속도는 평균 **400 cm/s** 정도입니다.

DB마다 독립적으로 정규화를 하면 Run DB의 큰 숫자(400)가 자체 평균·표준편차로 나뉘어 1.0 안팎의 작은 값으로 바뀌지만, Idle DB와 교차 비교할 때는 여전히 상대적으로 작지 않습니다.  
그 결과, 천천히 걷는 상황에서도 Run DB 프레임이 더 “저렴한” 코스트로 계산되어 잘못 선택될 수 있습니다.

- 해결책: Normalization Set + `NORMALIZE_WITH_COMMON_SCHEMA`

1. **Normalization Set 생성**
    - Idle DB와 Run DB를 하나의 Normalization Set에 추가합니다.
2. **스키마 설정**
    - Pose Search Schema의 Data Preprocessor를 `NORMALIZE_WITH_COMMON_SCHEMA`로 지정합니다.
3. **인덱스 다시 빌드**
    - 빌드 과정에서 두 DB의 모든 샘플을 함께 모아 각 특징 열의 전역 평균·표준편차를 계산하고, 그 값을 두 DB 모두에 적용합니다.

이렇게 하면 Idle과 Run 데이터가 동일한 통계 스케일을 공유하게 되어, 0.3 m/s 정도의 속도 오차가 어느 DB에서 발생하든 같은 코스트로 평가됩니다.

실무에서 적용 순서.
1. **Idle_DB**
    - IdleLoop, LookAround 클립을 30 Hz로 샘플링하여 인덱싱.
2. **Run_DB**
    - Run_Start, Run_Loop, Run_Stop 클립을 30 Hz로 샘플링하여 인덱싱.
3. **두 DB를 Normalization Set에 등록**
    - 두 DB 모두 설정 창에서 Normalization Set 항목을 동일 세트로 지정.
4. **두 DB 인덱스 재빌드**
    - `Build Index` 버튼으로 전역 정규화를 반영.
5. **Motion Matching 노드의 Searchable 배열에 Idle_DB와 Run_DB 모두 추가**

결과적으로 입력 속도가 낮을 때는 Idle DB 프레임, 속도가 높아질 때는 Run DB 프레임을 균형 잡힌 코스트 기준으로 자연스럽게 선택할 수 있습니다.