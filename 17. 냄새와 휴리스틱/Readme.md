# [CleanCode] 17장 - Smell & Heuristic

## Overview

- 휴리스틱
    - Heutiskein → '찾아내다', '발견하다’

<aside>
🌱 발견법 - 간편 추론, 경험을 기반으로 **그때그때 상황과 직관에 따라 행동**하여 결론을 도출하는 것

</aside>

- 휴리스틱 알고리즘
    - 최적 해를 보장하지 않는다.
    - Greedy
    - Travelling Salesman Problem

---

## 저자의 휴리스틱

### 주석

<aside>
🌱 주석은 코드만으로 다하지 못하는 설명을 부언한다.

</aside>

1. 부적절한 정보
    - 코드만으로 충분한데 구구절절 설명하는 주석은 없어야 한다.
    
    ```kotlin
    i++ // i 증가
    ```
    
    - function **signature**만 달랑 기술하는 Javadoc은 사용하지 않아야한다.
    
    ```kotlin
    /** 
     * @param sellRequest
     * @return
     * @throws ManagedComponentException
     */
    public SellResponse beginSellItem(SellRequest sellRequest)
    throws ManagedComponentException
    ```
    
2. 주석 처리된 코드
    - 주석 처리된 코드를 발견하면 즉각 지워버려야 한다.

### 함수

1. 너무 많은 parameter
    - parameter 개수는 작을수록 좋다. 아예 없는게 가장 좋다!
    - 넷 이상은 최대한 피해야한다.
    
    ```kotlin
    fun testEffect(key1: Any?, key2: Any?, key3: Any?, key4: Any?) { }
    
    fun testEffect(
        key1: Any?, 
        key2: Any?, 
        key3: Any?, 
        key4: Any?
    ) { }
    ```
    

1. flag parameter ✨
    - boolean, int, enum 등 플래그 파라미터는 함수가 내부적으로 여러 기능을 수행한다는 뜻
    - **플래그를 넘겨 제어하는 대신, 새로운 함수를 만들자.**

1. 죽은 함수
    - 아무도 호출하지 않는 함수는 삭제해야 한다.

---

### 일반

1. 한 소스 파일에 여러 언어를 사용하는 것
    - 이상적으로는 소스 파일 하나 당, 하나의 언어만 사용해야 한다.
    - 불가피한 경우에 최대한 언어 범위를 줄여야 한다.

1. 당연한 기능을 구현해라
    - 함수나 클래스는 프로그래머가 당연히 받아들일 수 있는 기능을 제공해야 한다.
    
    ```kotlin
    Day day = DayDate.StringToDay(String dayName);
    
    // Monday -> Day.MONDAY
    ```
    

1. 중복✨
    - DRY (Don’t Repeat Yourself)
    - 코드에서 중복을 발견하면, 추상화의 기회이다!
    - 중복 코드 → 하위 루틴이나 클래스로 분리 가능

1. 올바른 추상화 수준을 설정하기 ✨
    - high level - 집의 형태, 외관, 공간, 방의 배치 (**정책**)
    - low level - 콘센트, 전등의 위치, 지붕의 크기 등의 기초공사 수준 (**세부사항**)
    - **의존성 역전 법칙 (DIP)** → 집의 형태가 전등의 위치나 색깔에 의해 바뀌어선 안된다. low level이 high level에 의존
    - 기초 클래스 (interface, abstract) 에서 파생 클래스에 의존해서는 안된다.

1. 기능 욕심
    - 클래스 메서드는 자기 클래스의 상태에 관심을 가져야지, 다른 객체를 조작해서는 안된다.
    
    ```kotlin
    public class HourlyPayCalculator {
        public Money calculateWeeklyPay(**HourlyEmployee e**) {
            int tenthRate = e.getTenthRate().getPennies();
            int tenthsWorked = e.getTenthsWorked();
            int straightTime = Math.min(400, tenthWorked);
            int overTime = Math.max(0, tenthsWorked - straightTime);
            int straightPay = straightTime * tenthRate;
            int overtimePay = (int)Math.round(overTime * tenthRate * 1.5);
            return new Money(straightPay + overtimePay);
        }
    }
    ```
    

1. 부적절한 static 함수
    - Math.max(double a, double b) 는 좋은 static 함수. 모든 정보를 parameter를 통해 가져온다.
    - **재정의 할 가능성이 있는 함수를 static으로 만들어선 안된다. ✨**
    - 시급을 계산하는 방법은 언제든 바뀔 수 있다.
    
    ```kotlin
    HourlyPayCalculator.calculatePay(employee, overtimeRate);
    ```
    

