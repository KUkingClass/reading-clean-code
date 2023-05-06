# [클린코드] 14. 점진적인 개선

---
## 결론

그저 돌아만가는 코드만으로는 부족하다

나쁜 코드가 프로젝트에 악영향을 끼친다

코드는 개선할 수 있다 하지만 나중에 개선하기 어렵다

처음부터 깨끗하게 유지하는 것이 효율적이다

## 점진적인 개선

명령행 인수의 구문을 분석하는 Args 클래스를 만들어보자

```java
public static void main(String[] args) {
	try {
		Args arg = new Args("l,p#,d*", args);
		boolean logging = arg.getBoolean('l');
		int port = arg.getInt('p');
		String directory = arg.getString('d');
		executeApplication(logging, port, directory);
	} catch (ArgsException e) {
		System.out.printf("Argument error: %s\n", e.errorMessage());
	}
}
```
예시
```java
-l -p Integer -d String
```

Args 클래스가 하는 일

- 스키마를 분석
- 명령행 인수들의 값을 관리
---

## 최종
    
    ```java
    private void parseSchema(String schema) throws ArgsException { 
        for (String element : schema.split(","))
          if (element.length() > 0) 
            parseSchemaElement(element.trim());
      }
      
    private void parseSchemaElement(String element) throws ArgsException { // "l,p#,d*"
      char elementId = element.charAt(0);
      String elementTail = element.substring(1);
      validateSchemaElementId(elementId);
      if (elementTail.length() == 0)
        marshalers.put(elementId, new BooleanArgumentMarshaler());
      else if (elementTail.equals("*")) 
        marshalers.put(elementId, new StringArgumentMarshaler());
      else if (elementTail.equals("#"))
        marshalers.put(elementId, new IntegerArgumentMarshaler());
      else if (elementTail.equals("##")) 
        marshalers.put(elementId, new DoubleArgumentMarshaler());
      else if (elementTail.equals("[*]"))
        marshalers.put(elementId, new StringArrayArgumentMarshaler());
      else
        throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
    }
    ```
    
    ```java
    private void parseArgumentStrings(List<String> argsList) throws ArgsException {
      for (currentArgument = argsList.listIterator(); currentArgument.hasNext();) {
        String argString = currentArgument.next(); 
        if (argString.startsWith("-")) {
          parseArgumentCharacters(argString.substring(1)); 
        } else {
          currentArgument.previous();
          break; 
        }
      } 
    }
    ```
    

## 1차 초안

```java
private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();

private boolean parseSchema() { 
  for (String element : schema.split(",")) {
    parseSchemaElement(element);
  }
  return true; 
}

private void parseSchemaElement(String element) { 
  if (element.length() == 1) 
    parseBooleanSchemaElement(element);
}

private void parseBooleanSchemaElement(char element) { 
	char c = element.charAt(0);
	if (Character.isLetter(c)) {
    booleanArgs.put(c, false);
	}
}
```

boolean 인수들만 가능하고 map에 저장

```java
private boolean parseArguments() {
  for (String arg : args)
    parseArgument(arg); 
  return true; 
}

private void parseArgument(String arg) { 
  if (arg.startsWith("-"))
    parseElements(arg); 
}

private void parseElements(String arg) { 
  for (int i = 1; i < arg.length(); i++)
    parseElement(arg.charAt(i)); 
}

private void parseElement(char argChar) { 
  if (isBoolean(argChar)) {
		numberOfArguments++;
		setBooleanArg(argChar, true);
  } else 
    unexpectedArguments.add(argChar); 
}

private void setBooleanArg(char argChar, boolean value) { 
  booleanArgs.put(argChar, value);
}

private boolean isBooleanArg(char argChar) { 
  return booleanArgs.containsKey(argChar);
}

public boolean getBoolean(char arg) { 
  return falseIfNull(booleanArgs.get(arg));
}
```

명령행 인수 문자열 배열에서 `-` 로 시작하는 문자열이 어떤 인수 유형인지를 map에서 key로 가지고 있는지로 확인하고  value를 갱신하여 저장

## Integer와 String 추가


