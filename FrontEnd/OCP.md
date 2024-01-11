# Design Pattern

## OCP

### OCP: 계방-폐쇠 원칙 (Open/Close Principle)

- **기존의 코드를 변경하지 않으면서, 기능을 추가할 수 있도록 설계**가 되어야 한다는 원칙
- 쉽게 얘기해 **_확장_ 에 대해서는 개방적(Open)** 이고, **_수정_ 에 대해서는 폐쇄적(Close)** 이여야 한다는 의미로 정의 할 수 있음

---

#### OPEN

- 그렇다면 확장은 무엇일까?
  - 새로운 기능이 추가됨을 의미함
- `확장에 개방적이다` 가 의미하는것?

  - 모듈의 확장성을 보장하는 것
  - 새로운 변경 사항이 발생했을 때 유연하게 코드를 추가함으로써 애플리케이션의 기능을 쉽게 확장 할 수 있는 것

  > 즉, 기능 추가 요청이 오면 클래스를 확장을 통해 손쉽게 구현하면서, 확장에 따른 클래스 수정은 최소화 하도록 프로그램을 작성해야 하는 설계 하는것

#### CLOSE

- `수정에 대해서 폐쇄적이다`

  - 객체를 직접적으로 수정하는것을 제한해야 한다는 것

    - 새로운 변경 사항이 발생했을 때 객체를 직접적으로 수정해야 한다면 **새로운 변경사항에 대해 유연하게 대응할 수 없는** 애플리케이션이며, 유지보수에 리소스가 많이 들어가는것 이라고 볼 수 있음

      > 즉, **수정에 폐쇄적이다** === 객체를 직접 수정하지 않고도 변경사항을 적용할 수 있도록 설계하는 것

---

- _쉽게 이해하자면, OCP 원칙은 객체 지향 프로그래밍의 **추상화**를 의미하는 것이다_
- OCP는 다형성과 확장을 가능케 하는 객체지향의 장점을 극대화하는 설계 원칙임으로 우리는 객체 지향 프로그래밍을 하면서, OCP원칙을 지키고, 이용한 것이다

OCP 원칙 위반

```javascript
const friends = () => {
  function live(food) {
    const eat = (food) => {
      if (food === "과자") {
        console.log("냠냠 과자 맛있다!");
      }
      if (food === "과일") {
        console.log("냠냠 과일 맛있다!");
      }
      if (food === "밥") {
        console.log("냠냠 밥 맛있다!");
      }
    };

    eat(food);
  }

  const harry = live("과자");
  const ron = live("과일");
  const hermione = live("밥");
  const malfoy = live("술"); // 말포이 등장
};

friends();
```

<img width="572" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/213e55b3-4e54-4dcb-9885-ea5fd562aa51">

- 위의 상황에서 말포이를 추가하게 된다면 live 함수 내부에 **말포이에 대한 분기를 추가**해 줘야한다
  - 위와 같은 간단한 로직일 경우 변경이 쉽겠지만 보다 복잡한 로직일 경우 말포이 하나를 추가하기 위해서 수많은 변경사항이 필요할 것 > **유지보수의 비용 증가**
- 이러한 상황을 위해서 OCP 원칙을 준수해야한다, 그렇다면 어떤 방식으로 이를 실현해야 할까?
  - 달라지는 부분을 찾아내고, 달라지지 않는 부분과 분리하는 것

OCP 원칙

```javascript
const friends = () => {
  const live = (food) => {
    const eat = () => {
      console.log(`냠냠 ${food} 맛있다!`);
    };
    eat(food);
  };

  const harry = live("과자");
  const ron = live("과일");
  const hermione = live("밥");
  const malfoy = live("술");
};

friends();
```

<img width="576" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/9936e0f8-a746-42fc-817f-ea34235fb2d5">

> OCP === 변경을 위한 비용은 최소화, 확장을 위한 비용은 극대화 하는 것
