# 3장. 함수

함수를 잘 만드는 방법을 소개한다.

## 목차

* [작게 만들어라](#-------)
* [한 가지만 해라! ← 🌟 함수 당 추상화 수준은 하나로 🌟](#----------------------------------)
* [Switch 문](#switch--)
* [서술적인 이름을 사용하라!](#--------------)
* [함수 인수](#-----)
  + [인수 1개를 넘기는 경우 3가지](#---1----------3--)
  + [플래그 인수](#------)
  + [이항 함수](#-----)
* [부수 효과를 일으키지 마라!](#---------------)
  + [출력 인수](#-----)
* [명령과 조회를 분리하라!](#-------------)
* [오류 코드보다 예외를 사용하라!](#-----------------)
  + [오류 코드 의존성](#---------)
* [반복하지 마라! `DRY: Don’t Repeat Yourself`](#----------dry--don-t-repeat-yourself-)
  + [DRY를 지키지 않는 예시? 소스코드 복붙?](#dry---------------------)
  + [중복일까?](#-----)
  + [중복이 아닐까?](#--------)
* [함수를 어떻게 짜죠?](#-----------)
* [결론](#--)

## 작게 만들어라

- 각 함수가 한 가지 일만 하도록 최대한 작게 만들 것.
  - <u>**줄 수에 집착하기보다 정말로 ‘한 가지’ 일만 하는지**</u>를 생각하는 게 좋을 것 같다.
- 함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안된다. 넘어서야 한다면 함수 이름을 적절하게 짓고 함수를 호출해라.

## 한 가지만 해라! ← 🌟 함수 당 추상화 수준은 하나로 🌟

그래서 그 ‘<u>**한 가지**</u>’의 기준이 뭘까?

추상화 수준이 하나여야 한다는 것.

- 추상화 수준을 섞으면 근본 개념인지 세부사항인지 구분하기 어려워 뒤섞인다. → <mark>최악은 점점 **세부사항이 더 추가**된다는 것!</mark>

- 추상화 수준을 유지하기 어렵다면… 위에서 아래로 이야기를 읽어 내려가듯이 코드를 작성하자. 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 오는 것. 그리고 각 함수는 한 가지만 한다.

  ```java
  public class Human {
  	public void spendADay() {
  		spendMorning()
  		spendAfternoon()
  		spendEvening()
  	}
  
  	private void spendMorning() {
  		getUp()
  		takeAShower()
  		eatBreakFast()
  		goToWork()
  	}
  
  	private void getUp() {
  		turnOffAlarm()
  		makingABed()
  	}	
  
  	private void takeAShower() {
  		goToBathroom()
  		brushTeeth()
  		turnOnShower()
  		//...
  	}
  
  	//...
  
  	private void spendAfternoon() {
  		//...
  	}
  	
  	//...
  }
  ```

- 내 함수가 한가지 작업을 하는 지 모르겠다면 생각해보자

  - 함수에서 의미 있는 이름으로 다른 함수를 추출할 수 있다면 여러 작업을 하는 셈

  - 함수를 여러 섹션으로 나눌 수 있다면 여러 작업을 하는 셈

## Switch 문

switch 문은 N가지를 처리하기 때문에 <u>**한가지를 처리하지 않는다**</u>.

하지만 꼭 switch 문을 써야할 때가 있다. 그럴 땐 각 switch 문으로 구분되는 것들을 클래스로 만들고, 각 클래스의 객체를 생성하는 저차원클래스를 만들어 그 부분에만 switch 문을 숨겨라. 

함수에 switch 문을 사용했을 때 큰 문제는 해당 함수와 구조가 동일한 함수가 무한정 존재할 수 있다는 것.

- 동일한 구조의 반복
    - 여러가지 일을 한다.
    
    ```java
    public void play(Instrument i) throws InvalidInstrumentType {
    	switch(i.type) {
    		case BASS_GUITAR:
    			playBassGuitar(i)
    			return;
    		case PIANO:
    			playPiano(i)
    			return;
    		case DRUM:
    			playDrum(i)
    			return;
    		default:
    			throw new InvalidInstrumentType(i.type);
    	}
    }
    
    public void tune(Instrument i) throws InvalidInstrumentType {
    	switch(i.type) {
    		case BASS_GUITAR:
    			tuneBassGuitar(i)
    			return;
    		case PIANO:
    			tunePiano(i)
    			return;
    		case DRUM:
    			tuneDrum(i)
    			return;
    		default:
    			throw new InvalidInstrumentType(i.type);
    	}
    }
    //...
    ```

- 책 예제 - 추상 팩토리? 추상 팩토리 패턴이라기 보다는 그냥 팩토리 메서드 패턴 같음

<details><summary><code>switch문 객체 생성 예제</code></summary>
  [참고](https://yeah.tistory.com/4)
  - 기본 객체

    ```java
    abstract class Instrument {
      String name;
      Instrument(String name) {
        this.name = name;
      }
      void play();
      void tune();
    }
    ---
    public class BassGuitar implements Instrument {/*..*/} 
    public class Piano implements Instrument {/*..*/} 
    public class Drum implements Instrument {/*..*/} 
    ```

  - 팩토리 패턴

    ```java
    public class InstrumentFactory {
      public Instrument create(InstrumentType type, String name) throws InvalidInstrumentType {
        switch(type) {
          case BASS_GUITAR:
            return new BaseGuitar(name);
          case PIANO:
            return new Piano(name);
          case DRUM:
            return new Drum(name);
          default:
            throw new InvalidInstrumentType(type);
        }
    }
    ```

    - 생성 코드와의 강력한 결합

  - 팩토리 메서드 패턴

    ```java
    abstract class InstrumentFactory {
      public final Instrument create(InstrumentType type, String name) {
        // 하위 클래스로 위임
        return this.createInstrument(type, name);
      }

      abstract public Instrument createInstrument(InstrumentType type, String name);
    }
    ---
    public class InstrumentFactoryImpl extends Factory {
      @Override
      public Instrument createInstrument(InstrumentType type, String name) {
        switch(type) {
          case BASS_GUITAR:
            return new BaseGuitar(name);
          case PIANO:
            return new Piano(name);
          case DRUM:
            return new Drum(name);
          default:
            throw new InvalidInstrumentType(type);
        }
      }
    }
    ```

    - 기존 Factory 클래스를 추상 클래스로 적용하여 객체 생산 분리, 구현부 숨겨짐

  - 추상 팩토리 패턴: 생성해야 될 각각의 객체마다 팩토리 생성

    ```java
    public class BaseGuitarFactory extends InstrumentFactory {
      @Override
      public Instrument createInstrument(InstrumentType type, String name) {
        return new BaseGuitar(name);
      } 
    }
    ----
    public class DrumFactory extends InstrumentFactory {
      @Override
      public Instrument createInstrument(InstrumentType type, String name) {
        return new Drum(name);
      } 
    }
    ```
</details>
  
## 서술적인 이름을 사용하라!

- 즉 함수가 하는 일을 그대로 표현할 수 있는 이름을 사용해라.

- 함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.
  - 이름이 긴 건 상관없지만, 이름이 너무 길고 짓기 어렵다면 <u>**함수가 한가지 일만 하는 게 맞는 지 한 번 생각**</u>해보고, 아니라면 함수를 나눠보자.

- 이름을 붙일 때는 일관성이 있어야 한다. 이름을 보고 나머지 함수도 이렇겠지? 라고 생각하는 대로 동작해야 한다.

## 함수 인수

- 함수의 인수는 적을 수록 이상적이다.
- 함수의 추상화 수준을 따질 땐 **함수 이름과 인수 사이에 추상화 수준**도 따져봐야 한다. 함수 이름과 인수는 동사/명사 쌍을 이루어서 바로 이해할 수 있어야 한다.
- 인수가 많으면 테스트할 때도 부담스러워 진다.

### 인수 1개를 넘기는 경우 3가지

1. 질문을 던지는 경우 - 예) 인수로 넘기는 게 존재하니?

2. 인수로 뭔가를 변환해 결과를 반환하는 경우

3. 이벤트 함수

   - 출력 인수는 없지만 입력 인수로 시스템 상태를 바꿈

   - 예) 

     ```java
     passwordAttemptFailedNtimes(int attempts)
     ```

     - 이벤트라는 사실이 코드에 명확히 드러나야 함. 그러므로 이름과 문맥을 주의해서 선택하자.

     - 🧐 이벤트 함수가 정확히 뭐를 말하는 건지 모르겠는데 위의 예시를 들어보면

       ```java
       public void passwordAttemptFailedNtime(int attempts) {
       	if (attempts < MAX_PASSWORD_ATTEMPT) {
       		return;
       	}
       	throw new PasswordAttemptFailedException(attempts);
       }
       ```

       이런 식의 함수?

위 세 가지 경우가 아니라면 단항 함수는 피하자.

예를 들어 인수로 뭔가를 변환하고 결과를 반환하지 않는 경우, 즉 인수를 출력 인수로 쓰는 경우 같을 땐 인수를 그대로 돌려주더라고 결과를 반환하자.

또 위 세 가지 경우가 아니라면 피하라는 의미는, **각각의 경우를 한꺼번에 하지 말라는 것**도 포함이다. 즉 한 가지만 할 것.

### 플래그 인수

- 함수로 bool 값을 넘긴다는 것의 의미 == 이 함수는 bool 값에 따라 **여러 가지 일**을 한다고 선포하는 것과 같음.
- 이럴 땐 bool값에 따른 함수를 여러 개로 나누는 것이 좋다.

### 이항 함수

- 인수가 2개가 오기 위해선 그 인수들 간에 자연스러운 순서가 있어야 한다.

- 만약 그렇지 않은 경우 함수의 이름에 인수를 순서대로 넣어 기억할 필요가 없게 하자.

  ```java
  assertEquals(expected, actual)
  // better
  assertExpectedEqualsActual(expected, actual)
  ```

- 만약 꼭 이항 함수를 사용해야 할 경우 - 소개된 방법

  - 함수를 한 인수의 클래스의 메서드로 넣어 보자
  - 한 인수를 현재 클래스의 멤버 변수로 넣어보자
  - 새 클래스를 만들어 생성자에서 한 인수를 받고 메서드에서 나머지 인수를 받는 식으로 해보자..
  - 혹은 인수들을 독자적인 클래스 변수로 선언해보자.

- 즉 **맥락을 부여**하자.

  - 2장 의미 있는 이름: 의미 있는 맥락을 추가하라

## 부수 효과를 일으키지 마라!

> side effect: 함수에 의해 함수 외부의 값이 변하는 것

부수 효과를 일으키는 함수의 경우 시간적 결합이나 순서 종속성을 일으킨다.

- 시간적 결합: 특정 상황에서만 호출할 수 있는 함수가 됨
- 순서 종속성: 반드시 메서드 A가 호출된 다음에야 메서드 B가 호출될 수 있음

만약 꼭 시간적 결합이 필요하다면 함수 이름에 분명히 명시할 것.

### 출력 인수

- 출력 인수는 독자가 코드를 재차 확인하게 만드므로 좋지 않다.

- 객체 지향 언어에서는 출력 인수를 사용할 필요가 거의 없다. 

  출력 인수로 사용하라고 설계한 변수가 `this` 이기 때문

  - 즉 출력 인수를 사용할 상황에 있다면 그 인수 클래스의 메서드로 만들어야 하는 건 아닐 지 생각해보자.

## 명령과 조회를 분리하라!

함수는 뭔가를 수행하거나 답하거나 둘 중 하나만.

**객체 상태를 변경하거나 반환하거나 둘 중 하나만.**

## 오류 코드보다 예외를 사용하라!

오류 코드를 사용할 경우 여러 단계로 중첩될 수 있고 곧바로 오류를 처리해야 한다.

반면 오류 코드 대신 예외를 사용하면 오류 처리 코드를 분리할 수 있다.

- 이 때도 ‘한 가지’만 해야 한다는 걸 기억하자.

  ```java
  void action() { // 이 함수는 try - catch 블록만 있다.
  	try {
  		정상_동작();
  	}
  	catch (Exception e) {
  		오류_처리();
  	}
  }
  ```

### 오류 코드 의존성

- 오류 코드를 반환한다는 얘기는 어디선가 오류 코드를 정의한다는 것. <u>**Error Enum으로 정의한다면 해당 enum이 변한다면 enum을 사용하는 클래스 전부를 다시 컴파일하고 배치해야되기 때문에 변경이 어려워진다**</u>.

- 오류 코드 대신 예외를 사용하면 새 예외는 Exception 클래스에서 파생되므로 재컴파일/재배치 없이도 새 예외 클래스를 추가할 수 있다.

- 🧐 여기서 해당 enum이 변한다면 클래스 전부가 모두 변한다는 것의 의미?

  ```java
  public enum Error {
  	OK,
  	INVALID,
  	NO_SUCH,
  	...
  }
  ```

  - **이펙티브 자바: 자바 enum은 클라이언트 코드를 재컴파일 하지 않고도 상수를 더하거나 순서를 바꿀 수 있음**.

    - int 상수값과 비교한 enum 장점: 공개되는 것이 오직 필드의 이름뿐이라, 정수 열거 패턴과 달리 상수 값이 클라이언트로 컴파일되어 각인되지 않기 때문에 새로운 상수나 순서를 바꾸더라도 클라이언트는 다시 컴파일 필요가 없음
    - 그럼 어떤 경우에서 재컴파일을 얘기하는 걸까?

  - 하고 찾아봤더니 같은 질문이 있었음: https://stackoverflow.com/questions/38150393/enum-change-vs-adding-new-exception-need-for-recompilation

    > adding another value to the enum requires another compilation for that change to be picked up.

  - 결론

    - 단순히 새로운 값을 추가하거나 순서를 바꾸는 건 재컴파일을 하지 않아도 되는 게 맞음 :o: (하지만 만약 클라이언트 코드에 스위치 문으로 enum을 구분하는 게 있다면 switch문도 수정해야할 것)
    - 만약 enum의 행동이 바뀌면 재컴파일해야되는 게 맞음. enum에 method가 추가되면 재컴파일 해야한다는 뜻
    - ∴ 따라서 만약 enum이 정말 '에러 코드들 모음집'으로만 쓰인다면 재컴파일 측면에서 오류 코드와 비교해서 큰 효율성 차이는 없는 것으로 보임. 근데 만약 에러 코드에 따른 추가 메서드(원인 저장하거나, 에러에 따른 다른 행동 제공하거나)가 필요하다면 재컴파일이 필요하니 효율성 차이가 있음

## 반복하지 마라! `DRY: Don’t Repeat Yourself`

같은 알고리즘을 여러 곳에 쓰지 말자.

### DRY를 지키지 않는 예시? 소스코드 복붙?

단순히 소스코드 복붙만이 DRY를 지키지 않는 예시가 아니다. DRY는 **‘지식의 중복, 의도의 중복’**이다. 똑같이 생긴 소스코드라도 지식, 의도가 다르다면 중복이 아니다. 다르게 생긴 코드라도 지식, 의도의 중복이면 중복이다.

### 중복일까?

주문 프로그램에서 연령과 주문 수량을 기록하고 검증하는 기능이 필요하다고 해보자.

연령과 주문수량은 모두 숫자이고, 0보다 커야 한다. 이걸 검증 코드로 작성하면 아래와 같다.

```java
public void validateAge(String value):
	validateType(value, Integer.class)
	validateMinInteger(value, 1)

public void validate_quantity(String value):
	validateType(value, Integer.class)
	validateMinInteger(value, 1)
```

두 함수의 내용이 동일하다. 그럼 DRY 위반일까?

아니다. 코드는 동일하지만 두 함수가 필요하는 지식은 다르다. 두 함수는 각각 서로 다른 것을 검증하고 있지만, 우연히 규칙이 같은 것 뿐이다.

### 중복이 아닐까?

예를 들어 선을 표현하는 클래스가 아래와 같이 있다.

```java
class Line {
	Point start;
	Point end;
	double length;
}
```

이 때 시작점과 끝점이 정해지면 길이도 정해진다. 근데 길이가 또 있다! 그래서 중복이다. 따라서 길이는 계산되는 필드로 만드는 것이 낫다.

```java
class Line {
	Point start;
	Point end;
	double length() { return start.distanceTo(end); }
}
```

> 하지만 개발을 진행하다 보면 성능 상의 이유로 DRY 원칙을 위배할 수도 있다. 비용이 많이 드는 연산을 여러 번 수행하지 않기 위해 데이터를 캐싱할 때 종종 일어난다.

## 함수를 어떻게 짜죠?

글짓기를 하는 것처럼 짜자. 처음엔 함수가 복잡하더라도 일단 그 코드를 테스트하는 단위 테스트 케이스를 만들자. 그리고 계속해서 리팩터링하면서 단위 테스트를 통과하는 걸 확인하자.

처음부터 탁 짜내지 않는다. 그게 가능한 사람은 없다.

## 결론

시스템을 구현할 프로그램이 아니라 풀어갈 이야기로 여기자. 진짜 목표는 시스템이라는 이야기를 풀어나가는 것이다.

함수는 한 가지만 한다. 한 함수 내에서 추상화 수준을 유지하고 높은 것부터 써내려가자. 인수가 많아진다면 해당 인수와 함수간의 관계를 생각해보고 클래스를 생성하거나 멤버 변수로 추가할 지 생각해보자.
