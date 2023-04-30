# [CleanCode] 5장-형식 맞추기

> *프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야한다*
> 
- 코드 형식을 맞추기 위해 **규칙을 정하기**
- 팀이 합의하여 규칙을 정하고 **모두가 규칙을 따르기**

# 형식을 맞추는 목적

> 코드 형식은 의사소통의 일환이다. ✨
> 

- 현재 구현한 기능은 언제든 다음 버전에서 바뀔 수 있음
- 현재 구현한 코드는 다음 버전의 코드 품질에 큰 영향을 줌
- 초기에 잡은 코드 스타일과 가독성 수준은 계속해서 영향을 미침

원활하게 소통할 수 있는 코드 형식이란?

# 적절한 행 길이를 유지하라

일반적으로 큰 파일보다 작은 파일이 이해하기 쉬울 것이다.

> [Q] *파일의 길이가 길어지기 vs 파일의 개수가 여러개로 나뉘기*
> 

### 신문 기사처럼 작성하기

> *독자는 위에서 아래로 기사를 읽는다.*
> 

기사의 대표적인 **표제** / 기사 내용을 요약하는 **첫문단** / 밑으로 내려갈수록 **세부사항**

소스파일을 생각해보자

- 파일 이름은 간단하면서도 이름만 보고도 파악이 가능한지 신경써서 짓는다.
- 첫 부분은 고차원 개념과 알고리즘을 설명한다.
- 아래로 내려갈수록 의도를 세세하게 묘사한다.
- **마지막에는 가장 저차원 함수와 세부 사항이 나온다.**

### 개념은 빈 행으로 분리하기

```kotlin
/**
* NewsScreen.kt
**/
@Composable
fun NewsScreen(
		viewModel: NewsViewModel
) {
			val topNews by viewModel.topNews.collectAsStateWithLifecycle()
			val newsList by viewModel.newsList.collectAsStateWithLifecycle()

			Surface {
				Column {
						**TopNews(topNews)**
						**NewsList(newsList)**
				}
			}
}

@Composable
fun TopNews(topNews: News) {
		Column(
				modifier = Modifier.clickable { navigateToDetailNews() }
		) {
				Text(text = topNews.headLine, style = theme.heading1)
				Image(painter = painterResource(topNews.image))
				Text(text = topNews.content, style = theme.body2)
		}
}

@Composable
fun NewsList(newsList) {
		LazyColumn {
				items(newsList) { news ->
						NewsItem(news)
				}
		}
}

@Composable
fun NewsItem(news: News) {
		// 
}

private fun navigateToDetailNews() {
		// navController.navigate("news/detail?id={id})
}
```

### 세로 밀집도 / 수직 거리

- 수직(세로) 거리로 연관성을 표현한다.

<aside>
🌱 서로 밀접한 개념은 세로로 가까이 두기

</aside>

**변수 선언**

- 변수는 사용하는 위치에 최대한 가까이 선언한다.
    - 지역 변수는 각 함수 맨 첫음에 선언하기
    
    ```kotlin
    private fun calculateTotalProfitRate(
            lottoTicket: LottoTicket,
            winningHistories: MutableMap<LottoWinningResult, Int>
        ): Double {
            **var totalProfit = 0.00**
    
            winningHistories.forEach { (lottoWinningResult, ticketCount) ->
                totalProfit += lottoWinningResult.profit * ticketCount
            }
    
            return roundProfitRate(((totalProfit / lottoTicket.ticketMoney) * 100))
        }
    ```
    
- 루프를 제어하는 변수는 루프문 내부에 선언한다.
    - 드물지만 루프 직전에 변수를 선언할 때도 있다.
    
    ```kotlin
    for (i in 0 until 10) {
    
    }
    
    var i = 0
    while (i < 10) {
    	//
    	i++
    }
    ```
    

<aside>
🌱 서로 밀접한 개념은 한 파일내에 있는 것이 좋다.

</aside>

> *[Q] protected 변수를 피해야 하는 이유?*
> 

**인스턴스 변수**

