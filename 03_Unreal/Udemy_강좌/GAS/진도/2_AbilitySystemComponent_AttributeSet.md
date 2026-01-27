# AbilitySystem 추가

- AbilitySystemComponent 플러그인 추가.
- build.cs 수정.
	- PrivateDependencyModuleNames.AddRange(new string[] { "GameplayAbilities", "GameplayTags", "GameplayTasks" }); 추가
- GP_AbilitySystemComponent 와 GP_AttributeSet 추가.
	- GP_AbilitySystemComponent 의 Constructor 에서 Net Update Frequency 값 설정.
- PlayerState 와 CharacterBase 에 
	- AbilitySystemCompeont 와 AttributeSet 멤버 변수 선언
- PlayerState 와 CharacterAI 에 IAbilitySystemInterface 상속.
	- virtual GetAbilitySystemComponent () override 추가.
	- constructor 에서 AbilitySystemComponent 와 AttributeSet 초기화.

# GAS in Multiplay

Net Update Frequency 는 서버에서 어떤 변경이 발생했을 때, 그 변경을 클라이언트에게 전송하는 빈도를 의미한다. Net Update Frequency 가 100 이라는 것은 서버가 감당 가능한 한, 매초 100 회 클라이언트로 변경사항을 전송하겠다는 의미이다. 이 동작을 Replication 이라고 부르며, 작동 방식은 다음과 같다.
Pawn 클래스에 어떤 변수가 있다고 가정해보자.
해당 변수는 Pawn 클래스에 존재하므로, 서버와 클라이언트의 모든 Pawn 인스턴스가 그 변수를 가지게 된다.
만약 그 변수가 Replicated 로 지정되어 있다면, 서버에서 값이 변경될 경우 다음 NetUpdate주기에 클라이언트에게 전송되어 클라이언트 쪽 변수도 최신 값으로 업데이트 된다.
이처럼 데이터를 클라이언트로 전송하는 것을 Replication 이라고 한다.
Replication 은 항상 서버 -> 클라이언트 방향으로만 작동한다. 따라서 Replicated 로 설정된 변수는 클라이언트에서 변경하면 안된다. 서버와 다른 클라이언트는 그 변경을 알지 못하기 때문이다.
해당 변수를 변경한 클라이언트만 그 값을 알고 있을 뿐, 서버와 동기화 되지 않아 버전 차이가 발생하기 된다.
그리고 항상 서버가 correct version 이기 때문에 해당 클라이언트의 변수는 잘못된 값으로 간주된다.
클라이언트가 서버로 데이터를 보내기 위해서는 RPC(Remote Procedure Call) 를 활용해야 한다.
GAS 에서는 RPC 없이 클라이언트와 서버간의 양방향 데이터 전달이 되는 것 처럼 보이게 자동으로 작동한다.

- 생성자에서 
	- AbilitySystemComponent -> SetIsReplicated(true)

- EGameplayEffectReplicationMode
	- Full : GameplayEffect 가 모든 client 에 복제된다. 즉, Gameplay Effect 가 발생하면 그 정보다 replicate 된다. (single play)
	- Mixed : GameplayEffect 가 owning client 에게만 replicate 된다. Gameplay Cues 와 Gameplay Tags 들도 모든 클라이언트에 replicate 된다. (Multiplayer, Player Controllered Character)
	- Minimal : GameplayEffect 가 복제되지 않는다. 하지만 GameplayCue 와 GameplayTag 는 복제된다. (Multi Player, AI Controlled Character)
	- 플레이어가 조종하는 캐릭터가 접근할 PlayerState 의 AbilitySystemComponent 는 Mixed 로 설정하고, AI 에게 조종되는 캐릭터가 접근할 CharacterAi 의 AbilitySystemComponent 는 Minimal 로 설정한다.

- AbilitySystemComponent 가 자신의 소유자를 알도록한다.
	- AbilityActorInfo 개념을 통해 ASC 는 누가 소유자인지 등의 정보를 항상 할 수 있다.
	- AbilitySystemComponent 는 OwnerActor 와 AvatorActor 를 갖고 있다. OwnerActor 는 실제로 해당 컴포넌트를 소유한 클래스이고 AvatorActor 는 컴포넌트와 연관된 게임월드 상에서 시각화된 객체이다. CharacterAI 의 경우 OwnerActor 와 AvatorActor 가 동일하다. 하지만 플레이어의 경우 OwnerActor 가 PlayerState 가 되고 AvatorActor 가 CharacterPlayer 가 된다.
	- InitAbilityActorInfo() 를 통해 OwnerActor와 AvatorActor 를 설정한다.
		- InitAbilityActorInfo 는 Possession 이후에 호출해야 한다.