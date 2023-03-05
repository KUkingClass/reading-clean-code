# [클린코드] II. 의미 있는 이름

---

## 의도를 분명히 밝혀라

- 변수(함수, 클래스)의 존재 이유는?
- 수행 기능은?
- 사용 방법은?

```java
int d;

int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

```java
public List<int[]> getThem() {
	List<int[]> list1 = new ArrayList<>();
	for(int[] x : theList) { // theList?
		if(x[0] == 4) // 0? 4?
			list1.add(x);
	return list1;
}

public List<Cell> getFlaggedCells() {
	List<Cell> flaggedCells = new ArrayList<>();
	for(Cell cell : gameBoard) {
		if(cell.isFlagged())
			flaggedCells.add(cell);
	return flaggedCells;
}
```

## 그릇된 정보를 피하라

- 예약어 사용 X ⇒ accountList, accountGroup, bunchOfAccounts 대신 Accounts
- 비슷한 이름 사용 X
- l, O 사용 X

## 의미 있게 구분하라

- 추가해도 의미는 변하지 않는 단어를 추가 X
    - Product클래스가 이미 있다고 ProductInfo, ProductData클래스를 만들지 말자
    
    ```java
    public static void copyChars(char[] a1, char[] a2) {
    	for(int i=0; i<a1.length; i++) {
    		a2[i] = a1[i];
    	}
    }
    
    public static void copyChars(char[] source, char[] destination) {
    	for(int i=0; i<source.length; i++) {
    		destination[i] = source[i];
    	}
    }
    ```
    

## 발음하기 쉬운 이름을 사용하라

## 검색하기 쉬운 이름을 사용하라

- 한 글자 이름 X
- 상수 ⇒ 상수 7을 사용하면 7을 포함하는 변수명, 파일명, 수식이 모두 검색된다
- 이름 길이는 범위 크기에 비례해야 한다.

```java
for(int j=0; j<34; j++) {
	s += (t[i]*4)/5;
}

int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for(int j=0; j< NUMBER_OF_TASKS; j++) {
	int realTaskDays = taskEstimate[j] * readDaysPerIdealDay;
	int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
	sum += realTaskWeeks;
} 
```

## 인코딩을 피하라

- 헝가리식 표기법, 멤버변수 접두어 X
- 인터페이스 클래스와 구현 클래스
    - InterfaceName : InterfaceNameImpl

## 자신의 기억력을 자랑하지 마라

- 문자 하나만을 사용하는 이름은 반복문에서 반복 횟수를 셀 때나 사용
- 명료함을 위해

## 클래스 이름

- 명사
- noise word(Manager, Data, Info)와 동사는 피함

## 메서드 이름

- 동사
- get, set, is 활용
- 생성자 오버로딩 할 때는 스태틱 메소드 활용하고 해당 생성자 private 선언

```java
Complex fulcrumPoint = new Complex(23.0);

Complex fulcrumPoint = Complex.FromRealNumber(23.0);
```

## 기발한 이름은 피하라

## 한 개념에 한 단어를 사용하라

- fetch, retrieve, get 중에서 하나만 사용
- driver, controller, manager 중에서 하나만 사용

## 말장난을 하지 마라

- add를 두 원소를 더하는 메소드 이름으로 사용 중인데
    
    집합에 값을 하나 추가하는 메소드도 add로 하진 말자
    
    - insert, append

## 해법 영역에서 가져온 이름을 사용하라

- 모든 이름을 문제 영역에서 가져올 필요는 없다
- 모두가 아는 용어, 알고리즘 이름, 패턴 이름, 수학 용어를 사용해도 된다

## 문제 영역에서 가져온 이름을 사용하라

- 적절한 용어가 없가면 문제 영역에서 이름을 가져온다

## 의미 있는 맥락을 추가하라

- 이름만으로는 의미가 부족할 수 있다.
    - 클래스, 함수, namespace를 활용
    - 접두어
    - street, houseNumber, city, state, zipcode 가 모두 있다면 주소라는 맥락을 알수 있지만 state 하나만 있다면 맥락을 알기 어렵다.
        - Address클래스에 담기, 접두어 addr 붙이기
        

## 불필요한 맥락을 없애라

- 긴 이름 보다는 짧은 이름이 바람직하다.
- AccountAddress, CustomerAddress는 클래스 이름보다는 인스턴스 이름으로 적절하고 Address라는 이름이 적절하다.