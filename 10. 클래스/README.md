# 클린코드 10장 클래스

## 클래스는 작아야한다

- 클래스가 맡은 책임을 줄이자!

- 단일 책임 원칙 (SRP Single Responsibility Principle)

## 클래스 이름을 명확히 하자

- 클래스의 이름은 클래스의 책임을 보여줘야 한다.

## 클래스는 추상화수준이 일치해야한다

- 클래스가 다른 클래스에 의존할 때, 두 클래스의 추상화 수준이 일치해야 한다.

## OCP에 맞추어 작성하자

- Open-Closed Principle

- `확장`에 대해 `개방적`이고, `변경`에 대해서는 `폐쇄적`이어야 한다.

## 응집도 - Cohesion

    클래스는 인스턴스 변수가 작아야한다. 몇몇 함수가 몇몇 변수만 사용하면 클래스를 분리하자.

객체지향에서는 모든 것이 변한다. 오브젝트에 대한 설계와 이를 구현하는 코드가 변한다. **관심사의 분리(Separation of Concerns)** 는 관심이 같은 것끼리는 하나의 객체 안으로 또는 친한 객체로 모이게 하고, 관심이 다른 것은 가능한 따로 떨어져서 서로 영향을 주지 않도록 분리하는 것이라고 생각할 수 있다.

## DIP를 준수하자

    Dependency Inversion Principle

- 구현 클래스에 의존하는 것이 아니라 추상화된 인터페이스나 추상 클래스에 의존해야한다.

- **`낮은 결합도는 높은 응집도보다 더 민감한 원칙이다.`** 느슨한 연결은 관계를 유지하는데 꼭 필요한 최소한의 방법만 간접적인 형태로 제공하고, 나머지는 서로 독립적이고 알 필요도 없게 만들어주는 것이다. 결합도가 낮아지면 변화에 대응하는 속도가 높아지고, 구성이 깔끔해진다. 또한 확장하기에도 매우 편리하다.

## SOLID

    SRP, OCP, LSP, ISP, DIP

- Single responsibility principle
- Open/closed principle
- Liskov substitution principle
- Interface segregation principle
- Dependency inversion principle

## 컴포지션

- 상속보다는 컴포지션(Composition)을 사용하여 재사용성을 높이자.

- is-a 관계보단 has-a 관계로 가자

- 상속은 결합도를 높이고, 유연성을 저하시킬 수 있습니다. 따라서, 컴포지션을 사용하여 클래스를 구성하는 것이 좋다.