- 클래스 맨 처음에 선언한다.
    - 변수간에 세로로 거리를 두지 않는다.

- C++ : 모든 인스턴스 변수를 마지막에 선언하는 scissors rule
- Java : 클래스 맨 처음에 인스턴스 변수 선언

<aside>
💡 잘 알려진 위치에 인스턴스 변수를 모은다는 사실이 중요

</aside>

종속 함수

- 한 함수가 다른 함수를 호출한다면, 두 함수는 세로 가까이 배치한다.
- Caller를 Callee보다 먼저 배치

개념적 유사성

- 친화도가 높을수록 가까이 배치
    - 한 함수가 다른 함수를 호출할 때
    - 변수와 그 변수를 사용하는 함수
    - 비슷한 동작을 수행하는 일군의 함수 ( + 오버로딩)

- Overriding(오버라이딩) vs Overloading(오버로딩)
    
    오버라이딩 (Overriding)
    
    - 부모 클래스로부터 상속받은 메서드를 자식 클래스에서 **재정의**
    
    ```kotlin
    open class Animal {
    		open fun cry() {
    				pinrtln("캬아악")
    		}
    }
    
    class Cat: Animal {
    		**override fun cry() {**
    				pinrtln("야옹")
    		}
    }
    ```
    
    오버로딩(Overloading)
    
    - 이미 같은 이름을 가진 클래스 or 메서드가 있더라도 같은 이름을 사용해서 정의 가능
        - parameter의 타입, parameter의 개수
    
    ```kotlin
    // 생성자 오버로딩
    // Animal("동이")
    // Animal("동이", 13)
    class Animal(
        name: String
    ) {
        constructor(name: String, age: Int): this(name = name) {
            // 
        }
    }
    
    // 메서드 오버로딩
    class Animal {
    		fun cry() {
    				println("캬아악")
    		} 
    
    		fun cry(cryingSound: String) {
    				println(cryingSound)
    		} 
    
    		fun print(beforeCryingSound: String, afterCryingSound: String) {
    				println("${beforeCryingSound} -> ${afterCryingSound}")
    		}
    
    		// Possible?
    		fun cry(cryingSound: String): String {
    				println(cryingSound)
    				return cryingSound
    		}
    }
    ```
    

<aside>
🌱 코드에도 순서가 있다.

</aside>

1. static 변수 (public → protected → private)
2. instance 변수
3. 생성자
4. 메서드 (static → public → private)
    - public 메서드에서 호출되는 private 메서드는 바로 아래 둔다.
    
    ```kotlin
    fun calculateTotalProfitRate(
        lottoTicket: LottoTicket,
        winningHistories: MutableMap<LottoWinningResult, Int>
    ): Double {
        var totalProfit = 0.00
    
        winningHistories.forEach { (lottoWinningResult, ticketCount) ->
            totalProfit += lottoWinningResult.profit * ticketCount
        }
    
        return **roundProfitRate(**((totalProfit / lottoTicket.ticketMoney) * 100))
    }
    
    private fun **roundProfitRat**e(profitRate: Double): Double =
        ((profitRate * 100).roundToInt() / 100f).toDouble()
    ```
    

> *[Q] kotlin companion object 의 위치?*
> 

# 가로 형식 맞추기

> *프로그래머는 명백하게 짧은 행을 선호한다.*
> 
- 한 행을 100-120자 정도로 제한하는 것이 좋다.
    - InteliJ → 120자 (공백포함)

### 가로 공백과 밀집도

```kotlin
public class FitNesseExpediter implements ResponseSender {
    private     Socket         socket;
    private     InputStream    input;
    private     OutputStream   output;
    private     Reques         request;      
    private     Response       response; 
    private     FitNesseContex context; 
    protected   long           requestParsingTimeLimit;
    private     long           requestProgress;
    private     long           requestParsingDeadline;
    private     boolean        hasError;

	public FitNesseExpediter(Socket    s,
							 FitNesseContext context) throws Exception
	{
		this.context =            context;
		socket =                  s;
		input =                   s.getInputStream();
		output =                  s.getOutputStream();
		requestParsingTimeLimit = 10000;
	}
}
```