1. 서술적 변수 (Descriptive Variable)
    - 서술적 변수 이름을 사용해서 가독성을 높이자.
    
    ```kotlin
    Matcher match = headerPattern.matcher(line);
    if(match.find())
    {
      String **key** = match.group(1);
      String **value** = match.group(2);
      headers.put(key.toLowerCase(), value);
    }
    ```
    
    - 변수 이름을 지어보자
        - The total cost of a purchase (**totalCost**)
        - The total cost of a purchase, including tax (**totalCostWithTax**)
        - The number of columns in a grid (**colNum**)
        - The number of rows in a grid (**rowNum**)
        - The width of a rectangle (**rectWidth**)
    - 플래그 변수
        - **isRaining** , **isTooHot** , **hasPassedExam → is** , **has** 또는 **contains** 와 같은 동사로 시작
        - 변수 이름을 positive 하게 → `isNotFull` 대신 `isEmpty` (부정 조건을 피하자)

1. 명명된 상수로 교체해라! ✨
    - 코드에서 숫자를 사용하지 마라 (숫자는 명명된 상수 뒤로 숨기자)
    - ex) 책의 한 페이지당, 55줄을 인쇄한다 → `LINES_PER_PAGE`
    
    ```kotlin
    double circleArea = radius * radius * **Math.PI**
    ```
    

1. 조건을 캡슐화 하라

```kotlin
if (timer.hasExpired() && !timer.isRecurrent())

보다는

if (shouldBeDeleted(timer))

가 더 좋다.
```

10. 함수는 한 가지 일만 한다.

```kotlin
public void pay(){
	for (Employee e : employees) { // 직원 목록을 돌며
    	if (e.isPaypay()) { // 월급일이라면
        	Money pay = e.calculatePay(); // 급여를 계산해서
          e.deliverPay(pay); // 급여 지급
        }
    }
```

- 세개로 나눌 수 있다.

```kotlin
public void pay(){
	for (Employee e : employees) 
    	payIfNecessary(e);
}

private void payIfNecessary(Employee e) {
	if(e.isPayday()){
    	calculateAndDeliverPay(e)
    }
}

private void calculateAndDeliverPay(Employee e) {
	Money pay = e.calculatePay();
    e.deliverPay(pay);
}
```

1. 시간적인 결합을 드러내라
    - 함수 파라미터를 통해 함수가 호출되는 순서를 드러낼 수 있다.
    
    ```kotlin
    public class MoogDiver {
        Gradient gradient;
        List<Spline> splines;
    
        public void dive(String reason) {
          saturateGradient(); ...1
          reticulateSplines(); ...2
          diveForMoog(reason); ...3
        }
        ...
      }
    
    // 프로그래머가 reticulateSplines 를 먼저 호출한다면?
    ```
    
    - 파라미터를 사용!
    
    ```kotlin
    public class MoogDiver {
        Gradient gradient;
        List<Spline> splines;
    
        public void dive(String reason) {
          Gradient gradient = saturateGradient();
          List<Spline> splines = reticulateSplines(**gradient**);
          diveForMoog(**splines, reason**);
        }
        ...
      }
    ```
    
2. 경계 조건은 캡슐화 하라 ✨
    - 경계 조건은 한 곳에서 설정해두고 사용한다.
    
    ```kotlin
    if (**level + 1** < tags.length) {
        parts = new Parse(body, tags, **level + 1**, offset + endTag;
        body = null;
    }
    ```
    
    - 변수로 캡슐화
    
    ```kotlin
    int nextLevel = level + 1;
    if (nextLevel < tags.length)
    {
        parts = new Parse(body, tags, nextLevel, offset + endTag;
        body = null;
    }
    ```
    

---

### Java

1. 긴 import 목록을 피해라
    - 패키지에서 클래스 둘 이상을 사용한다면 **와일드 카드**를 사용한다.
    
    ```kotlin
    import package.*;
    ```
    
