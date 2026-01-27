So we're setting up our ability system components and we've set our replication mode.  
우리는 Ability System Component를 설정하고 Replication Mode도 지정했습니다.

Now, something should still be bothering you at this point,  
그런데 이 시점에서 여전히 찜찜한 부분이 하나 있을 겁니다.

and that is that our AuraCharacterBase class has an ability system component pointer and an attribute set pointer.  
그건 `AuraCharacterBase` 클래스에 `AbilitySystemComponent` 포인터와 `AttributeSet` 포인터가 있다는 점입니다.

And it's only on AuraEnemy that those pointers have been set with valid values.  
그런데 이 포인터들은 `AuraEnemy` 클래스에서만 유효한 값으로 설정되어 있습니다.

For AuraCharacter, those pointers are uninitialized because we constructed the ability system component and attribute set on the player state instead.  
반면, `AuraCharacter`에서는 이 포인터들이 초기화되지 않았습니다. 왜냐하면 우리는 `PlayerState`에서 해당 컴포넌트들을 생성했기 때문입니다.

These pointers have valid values for the player state, but not for the character.  
즉, 포인터는 `PlayerState`에서는 유효하지만, 캐릭터 자체에서는 유효하지 않은 상태입니다.

So I want that to keep bothering you.  
그래서 여러분이 이 점을 계속 염두에 두셨으면 좋겠습니다.

Keep that in the back of your mind, because it's something we do want to fix.  
이건 반드시 수정해야 할 부분이기 때문에, 마음속에 꼭 기억해 두세요.

Now, another thing we have to do is make sure that the ability system component itself knows who its owner is.  
또 한 가지 해야 할 일은 Ability System Component가 자신의 **소유자(owner)**가 누구인지 알도록 하는 것입니다.

Now, that could mean something different for our enemy versus our player controlled character, right?  
이 소유자는 AI 적과 플레이어 캐릭터의 경우 서로 다를 수 있겠죠?

Because for our enemy, the owner of the ability system component is clearly this enemy class.  
적의 경우에는 이 컴포넌트의 소유자는 명백히 적 클래스 자신입니다.

But for the character, that's not necessarily the case, is it?  
하지만 플레이어 캐릭터의 경우는 다르죠?

Because the player state is who actually constructs the ability system component?  
왜냐하면 AbilitySystemComponent는 `PlayerState`에서 생성되었기 때문입니다.

So how do we decide who's the owner of it?  
그렇다면 누가 소유자인지를 어떻게 결정할까요?

Let's talk about that.  
이 부분을 이야기해보죠.

Now the ability system component has this concept of ability actor info.  
AbilitySystemComponent에는 `AbilityActorInfo`라는 개념이 있습니다.

That way, the ability system component can always know the actor info such as who owns this ability system component.  
이를 통해 컴포넌트는 누가 소유자인지 등의 정보를 항상 알 수 있습니다.

But the ability system component also understands the concept that it might be owned by some actor, by some pawn, or it could be owned by another type of entity such as a player state.  
AbilitySystemComponent는 소유자가 Actor, Pawn 또는 `PlayerState`일 수 있다는 개념도 알고 있습니다.

So for that reason, the ability system component has two variables: an owner actor and an avatar actor.  
그래서 두 개의 변수를 가집니다: **OwnerActor** 와 **AvatarActor**입니다.

You see, the owner actor is whatever class actually owns the ability system component.  
OwnerActor는 실제로 이 컴포넌트를 소유한 클래스입니다.

But the avatar actor is the representation in the world associated with this ability system component.  
반면, AvatarActor는 이 컴포넌트와 연관된 **게임 월드에서의 시각적 표현 객체**입니다.

Now for our enemy character, these two are the same.  
적 캐릭터의 경우, 이 두 값은 동일합니다.

Because our enemy character class is the class that constructs our ability system component.  
왜냐하면 적 캐릭터 클래스 자체가 컴포넌트를 생성하기 때문입니다.

So the owner actor is the enemy character, and the avatar actor is also the enemy character.  
따라서 OwnerActor와 AvatarActor 모두 적 캐릭터 자신입니다.

That's what we see in the world.  
이것이 우리가 게임 세계에서 보게 되는 실제 캐릭터입니다.

Now for the player controlled character, this is a little different.  
하지만 플레이어 캐릭터의 경우는 약간 다릅니다.