```java
private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
private Map<Character, String> stringArgs = new HashMap<Character, String>(); 
private Map<Character, Integer> intArgs = new HashMap<Character, Integer>();

private void parseSchemaElement(String element) throws ParseException {
    char elementId = element.charAt(0);
    String elementTail = element.substring(1);
    validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
        booleanArgs.put(elementId, false);
    else if (elementTail.equals("*"))
        stringArgs.put(elementId, "");
    else if (elementTail.equals("#"))
        intArgs.put(elementId, 0);
    else
        throw new ParseException(String.format("Argument: %c has invalid format: %s.",
                elementId, elementTail), 0);
}
```

스키마 분석은 스키마를 타입별로 Map 을 여러 개 만들어 관리

```java
private boolean parseArguments() throws ArgsException {
    for (currentArgument = 0; currentArgument < args.length; currentArgument++) {
        String arg = args[currentArgument];
        parseArgument(arg);
    }
    return true;
}

private void parseArgument(String arg) throws ArgsException {
    if (arg.startsWith("-"))
        parseElements(arg);
}

private void parseElements(String arg) throws ArgsException {
    for (int i = 1; i < arg.length(); i++)
        parseElement(arg.charAt(i));
}

private void parseElement(char argChar) throws ArgsException {
    if (setArgument(argChar))
        argsFound.add(argChar);
    else {
        unexpectedArguments.add(argChar);
		    errorCode = ErrorCode.UNEXPECTED_ARGUMENT;
		    valid = false;
		}
}

private boolean setArgument(char argChar) throws ArgsException {
    if (isBooleanArg(argChar))
        setBooleanArg(argChar, true);
    else if (isStringArg(argChar))
        setStringArg(argChar);
    else if (isIntArg(argChar))
        setIntArg(argChar);
    else
        return false;

    return true;
}
```

```java
private boolean isStringArg(char argChar) {
    return stringArgs.containsKey(argChar);
}

private void setStringArg(char argChar) throws ArgsException {
    currentArgument++;
    try {
        stringArgs.put(argChar, args[currentArgument]);
    } catch (ArrayIndexOutOfBoundsException e) {
        valid = false;
        errorArgumentId = argChar;
        errorCode = ErrorCode.MISSING_STRING;
        throw new ArgsException();
    }
}

public String getString(char arg) {
    return blankIfNull(stringArgs.get(arg));
}
```

타입이 늘어남에 따라 setArgument가 변하고 is, set, get 메소드가 추가로 필요

## ArgumentMarshaler

개선이라는 이름으로 구조를 크게 뒤집지 말자

개선 전과 똑같은 프로그램으로 만드는 건 어렵기 때문이다

단위 테스트와 인수 테스트를 활용한 테스트 주도 개발로 개선하자

```java
private class ArgumentMarshaler { 
  private boolean booleanValue = false;

  public void setBoolean(boolean value) { 
    booleanValue = value;
  }
  
  public boolean getBoolean() {return booleanValue;} 
}

private class BooleanArgumentMarshaler extends ArgumentMarshaler { }
private class StringArgumentMarshaler extends ArgumentMarshaler { }
private class IntegerArgumentMarshaler extends ArgumentMarshaler { }
```

인수 유형은 여러 가지이지만 모두 유사한 메소드를 제공하므로 클래스로 추출

```java
private Map<Character, ArgumentMarshaler> booleanArgs = new HashMap<Character, ArgumentMarshaler>();

private void parseBooleanSchemaElement(char elementId) {
  booleanArgs.put(elementId, new BooleanArgumentMarshaler());
}

private void setBooleanArg(char argChar, boolean value) {
  booleanArgs.get(argChar).setBoolean(value);
}

public boolean getBoolean(char arg) {
  Args.ArgumentMarshaler am = booleanArgs.get(arg);
  return am != null && am.getBoolean();
}
```

ArgumentMarshaler 클래스를 도입하면서 실패하는 테스트를 위한 코드 수정

String과 Integer도 ArgumentMarshaler에게 로직을 옮기고 파생 클래스를 만들어 기능을 분산한다

```java
private abstract class ArgumentMarshaler { 
  public abstract void set(String s) throws ArgsException;
  public abstract Object get(); 
}

private class BooleanArgumentMarshaler extends ArgumentMarshaler { ... }
private class StringArgumentMarshaler extends ArgumentMarshaler { ... }
private class IntegerArgumentMarshaler extends ArgumentMarshaler {
	private boolean intValue = 0;

  public void set(String s) throws ArgsException {
		try {
			intValue = Integer.parseInt(s);
		} catch (NumberFormatException e) {
			throw new ArgsException;
		}
  }
  
  public boolean get() {return intValue;} 
}
```