2. 상수는 상속하지 않는다.
    - TENTHS_PER_WEEK, OVERTIME_RATE 상수의 출처는?
    
    ```kotlin
    public class HourlyEmployee extends Employee {
        private int tenthsWorked;
        private double hourlyRate;
    
        public Money calculatePay() {
          int straightTime = Math.min(tenthsWorked, **TENTHS_PER_WEEK**);
          int overTime = tenthsWorked - straightTime;
          return new Money(
            hourlyRate * (tenthsWorked + **OVERTIME_RATE** * overTime)
          );
        }
        ...
      }
    ```
    
    - 부모 클래스?
    
    ```kotlin
    public abstract class Employee implements PayrollConstants {
        public abstract boolean isPayday();
        public abstract Money calculatePay();
        public abstract void deliverPay(Money pay);
    }
    ```
    
    - 인터페이스?
        - 계층의 가장 상위에 선언되어 있다.
    
    ```kotlin
    public interface PayrollConstants {
       public static final int **TENTHS_PER_WEEK = 400;** 
       public static final double **OVERTIME_RATE = 1.5;** 
    }
    ```
    
    - static import 사용 ✨
    
    ```java
    import static PayrollConstants.*;
    
    public class HourlyEmployee extends Employee {
        private int tenthsWorked;
        private double hourlyRate;
    
        public Money calculatePay() {
          int straightTime = Math.min(tenthsWorked, TENTHS_PER_WEEK);
          int overTime = tenthsWorked - straightTime;
          return new Money(
            hourlyRate * (tenthsWorked + OVERTIME_RATE * overTime)
          );
        }
        ...
    }
    ```
    

1. 상수 vs Enum
    - 주석으로 상수의 의미를 전달,,?
    
    ```java
    public class EnumExample {
    		public static void main(String[] args) {
    			/*
    			* 월요일 == 1
    			* 화요일 == 2
    			* 수요일 == 3
    			* 목요일 == 4
    			* 금요일 == 5
    			* 토요일 == 6
    			* 일요일 == 7
    			*/
    			int day = 1;
    			
    			switch (day) {
    					**case 1:
    						System.out.println("월요일");**
    						break;
    					case 2:
    						// ...
    			}
    		}
    }ㅇ
    ```
    

- 상수들이 많아진다면? 집합을 만들고 싶다.

```java
public class EnumExample {
		private val static int MONDAY = 1;
		private val static int TUESDAY = 2;
		private val static int WENDESDAY = 3;
		private val static int THURSDAY = 4;
		private val static int FRIDAY = 5;
		private val static int SATURDAY = 6;
		private val static int SUNDAY = 7;

		public static void main(String[] args) {

			int day = MONDAY;
			
			switch (day) {
					**case MONDAY:
						System.out.println("월요일");**
						break;
					case TUESDAY:
						// ...
			}
		}
}
```

- Enum을 활용, 서로 연관된 상수들의 집합

```java
**enum Day {
		MONDAY,
		TUESDAY,
		WENDESDAY,
		THURSDAY,
		FRIDAY,
		SATURDAY,
		SUNDAY
}**

public class EnumExample {
		public static void main(String[] args) {

			int day = Day.MONDAY;
			
			switch (day) {
					**case MONDAY:
						System.out.println("월요일");**
						break;
					case TUESDAY:
						// ...
			}
		}
}
```

- 추가 속성 부여 가능

```java
**enum Day(String korDay) {
		MONDAY("월요일"),
		TUESDAY("화요일"),
		WENDESDAY("수요일"),
		THURSDAY("목요일"),
		FRIDAY("금요일"),
		SATURDAY("토요일"),
		SUNDAY("일요일")
}**

public class EnumExample {
		public static void main(String[] args) {

			int day = Day.MONDAY;
			
			**System.out.println(day.korDay);**
			}
		}
}
```

---

### 이름

1. 서술적인 이름을 사용하라
    - 이렇게는 쓰지마라,,
    
    ```java
      public int x() {
        int q = 0;
        int z = 0;
        for (int kk = 0; kk < 10; kk++) {
          if (l[z] == 10)
          {
            q += 10 + (l[z + 1] + l[z + 2]);
            z += 1;
          }
          else if (l[z] + l[z + 1] == 10)
          {
            q += 10 + l[z + 2];
            z += 2;
          } else {
            q += l[z] + l[z + 1];
            z +=2;
          }
        }
        return q;
    }
    ```
    
    - 명명법에 신경쓰기
    
    ```java
    public int score() {
        int score = 0;
        int frame = 0;
        for (int frameNumber = 0; frameNumber < 10; frameNumber++) {
          if (isStrike(frame)) {
            score += 10 + nextTwoBallsForStrike(frame);
            frame += 1;
          }
          else if (isSpare(frame)) {
            score += 10 + nextBallForSpare(frame);
            frame += 2;
          } else {
            score += twoBallsInFrame(frame);
            frame += 2;
          }
        }
        return score;
    }
    ```
    
