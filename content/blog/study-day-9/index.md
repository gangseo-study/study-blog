---
title: Study Day 9 Interceptor, Exception
date: "2023-04-02"
description: "Interceptor, Exception"
---

#### 인터셉터

스프링 인터셉터는 클라이언트의 요청이 컨트롤러로 전달되기 전이나  
후에 해당 요청을 가로채어 추가적인 작업을 수행할 수 있는 기능이다.  
즉, 요청에 대한 전처리나 후처리 작업을 수행할 수 있다.  
예를 들어, 요청에 대한 로깅, 인증/인가, 권한 체크, 캐싱, 암호화/복호화 등의 작업을 수행할 수 있다.

인터셉터는 스프링 프레임워크에서 제공하는 HandlerInterceptor 인터페이스를 구현하여 사용할 수 있다.  
HandlerInterceptor 인터페이스는 다음과 같은 3개의 메소드를 가지고 있다.

```
1. preHandle: 컨트롤러 메소드 실행 전에 호출되는 메소드로, 해당 요청에 대한 전처리 작업을 수행한다.
               이 메소드가 false를 반환하면 컨트롤러 메소드가 실행되지 않는다.
2. postHandle: 컨트롤러 메소드 실행 후, 뷰가 렌더링되기 전에 호출되는 메소드로,
                해당 요청에 대한 후처리 작업을 수행한다.
3. afterCompletion: 컨트롤러 메소드와 뷰가 모두 렌더링된 후에 호출되는 메소드로,
                     인터셉터에서 사용한 리소스를 해제하는 등의 작업을 수행한다.
```

인터셉터를 등록하려면 스프링 설정 파일에서 WebMvcConfigurer 인터페이스를 구현한 클래스를 생성하고,  
addInterceptors 메소드를 오버라이딩하여 인터셉터를 등록하면 된다.  
이 때, 등록 순서에 따라 인터셉터가 실행된다.

```java
public class CustomInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String name = request.getParameter("name");
        if (name == null || name.isEmpty()) {
            throw new RuntimeException("이름이 입력되지 않았습니다.");
        }
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        // postHandle() 메소드는 preHandle() 메소드가 반환한 true인 경우에만 실행된다.
        System.out.println("CustomInterceptor - postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // afterCompletion() 메소드는 postHandle() 메소드가 실행된 후에 호출된다.
        System.out.println("CustomInterceptor - afterCompletion");
    }
}
```

위의 예제에서는 preHandle 메소드에서 name 파라미터가 입력되지 않은 경우 RuntimeException을 발생시킨다.  
이 예외는 GlobalExceptionHandler 클래스에서 처리된다.

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ModelAndView handleRuntimeException(RuntimeException ex) {
        ModelAndView mav = new ModelAndView();
        mav.addObject("errorMessage", ex.getMessage());
        mav.setViewName("error");
        return mav;
    }
}
```

```java
// 예외 처리를 위한 메소드
@ExceptionHandler(Exception.class)
public ResponseEntity<ErrorResponse> handleException(Exception ex) {
    ErrorResponse errorResponse = new ErrorResponse(HttpStatus.NOT_FOUND.value(), ex.getMessage());
      return ResponseEntity.status(HttpStatus.NOT_FOUND).body(errorResponse);
    }
}
```

스프링 인터셉터는 스프링 MVC에서 요청을 가로채고 처리하기 전에 추가로 작업을 수행할 수 있는 기능을 제공한다.  
예를 들어, 로그인 상태 확인, 권한 검사, 로깅 등의 작업을 수행할 수 있다.  
스프링 인터셉터는 HTTP 요청과 응답에만 적용된다.

###### 장점:

```
1. 중복 코드 제거: 스프링 인터셉터를 이용하면, 각 컨트롤러에서 반복적으로 수행되는 코드들을 한 곳에서 관리할 수 있다.
                   이를 통해 코드 중복을 제거하고, 개발자들은 보다 간결하고 유지보수가 용이한 코드를 작성할 수 있다.
2. 효율적인 처리: 스프링 인터셉터를 이용하면, 요청을 가로채어 처리할 수 있다.
                  이를 통해 요청 처리의 효율성을 높일 수 있으며,
                  특정 요청에 대한 예외처리나 로깅 등의 공통적인 작업을 효율적으로 처리할 수 있다.
3. 보안 강화: 스프링 인터셉터를 이용하면, 요청에 대한 권한 확인 및 인증 작업을 수행할 수 있다.
              이를 통해 보안성을 강화할 수 있다.