Because our ability system component is constructed by the player state.  
AbilitySystemComponent가 PlayerState에서 생성되기 때문입니다.

So the owner actor in this case should be the player state.  
이 경우 OwnerActor는 `PlayerState`가 되어야 합니다.

And the avatar actor—that actor that we actually see in the world—that should be the character.  
AvatarActor, 즉 월드에서 보이는 실제 캐릭터는 `Character`가 되어야 합니다.

So in this case, the owner actor and avatar actor are two different actors.  
따라서 이 경우 Owner와 Avatar는 서로 다른 객체입니다.

And the ability system component distinguishes between those two.  
AbilitySystemComponent는 이 둘을 구분할 수 있습니다.

So it's important to understand that the owner actor and avatar actor may or may not be the same actor in our project.  
즉, 우리의 프로젝트에서 OwnerActor와 AvatarActor는 같을 수도, 다를 수도 있다는 점을 이해하는 것이 중요합니다.

For the Enemy, they are the same.  
AI 적 캐릭터에서는 둘이 같습니다.

And for the player controlled character, they are different.  
하지만 플레이어 캐릭터에서는 서로 다릅니다.

Now we are the ones who decide these things.  
이 설정은 우리가 직접 해줘야 합니다.

We call the function to set the owner and avatar actors.  
우리는 Owner와 Avatar를 설정해주는 함수를 호출해야 합니다.

It's a function on the ability system component called `InitAbilityActorInfo`.  
그 함수의 이름은 `InitAbilityActorInfo`입니다.

So we need to initialize the ability system component with these actors ourselves.  
우리는 직접 AbilitySystemComponent에 Owner와 Avatar를 설정해줘야 합니다.

Now where and when do we call this function?  
그렇다면 이 함수를 언제, 어디서 호출해야 할까요?

Well, that's something that we have to give some thought to.  
이건 약간의 고민이 필요한 부분입니다.

You see, it depends.  
왜냐하면 상황에 따라 다르기 때문입니다.

Now here are some rules of thumb and good places to do so in a multiplayer game:  
멀티플레이어 게임에서 사용할 수 있는 몇 가지 기본 규칙을 알려드리겠습니다.

The first thing we need to know is that calling `InitAbilityActorInfo` must be done **after possession**.  
첫 번째로 알아야 할 것은 `InitAbilityActorInfo`는 **Possession 이후**에 호출해야 한다는 것입니다.

In other words, the controller has to have been set for the pawn.  
즉, Pawn에 Controller가 설정된 이후여야 합니다.

So it's important that we do this after the controller has been set for the pawn.  
즉, Pawn이 Controller를 할당받은 이후에 호출해야 한다는 것입니다.

So when is that?  
그럼 언제가 적절할까요?

Well, for a player controlled character in which the ability system component lives on the pawn itself,  
만약 AbilitySystemComponent가 `Pawn` 자체에 있는 플레이어 캐릭터라면,

a good place to call `InitAbilityActorInfo` would be in the `PossessedBy` function on the pawn.  
이 경우 `Pawn` 클래스의 `PossessedBy` 함수에서 호출하는 것이 좋습니다.

This ensures that the ability system component is initialized with the avatar and owner actor values on the server.  
이렇게 하면 서버에서 Avatar와 Owner 값을 정확히 설정할 수 있습니다.

But this function only applies on the server.  
하지만 이 함수는 서버에서만 호출됩니다.

On the client, we have to use a different function.  
클라이언트에서는 다른 함수를 사용해야 합니다.

There's a function called `OnRep_Controller` for that.  
그 함수는 `OnRep_Controller`입니다.

And in this function we know that possession has occurred.  
이 함수는 Possession이 완료되었음을 보장합니다.

So in this case, these two functions are where we would call `InitAbilityActorInfo`.  
그래서 이 두 함수가 AbilitySystemComponent를 초기화하는 시점입니다.

Now, for player controlled characters in which the ability system component lives on the player state (our case),  
이제 우리처럼 AbilitySystemComponent가 PlayerState에 있는 경우를 봅시다.

we're going to call `InitAbilityActorInfo` for the server in `PossessedBy`.  
서버 측에서는 `PossessedBy`에서 초기화합니다.

But for the client, we're not going to use `OnRep_Controller`.  
하지만 클라이언트에서는 `OnRep_Controller`를 사용하지 않습니다.

