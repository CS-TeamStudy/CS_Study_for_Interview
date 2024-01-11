# 의존 역전 원칙 - DIP (Dependency Inversion Principle)

- 고수준 모듈이 저수준 모듈에 의존해서는 안됩니다.
- Class간 의존관계가 형성되는 상황이 생긴다면, 그 Class를 직접 참조하는 것이 아니라 그 대상의 상위 요소로 참조하라는 원칙이다.

# 예시

1. DIP를 준수하지 않은 코드

```js
// 저수준 모듈
class LightBulb {
  constructor() {
    this.isOn = false;
  }

  turnOn() {
    this.isOn = true;
    console.log('Light Bulb ON');
  }

  turnOff() {
    this.isOn = false;
    console.log('Light Bulb OFF');
  }
}

// 고수준 모듈
class Switch {
  constructor() {
    this.lightBulb = new LightBulb(); // DIP를 준수하지 않음
  }

  operateSwitch() {
    if (this.lightBulb.isOn) {
      this.lightBulb.turnOff();
    } else {
      this.lightBulb.turnOn();
    }
  }
}

// 클라이언트 코드
const lightSwitch = new Switch();
lightSwitch.operateSwitch(); // Light Bulb ON
lightSwitch.operateSwitch(); // Light Bulb OFF
```

- Switch가 LightBulb에 직접 의존하고 있다. 이는 전구의 구현에 의존하기 때문에 유연성이 떨어지고, 다른 장치로 교체하기가 어렵다.

2. DIP를 준수한 코드

```js
// 저수준 모듈
class LightBulb {
  constructor() {
    this.isOn = false;
  }

  turnOn() {
    this.isOn = true;
    console.log('Light Bulb ON');
  }

  turnOff() {
    this.isOn = false;
    console.log('Light Bulb OFF');
  }
}

// 고수준 모듈
class Switch {
  constructor(device) {
    this.device = device; // DIP를 준수함
  }

  operateSwitch() {
    if (this.device.isOn) {
      this.device.turnOff();
    } else {
      this.device.turnOn();
    }
  }
}

// 클라이언트 코드
const lightBulb = new LightBulb();
const lightSwitch = new Switch(lightBulb);
lightSwitch.operateSwitch(); // Light Bulb ON
lightSwitch.operateSwitch(); // Light Bulb OFF

// 저수준 모듈
class VacuumCleaner {
  constructor() {
    this.isOn = false;
  }

  turnOn() {
    this.isOn = true;
    console.log('Vacuum Cleaner ON');
  }

  turnOff() {
    this.isOn = false;
    console.log('Vacuum Cleaner OFF');
  }
}

// 클라이언트 코드
const vacuumCleaner = new VacuumCleaner();
const vacuumCleanerSwitch = new Switch(vacuumCleaner);
vacuumCleanerSwitch.operateSwitch(); // Vacuum Cleaner ON
vacuumCleanerSwitch.operateSwitch(); // Vacuum Cleaner OFF
```

- Switch가 추상화된 device에 의존하도록 수정
- 클라이언트 코드에서는 Switch를 생성할 때 어떤 종류의 장치를 사용할지 주입하므로, 전구 뿐만 아니라 다른 장치로도 쉽게 교체할 수 있다.

## 리액트에서의 적용

1. DIP를 준수하지 않은 코드

```js
// 저수준 모듈
const apiService = {
  fetchData: () => {
    return Promise.resolve('Data from the API');
  },
  ...
};

// 고수준 모듈
const DataFetcher = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    apiService.fetchData().then((result) => {
      setData(result);
    });
  }, [apiService]);

  return (
    <div>
      <h1>Data Fetcher</h1>
      <p>{data}</p>
    </div>
  );
};

// 클라이언트 코드
const App = () => {
  return <DataFetcher />;
};
```

- DataFetcher가 apiService에 직접 의존하고 있다.

1. DIP를 준수한 코드

```jsx
// 저수준 모듈
const apiService = {
  fetchData: () => {
    return Promise.resolve('Data from the API');
  },
  ...
};

// 고수준 모듈
const DataFetcher = ({ api }) => {
  const [data, setData] = useState(null);

  useEffect(() => {
    api.fetchData().then((result) => {
      setData(result);
    });
  }, [api]);

  return (
    <div>
      <h1>Data Fetcher</h1>
      <p>{data}</p>
    </div>
  );
};

// 클라이언트 코드
const App = () => {
  return <DataFetcher api={apiService} />;
};
```

- DataFetcher가 apiService에 직접 의존하지 않기 때문에 다른 api 서비스로 쉽게 변경 가능