- 과도한 정렬은 하지 않기

```kotlin
public class FitNesseExpediter implements ResponseSender {
    private Socket socket;
    private InputStream input;
    private OutputStream output;
    private Request request;      
    private Response response; 
    private FitNesseContex context; 
    protected long requestParsingTimeLimit;
    private long requestProgress;
    private long requestParsingDeadline;
    private boolean hasError;

	public FitNesseExpediter(Socket s,
		FitNesseContext context) throws Exception {
		this.context = context;
		socket = s;
		input = s.getInputStream();
		output = s.getOutputStream();
		requestParsingTimeLimit = 10000;
	}
```

- 공백은 강조를 뜻하기도 한다.
    - 연산자
    - 람다

```kotlin
val two = 1+1

val two = 1 + 1

---

users.map { user-> user.toUserEntity() }

users.map { user -> user.toUserEntity() }
```

- 공백에도 규칙이 있다.
    - 조건문, 반복문 (if, when, for, while 등), try-catch 등
    
    ```kotlin
    if(num > 10) { }
    
    if (num > 10) { }
    
    ---
    
    while(true) { }
    
    while (true) { }
    ```
    
    - 클래스, 제네릭, 리턴 값 등
    
    ```kotlin
    class Foo: Runnable
    
    class Foo : Runnable
    
    ---
    
    fun <T: Comparable> max(a: T, b: T)
    
    fun <T : Comparable> max(a: T, b: T)
    
    ---
    
    override fun toString(): String {
    
    }
    
    override fun toString() : String {
    
    }
    ```
    

### 들여쓰기

- 파일 수준(최상위 수준)의 문장은 들여쓰지 않기 (클래스 정의 등)
- 클래스 내의 메서드는 클래스보다 한 수준 들여쓰기
- 블록 코드는 블록을 포함하는 코드보다 한 수준 들여쓰기
    - InteliJ → 4칸

- 중괄호와 개행
    
    ```kotlin
    if (string.isEmpty()) return
    
    if (string.isEmpty())
        return
    
    if (string.isEmpty()) {
        return
    }
    ```
    
    - 빈 블록
    
    ```kotlin
    try {
        doSomething()
    } catch (e: Exception) {} 
    
    try {
        doSomething()
    } catch (e: Exception) {
    } // Okay
    ```
    
    - 표현식
        - if - else 브랜치가 두개 미만 → 개행 X
        - 한 줄에 들어간다면 중괄호 생략
        
        ```kotlin
        val value = if (string.isEmpty()) 0 else 1
        
        ---
        
        when (value) {
            0 -> return
            // …
        }
        
        ---
        
        val value = if (string.isEmpty())
            0
        else
            1
        
        val value = if (string.isEmpty()) {
            0
        } else {
            1
        }
        ```
        
    

# 팀 규칙

> 팀은  한 가지 규칙에 합의해야 한다.
> 
- code convention
    - 어디에 괄호를 넣을지, 들여쓰기는 몇자를 할지
    - 클래스, 변수, 메서드 이름은 어떻게 지을지
- git convention
    - commit message
    - branch
    - PR
- **마치 한 사람이 구현한 듯한** 일관성 있는 코드를 제공하기

# 밥 아저씨의 형식 규칙