We're going to use `OnRep_PlayerState`.  
대신 `OnRep_PlayerState`를 사용합니다.

Why?  
왜일까요?

Because our ASC (AbilitySystemComponent) lives on the player state.  
왜냐하면 ASC는 PlayerState에 있기 때문입니다.

So we need to make sure that the controller and the player state are both valid.  
Controller와 PlayerState가 모두 유효해야 합니다.

Now `OnRep_PlayerState` is safe.  
`OnRep_PlayerState`는 안전한 호출 시점입니다.

Both of those things will have been done by that point in time.  
이 시점에서는 Controller도 PlayerState도 유효합니다.

So that is where we will initialize ability actor info for our ability system component.  
따라서 이 시점에 AbilitySystemComponent를 초기화하면 됩니다.

And in this case, we'll be setting our owner actor to the player state,  
이 경우 OwnerActor는 PlayerState로 설정하고,

and our avatar actor to the pawn itself—in our case, AuraCharacter.  
AvatarActor는 Pawn, 즉 AuraCharacter로 설정합니다.

So this is what we'll be doing for our player controlled character.  
플레이어 캐릭터에서는 이렇게 처리해야 합니다.

Now for the AI controlled characters, it's a bit more simple.  
AI가 조종하는 캐릭터의 경우는 더 단순합니다.

We'll have our ability system component on the pawn itself.  
AbilitySystemComponent가 Pawn 자체에 있기 때문입니다.

That's our AuraEnemy.  
이 경우 `AuraEnemy`가 되겠죠.

And because of that we really only need to call `InitAbilityActorInfo` in `BeginPlay`.  
그래서 단순히 `BeginPlay`에서 `InitAbilityActorInfo`를 호출하면 충분합니다.

We know that our AI controlled character is going to have a valid ability system component and controller.  
AI 캐릭터는 ASC와 Controller가 정상적으로 세팅되어 있을 것이기 때문입니다.

And we're just going to call `InitAbilityActorInfo` in `BeginPlay`.  
그래서 `BeginPlay`에서 호출하면 됩니다.

This will work just fine for our AI controlled character.  
이 방식은 AI 캐릭터에 아주 잘 작동합니다.

**So that's what we're going to do.**  
자, 그게 우리가 지금부터 할 일입니다.

**So if you're a note taker, if you like taking notes, this would be one of those important things to write down,**  
필기를 좋아하시는 분이라면 지금은 꼭 메모해야 할 중요한 내용입니다.

**so you can pause the video and write these things down,**  
그러니 지금 영상을 잠시 멈추고 필기하셔도 좋습니다.

**because these are the functions that you want to know about when it comes to calling InitAbilityActorInfo and initializing it.**  
왜냐하면 이 함수들은 `InitAbilityActorInfo`를 호출하고 초기화할 때 꼭 알고 있어야 할 것들이기 때문입니다.

---

**And now that we know what we're going to do to init ability actor info, it's time to actually do it in our project.**  
이제 `InitAbilityActorInfo`에 대해 무엇을 해야 할지 알았으니, 실제로 우리 프로젝트에 구현해볼 시간입니다.

**And this is going to be your quest.**  
이것이 이번 과제입니다.

**I want you to initialize the actor info by calling InitAbilityActorInfo and you're going to do this for the AuraEnemy class.**  
여러분은 `AuraEnemy` 클래스에서 `InitAbilityActorInfo`를 호출하여 Actor 정보를 초기화해야 합니다.

**That's going to be easy. You'll just do it in BeginPlay, right?**  
이건 간단합니다. `BeginPlay`에서 호출하면 되니까요.

---

**But also handle this for AuraCharacter.**  
하지만 `AuraCharacter`도 처리해야 합니다.

**And remember, this one's a little bit more complicated.**  
그리고 이건 조금 더 복잡하다는 걸 기억하세요.

**And also remember the ASC.**  
그리고 ASC(AbilitySystemComponent)에 대해서도 기억해두세요.

**The ability system component is on the player state, which means you're going to need to access the player state.**  
ASC는 `PlayerState`에 있으므로, `PlayerState`에 접근해야 한다는 뜻입니다.

