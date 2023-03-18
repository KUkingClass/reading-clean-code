# 9장. 단위 테스트

- [9장. 단위 테스트](#9--------)
  * [TDD 법칙 세가지](#tdd-------)
  * [깨끗한 테스트 코드 유지하기](#---------------)
    + [테스트가 우리에게 주는 것](#--------------)
  * [깨끗한 테스트 코드 만들기](#--------------)
    + [Build Operate Check 패턴](#build-operate-check---)
    + [Given When Then 관례](#given-when-then---)
    + [도메인에 특화된 테스트 언어](#---------------)
      - [DSL?](#dsl-)
    + [이중 표준](#-----)
  * [테스트 당 assert 하나](#------assert---)
  * [테스트 당 개념 하나](#-----------)
  * [F.I.R.S.T](#first)
    + [Fast](#fast)
    + [Independent](#independent)
    + [Repeatable](#repeatable)
    + [Self-Validating](#self-validating)
    + [Timely](#timely)
  * [결론](#--)
  * [테스트 코드 짤 때 참고할 수 있는 🍯팁](#----------------------)
    + [테스트하기 어려운 경우를 쉽게 바꿔보자](#---------------------)
    + [테스트 코드 작성 시 지켜야할 주의사항](#---------------------)


## TDD 법칙 세가지

세 가지 법칙이 있다.

1. 실패하는 단위 테스트 작성
2. 컴파일을 통과하는 정도로만 작성
3. 실패하는 테스트를 통과할 정도로만 실제 코드 구현

TDD를 통하면 사실상 모든 코드를 전부 테스트하는 테스트 케이스가 나온다.

하지만 실제 코드와 맞먹을 정도로 방대한 테스트 코드는 관리 문제가 생긴다.

## 깨끗한 테스트 코드 유지하기

**실제 코드는 계속해서 변한다**. 그리고 **실제 코드가 변하면 테스트 코드도 변해야 한다**.

하지만 테스트 코드가 지저분하면 변경하기 어려울 것이다. 

- 그럼 테스트 코드를 유지보수하기 점점 짜증나지고, 테스트를 안하게 되고, 그럼 결함율이 높아지고…

어쨌든 원인은 **테스트 코드는 막 짜도 된다**고 생각한 것이다.

그렇다면 테스트 코드 역시 실제 코드처럼 깨끗하게 짜면 된다. **테스트 코드는 실제 코드 못지 않게 깨끗하게** 짜야 한다.

### 테스트가 우리에게 주는 것

테스트 케이스가 있으면 변경이 두렵지 않다. 안심하고 아키텍처와 설계를 개선할 수 있다. 그러므로 테스트는 유연성, 유지보수성, 재사용성을 제공해준다.

**테스트 코드가 지저분하면 곧 실제 코드도 지저분해진다.**

## 깨끗한 테스트 코드 만들기

가장 중요한 건 **가독성**이다. 어쩌면 가독성은 실제 코드보다 테스트 코드에서 더 중요하다.

### Build Operate Check 패턴

이 패턴을 적용해서 이해하기 쉽게 만들 수 있다. 이 패턴에서 테스트는 세 부분으로 나눠진다.

1. 테스트 자료 만들기
2. 테스트 자료 조작하기
3. 조작한 결과가 올바른지 확인

읽는 사람을 생각하자. 필요없거나, 너무 세부적인 내용 같은 코드는 없애자. 그런 내용이 꼭 필요하다면 더 추상적인 함수를 만들어보자. (→ 도메인에 특화된 테스트 언어)

### Given When Then 관례

상황, 실행, 결과 확인의 세 가지 요소로 테스트 구성 가능

상황이 없을 때도 있고… 상황-실행-결과 확인 구조에 너무 집착하지는 말자.

테스트 코드를 작성하는 데 도움이 되는 것은 맞지만 꼭 모든 테스트 메서드를 이 구조로 만들 필요는 없다. 테스트 코드를 보고 테스트 내용을 이해할 수 있으면 된다.

### 도메인에 특화된 테스트 언어

---

#### DSL?

가장 익숙한 DSL은 SQL, 정규식

둘다 특정 작업에 유리. 

- 장점
    
    **DSL이 범용 프로그래밍 언어와 달리 더 선언적(declarative)라는 것이 중요**
    
    범용 프로그래밍 언어는 보통 명령적(imperative). 따라서 어떤 연산을 위한 각 단계를 세세하게 정확히 기술. 하지만 DSL은, SQL을 예로 들면 어떤 걸 SELECT, 조건을 WHERE를 통해 명시할 뿐 어떤 방식으로 가져올 지 세부 실행은 언어를 해석하는 엔진에 맡김. 또한 전체적으로 실행엔진이 한꺼번에 최적화하면서 더 효율적일 수 있음
    
- 단점
    
    범용 언어로 만든 호스트 애플리케이션과 조합하기 어려움. 예를 들어 SQL을 코틀린 프로그램에서 쓸려면 “SELECT…” 와 같이 문자열 리터럴로 저장해야 함. 그럼 DSL에서의 문법 검사 등도 힘들다
    
    → 이를 해결하면서 장점은 가져가기 위해 내부 internal DSL이라는 개념이 유명해짐
    
    > **내부 DSL**
    > 
    > 독립적인 문법 구조를 가진 외부 DSL과 달리 범용 언어로 작성된 프로그램의 일부. 따라서 완전히 다른 언어가 아닌 DSL의 장점을 유지하면서 주 언어를 특별한 방법으로 사용하는 것
    > DSL을 통해 특정한 과업을 달성하려 하지만 구현은 범용 언어(코틀린)으로 이뤄짐

---

도메인에 특화된 테스트 언어라고 해서 말이 어렵지만.. 이 테스트를 위해 쓰인 특수 메서드를 만들라는 말과 비슷하다.

예를 들어 UserService 관련 테스트를 한다고 생각해보자. 테스트 메서드에서 매번 아래와 같은 상황 설정이 필요할 수 있다.

```java
@Test
void 중복된_id있으면_가입불가() {
	userRepository.save(new User("id", "pw", "email"));
	userService.join(new User("id", "pw", "email"));
}

@Test
void 존재하는_User_탈퇴가능() {
  userRepository.save(new User("id", "pw", "email"));
	//...
}
```

여기서 계속 반복되는 `userRepository.save(new User("id", "pw", "email"));` 처럼 세세하게 드러내지 말고 DSL로 테스트 코드를 구현해보자.

```java
void givenUser(String id, String pw, String email) {
    userRepository.save(new User(id, pw, email));
}

@Test
void 중복된_id있으면_가입불가() {
	givenUser("id", "pw", "email");
	userService.join(new User("id", "pw", "email"));
}

@Test
void 존재하는_User_탈퇴가능() {
  givenUser("id", "pw", "email");
	//...
}
```

훨씬 읽기 좋아졌다.

이런 상황 설정을 위한 API가 여러개 필요할 수 있다. 그럼 Helper 클래스를 만들어서 사용할 수도 있다.

```java
public class UserGivenHelper {
  private UserRepository userRepository;

  public UserGivenHelper(UserRepository userRepository) {
      this.userRepository = userRepository;
  }
  
  public void givenUser(String id, String pw, String email) {
      userRepository.save(new User(id, pw, email));
  }

	// 기타 필요한 메서드들...
}
```

```java
public class UserServiceTestWithHelper {
  private UserRepository userRepository;
  private UserGivenHelper given;
  
  @BeforeEach
  void setUp() {
      userRepository = new MemoryUserRepository();
			given = new UserGivenHelper(userRepository);
  }
  
	@Test
	void 중복된_id있으면_가입불가() {
		given.givenUser("id", "pw", "email");
		userService.join(new User("id", "pw", "email"));
	}
	
	@Test
	void 존재하는_User_탈퇴가능() {
	  given.givenUser("id", "pw", "email");
		//...
	}
  
}
```

처음부터 필요한 상황 설정별로 메서드를 다 만들고.. 클래스를 만들고.. 하는 것이 아니라

하나씩 구현하다가 어떤 상황 설정이 계속 반복되고, 필요할 것 같다 싶을 때 만들어보자.

### 이중 표준

어쨌든 테스트 코드는 실제 코드와 다르다. 실제 코드만큼 효율적일 필요가 없다.

효율적이지 않거나, 실제 환경에선 사용할 수 없는 코드라도 가독성을 위해서라면 테스트 환경에선 가능할 수 있다.

## 테스트 당 assert 하나

assert 문이 테스트당 반드시 하나라면 코드를 이해하기 쉬워질 수도 있다.

하지만 필요하면 assert 문을 여러개 넣자. 어쨌든 최대한 줄일 수 있다면 줄여보자.

---

저자는 중복을 제거하기 위해서라면 `@Before` 부분에 `given/when` 을 모두 넣는 식으로도 중복을 제거할 수 있다고 했다. 하지만 이렇게 중복을 제거하는 건 조금 고려해보는 편이 좋을 것 같다.

몇개월 뒤에 본인이, 혹은 다른 개발자가 그 테스트 코드를 본다고 생각해보자. 테스트 코드를 이해하기 위해 `@Before` 부분을 계속 확인해야 한다. 또 `@Before` 에 있는 부분과 충돌이 나서 예상치 못하게 테스트 코드가 실패해서 문제점을 파악하기 어려워질 수도 있다.

**테스트 함수는 스스로 상황을 설명할 수 있는 편이 좋다.**

## 테스트 당 개념 하나

테스트 함수 당 `assert` 하나보단 **개념 하나만 테스트**하라고 하는 편이 낫다.

여러 개념을 한 메서드에 넣으면, 이해하기 힘들 뿐만 아니라 어떤 곳에서 테스트가 실패하면, 나머지 테스트 코드는 실패할지 성공할지 여부를 알 수 없는 문제도 있다.

```java
@Test
void 여러개_테스트() {
	어쩌구();	
	assert...();
	assert...(); // 여기서 실패함 -> 아래는 실행되지 않음
	assert...();

	저쩌구();
	assert...(); // 그럼 얘는 실패인가 성공인가?
	assert...();
	assert...();
}
```

## F.I.R.S.T

깨끗한 테스트는 다음 다섯 가지 규칙을 따른다.

### Fast

테스트는 빨라야 한다. 느리면 돌리기 싫어져서 테스트를 안하게 되기 때문이다.

### Independent

각 테스트는 서로 의존하면 안된다. (예를 들어 B테스트가 실행되기 전에 꼭 A테스트가 실행되어야 하고..)

테스트 실패시 원인을 파악하기 어려워진다.

### Repeatable

테스트는 어떤 환경에서도 반복 가능해야 한다.

- 예를 들어 특정 날짜가 지나면 실패한다거나
- 랜덤 값을 이용해서 랜덤으로 실패한다거나
- 특정 OS에서만 성공한다거나

### Self-Validating

테스트는 꼭 assert문을 실행하고 성공이나 실패로 끝나야 한다.

### Timely

테스트하려는 실제 코드를 구현하기 직전에 구현하자. 

실제 코드를 먼저 구현하면 테스트하기 어렵게 짰다는 걸 나중에 발견하고 테스트를 포기하게 될 수 있다.

## 결론

테스트 코드는 실제 코드만큼, 혹은 그보다 더 중요하다.

계속 깨끗하게 유지하자. 

테스트 코드 역시 코드라는 점을 계속 생각하고, 필요하면 리팩토링하자.

---

## 테스트 코드 짤 때 참고할 수 있는 🍯팁

### 테스트하기 어려운 경우를 쉽게 바꿔보자

핵심은 다 **쉽게 교체할 수 있도록** 하기

- 하드 코딩된 경로
    - 하드 코딩된 상수를 **생성자나 메서드 파라미터**로 받기
- 의존 객체를 직접 생성(주입이 아닌)
    - 의존 대상을 주입 받기
- 실행 시점에 따라 달라지는 결과
    - **시간이나 임의의 값 생성 기능 분리하기.**
        - 예를 들어 `LocalDate.now()` 를 이용할 때
            
            `LocalDate.now()` 대신에
            
            ```java
            public class Times {
            	public LocalDate today() {
            		return LocalDate.now();
            	}
            }
            ```
            
            현재 일자 구하는 기능을 위처럼 분리하자.
            
            그럼 이제 대역 등을 이용해서
            
            ```java
            given(mockTimes.today()).willReturn(LocalDate.of(원하는,날짜,일));
            ```
            
            위 내용처럼 바꿀 수 있다.
            
    - 임의 값도 비슷하다. 임의 값을 제공하는 라이브러리를 (Random.어쩌구..) 직접 사용하지 말고 별도로 분리한 타입을 사용해서 대역으로 처리할 수 있어야 테스트 가능하게 만들 수 있다.
- 역할이 섞여 있는 코드: 여러 로직이 섞여 있어 한 로직만 테스트할 수 없다든가…
    - 테스트하고 싶은 코드 분리하기
- 메서드 중간에 소켓 통신 코드 포함되어 있을 때
    - 소켓 통신이나 HTTP 통신은 실제를 대체할 서버를 로컬에 띄워서 처리하게 할 수도 있음. 즉 서버 수준에서 대역.
- 콘솔에서 입력을 받거나 결과를 콘솔에 출력
- 테스트 대상의 소스를 소유하고 있지 않아 수정이 어려움
    - 외부 라이브러리는 직접 사용하지 말고 감싸서 사용하기
        - 예를 들어 `AuthUtil` 이라는 클래스가 외부에서 제공한 라이브러리고, 거기서 제공되는 정적 메서드를 쓴다면 테스트하기 어렵다.
        - 그럼 `AuthService` 라는 래퍼 클래스를 만들고, 해당 클래스에서 라이브러리를 쓰게 만들면 AuthService를 대역으로 대체할 수 있게 됐다.
        - ⬇️ 아래와 같을 때도 역시 래퍼 클래스를 만들어서 대역으로 대체하기
- 테스트 대상이 사용하는 의존 대상 클래스나 메서드가 final이라 대역으로 대체하기 어려움

### 테스트 코드 작성 시 지켜야할 주의사항

#### 변수나 필드를 사용해서 기댓값 표현하지 않기

- 만약 날짜를 특정 문자열 형식으로 변환하는 함수를 테스트할 때
    
    ```java
    assertThat(result).isEqualTo(date.getYear + "년 " + date.getMonthValue() + "월");
    ```
    
    위와 같이 변수를 사용하면 논리적으론 맞지만
    
    **문자열 연결이 있어 코드가 복잡하다.**
    
    또 실수로 변수에서 get을 잘못했을 때 테스트가 깨져야 잘못 작성했음을 깨닫기도 한다.
    
    - **기대하는 값에 문자열 값을 사용하자**
        
        ```java
        assertThat(result).isEqualTo("2023년 11월");
        ```
        
        복잡하지도 않고 기대하는 값도 명확하게 표현하며 메서드를 잘못 사용할 일도 없다.
        
- 기대하는 값 기술 시 로컬 변수와 필드 사용한다면
    
    ```java
    // 필드
    private List<Integer> answers = Arrays.asLit(1,2,3,4);
    private Long respondentId = 100L;
    
    // 테스트 메서드
    // 답변에 성공 시 결과 저장함
    
    // given
    // 로컬 변수
    Survey survey = 어쩌구
    surveyRepository.save(survey)
    
    SurveyAnswerRequest = surveyAnswer =
    	SurveyAnswerRequest.builder()
    		.surveyId(survey.getId()) // 필드
    		.respondentId(respondentId) // 필드
    		.answers(answers) // 필드
    		.build();
    
    // when
    svc.answerSurvey(surveyAnswer);
    
    // then
    SurveyAnswer savedAnswer = 리포지토리.find어쩌구(survey.getId(), respondentId); // 로컬 변수
    assertAll(
    	() -> assertEquals(respondentId, savedAnswer.getRespondentId()),
    	// 미리 기대한 결과 필드 가져와서 같이 비교
    	() -> assertEquals(answer.size(), savedAnswer.getAnswers().size()),
    	() -> assertEquals(answer.get(0), savedAnswer.getAnswers().get(0)),
      () -> assertEquals(answer.get(1), savedAnswer.getAnswers().get(1)),
    	() -> assertEquals(answer.get(2), savedAnswer.getAnswers().get(2)),
    	// 이렇게 오타가 나도 모를 수가 있다.
    	() -> assertEquals(answer.get(2), savedAnswer.getAnswers().get(3)),
    );
    ```
    
    여기서 만약 원소들을 비교하다가 테스트에 통과하지 못하면 실패 메시지는 다음과 같을 것이다. 
    
    ```java
    AssertionFailedError:
    Expected: 3
    Actual: 4
    ```
    
    **위 에러만 보고 어디서 어떤 문제가 났는 지 알 수 있는가?**
    
    기대한 값이 왜 3인지 확인하려면 필드로 가서 answers를 또 확인해야 한다. 만약 또 필드나 변수에서 NPE가 발생하면 또 살펴봐야 한다.
    
    테스트에 성공하더라도 테스트 코드를 처음 보는 사람은 **변수와 필드를 오가며 테스트 코드를 이해**해야 한다. 또 get(0)~get(3) 행을 이해하려면 answers 필드를 왔다갔다하며 참조해야 한다.
    
    위 코드를 아래처럼 수정해보자.
    
    ```java
    // 테스트 메서드
    // 답변에 성공 시 결과 저장함
    
    // given
    // 로컬 변수
    Survey survey = 어쩌구
    surveyRepository.save(survey)
    
    SurveyAnswerRequest = surveyAnswer =
    	SurveyAnswerRequest.builder()
    		.surveyId(1L) // 필드X
    		.respondentId(100L) // 필드X
    		.answers(Arrays.asList(1,2,3,4)) // 필드X
    		.build();
    
    // when
    svc.answerSurvey(surveyAnswer);
    
    // then
    SurveyAnswer savedAnswer = 리포지토리.find어쩌구(1L, 100L); // 로컬 변수X
    assertAll(
    	() -> assertEquals(100L, savedAnswer.getRespondentId()),
    	// 미리 기대한 결과 필드 가져와서 같이 비교
    	() -> assertEquals(answer.size(), savedAnswer.getAnswers().size()),
    	() -> assertEquals(1, savedAnswer.getAnswers().get(0)),
      () -> assertEquals(2, savedAnswer.getAnswers().get(1)),
    	() -> assertEquals(3, savedAnswer.getAnswers().get(2)),
    	// 이렇게 오타가 나도 모를 수가 있다.
    	() -> assertEquals(4, savedAnswer.getAnswers().get(3)),
    );
    ```
    
    코드 가독성이 좋아져서 테스트 코드를 더욱 쉽게 파악할 수 있다. **객체 생성 시 사용한 값이 무엇인지 알아보기 위해** 필드와 변수를 참조하지 않아도 된다. 또 **단언할 때 사용한 값이 무엇인지 알기 위해 필드와 변수**를 오갈 필요도 없다.
    

#### 정확하게 일치하는 값으로 모의 객체 설정하지 않기

```java
// Mock
@DisplayName("약한 암호면 가입 실패 - Mock Object")
@Disabled
@Test
void weakPassword_Mockito() {
    // stubPasswordChecker.setWeak(true); // 암호가 약하다고 응답하도록 스텁 설정
    BDDMockito.given(mockPasswordChecked.checkPasswordWeak("pw")).willReturn(true);

    assertThatThrownBy
            (() -> userRegister.register("id", "pw", "email"))
            .isInstanceOf(WeakPasswordException.class);
}
```

**이 테스트는 작은 변화에도 실패한다.**

만약 “pw”가 아닌 “pwa”를 register한다면 실패한다.

**이 코드의 목적은 약한 암호일 경우 UserRegister가 원하는 대로 동작하는 지 확인하기 위한 테스트지 “pw”나 “pwa”가 약한 암호인지 확인하는 테스트가 아니다.**

즉 모의 객체가 pw를 받을 필요 없이 임의의 문자열에 대해 true를 요청해도 전혀 문제가 없는 테스트이다. 

```java
BDDMockito.given(mockPasswordChecked.checkPasswordWeak(Mockito.anyString())).willReturn(true);
```

따라서 위처럼 변경해도 괜찮다.

---

위처럼 스텁 설정 대신 모의 객체 호출 여부를 확인할 때도 동일하다.

```java
// Mock
@DisplayName("회원 가입시 암호 검사 수행함 - mock")
@Disabled
@Test
void checkPassword() {
    // given

    // when
    userRegister.register("id", "pw", "email");
    // then
    BDDMockito.then(mockPasswordChecked)
            .should()
            // 이렇게 하지 말것
            // .checkPasswordWeak("pw");
            .checkPasswordWeak(BDDMockito.anyString());
}
```

“pw”와 같은 특정 값으로 호출될것을 확인하기 모다는 역시 anyString을 이용하자.

**모의 객체는 가능한 범용적인 값을 사용해서 기술해야 한다.**

한정된 값에 일치하도록 모의 객체를 사용하면 약간의 코드 수정만으로도 테스트는 실패하게 된다. 그럼 테스트 코드의 일부 값을 수정 시 모의 객체 관련 코드도 함께 수정해야 한다.

**테스트의 의도를 해치지 않는 선에서** “pw”같은 특정 값보다는 anyString()같이 범용적인 값을 사용하자.

#### 과도하게 구현 검증하지 않기

**테스트 대상의 내부 구현**을 검증하지 않도록 주의하자. 모의 객체를 처음 사용할 때 특히 이런 유혹에 빠지기 쉽다.

**필자의 경우 모의 객체를 처음 접했을 때 모의 객체를 이용해서 내부 구현을 검증하는 코드를 많이 작성했는데 이것이 결과적으로 테스트 코드 유지 보수에 도움이 되지 않았다.**

```java
@DisplayName("회원 가입시 암호 검사 수행함 - mock")
@Disabled
@Test
void checkPassword() {
    // given

    // when
    userRegister.register("id", "pw", "email");
    // then
    BDDMockito.then(mockPasswordChecked)
            .should()
            // ch10 - 이렇게 하지 말것
            // .checkPasswordWeak("pw");
            .checkPasswordWeak(BDDMockito.anyString());
}
```

원래 위와 같은 메소드가 있었다면….

```java
// 테스트 대상의 내부 구현을 검증하는 코드 - 이렇게 하지 말자!
@DisplayName("회원 가입시 암호 검사 수행함 : 내부 구현 검사 - mock")
@Disabled
@Test
void checkPassword_Implementation() {
    // given
    userRegister = new UserRegister(mockPasswordChecker, mockUserRepository, mockEmailNotifier);
    // when
    userRegister.register("id", "pw", "email");
    // then
    BDDMockito.then(mockPasswordChecker)
            .should() // 호출 되어야하고
            .checkPasswordWeak(Mockito.anyString());

    BDDMockito.then(mockUserRepository)
            .should(Mockito.never()) // 호출돼선 안된다
            .findById(Mockito.any());
}
```

테스트 대상인 `UserRegister#register()` 메서드가 Checker의 check 메서드를 호출하는지, 또 UserRepository#findById를 호출하지 않는 지도 검증한다.

결국 테스트 대상인 register 메서드의 내부 구현을 검증하는 것이다.

내부 구현을 검증하는 것이 나쁜 것은 아니지만 한 가지 단점이 있다.

**구현을 조금만 변경해도 테스트가 깨질 가능성이 커진다!**

**내부 구현은 언제든지 바뀔 수 있기 때문에 테스트 코드는 내부 구현보다 실행 결과를 검증해야 한다.**

- **언제든지 변하는 구현 말고 변하지 않는 인터페이스에 의존하자 !!!**

예제 코드의 경우 PasswordChecker가 호출되는 지가 중요한 게 아니고 약한 암호일 때 register가 실패하는 지, **register()가 올바른지 검사**하는 것이 더 맞다.

---

**이미 존재하는 코드에 단위 테스트를 추가할 경우**

어쩔 수 없이 내부 구현을 검증해야 할 때도 있다. 레거시 코드에서 DAO는 다양한 update, select 메서드를 정의하고 있을 때가 있기 때문에 메모리를 이용한 가짜 구현으로 대체하기가 쉽지 않다.그래서 레거시 코드에 대한 테스트는 모의 객체를 많이 활용한다.

> **거꾸로 만약 내부 구현을 테스트해야하는 상황이라면 메소드를 나눠야 하는 경우가 아닌지 생각해볼 수도 있다**

기능이 정상적으로 동작하는지 확인할 수단이 구현 검증밖에 없다면 일단 모의 객체를 사용해서 테스트 코드를 작성하더라도 **작성 후에는 점진적으로 코드를 리팩토링해서 구현이 아닌 결과를 검증할 수 있도록 시도**해야 한다.

#### 실행 시점이 다르다고 실패하지 않기

예를 들어 회원의 만료 기능을 테스트한다고 해보자.

회원은 필드에 저장된 `expiryDate` 를 만료 검사하는 메서드인 isExpired를 실행할 때의 `LocalDate.now()` 와 비교하여 만료 여부를 판단한다.

```java
public class Member {
    private LocalDateTime expiryDate;

    public Member(LocalDateTime expiryDate) {
        this.expiryDate = expiryDate;
    }

    // bad
    public boolean isExpired() {
        return expiryDate.isBefore(LocalDateTime.now());
    }
}
```

```java
class MemberTest {
    @Test
    void 만료된_회원인지_확인() {
        // given
        // when
        Member member = new Member(LocalDateTime.of(1999, 11, 17, 10, 10));
        // then
        assertThat(member.isExpired()).isFalse();
    }
}
```

만약 테스트에서 회원의 만료일을 설정한 코드가 있을 때, 그 코드는 언젠가 설정한 만료일이 지나면 테스트가 깨질 것이다.

물론 아주 오랜 미래를 설정하면 몇십년간 깨지지 않게 할 수 있지만,

이보다는 테스트 코드에서 시간을 명시적으로 제어할 수 있는 방법을 선택하는 것이 좋다.

- 예를 들어 위에서 `isExipired` 보다 `passedExpiryDate(LocalDateTime time)` 으로 날짜를 받아서 판단하게 할 수 있다.
    
    ```java
    // better
    public boolean passedExpiryDate(LocalDateTime time) {
        return expiryDate.isBefore(time);
    }
    ```
    
    ```java
    @Test
    void 만료된_회원인지_확인시_인자로_시간지정() {
        // given
        // when
        Member member = new Member(LocalDateTime.of(1999, 11, 17, 10, 10));
    
        // then
        assertThat(member.passedExpiryDate(LocalDateTime.of(1999, 11, 18, 00, 00))).isTrue();
    }
    ```
    
- 혹은 **별도의 시간 클래스를 작성**한다.
    
    ```java
    public class BizClock {
        private static BizClock DEFAULT = new BizClock();
        private static BizClock instance = DEFAULT;
    
        public static void reset() {
    			System.out.println("Biz Clock reset!");
            instance = DEFAULT;
        }
    
        public static LocalDateTime now() {
            return instance.timeNow();
        }
    
        public LocalDateTime timeNow() {
            return LocalDateTime.now();
        }
    
        public static void setInstance(BizClock instance) {
            BizClock.instance = instance;
        }
    }
    ```
    
    ```java
    // better
    public boolean isExpiredWithCustomTime() {
        return expiryDate.isBefore(BizClock.now());
    }
    ```
    
    BizClock 클래스의 now는 instance의 timeNow를 통해 현재 시간 값을 리턴한다. 이때 `setInstance` 를 통해 now가 원하는 시간을 제공하도록 만들 수 있다. 
    
    예를 들어 BizClock 클래스를 확장해서 아래와 같이 만들 수 있다.
    
    ```java
    public class TestBizClock extends BizClock {
        private LocalDateTime now;
    
        public TestBizClock() {
            setInstance(this);
        }
    
        public void setNow(LocalDateTime now) {
            this.now = now;
        }
    
        @Override
        public LocalDateTime timeNow() {
            return now != null ? now : BizClock.now();
        }
    }
    ```
    
    그리고 아래와 같이 테스트할 수 있다.
    
    ```java
    TestBizClock testClock = new TestBizClock();
    
    @AfterEach
    void resetClock() {
        // TestBizClock이 BizClock을 상속하므로
        // BizClock.reset() 과 같다
    		// testClock.reset();
        BizClock.reset();
    }
    
    @Test
    void 만료된_회원인지_확인시_커스텀시간클래스_이용해서_시간지정() {
        // given
        testClock.setNow(LocalDateTime.of(1999, 11, 10, 00, 00));
        // when
        Member member = new Member(LocalDateTime.of(1999, 11, 17, 10, 10));
    
        // then
        assertThat(member.isExpiredWithCustomTime()).isFalse();
    }
    ```
    
    - now()를 instance.timeNow()로 얻기 때문에 다른 timeNow를 응답하도록 오버라이딩하고 생성자에서 setInstance로 인스턴스를 바꿔준다!

이렇게 했을 때 장점은 단순히 깨지지 않는다는 것만이 아니라, **경계 조건도 쉽게 테스트**할 수 있다는 것이다. 

아래와 같이 ‘1 나노초’ 가 지났는지 테스트하는 것도 가능하다.

```java
@Test
void 만료된_회원인지_확인시_인자로_시간지정() {
    // given
    // when
    Member member = new Member(LocalDateTime.of(1999, 11, 17, 10, 10));

    // then
    assertThat(member.passedExpiryDate(LocalDateTime.of(1999, 11, 17, 10, 10,0,1))).isTrue();
}
```

#### 랜덤하게 실패하지 않기

예를 들어 랜덤값을 이용할 경우

- 역시 인자로 랜덤값을 받게 해준다
- 또는 랜덤 값 생성을 다른 객체에게 위임하게 바꾼다
    - 그럼 그 다른 객체의 Mock을 생성해서 원하는 값을 return하게 만들 수 있다.

#### 필요하지 않은 값은 설정하지 않기

예를 들어 **중복 아이디**를 가진 회원은 가입이 불가능하다는 걸 테스트한다고 해보자.

그런데 만약 User를 만드는데

```java
User.builder()
	.id("dupId")
	.name("name")
	.pw("pw")
	.build();
```

위와 같이 생성해서 테스트한다면, 잘못된 코드는 아니지만 **필요하지 않은 값까지 설정**하고 있다.

따라서 id만 생성하도록 하자.

필요하지 않은 값을 설정하느라 고민할 필요도 없고 테스트 코드가 짧아져서 한눈에 파악할 수 있다.

- **만약 모두 지정하지 않으면 에러가 날 때는? 객체 생성 보조 클래스를 만들자**

#### 단위 테스트를 위한 객체 생성 보조 클래스

예를 들어 상황이 복잡하다면 여러 파라미터가 필요할 것이다. 게다가 null이면 안되는 속성이 많다면 더욱 복잡해질 것이다.

**이럴 땐 테스트를 위한 객체 생성 클래스를 따로 만들어 복잡함을 줄일 수 있다.**

#### 팩토리 클래스

팩토리 클래스로 필요한 인자를 받은 뒤 나머지는 기본 값으로 채워서 응답해줄 수 있다.

하지만 **팩토리 메서드**의 단점은 인자로 미리 정해놓은 것말고는 계속해서 추가로 메소드를 작성해야 하기 때문에 **유연하지 않다**는 것이다.

#### 빌더 패턴

빌더 패턴을 사용하면 유연함을 더할 수 있다. 

```java
package com.ggyul.chapter10;

import java.time.LocalDateTime;

public class TestMemberBuilder {
    private String id = "hi1234";
    private String name = "member";
    private String email = "email@email.com";
    private String password = "1234";
    private LocalDateTime expiryDate = LocalDateTime.of(2023, 10, 10, 22, 10);

    public static TestMemberBuilder Builder() {
        return new TestMemberBuilder();
    }

    public TestMemberBuilder id(String id) {
        this.id = id;
        return this;
    }

    public TestMemberBuilder name(String name) {
        this.name = name;
        return this;
    }
    public Member build() {
        return new Member(id, name, email, password, expiryDate);
    }
}
```

위와 같이 필드에 기본값을 갖게 하고 주어지지 않은건 기본값으로 생성해주고, Builder를 통해 테스트에 필요한 속성만 변경할 수 있도록 할 수 있다.

---

코틀린 같은 경우 이름 있는 파라미터와 기본값을 줄 수 있으므로 팩토리 메서드와 빌더를 따로 할 필요 없이, 메서드 파라미터에 기본값을 설정후 모든 인자를 받고 그냥 생성해서 리턴해주면 자동으로 팩토리 메서드와 빌더 패턴의 장점을 모두 가질 수 있다.

#### 조건부로 검증하지 않기

테스트에서

```java
if (어쩌구) {
	assertThat(...)....
}
```

위와 같이 특정 조건에서만 단언이 실행되면 안된다.

**테스트는 성공하거나 실패해야하고, 그러려면 반드시 단언을 실행해야 한다.**

단언을 실행하지 않으면 성공하지도 실패하지도 않은 것.

이럴 땐 조건에 대한 단언도 추가해야 한다.

```java
assertThat(어쩌구).isTrue();
assertThat(...)....
```

#### 통합 테스트는 필요하지 않은 범위까지 연동하지 않기

예를 들어 Jdbc 테스트시 @JdbcTest만 있어도 되는데 @SpringBootTest를 붙여 모든 빈을 초기화한다든가…

시간도 오래 걸린다.
