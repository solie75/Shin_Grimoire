## I. 클린 코드의 11가지 원칙

### 1. 논리가 간단해야 버그가 숨어들지 못한다

복잡한 if-else 중첩은 버그의 온상입니다. 논리를 직선적으로 만드세요.

- **Bad:** `if`문 안에 `if`가 또 있고, 그 안에 `else`가 복잡하게 얽힌 구조.
    
- **Good (Guard Clause):** 조건이 맞지 않으면 빨리 리턴(Return Early)하여 뒤쪽 논리를 단순하게 유지합니다.
    

Java

```
public void processOrder(Order order) {
    if (order == null) return; // 논리 단순화
    if (!order.isValid()) throw new IllegalStateException();
    
    pay(order);
}
```

### 2. 의존성을 최대한 줄여야 유지보수가 쉬워진다

클래스가 다른 클래스를 너무 많이 알면, 하나를 고칠 때 다 같이 무너집니다.

- **Good:** 내부에서 직접 객체를 생성하지 않고, 필요한 기능을 인터페이스로 추상화하여 결합도를 낮춥니다.
    

### 3. 오류는 명백한 전략에 의거해 철저히 처리한다

예외를 무시하거나(`catch {}`), 단순히 `Exception`으로 뭉뚱그리지 마세요.

- **Good:** 비즈니스 의미가 담긴 커스텀 예외를 사용하고, 전역 예외 처리기(Global Exception Handler)를 두어 일관되게 처리합니다.
    

Java

```
try {
    paymentService.pay();
} catch (InsufficientBalanceException e) {
    // 잔액 부족이라는 명확한 전략에 따른 처리
    log.error("Payment failed: {}", e.getMessage());
}
```

### 4. 성능을 최적으로 유지하라

성능이 너무 나쁘면 개발자들이 성능을 핑계로 코드를 지저분하게(Hack) 짤 변명을 하게 됩니다.

- **Good:** 기본적인 알고리즘 효율을 지키고, 불필요한 DB 호출을 줄여 깔끔한 상태에서도 충분한 성능을 내게 합니다.
    

### 5. 깨끗한 코드는 한 가지를 제대로 한다

함수 이름이 `doEverything()`이라면 잘못된 것입니다.

- **Good:** `calculateTax()`, `saveUser()`처럼 딱 그 일만 하는 함수를 만듭니다.
    

### 6. 깨끗한 코드는 문장처럼 읽혀야 한다

코드는 인간이 읽는 산문과 같아야 합니다.

Java

```
// Good
if (user.isEligibleForDiscount()) { ... }
```

### 7. 단위 테스트와 인수 테스트가 존재해야 한다

테스트는 코드의 '명세서'이자 '안전망'입니다.

- **Unit Test:** 함수 하나하나를 검증.
    
- **Acceptance Test:** 사용자 시나리오(요구사항)가 실제로 작동하는지 검증.
    

### 8. 깨끗한 코드는 의미 있는 이름이 붙는다

변수명에 `a`, `b`, `list1` 같은 이름을 쓰지 마세요.

- **Bad:** `int d; // 경과 시간(일)`
    
- **Good:** `int daysSinceLastUpdate;`
    

### 9. 특정 목적을 달성하는 방법은 하나만 제공한다

팀원마다 삭제를 `delete()`, `remove()`, `clear()`로 제각각 구현하면 혼란이 옵니다. 표준화된 한 가지 방법만 제공하세요.

### 10. 각 의존성을 명확히 정의한다

이 클래스가 작동하기 위해 무엇이 필요한지(DB, API 등)를 명확히 드러내야 합니다. 주로 생성자 주입을 통해 명시합니다.

### 11. API는 명확하며 최소로 줄인다

외부에 노출하는 `public` 메서드는 적을수록 좋습니다. 내부 구현은 `private`으로 숨기세요.

---

## II. 객체지향 설계 5대 원칙 (SOLID)

### 1. SRP: 단일 책임 원칙

- **개념:** 클래스는 단 하나의 변경 이유만 가져야 함.
    
- **Code:**
    

Java

```
// Bad: 정보 유지와 파일 저장을 한꺼번에 함
class UserSettings {
    void changeEmail(String email) { ... }
    void saveToFile() { ... }
}

// Good: 분리함
class UserSettings { void changeEmail(String email) { ... } }
class SettingsRepository { void save(UserSettings settings) { ... } }
```

### 2. OCP: 개방-폐쇄 원칙

- **개념:** 확장에는 열려 있고 수정에는 닫혀 있어야 함.
    
- **Code:** 새로운 할인 정책이 추가되어도 기존 `OrderService`는 수정하지 않습니다.
    

Java

```
interface DiscountPolicy { int apply(int price); }

class FixedDiscount implements DiscountPolicy { ... }
class RateDiscount implements DiscountPolicy { ... } // 추가 시 기존 코드 수정 없음
```

### 3. LSP: 리스코프 치환 원칙

- **개념:** 자식은 언제나 부모를 대체할 수 있어야 함. (부모의 규약을 깨지 마라)
    
- **Bad:** `Bird` 부모 클래스의 `fly()` 메서드를 `Penguin` 자식 클래스에서 "못 날아요"라고 예외를 던지게 구현하면 LSP 위반입니다.
    

### 4. ISP: 인터페이스 분리 원칙

- **개념:** 쓰지 않는 기능은 구현하지 않도록 인터페이스를 작게 쪼개라.
    
- **Code:**
    

Java

```
// Bad: 스마트폰 인터페이스에 '유선전화' 기능이 포함된 경우
// Good: CallingDevice, InternetDevice로 쪼개어 필요한 것만 구현
```

### 5. DIP: 의존역전 원칙

- **개념:** 구체적인 것(Class)보다 추상적인 것(Interface)에 의존하라.
    
- **Code:**
    

Java

```
// Good: 운전자는 '아반떼'나 '테슬라'에 의존하는 게 아니라 '자동차'라는 인터페이스에 의존함
class Driver {
    private final Car car; // 특정 모델이 아닌 인터페이스에 의존
    public Driver(Car car) { this.car = car; }
}
```

---

이 원칙들을 한 문장으로 요약하면 **"변화에 유연하고 읽기 편한 코드를 만드는 법"**입니다. 이 중에서 본인이 작성한 코드에 바로 적용해보고 싶은 원칙이 있다면 말씀해 주세요! 그 부분을 집중적으로 리팩토링 예시를 보여드릴 수 있습니다.