**And while you're at it, this is a great opportunity to set the AuraCharacter's ability system component and attribute set pointers.**  
그리고 이 참에 `AuraCharacter`의 ASC 포인터와 AttributeSet 포인터도 설정할 수 있는 좋은 기회입니다.

---

**Remember I told you that should be bothering you in the back of your mind.**  
예전에 이것 때문에 신경 써야 한다고 말했었죠?

**Those pointers are uninitialized.**  
그 포인터들은 아직 초기화되지 않았습니다.

**Well, as soon as we access the player state, we should then have access to the ability system component and attribute sets.**  
우리가 `PlayerState`에 접근하는 순간 ASC와 AttributeSet에도 접근할 수 있게 됩니다.

**The player state has getters for those and our AuraCharacter will now have the ability to set those pointers.**  
`PlayerState`는 이를 반환하는 getter를 가지고 있으므로, `AuraCharacter`도 포인터를 설정할 수 있습니다.

**So pause the video and initialize your ability actor info now.**  
그러니 영상을 멈추고 지금 `AbilityActorInfo`를 초기화해보세요.

---

**Okay, so we're ready to init ability actor info.**  
좋아요, 이제 `AbilityActorInfo`를 초기화할 준비가 되었습니다.

**And I'd like to do the enemy first because that's the simplest case.**  
먼저 적 캐릭터부터 처리해보겠습니다. 이게 가장 간단하니까요.

**We're going to do this in BeginPlay now.**  
`BeginPlay` 함수 안에서 처리할 예정입니다.

**Our enemy doesn't have BeginPlay, which means we're going to need to override that here.**  
현재 적 클래스에는 `BeginPlay`가 없으므로, 여기서 오버라이드해야 합니다.

---

**And while I'm at it, I'd like to make this a little more organized.**  
이왕 하는 김에, 코드를 좀 더 정리해보겠습니다.

**I'm going to put a comment up here above our interface functions because I know that this belongs to our enemy interface.**  
인터페이스 함수들 위에 주석을 달아서 이것들이 `EnemyInterface`와 관련된 함수임을 명확히 하겠습니다.

**So I'm going to say enemy interface right here.**  
그래서 여기에는 "Enemy Interface"라는 주석을 적겠습니다.

**And I'm going to put a comment below these two that says End enemy interface.**  
그리고 두 함수 아래에는 "End Enemy Interface"라고 주석을 달겠습니다.

---

**That way I know that these are from that interface and these are public.**  
이렇게 하면 어떤 함수들이 인터페이스에서 왔고 public인지 알 수 있습니다.

**Begin play is protected.**  
`BeginPlay`는 `protected`로 선언되어야 합니다.

**So we'll make a protected section and we'll override begin play down here.**  
그래서 `protected` 섹션을 만든 후, 그 아래에 `BeginPlay`를 오버라이드하겠습니다.

---

**So we're going to say virtual void BeginPlay override.**  
`virtual void BeginPlay() override;`라고 선언하겠습니다.

**Let's generate the definition, we'll get our super call and then we can init ability actor info here.**  
정의도 생성하고, `Super::BeginPlay()`를 호출한 다음 `InitAbilityActorInfo`도 여기에 호출할 수 있습니다.

**So to do that we access our ability system component pointer and we call init ability actor info and we can see that the first input is the owner actor that will be this and the avatar actor will also be this.**  
이를 위해 ASC 포인터에 접근해서 `InitAbilityActorInfo(this, this)`를 호출하면 됩니다. 첫 번째 인자는 OwnerActor이고 두 번째는 AvatarActor인데, 둘 다 `this`가 됩니다.

---

**Now if you like, you can check the pointer first.**  
원하신다면 먼저 포인터가 유효한지 확인할 수도 있습니다.

**You can even put an assert such as check(AbilitySystemComponent).**  
`check(AbilitySystemComponent)` 같은 어서션을 걸 수도 있죠.

**But if I get a crash on this line, then I know why.**  
그런데 이 줄에서 크래시가 나면 원인을 알 수 있습니다.

**It's because the ability system component pointer is null, but I don't expect it to be null because I'm initializing that in the constructor.**  
그건 ASC 포인터가 널이라는 뜻인데, 생성자에서 이미 초기화했기 때문에 널일 리 없습니다.

**So something is terribly wrong if I crash accessing this pointer, I'm confident enough to use it without checking.**  
이 포인터에 접근하다가 크래시가 나면 무언가 심각하게 잘못된 것이므로, 저는 체크 없이 사용하겠습니다.