```

###### 단점:

```
1. 복잡성: 스프링 인터셉터는 컨트롤러 이전 또는 이후에 작업을 수행하므로, 컨트롤러와의 연동성이 중요하다.
           이를 위해서는 인터셉터와 컨트롤러 간의 연동 관계를 잘 이해하고 있어야 하며, 이는 추가적인 로직이 필요하다.
2. 성능 저하: 스프링 인터셉터를 이용하면, 요청 처리 시간이 길어지는 경우가 있다.
              이는 인터셉터가 요청을 가로채어 추가적인 작업을 수행하므로, 성능 저하가 발생할 수 있다.
3. 예외 처리: 스프링 인터셉터는 요청을 가로채어 처리하는 과정에서 예외가 발생할 수 있다.
              이를 처리하기 위해서는 예외 처리에 대한 추가적인 로직이 필요하다.
```

예를 들어 SampleInterceptor에서 예외가 발생했을 때,  
해당 예외를 GlobalExceptionHandler 클래스에서 구현한 handleException 메소드로 던진다.  
이때 handleException 메소드에서는 해당 예외에 대한 처리를 수행하고,  
예외 처리 결과를 이용하여 적절한 뷰를 반환한다.

위의 예제에서는 RuntimeException 타입의 예외가 발생한 경우 handleRuntimeException 메소드가 호출되어 예외 처리를 수행한다.  
이 메소드에서는 ModelAndView 객체를 이용하여 예외 발생 시 보여줄 에러 페이지를 지정한다.

이렇게 구현된 예제에서 name 파라미터가 입력되지 않은 경우,  
RuntimeException이 발생하고 GlobalExceptionHandler에서 예외 처리가 수행된다.  
그리고 예외 처리 결과인 ModelAndView 객체를 통해 error.jsp 페이지가 보여지게 된다.

##### AOP와의 유사성?

스프링 인터셉터와 AOP는 기능적으로 비슷한 부분이 있다.  
둘 다 요청 처리에 대한 전처리나 후처리 작업을 수행할 수 있으며,  
공통적인 부가 기능을 제공하는 것이라는 점에서 비슷하다.

하지만 둘은 목적과 사용 방법에 차이가 있다.  
스프링 인터셉터는 HTTP 요청 처리와 관련된 작업을 수행하는 것이 주된 목적이며,  
클라이언트의 요청을 가로채어 추가적인 작업을 수행하는 방식으로 사용된다.  
반면에, AOP는 여러 객체에서 공통적으로 사용되는 부가 기능을 분리하여 관리하는 것이 목적이며,  
이를 위해 프록시 패턴을 이용하여 구현되고, 대표적으로 로깅, 보안, 트랜잭션 등의 부가 기능을 제공한다.

따라서, 스프링 인터셉터와 AOP는 기능적으로는 비슷하지만,  
목적과 사용 방법에 따라서 차이가 있다.

#### 예외처리

예외 처리란, 프로그램 실행 중 예기치 않은 상황이 발생했을 때 이를 적절하게 처리하는 것을 의미한다.  
예외 처리는 프로그램의 안정성과 신뢰성을 높이기 위해 필수적이다.

자바에서는 예외(Exception)와 에러(Error)를 구분한다.  
**예외**는 일반적으로 프로그램에서 처리 가능한 예기치 않은 상황을 의미하며,  
**에러**는 일반적으로 시스템 수준의 치명적인 오류를 의미한다.

예외 처리는 try-catch문을 사용하여 처리할 수 있다.  
try 블록 내에서 예외가 발생하면, catch 블록에서 해당 예외를 처리한다.

예시)

```java
try {
    System.out.println("try 블록 시작");
    int result = 10 / 0; // ArithmeticException 발생 가능
    System.out.println("try 블록 끝");
} catch (ArithmeticException e) {
    System.out.println("0으로 나눌 수 없습니다.");
}
```

다음과 같은 코드에서는, 숫자를 0으로 나누는 경우 ArithmeticException이 발생할 수 있다.  
이 예외를 try-catch문으로 처리할 수 있다.

try 블록에서 예외가 발생하면 해당 예외를 처리하기 위한 catch 블록으로 바로 이동하게 된다.  
따라서, try 블록에서 예외가 발생하면 try 블록 내에서 이후 로직은 실행되지 않는다.  
대신, 예외 처리를 위한 catch 블록으로 이동하게 되고, catch 블록의 로직이 실행된다.

##### 예외 이전에 로직을 rollback하려면 ?

```java
public void myMethod() {
    Connection conn = null;
    PreparedStatement pstmt = null;

    try {
        conn = getConnection();
        conn.setAutoCommit(false); // AutoCommit 모드를 false로 변경
        pstmt = conn.prepareStatement("INSERT INTO my_table (name, age) VALUES (?, ?)");
        pstmt.setString(1, "John Doe");
        pstmt.setInt(2, 30);
        pstmt.executeUpdate();
        // 예외 발생 가능성이 있는 코드
        conn.commit(); // 모든 SQL 문이 정상적으로 실행되었을 경우, commit
    } catch (SQLException e) {
        conn.rollback(); // 예외 발생 시, rollback 처리
        e.printStackTrace();
        // 예외 처리
    } finally {
        try {
            conn.setAutoCommit(true); // AutoCommit 모드를 true로 변경
        } catch (SQLException e) {
            e.printStackTrace();
        }
        close(pstmt);
        close(conn);
    }
}
```

예외 발생 시에 이미 실행된 SQL 문을 롤백하려면,  
Connection 객체의 setAutoCommit 메서드를 false로 설정하고,  
예외 발생 시 rollback 메서드를 호출하면 된다.  
롤백 처리를 하게 되면, 이미 실행된 SQL 문도 함께 취소되므로, 데이터베이스에는 어떤 데이터도 삽입되지 않는다.  
롤백 처리 후에는, catch 블록에서 예외를 처리하고 finally 블록에서 Connection, PreparedStatement 등을 반드시 닫아주어야 한다.

##### 커스텀 예외처리

```java
public class InvalidParameterException extends Exception {
    private String parameterName;
    private String parameterValue;

