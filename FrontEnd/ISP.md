### 객체 지향의 5대 원칙 (SOLID)

> 1. **단일 책임 원칙** (Single Responsibility Principle)
> 2. **개방 폐쇄 원칙** (Open Closed Principle)
> 3. **리스코프 치환 원칙** (Liscov Substitution Principle)
> 4. **인터페이스 분리 원칙** (Interface Sergregation Principle)
> 5. **의존성 역전 원칙** (Dependency Inversion Principle)

<br />

## ISP (Interface Segregation Principle, 인터페이스 분리 원칙)

인터페이스 분리 원칙이란 객체는 자신이 사용하는 메서드에만 의존해야 한다는 법칙입니다.<br />
즉 범용적인 인터페이스보다 사용자가 실제로 사용하는 것에 맞게 인터페이스를 분리해야 하는 설계입니다.

```tsx
interface Espresso {
  Espresso(): void;
}

interface HotWater {
  HotWater(): void;
}

interface SteamMilk {
  SteamMilk(): void;
}

// 에스프레소만 관여하는 클래스
class EspressoMachine implements Espresso {
  public Espresso(): void {
    // ...
  }
}

// 스팀우유만 관여하는 클래스
class SteamMachine implements SteamMilk {
  public SteamMilk(): void {
    // ...
  }
}

// 다양한 기능이 있는 커피머신 클래스
class CoffeeMachine implements Espresso, HotWater, SteamMilk {
  public Espresso(): void {
    // ...
  }
  
  public HotWater(): void {
    // ...
  }
  
  public SteamMilk(): void {
    // ...
  }
}
```

만약에 하나의 커피 머신이 있다고 가정했을 때, 에스프레소 샷을 내리려는 사람, 뜨거운 물을 받으려는 사람, 스팀봉을 사용하는 사람이 있습니다. 이처럼 커피 머신은 사용자에게 다양한 기능을 제공하지만, 다른 기능에 영향을 받지 않고 사용자가 필요한 기능만 수행해야 합니다.


**implements**❓<br />
class의 interface에 만족하는지 여부를 체크할 때 사용됩니다.
implements한 interface의 타입이 없다면 에러를 반환합니다.

<br />

### SRP와 ISP의 차이

ISP(인터페이스 분리 원칙)는 SRP(단일책임원칙)와 비슷하다고 생각할 수 있습니다. 그러나 ISP 인터페이스의 단일 책임을 강조하며, SRP는 클래스의 단일 책임과 관련되어 있습니다. **SRP를 맞추기 위해서는 클래스를 분리**해야 한다면, **ISP는 인터페이스를 분리**합니다.

![image](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/116873887/117078f7-9348-46f0-a656-f75c7f1487ff)


위와 같이 SRP를 만족한다고 해서 절대 ISP도 만족되는 것은 아닙니다.

<br />

### 주의사항

ISP 원칙에서는 **한 번 분리된 인터페이스를 다시 분리하는 것을 금지**합니다.<br />
이미 구현되어 있는 프로젝트의 특정 인터페이스를 사용하던 클래스와 해당 클래스 내부의 여러 로직에서 문제가 일어날 수 있기 때문입니다.