**So that takes care of the enemy.**  
이제 적 캐릭터는 처리가 완료되었습니다.

**Now for the character, which is going to be a little more complicated, but not much.**  
이제 플레이어 캐릭터를 처리할 차례인데, 약간 더 복잡하지만 그렇게 어렵진 않습니다.

**Let's find our AuraCharacter class, the CPP version, and we're going to want to override a couple of functions.**  
`AuraCharacter` 클래스의 CPP 파일을 열고 몇 가지 함수를 오버라이드할 것입니다.

---

**I'll just pull up my slide here again and remind myself that this is the case where concerned with where the ability system component lives on the player state,**  
다시 슬라이드를 열어 확인해보면, ASC가 `PlayerState`에 있는 경우라는 점을 기억해야 합니다.

**we're going to initialize ability actor info in PossessedBy for the server and OnRep_PlayerState for the client.**  
서버 측에서는 `PossessedBy`에서, 클라이언트 측에서는 `OnRep_PlayerState`에서 `InitAbilityActorInfo`를 호출해야 합니다.

**So we need to override those functions.**  
따라서 이 두 함수를 오버라이드해야 합니다.

---

**So that means I'm going to hit Alt+O or Ctrl+K,O on Visual Studio to come into AuraCharacter,**  
Visual Studio에서 `Alt+O` 또는 `Ctrl+K, O`를 눌러 `AuraCharacter`로 이동합니다.

**and I'm going to override a couple of virtual functions.**  
그리고 몇 가지 가상 함수를 오버라이드합니다.

**First, we need PossessedBy, so virtual void PossessedBy.**  
먼저 필요한 것은 `PossessedBy`, 따라서 `virtual void PossessedBy`를 선언합니다.

---

**And if I right click and go to declaration or usages, we'll see that this PossessedBy belongs to the ACharacter class.**  
우클릭 후 선언이나 사용처로 이동하면, 이 `PossessedBy`가 `ACharacter` 클래스의 멤버임을 알 수 있습니다.

**And if I go to Character and search for PossessedBy, I can see that it is in the public section.**  
`ACharacter`로 이동해 `PossessedBy`를 검색하면, public 영역에 정의된 것을 확인할 수 있습니다.

---

**And by the way notice that we see this comment thing happening here for functions that belong to interfaces.**  
그리고 인터페이스 관련 함수들에는 주석이 붙어 있다는 것도 볼 수 있습니다.

**We're actually seeing the comment for parent classes being referred to as interfaces.**  
이는 상위 클래스에서 가져온 함수들이 인터페이스처럼 보이도록 주석을 붙인 것입니다.

---

**Now, we could do that too, if we want.**  
원한다면 우리도 똑같이 따라할 수 있습니다.

**In fact, if you want to copy the same sort of comment style, you can do that as well.**  
같은 스타일로 주석을 붙이고 싶다면 그렇게 하셔도 됩니다.

**It's up to you.**  
완전히 여러분의 선택입니다.

---

**We can say "Begin Interface" and "End Interface".**  
"Begin Interface" 와 "End Interface" 같은 주석을 사용할 수 있습니다.

**So if you want to use that comment style, feel free.**  
이 스타일이 마음에 드신다면 자유롭게 사용하세요.

---

**But I wanted to see that PossessedBy is a public function, and we're also going to want to override virtual void OnRep_PlayerState.**  
중요한 건 `PossessedBy`가 public 함수라는 점이며, `virtual void OnRep_PlayerState`도 오버라이드해야 합니다.

**And we can go ahead and generate the definitions for both of these functions.**  
이 두 함수의 정의도 바로 생성할 수 있습니다.

**There they are.**  
정의가 생성되었습니다.

---

**And if we hit Shift+Shift that allows us to search, we can search for OnRep_PlayerState and we see that that one belongs to the APawn class.**  
`Shift+Shift`를 눌러 검색해보면 `OnRep_PlayerState`는 `APawn` 클래스에 속해 있는 것을 확인할 수 있습니다.

**Here it is, OnRep_PlayerState.**  
여기 `OnRep_PlayerState`가 있습니다.

**And we see here in the Pawn class that this function is also public.**  
`APawn` 클래스에서 이 함수도 public으로 정의되어 있는 걸 볼 수 있습니다.