    public InvalidParameterException(String parameterName, String parameterValue) {
        this.parameterName = parameterName;
        this.parameterValue = parameterValue;
    }

    @Override
    public String getMessage() {
        return "Invalid Parameter: " + parameterName + " = " + parameterValue;
    }
}
```

```java
public void myMethod(String name, int age) throws SQLException, InvalidParameterException {
    Connection conn = null;
    PreparedStatement pstmt = null;

    if (name == null) {
        throw new InvalidParameterException("name", "null"); // 커스텀 예외 발생
    }

    try {
        conn = getConnection();
        pstmt = conn.prepareStatement("INSERT INTO my_table (name, age) VALUES (?, ?)");
        pstmt.setString(1, name);
        pstmt.setInt(2, age);
        pstmt.executeUpdate();
        conn.commit();
    } catch (SQLException e) {
        conn.rollback();
        e.printStackTrace();
    } finally {
        close(pstmt);
        close(conn);
    }
}
```

```java
public void myMethod(String name, int age) throws SQLException {
    Connection conn = null;
    PreparedStatement pstmt = null;

    try {
        if (name == null) {
            throw new InvalidParameterException("name", "null"); // 커스텀 예외 발생
        }

        conn = getConnection();
        pstmt = conn.prepareStatement("INSERT INTO my_table (name, age) VALUES (?, ?)");
        pstmt.setString(1, name);
        pstmt.setInt(2, age);
        pstmt.executeUpdate();
        conn.commit();
    } catch (InvalidParameterException e) {
        // InvalidParameterException 예외 처리
        e.printStackTrace();
    } catch (SQLException e) {
        // SQLException 예외 처리
        conn.rollback();
        e.printStackTrace();
    } finally {
        close(pstmt);
        close(conn);
    }
}
```

**예외 처리를 위한 몇 가지 방법이 있다.**

###### throws

메소드 뒤에 throws 키워드를 이용하여, 해당 메소드가 발생시킬 수 있는 예외를 명시할 수 있다.  
이렇게 하면 해당 메소드를 호출하는 곳에서 예외 처리를 하도록 **강제**할 수 있다.

```java
public void myMethod() throws Exception {
    // 예외 발생 가능한 로직
}
```

###### finally

finally 블록을 이용하여, 예외 발생 여부와 상관없이 항상 실행되는 로직을 작성할 수 있다.  
finally 블록은 try-catch 블록 뒤에 위치하며, 예외가 발생해도 **반드시 실행**된다.

```java
public void myMethod() {
    try {
        // 예외 발생 가능한 로직
    } catch (Exception e) {
        // 예외 처리 로직
    } finally {
        // 항상 실행되는 로직
    }
}
```

###### try-with-resources

Java 7부터는 try-with-resources 구문을 이용하여, try 블록 안에서 사용한 리소스를 자동으로 닫아줄 수 있다.  
이렇게 하면, 코드가 더 간결해지고 예외 처리도 더욱 안전해진다.

```java
public void myMethod() throws IOException {
    try (FileInputStream input = new FileInputStream("myFile.txt")) {
        // 파일 읽기 로직
    } catch (IOException e) {
        // 예외 처리 로직
    }
}
```

위 예제에서, FileInputStream은 try 블록에서 초기화되며, try 블록을 빠져나갈 때 자동으로 닫힌다.  
이렇게 하면, 명시적으로 close() 메소드를 호출하지 않아도 된다.

###### throw new는 예외를 직접 발생시키는 방법 중 하나이다.

throw 키워드를 사용하여 예외 객체를 던지는 것이 가능한데,  
이 때 new 키워드를 사용하여 새로운 예외 객체를 생성하고 던진다.

예를 들어, 다음과 같이 IllegalArgumentException 예외를 발생시키는 코드를 작성할 수 있다.

```java
public void myMethod(String arg) {
    if (arg == null) {
        throw new IllegalArgumentException("argument must not be null");
    }
    //...
}
```

위 코드에서, arg가 null인 경우, IllegalArgumentException 예외를 발생시킨다.  
이 때 new 키워드를 사용하여 새로운 IllegalArgumentException 객체를 생성하고, 메시지를 전달한다.  
예외 객체가 생성되면, 해당 예외를 던지고 호출자 쪽으로 예외 처리를 넘긴다.

##### 리턴 타입이 String인 경우?

**예외를 직접 발생시키는 것은 불가능**하다.  
대신 예외가 발생한 경우, 호출자에게 예외를 알리는 방법으로 예외를 처리해야 한다.  
이 때 주로 **두 가지 방법**을 사용한다.

###### 첫 번째 방법

메서드 시그니처에 throws 키워드를 사용하여 예외를 던짐으로써 호출자에게 알리는 것이다.  
이 방법은 해당 메서드를 호출하는 쪽에서 예외 처리를 강제할 수 있는 장점이 있다.

```java
public String myMethod(String arg) throws MyException {
    if (arg == null) {
        throw new MyException("argument must not be null");
    }
    //...
    return result;
}
```

위 코드에서, MyException 예외를 발생시키고, 해당 예외를 호출자에게 알리기 위해 throws 키워드를 사용했다.  
이렇게 작성된 메서드를 호출하는 쪽에서는 반드시 try-catch 구문을 사용하여 예외 처리를 해야 한다.

###### 두 번째 방법

예외가 발생한 경우 예외 처리를 담당하는 별도의 메서드를 작성하는 것이다.  
이 때 호출자는 해당 메서드를 호출하여 예외 처리를 수행한다.

```java
public String myMethod(String arg) {
    try {
        if (arg == null) {
            throw new MyException("argument must not be null");
        }
        //...
        return result;
    } catch (MyException e) {
        return handleMyException(e);
    }
}