```kotlin
public class CodeAnalyzer implements JavaFileAnalysis { 
    private int lineCount;
    private int maxLineWidth;
    private int widestLineNumber;
    private LineWidthHistogram lineWidthHistogram; 
    private int totalChars;

    public CodeAnalyzer() {
        lineWidthHistogram = new LineWidthHistogram();
    }

    public static List<File> findJavaFiles(File parentDirectory) { 
        List<File> files = new ArrayList<File>(); 
        findJavaFiles(parentDirectory, files);
        return files;
    }

    private static void findJavaFiles(File parentDirectory, List<File> files) {
        for (File file : parentDirectory.listFiles()) {
            if (file.getName().endsWith(".java")) 
                files.add(file);
            else if (file.isDirectory()) 
                findJavaFiles(file, files);
        } 
    }

    public void analyzeFile(File javaFile) throws Exception { 
        BufferedReader br = new BufferedReader(new FileReader(javaFile)); 
        String line;
        while ((line = br.readLine()) != null)
            measureLine(line); 
    }

    private void measureLine(String line) { 
        lineCount++;
        int lineSize = line.length();
        totalChars += lineSize; 
        lineWidthHistogram.addLine(lineSize, lineCount);
        recordWidestLine(lineSize);
    }

    private void recordWidestLine(int lineSize) { 
        if (lineSize > maxLineWidth) {
            maxLineWidth = lineSize;
            widestLineNumber = lineCount; 
        }
    }

    public int getLineCount() { 
        return lineCount;
    }

    public int getMaxLineWidth() { 
        return maxLineWidth;
    }

    public int getWidestLineNumber() { 
        return widestLineNumber;
    }

    public LineWidthHistogram getLineWidthHistogram() {
        return lineWidthHistogram;
    }

    public double getMeanLineWidth() { 
        return (double)totalChars/lineCount;
    }

    public int getMedianLineWidth() {
        Integer[] sortedWidths = getSortedWidths(); 
        int cumulativeLineCount = 0;
        for (int width : sortedWidths) {
            cumulativeLineCount += lineCountForWidth(width); 
            if (cumulativeLineCount > lineCount/2)
                return width;
        }
        throw new Error("Cannot get here"); 
    }

    private int lineCountForWidth(int width) {
        return lineWidthHistogram.getLinesforWidth(width).size();
    }

    private Integer[] getSortedWidths() {
        Set<Integer> widths = lineWidthHistogram.getWidths(); 
        Integer[] sortedWidths = (widths.toArray(new Integer[0])); 
        Arrays.sort(sortedWidths);
        return sortedWidths;
    } 
}
```

---

- Kotlin(Java) Style Guide
    - camelCase
        - 상수가 아닌 변수
        - 함수
        
        | 기능 | O | X |
        | --- | --- | --- |
        | XML HTTP 요청 | requestXmlHttp | requestXMLHTTP |
        | 새 유저 ID | newCustomerId | newCustomerID |
        | 내부 스톱워치 | innerStopwatch | ineerStopWatch |
        | iOS에서 IPv6 지원 | supportIpv6OnIos | supportIPv6OnIOS |
    - backing property
    
    ```kotlin
    private val **_names** = mutableListOf<String>()
    val names: List<String>
        get() = _names
    ```
    
    - UPPER_SNAKE_CASE
        - 상수
        
        > [Q] `const val (static final)` vs `val`
        > 
        
        ```kotlin
        const val NUMBER = 5
        val NAMES = listOf("Alice", "Bob")
        val AGES = mapOf("Alice" to 35, "Bob" to 32)
        val COMMA_JOINER = Joiner.on(',') // Joiner is immutable
        val EMPTY_ARRAY = arrayOf()
        ```
        
        - Enum 클래스
        
        > [Q] enum은 `static` or `not static`?
        > 
        
        ```kotlin
        enum class Answer { YES, NO, MAYBE }
        ```
        
    
    - PascalCase
        - 소스 파일
            
            > *[Q] 파일 내부에 public function이 하나만 있는 경우의 파일 이름?*
            > 
            
            ```kotlin
            // 파일명 -> Main.kt vs main.kt
            fun main(args: List<String>) {
            	// 
            }
            ```
            
        - 클래스
        - @Composable () → Unit
    
    ```kotlin
    @Composable
    fun TextButton(text: String) {
        // …
    }
    ```
    
    - 패키지 이름
        
        ```kotlin
        // Okay
        package com.konkuk.cleancodestudy
        
        // WRONG
        pacakge com.konkuk.cleanCodeStudy
        // WRONG
        package com.konkuk.clean_code_study
        ```
        
    
- Java

[Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)

- Kotlin

[Coding conventions | Kotlin](https://kotlinlang.org/docs/coding-conventions.html)