## 첫 번째 리팩터링 이후

이제 ArgumentMarshaler를 활용해 인수 유형 마다 존재하고 있던 Map들을 교체하고 관련 메소드를 변경한다

```java
private Map<Character, ArgumentMarshaler> marshalers = new HashMap<Character, ArgumentMarshaler>();

private void parseSchemaElement(String element) throws ParseException {
  char elementId = element.charAt(0);
  String elementTail = element.substring(1); 
  validateSchemaElementId(elementId);
  if (elementTail.length() == 0) 
    marshalers.put(elementId, new BooleanArgumentMarshalers());
  else if (elementTail.equals("*")) 
    marshalers.put(elementId, new StringArgumentMarshalers());
  else if (elementTail.equals("#")) 
    marshalers.put(elementId, new IntegerArgumentMarshalers());
  else
    throw new ParseException(String.format("Argument: %c has invalid format: %s.", 
      elementId, elementTail), 0);
  } 
}

private boolean setArgument(char argChar) throws ArgsException {
	ArgumentMarshaler m = marshalers.get(argChar);
	try {
    if (m instanceof BooleanArgumentMarshaler)
      setBooleanArg(argChar, true); 
    else if (m instanceof StringArgumentMarshaler)
      setStringArg(argChar); 
    else if (m instanceof IntegerArgumentMarshaler)
      setIntArg(argChar); 
    else
      return false;
  } catch (ArgsException e) {
		valid = false;
		errorArgumentId = argChar;
		throw e;
	}
  return true; 
}

private void setIntArg(ArgumentMarshaler m) throws ArgsException {
  currentArgument++;
  String parameter = null;
  try {
    parameter = args[currentArgument];
    m.set(parameter);
  } catch (ArrayIndexOutOfBoundsException e) {
    errorCode = ErrorCode.MISSING_INTEGER;
    throw new ArgsException();
  } catch (ArgsException e) {
    errorParameter = parameter;
    errorCode = ErrorCode.INVALID_INTEGER;
    throw e;
  }
}

public int getInt(char arg) {
  Args.ArgumentMarshaler am = marshalers.get(arg);
  boolean b = false;
  try {
    return am == null ? 0 : (Integer) am.get();
  } catch (Exception e) {
    return 0;
  }
}
```

setArgument에서의 유형을 일일이 확이하는 부분을 없애고 싶다

ArgumentMarshaler.set만 호출해도 충분하게 만들고 싶다

그러면 setXXXArg도 각 ArgumentMarshaler의 파생클래스로 내리자

파라미터인 args(String[])와 currentArgument(int) 대신 args를 리스트로 바꾸고 iterator만 전달하자


```java
private boolean setArgument(char argChar) throws ArgsException {
	ArgumentMarshaler m = marshalers.get(argChar);
	if (m == null)
		return false;
	try {
    m.set(currentArgument); 
    return true;
  } catch (ArgsException e) {
		valid = false;
		errorArgumentId = argChar;
		throw e;
	}
}

private interface ArgumentMarshaler {
	void set(Iterator<String> currentArgument) throws ArgsException;
	Object get();
}

private class IntegerArgumentMarshaler implements ArgumentMarshaler {
  private int intValue = 0;

  public void set(Iterator<String> currentArgument) throws ArgsException {
    String parameter = null;
    try {
      parameter = currentArgument.next();
      intValue = Integer.parseInt(parameter);
    } catch (NoSuchElementException e) {
      errorCode = ErrorCode.MISSING_INTEGER;
      throw new ArgsException();
    } catch (NumberFormatException e) {
      errorParameter = parameter;
      errorCode = ErrorCode.INVALID_INTEGER;
      throw new ArgsException();
    }
  }

  public int get() {
      return intValue;
  }
}
```

이젠 새로운 인수 유형 추가하기도 쉬워졌다

parseSchemaElement에서 새로운 인수유형에 대한 판별 코드를 추가하고

새로운 인수 유형에 대한 ArgumentMarshaler 의 구현 클래스를 만들고

getXXX와 새로운 오류 처리 코드만 추가하면 된다

Args 클래스에서 ArgsException과 모든 ArgumentMarshaler 클래스를 빼서 각자 파일로 옮긴다

분할도 이해하기 쉬워지고 유지보수가 쉬워지므로 소프트웨서 설계의 품질을 높인다