---

**So we'll go ahead and override that one.**  
따라서 이 함수도 오버라이드하겠습니다.

**Now that we have these two functions, we can do what we need to do, which is call InitAbilityActorInfo.**  
이제 두 함수가 준비되었으니, 해야 할 일을 하면 됩니다. 즉, `InitAbilityActorInfo`를 호출하는 것입니다.

---

**So we'll start with PossessedBy now.**  
`PossessedBy`부터 시작해보겠습니다.

**Remember, this one's for the server, so I'm going to make a comment so we remember: InitAbilityActorInfo for the server.**  
이건 서버에서만 호출되는 함수이므로, `// InitAbilityActorInfo for the server` 라는 주석을 달아 기억하기 쉽게 만듭니다.

---

**And remember, we need to access the player state.**  
그리고 `PlayerState`에 접근해야 한다는 것도 잊지 마세요.

**So I need to call GetPlayerState and we have a template version of this, so we can pass in AAuraPlayerState as the type.**  
`GetPlayerState<AAuraPlayerState>()`를 사용하면, 형변환 없이 바로 `AuraPlayerState` 타입으로 가져올 수 있습니다.

---

**Now I'm going to make a local AuraPlayerState variable.**  
지역 변수로 `AuraPlayerState`를 선언합니다.

__I'm going to say AAuraPlayerState_ AuraPlayerState = GetPlayerState<AAuraPlayerState>();_*  
`AAuraPlayerState* AuraPlayerState = GetPlayerState<AAuraPlayerState>();` 라고 작성합니다.

---

**And I'm going to wrap this in an if statement—actually no, let's just use check instead.**  
이걸 `if` 문으로 감쌀 수도 있지만, `check()` 매크로로 확신을 줄 수 있습니다.

**This should not be a null pointer.**  
이 포인터는 null이 아니어야 합니다.

---

**So we'll say check(AuraPlayerState);**  
따라서 `check(AuraPlayerState);` 라고 작성합니다.

**And then we'll get the AbilitySystemComponent from this player state.**  
그 다음, `AuraPlayerState`로부터 `AbilitySystemComponent`를 가져옵니다.

**We'll call GetAbilitySystemComponent().**  
`GetAbilitySystemComponent()`를 호출합니다.

---

**And from that, we now have the AbilitySystemComponent.**  
이제 ASC를 확보했으니,

**We can call InitAbilityActorInfo and we can pass in the OwnerActor and AvatarActor.**  
`InitAbilityActorInfo()`를 호출하면서 Owner와 Avatar를 전달하면 됩니다.

---

**Now in this case, the OwnerActor is going to be the PlayerState.**  
이 경우 OwnerActor는 `PlayerState`입니다.

**So we're going to pass in AuraPlayerState for the OwnerActor and "this" for the AvatarActor.**  
Owner는 `AuraPlayerState`, Avatar는 `this`(캐릭터 자신)를 넘깁니다.

---

**So that's the difference when you have the ASC on the PlayerState.**  
ASC가 `PlayerState`에 있을 경우 이렇게 차이가 납니다.

**But we have to set this here in PossessedBy because we need to be sure that we have a valid PlayerController set already and that our PlayerState is valid and accessible and we can call this function here.**  
하지만 `PossessedBy`에서 이 작업을 해야 하는 이유는, 이 시점에서 유효한 `PlayerController`와 `PlayerState`가 설정되어 있어야 하기 때문입니다.

**So that takes care of this.**  
이렇게 해서 이 부분은 완료되었습니다.

---

**But remember, we have the PlayerState now. That's a good opportunity to set the AuraCharacter's AbilitySystemComponent pointer.**  
하지만 지금 우리는 `PlayerState`를 가지고 있으므로, 이 기회에 `AuraCharacter`의 `AbilitySystemComponent` 포인터를 설정해줄 수 있습니다.

**So we're going to say AbilitySystemComponent equals... and we'll take AuraPlayerState and call GetAbilitySystemComponent.**  
`AbilitySystemComponent = AuraPlayerState->GetAbilitySystemComponent();` 처럼 설정합니다.

---

**We'll set that and we'll set our AttributeSet as well.**  
이 포인터를 설정한 다음, `AttributeSet`도 설정할 수 있습니다.

