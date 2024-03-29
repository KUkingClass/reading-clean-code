# 1장 클린코드

## 프로그래밍이란?

: 기계가 실행할 정도로 상세하게 요구사항을 명시하는 작업

**코드** : 요구사항을 표현하는 언어

<br>

## 르블랑의 법칙 (leblanc's Law)

:  나중은 결코 오지 않는다.

우리 모두는 자신이 짠 코드를 쳐다보며 나중에 손보겠다고 생각한 경험이 있다.

### 나쁜 코드로 치르는 대가

1. 개발 속도를 크게 떨어뜨린다.
2. 나쁜 코드가 쌓일수록 팀 생산성이 떨어진다.
3. 나쁜 코드를 복구하기 위해 인력이 추가로 투입되지만, 결국은 나쁜 코드가 더 많이 양산되어 생산성이 더더욱 떨어진다.

—> 결국, 시간을 들여 **깨끗한 코드를 만드는 노력이 비용을 절감하는 방법**일 뿐만 아니라 전문가로서 살아남는 길이다.

<br>

## 나쁜코드가 생기는 이유

: 대부분의 핑계는 원래 설계를 뒤집는 방향으로 요구사항이 발생하여 일정이 촉박해 제대로 할 시간이 없었다는 이유이다.

하지만 잘못은 전적으로 프로그래머에게 있다. 우리가 전문가답지 못했기 때문이다.

**좋은 코드를 사수하는 일은 바로 프로그래머들의 책임**이다.

'나쁜 코드의 위험을 이해하지 못하는 관리자'의 말을 그대로 따르는 행동은 전문가답지 못하다.

### 기한을 맞추기 위한 나쁜 코드

: 우리는 나쁜 코드가 개발 속도를 떨어뜨린다는 것을 안다. 그럼에도 기한을 맞추기 위해 나쁜 코드를 양산할 수 밖에 없다.

하지만 나쁜 코드를 양산하면 오히려 속도가 늦어져 기한을 맞추지 못한다.

그러므로 기한을 맞추는 유일한 방법은,

언제나 **코드를 최대한 깨끗하게 유지하는 습관**을 들이는 것이고 그것이 전문가이다.

<br>

## 깨끗한 코드

(각 분야의 유명한 프로그래머들의 클린 코드에 대한 의견)

- **우아하고 효율적인 코드**
    - 나쁜 코드는 나쁜 코드를 유혹한다. 나쁜코드는 존재만으로도 계속해서 다른 나쁜코드를 양산해낸다.
    - 철저한 오류 처리세세한 사항까지 철저하게 처리하는 코드를 작성하자.
    - 한 가지를 잘하는 코드함수와 클래스와 모듈은 하나의 로직만 수행하게 하자.  
    
- **가독성이 좋은 코드**
    - 깨끗한 코드는 잘 쓴 문장처럼 읽혀야 한다.
    - 코드는 추측이 아닌 사실에 기반해야 하며 필요한 내용만 담아야 한다.새 코드를 짜면서 작성하는 시간보다 코드를 읽는 시간이 더 많기 때문이다!  
    
- **다른 사람이 고치기 쉬운 코드**
    - 테스트 케이스 없이 깨끗한 코드는 없다.아무리 코드가 우아하고 가독성이 높아도 테스트 케이스가 없으면 깨끗하지 않다.
    - 문학적인 코드읽기 좋은 코드를 작성함으로써 그 코드를 고치기 쉽게 만들자.  
    
- **주의 깊게 작성한 코드**
    - 시간을 들여 깔끔하고 단정하게 정리한 코드가 곧 클린 코드다.  
    
- **중복이 없는 코드**
    - 여러 기능을 수해하는 메서드를 찾아 메서드 추출 기법을 적용해 기능을 분리하자  
    
- **집합이 추상화된 코드**
    - 캡슐화를 하여 확장에 용이하게 하자

<br>

## 보이스카우트 규칙

"캠프장은 처음 왔을 때보다 더 깨끗하게 해놓고 떠나라."

- 한꺼번에 많은 시간과 노력을 투자해 코드를 정리할 필요가 없다.
- 변수 이름 하나를 개선하고, 조금 긴 함수를 분할하고, 약간의 중복을 제거하고, 복잡한 if 문 하나를 정리하면 충분하다.
- 결국 시간이 지날수록 코드가 좋아지는 프로젝트가 된다.