2. 적절한 추상화 수준에서 이름을 선택하라
    - detail한 구현을 드러내는 이름을 피하라
    
    ```java
    public interface Modem {
        boolean **dial(String phoneNumber**);
        boolean disconnect();
        boolean send(char c);
        char recv();
        String **getConnectedPhoneNumber();**
      }
      
    // Refactor
    public interface Modem {
        boolean **connect(String connectionLocator);**
        boolean disconnect();
        boolean send(char c);
        char recv();
        String **getConnectedLocator();**
    }
    ```
    

1. 이름과 범위는 비례한다
    - 짧은 범위에는 짧은 이름을 사용해도 괜찮다.
    
    ```kotlin
    private void rollMany(int n, int pins) {
        for (**int i = 0**; i < n; i++)
          g.roll(pins);
    }
    ```
    

1. 이름에 부수효과를 설명하라
    - 함수, 변수, 클래스가 하는 모든 일을 기술해야 한다.
    
    ```kotlin
    public ObjectOutputStream **getOos()** throws IOException {
        if (m_oos == null) {
          m_oos = new ObjectOutputStream(m_socket.getOutputStream());
        }
        return m_oos;
    }
    
    -> **createOrReturnOos()**
    ```
    

---

### 테스트

1. 잠재적으로 불안정한 모든 부분을 테스트 해야한다.
2. 사소한 테스트를 건너뛰지 마라
3. `@Ignore` 를 붙여 불분명한 요구사항을 표시하라
4. 경계 조건을 테스트 하라
5. 버그 주변은 철저히 테스트하라

---

- 주소 값 비교
    - Int
    
    ```java
    val a: Int = 1
    val b: Int = 1
    
    println(a === b) // true
    ```
    
    - Int?
    
    ```java
    val a: Int? = 1
    val b: Int? = 1
    
    println(a === b) // true
    ```
    
    - ~128 - 127 사이만 캐싱
    
    ```java
    val a: Int? = 128
    val b: Int? = 128
    
    println(a === b) // false
    ```
    
    - String
    
    ```java
    val a: String = "1"
    val b: String = "1"
    
    println(a === b) // true
    ```
    
- Marker Interface
    
    ```java
    data class User(val name: String): Serializable
    ```
    
    - Serializable
    
    ![Untitled](%5BCleanCode%5D%2017%E1%84%8C%E1%85%A1%E1%86%BC%20-%20Smell%20&%20Heuristic%209369f46ca52f4fb5bbe7b5642d6e784d/Untitled.png)
    
    ```java
    import java.io.*;
    
    public class SerializableTester {
    
        public void serializableTest() throws IOException, ClassNotFoundException {
            File f = new File("user.txt");
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream(f));
            objectOutputStream.writeObject(new User("sujin", "sujin@gmail.com"));
        }
    
        class User {
            private String name;
            private String email;
    
            public User(String name, String email) {
                this.name = name;
                this.email = email;
            }
    
    				// ...
        }
    
        public static void main(String[] args) {
            serializableTest serializableTester = new SerializableTester();
            try {
                **serializableTester.serializableTest();**
            } catch (IOException e) {
                e.printStackTrace();
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
    
        }
    }
    ```
    
    - 익셉션!
    
    ![Untitled](%5BCleanCode%5D%2017%E1%84%8C%E1%85%A1%E1%86%BC%20-%20Smell%20&%20Heuristic%209369f46ca52f4fb5bbe7b5642d6e784d/Untitled%201.png)
    
    - Serializable 인터페이스를 구현함으로써 해결
    
    ```java
       class User implements Serializable {
            private String name;
            private String email;
    
            public User(String name, String email) {
                this.name = name;
                this.email = email;
            }
       // ...
    ```
    
    - writeObject()를 살펴보자
    
    ```java
    private void writeObject0(Object obj, boolean unshared)
            throws IOException
        {
    			... 
    
                if (obj instanceof String) {
                    writeString((String) obj, unshared);
                } else if (cl.isArray()) {
                    writeArray(obj, desc, unshared);
                } else if (obj instanceof Enum) {
                    writeEnum((Enum<?>) obj, desc, unshared);
                } **else if (obj instanceof Serializable) {**
                    writeOrdinaryObject(obj, desc, unshared);
                } else {
                    if (extendedDebugInfo) {
                        throw new NotSerializableException(
                            cl.getName() + "\n" + debugInfoStack.toString());
                    } else {
                        throw new NotSerializableException(cl.getName());
                    }
                }
            } finally {
                depth--;
                bout.setBlockDataMode(oldMode);
            }
        }
    ```