**We'll say AttributeSet equals AuraPlayerState->GetAttributeSet().**  
`AttributeSet = AuraPlayerState->GetAttributeSet();` 라고 작성합니다.

---

**And now we're starting to see the benefit of having those getter functions.**  
이제 이런 getter 함수들이 왜 유용한지 체감이 되시죠.

---

**Okay, so these pointers are set here on the server, and we've initialized AbilityActorInfo here on the server.**  
좋습니다. 이제 이 포인터들은 서버에서 설정되었고, `AbilityActorInfo`도 초기화가 완료되었습니다.

**We're going to want to initialize it for the client as well.**  
하지만 클라이언트 측에서도 초기화를 해줘야 합니다.

---

**That's why we're doing it in OnRep_PlayerState, which means we're going to do the very same thing we did here.**  
그래서 `OnRep_PlayerState`에서 동일한 작업을 반복합니다.

**We're going to get the PlayerState, check it, and call InitAbilityActorInfo and set these two pointers.**  
다시 `PlayerState`를 가져와 확인하고, `InitAbilityActorInfo`를 호출하며 포인터들을 설정합니다.

---

**We're going to do the same thing. Now rather than just repeating myself—you may have heard of the DRY principle—Don't Repeat Yourself.**  
이 과정을 반복하는 대신, 여러분도 들어봤을 "DRY(Don't Repeat Yourself)" 원칙을 적용할 수 있습니다.

---

**Rather than copying several lines of code and pasting them, I would like to instead create a function that I can reuse in both of these.**  
코드를 복붙하기보다는, 두 곳에서 재사용할 수 있는 함수를 하나 만드는 것이 좋습니다.

---

**So let's go ahead and go to AuraCharacter here and make a private function that we can just call twice.**  
`AuraCharacter` 클래스 안에 private 함수로 작성해봅시다. 두 군데서 호출만 하면 되니까요.

---

**So I'm going to say `void InitAbilityActorInfo()` — that's really all it's doing.**  
함수 이름은 `void InitAbilityActorInfo()` 정도면 충분합니다. 이 함수는 실제로 그 역할만 하니까요.

---

**So let's make a function definition and it's going to be these lines of code here.**  
이제 방금 작성한 코드들을 잘라내서 이 함수에 붙여넣을 겁니다.

---

**So I'm going to go ahead and Ctrl+X and Ctrl+V.**  
`Ctrl+X`로 잘라내고, `Ctrl+V`로 붙여넣습니다.

---

**Now we have a function that does these things for us.**  
이제 우리가 필요할 때마다 호출할 수 있는 함수가 완성됐습니다.

**We can call it here in PossessedBy and we can call it here in OnRep_PlayerState.**  
이 함수는 `PossessedBy()`와 `OnRep_PlayerState()`에서 모두 사용할 수 있습니다.

---

**And we'll have a comment here that says `InitAbilityActorInfo for the client` and that's it.**  
`OnRep_PlayerState()`에는 "InitAbilityActorInfo for the client"라는 주석을 달면 좋습니다. 이게 전부입니다.

---

**Now, if you didn't do this refactor into a function, that's totally fine.**  
만약 이걸 함수로 분리하지 않았더라도 괜찮습니다.

**It's okay if you repeated yourself. Not a big deal.**  
코드를 반복했어도 큰 문제는 아닙니다.

**I do recommend making a function, but you don't have to.**  
함수로 만드는 걸 추천하긴 하지만, 반드시 그래야 할 필요는 없습니다.

---

**And now we've achieved a couple things.**  
이제 우리는 몇 가지 중요한 것을 완수했습니다.

**For one, our AbilitySystemComponents know who their owner is.**  
첫째, ASC가 자신들의 소유자가 누구인지 알게 되었습니다.

**They know who the OwnerActor is, and they know who the AvatarActor is.**  
이제 OwnerActor와 AvatarActor가 각각 누구인지 정확히 알고 있습니다.

---

**That's going to be very, very useful and convenient as we continue programming in GAS throughout this course.**  
앞으로 GAS를 다뤄가는 과정에서 이건 굉장히 유용하고 편리할 것입니다.

**Because there will be many, many times when we need to access those owners in our abilities or in our various GAS classes.**  
왜냐하면 우리의 Ability나 GAS 관련 클래스들에서 소유자에 접근해야 하는 경우가 아주 많기 때문입니다.