private String handleMyException(MyException e) {
    // 예외 처리 로직
    return "error";
}
```

위 코드에서, myMethod 메서드에서는 예외가 발생한 경우 handleMyException 메서드를 호출하여 예외 처리를 수행한다.  
이 때 handleMyException 메서드에서는 예외 처리 로직을 구현하고, 해당 결과를 반환한다.  
이렇게 작성된 메서드를 호출하는 쪽에서는 예외 처리를 하지 않아도 되므로, 코드의 가독성을 높일 수 있다.

###### Service에서 던진 예외는 RestController에서 어떻게 받나 ?

```java
@RestControllerAdvice
public class ExceptionHandler {

    @org.springframework.web.bind.annotation.ExceptionHandler(CustomException.class)
    public ResponseEntity<ErrorResponse> handleCustomException(CustomException ex) {
        ErrorResponse response = new ErrorResponse(ex.getMessage(), ex.getErrorCode());
        return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
    }
}
```

컨트롤러에서 발생한 예외를 처리하고, 해당 예외에 대한 응답을 클라이언트에게 전달하는 경우,  
일반적으로는 예외를 처리하는 메서드에서 예외를 캐치하여 응답을 생성하게 된다.

예를 들어, 다음과 같은 코드에서는 CustomException 이 발생하면,  
ExceptionHandler 에서 해당 예외를 캐치하고,  
클라이언트에게 JSON 형태의 에러 응답을 반환한다.

여기서 CustomException 은 서비스에서 발생한 예외이며,  
ErrorResponse 는 클라이언트에게 반환될 예외 응답의 형식을 나타낸다.  
예외 응답에는 일반적으로 에러 메시지와 에러 코드를 포함하게 된다.

###### CustomException을 던지는 서비스 로직 예시

```java
public class CustomException extends RuntimeException {
    public CustomException(String message) {
        super(message);
    }
}
```

```java
@Service
public class SampleService {
    public void doSomething() {
        throw new CustomException("Custom Exception Occurred!");
    }
}
```

컨트롤러에서 CustomException을 처리하는 방법 예시:

```java
@RestController
public class SampleController {
    @Autowired
    private SampleService sampleService;

    @GetMapping("/sample")
    public String getSample() {
        try {
            sampleService.doSomething();
        } catch (CustomException e) {
            return "Custom Exception Occurred!";
        }

        return "Success!";
    }
}
```