---

**So this is a very important and crucial step.**  
따라서 이 과정은 매우 중요하고 핵심적인 단계입니다.

---

**And before we end the video, I'm going to bring your attention back to the ReplicationMode.**  
영상을 마무리하기 전에, 다시 한번 `ReplicationMode`에 주목해봅시다.

---

**Again, remember, we set our AbilitySystemComponent for the player-controlled character to `Mixed` replication mode.**  
다시 말하지만, 플레이어 캐릭터의 ASC는 `Mixed` 복제 모드로 설정했습니다.

---

**And it's important to know a couple of details when you do this.**  
그리고 이 설정을 할 때 꼭 알아야 할 세부 사항이 있습니다.

---

**For Mixed replication mode, it's important to know that the OwnerActor's Owner must be the Controller.**  
`Mixed` 모드에서는 **OwnerActor의 Owner가 반드시 Controller여야** 한다는 점이 중요합니다.

---

**Now, in our case, the OwnerActor for our ASC (for the player-controlled character) is the PlayerState.**  
지금 우리의 경우 ASC의 OwnerActor는 `PlayerState`입니다.

---

**That's who we set as the OwnerActor in InitAbilityActorInfo.**  
우리는 `InitAbilityActorInfo`에서 OwnerActor로 `PlayerState`를 설정했죠.

---

**Now, we don't need to set the PlayerState's owner to the Controller, because that's what happens by default.**  
우리는 `PlayerState`의 소유자를 따로 설정할 필요가 없습니다. 기본적으로 자동으로 설정되기 때문입니다.

---

**The PlayerState's owner is automatically set to the Controller.**  
`PlayerState`는 기본적으로 `Controller`를 소유자로 가지게 됩니다.

---

**Similarly, for Pawns, this is automatically set in PossessedBy.**  
`Pawn`도 마찬가지로 `PossessedBy`에서 자동으로 소유자가 설정됩니다.

---

**So if the OwnerActor for the ASC is the Pawn, well, in PossessedBy the Pawn's owner will be set to the Controller.**  
ASC의 OwnerActor가 `Pawn`이라면, `PossessedBy` 안에서 자동으로 `Controller`가 소유자로 지정됩니다.

---

**But if your OwnerActor is not the PlayerState and you're using Mixed replication mode,**  
하지만 만약 OwnerActor가 `PlayerState`가 아니고 Mixed 모드를 사용한다면,

**you must call SetOwner on the OwnerActor and set its owner to the Controller.**  
그 경우 `SetOwner()` 함수를 호출해 OwnerActor의 소유자를 명시적으로 `Controller`로 지정해야 합니다.

---

**So even though we don't need to worry about it because our OwnerActor is the PlayerState and its owner is already set to the Controller,**  
지금은 OwnerActor가 `PlayerState`이고, 이미 소유자가 설정되어 있어서 걱정할 필요는 없지만,

**you need to know this because your OwnerActor may be some class that does not automatically get its owner set to the Controller.**  
다른 경우에는 자동으로 설정되지 않는 클래스도 있을 수 있으므로 이 원칙은 알아두셔야 합니다.

---

**In that case, you're going to want to make sure that it is, if applicable.**  
그럴 경우에는 반드시 수동으로 설정해줘야 합니다.

---

**You might have an AbilitySystemComponent on some actor that doesn't have a Controller.**  
Controller가 없는 액터에 ASC가 붙는 경우도 있을 수 있습니다.

---

**In that case, that's fine. Don't worry about it.**  
그럴 때는 괜찮습니다. 걱정하지 않아도 됩니다.

---

**But for Mixed replication mode, the OwnerActor's owner must be the Controller.**  
하지만 Mixed 모드에서는 반드시 OwnerActor의 소유자가 Controller여야 합니다.

---

**So keep this in mind.**  
이 점 꼭 기억해두세요.

---

**This is not something that you'll worry about in this course, in this project,**  
이번 강의나 이 프로젝트에서는 걱정할 일이 없겠지만,

**but keep it in mind for your future GAS projects where things might be different.**  
미래의 GAS 프로젝트에서 상황이 다를 수 있으니 알아두면 좋습니다.

---

**So just something you might want to jot down.**  
그러니 이건 메모해둘 만한 가치가 있는 정보입니다.