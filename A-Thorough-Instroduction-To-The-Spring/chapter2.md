# 2장 스프링 코어(DI, AOP)

## 2.1. DI
 어떤 클래스가 필요로 하는 컴포넌트를 외부에서 생성한 후, 내부에서 사용 가능하게 만들어주는 과정을 '의존성을 주입(DI) 한다.' 또는 '인젝션(Injection) 한다'라고 말한다. <br>
 의존성 주입을 자동으로 처리하는 기반을 'DI 컨테이너'라고한다.
  - DI 장점
    - 의존성 해결
    - 싱글톤(Singleton) 및 프로토타입(Prototype)같은 인스턴스 스코프(Scope)관리를 DI가 대신함

### 2.1.1 DI 개요
 DI는 의존성 주입이라고도 하며, IoC라고하는 소프트웨어 디자인 패턴중 하나. 이때의 IoC는 인스턴스를 제어하는 주도권이 역전된다는 의미로 사용되는데, 컴퓨넌트를 구성하는 인스턴스의 생성과 의존 관계의 연결 처리를 해당 소스코드가 아닌 DI 컨테이너에서 대신해 주기 때문에 제어가 역전됐다고 보는 것
 - DI 컨테이터 관리 방식의 장점
    - 인스턴스의 스코프 제어
    - 인스턴스의 생명주기 제어
    - AOP 방식으로 공통 기능 넣을 수 있음
    - 의존하는 컴포넌트 간의 결합도를 낮춰 테스트하기 쉬움
 > 스프링 공식문서에는 스프링 프레임워크를 DI 컨테이너가 아니라 IoC 컨테이너라고 기재하고 있다.

### 2.1.2 ApplicationContext와 빈정의
 스프링 프레임워크에서는 Application Context가 DI 컨테이너 역할을 한다.
 - @Configuration과 @Bean 애너테이션을 사용해서 DI 컨테이너에 컴포넌트를 등록하면 애플리케이션은 DI 컨테이너에 있는 빈(Bean)을 ApplicationContext 인스턴스를 통해 가져올 수 있다. DI 컨테이너에 등록하는 컴포넌트를 빈(Bean)이라고 하고, 이 빈에 대한 설정을(Configuration) 정보를 '빈 정의(Bean Definition)'이라고 한다. DI 컨테이너에서 빈을 찾아오는 해위를 '룩업(Lookup)' 이라고 한다.

 - DI 컨테이너에서 빈 가져오기
    - XxxService xxxService = context.getBean(XxxService.class);
        - 가져오려는 빈 타입(Type)을 지정하는 방법. 지정한 타입에 해당하는 빈이 DI 컨테이너에 오직 하나만 있을 때 사용한다. 스프링 프레임워크 3.0.0 부터 사용 가능
    - XxxService xxxService = context.getBean("xxxService", XxxService.class);
        - 가져오려는 빈의 이름과 타임을 지정하는 방법이다. 지정한 타입에 해당하는 빈이 DI 컨테이너에 여러 개 있을 때 이름으로 구분하기 위해 사용한다. 스프링 프레임워크 2.5.6 버전까지는 반환값이 Object 타입이라서 원하느 빈의 타입으로 형변환 해야했지만 3.0.0 버전 이후부터는 형변환하지 않아도 된다.
    - XxxService xxxService = (XxxService) context.getBean("xxxService");
        - 가져오려는 빈의 이름을 지정하는 방법이다. 반환값이 Object 타입이라서 원하는 빈의 타입으로 형변환해야 한다. 스프링 프레임워크 1.0.0 부터 사용가능하다.

 - 빈을 설정하는 몇가지 방법
    - 자바기반 설정 방식(Java-based configuration)
        - 자바 클래스에 @Configuration 애너테이션을, 메서드에 @Bean 애너테이션을 사용해 빈을 정의하는 방법으로 스프링 프레임워크 3.0.0부터 사용할 수 있다. 최근에는 스프링 기반 애플리케이션 개발에 자주 사용되고 특히 스프링 부트에서 이 방식을 많이 활용한다.
    - XML 기반 설정 방식(XML-based configuration)
        - XML 파일을 사용하는 방법으로 &lt;bean&gt; 요소의 class 속성에 FQCN(Fully-Qualified Class Name)을 기술하면 빈이 정의된다. &lt;constructor-arg>나 &lt;property&gt; 요소를 사용해 의존성을 주입한다. 스프링 1.0.0부터 사용할 수 있다.
    - 애너테이션 기반설정 방식(Annotation-based configuration)
        - Component 같은 마커 애너테이션(Marker Annotation)이 부여된 클래스를 탐색해서(Component Scan) DI 컨테이너에 빈을 자동으로 등록하는 방법이다. 스프링 프레임워크 2.5부터 사용 할 수있다.
    
    - > 자바 기반 설정방식이나 XML 기반설정 방식만 사용해서 빈을 정의할 수도 있지만 대부분의경우 자가바 기반설정 방식과 애너테이션 기반설정 방식을 조합하거나 XML 기반 설정방식과 애너테이션 설정 방식의 조합을 많이 사용한다.


 - ApplicationContext을 생성하는 다양한 방법 
    - ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        - 자바 기반의 설정방식으로 AnnotationConfigApplicationContext의 생성자에 @configuration 애너테이션이 붙은 클래스를 인수로 전달한다.
    - ApplicationContext context = new AnnotationConfigApplicationContext("com.example.app");
        - 애너테이션 기반의 설정 방식으로 AnnotationConfigApplicationContext의 생성자에 패키지명을 인수로 전달한다. 지정된 패키지 이하의 경로에서 컴포넌트를 스캔한다.
    - ApplicationContext context = new ClassPathXmlApplicationContext("META-INF/spring/applicationContext.xml");
        - XML 기반의 설정 방식으로 ClassPathXmlApplicationContext의 생성자에 패키지명을 인수로 전달한다. 경로에 접두어(prefix)가 생략된 경우에는 클래스패스 안에서 상대 경로로 설정 파일을 탐색한다.
    - ApplicationContext context = new FileSystemXmlApplicationContext("./spring/applicationContext.xml");
        - XML 기반의 설정 방식으로 ClassPathXmlApplicationContext의 생성자에 패키지명을 인수로 전달한다. 경로에 접두어가 생략된 경우에는 JVM의 작업 디렉토리 안에서 상대 경로로 설정 파일을 탐색한다.
    
    - > ApplicationContext는 단독 애플리케이션에서 스프링 프레임워크를 사용하거나 JUnit으로 만든 테스트 케이스 안에서 스프링 프레임워크를 구동할 때 사용된다. 반면 웹 애플리케이션에서는 스프링 MVC를 활용하게 되는데, 이때는 ApplicationContext를 웹 환경에 맞게 확장한 WebApplicationContext를 사용한다.

### 2.1.3 빈설정
#### 자바기반의 설정 방식
 - 클래스에 @Configuration 애너네이션을 붙여 설정 클래스를 선언한다. 설정 클래스는 여러 개 정의 할 수 있다.
 - 메서드에 @Bean 애너테이션을 붙여 빈을 정의한다. 메서드명이 빈의 이름이 되고 그 빈의 인스턴스가 반환값이 된다. 반약 빈 이름을 다르게 명시하고 싶다면 @Bean(name="xxxx")와 같이 name 속성에 빈 이름을 재정의 하면된다.
  - 다른 컴포넌트를 참조해야 할 때는 해당 컴포넌트의 메서드를 호출한다. 의존성 주입이 프로그램 적인 방법으로 처리된다.

자바 기반 설정에서는 메서드 매개변수를 추가하는 방법으로 다른 컴포넌트에 의존성을 주입 할 수 있다. 단, 인수로 전달될 인스턴스에 대한 빈은 별도로 정의 돼 있어야한다.<br>
자바 기반 설정 방식만 사용해서 빈을 정의 할 때는 애플리케이션에서 사용 되는 모든 컴포넌트를 빈으로 정의해아한다.<br>
- 애너테이션 기반  설정 방식과 조압하면 설정 내용의 많은 부분을 줄일 수 있다.

#### XML 기반 설정 방식
XML 파일을 이용해 빈을 설정한다.
 - &lt;beans&gt; 요소 안에 빈 정의를 여러개 한다.
 - &lt;bean&gt; 요소에 빈을 정의한다. id 속성에 지정한 값의 빈의 이름이 되고, class 속송에 지정한 클래스가 해당 반의 구현 클래스다. 이때 class속성언 FQCN으로 패키지명부터 클래스명까지 정화하게 기재해야한다.
 - &lt;constructor-arg&gt; 요소에 생성자를 활용한 의존성 주입을한다. ref 속성에 주입할 빈의 이름을 기재한다.
    - 의존성 주입에 주입할 대상이 다른 빈이 아니라 특정 값인 경우. ref 속성을 사용하지 않고 value 속성을 사용한다.

XML만으로 설정하면 애플리케이션에서 사용하는 모든 컴포넌트의 빈을 정의해야하는 번거로움이 있따. 애너테이션 기반 설정과 조합하는 것이 일반적이다.

#### 애너테이션 기반 설정 방식
DI 컨테이너에 관리할 빈을 빈 설정에 정의하는 대신 빈을 정의하는 애너테이션을 빈의 클래스에 부여하는 방식
 - 애너테이션이 붙은 클래스를 탐석해서 DI 컨테이너에 자동등록
    - 컴포넌트스캔(Component Scan)
- 명시적 설정 X, 애너테이션이 붙었으면 DI 컨테이너가 자동으로 필요로 하는 의존 컴포넌트를 주입
    - 오토와이어링(Auto Wiring)
 
설정방법
 - 빈 클래스에 @Component 애너테이션을 붙여 컴포넌트 스캔이 되도록 만든다.
 - 생성자에 @Autowire 애너테이션을 부여해서 오토와이어링되도록 만든다. 오토이어링을 사용하면 기본적로 주입 대상과 같은 타입의 빈을 DI 컨테이너에서 찾아와 와이어링 대상에 주입하게 된다.

스캔 범위지정
 - 자바기반
    - 컴포넌트 스캔이 활성화되도록 클래스에 @ComponentScan 애너테이션을 부여, 애너테이션의 value 속성이나 basedPackages 속성에 컴포넌트 스캔을 패키지를 지정한다.
 - XML 기반
    - &lt;context:component-scan&gt; 요소의 base-packages 속성에 컴포넌트 스캔할 패키지를 지정한다.

DI 컨테이너에 등록되는 빈의 이름을 기본적으로 클래스명의 첫 글자를 소문제로 바꾼이름과 같다.


### 2.1.4 의존성 주입
 - 설정자 기반 의존성 주입 방식(setter-based dependency injection)
    - 메서드의 인수를 통해서 의존성을 주입하는 방식
        - 자바 기반
            - 자바 기반 설정 방식으로 세터 인젝션을 하는 경우, 마치 프로그램에서 인스턴스를 직접 생성하는 코드처럼 보이기 때문에, 빈을 정의한 설정인지 체감이 안될 수있다.
        - XML 기반
            - &lt;property&gt; 요소내에 name 속성에 주입할 대상의 이름을 지정
        - 애너테이션 기반 설정
            설정자 메서드에 @Autowired 애너테이션을 달아주기만 하면 된다.
 - 생성자 기반 의존성 주입 방식(constructor-based dependency injection)
    - 생성자의 인수를 사용해 의존성을 주입하는 방식
        - 자바기반 설정
            - 생성자에 의존 컴포넌트를 직접 설정
        - XML기반 설정
            - &lt;constructor-arg&gt; 요소에 참조하는 컴퍼넌트를 설정
        - 애너테이션 기반
            생성자에 @Autowired를 부여한다.
            - 스프링 4.3 부터 생성자가 하나만 존재하는 경우 생성자에 @Autowired를 지정하지 않아도 묵시적으로 컨스트럭터 인젝션이 수행된다.
    

 - 필드 기반 의존성 주입 방식(field-based injection)
    - 생성자나 설정자메서드를 쓰지않고 DI 컨테이너이너의 힘을 빌려 의종성을 주입하는 방식
    - 의종성을 주입하고 싶은 필드에 @Autowired 애너테이션을 달아주면된다.

### 2.1.5 오토와이어링
명시적으로 @Bean을 정의하지 않고도 DI 컨테이너에 빈을 자동으로 주입하는 방식이다.

- 타입을 사용한 방식(autowiring by type)
    - 기본적으로 의존성 주입이 반드시 성공한다고 가정
        - 실패시 : org.springframework.beans.factory.NosuchBeanDefinitionException Exceptio 발생
                - 의존성 주입실패
            - 필수조건 완화 방법 required 속성에 false (@Autowired(required = false)) ->  의존성 주입실패시 null 
        - 스프링4부터 Optional 사용가능 
            - @Autowired Optional&lt;XXXXX&gt; xxxx;
    - 같은타입의 빈이 여러 개 정의된 경우
        - @Qualifier 애너테이션을 추가해 빈 이름을 지정 
            - @Qualifier 수싱엑 구현 클래스의 이름이 포함된다거나 구현과 관련된 정보가 포함돼 있다면 빈의 명명 방법이 바람직하다고 볼수 없음.
                - 특정 구현체가 사용될 것으로 의식한 이름을 지정 (DI를 사용하는 의미 X)
                - 결합도가 높아짐
                - 요구사항과 취지에 맞춰 Qualifier역할을하는 애너테이션을 만들어 사용.
        - @Primary를 사용해 우선적으로 선택 될 빈지정
    

- 이름을 사용한 방식(autowiring by name)
    - 필드명이나 프로퍼티명이 일치 할 경우 빈 이름으로 필드 인젝션하는 방법이 존재 
        - @Resource 애너테이션
            - name 생략가능 : 필드이름과 같은 빈이 선택, 프로퍼티와 같은 이름의 빈이 선택 
            -  생성자 인젝션에서는 @Resouce 애너테이션을 사용하지 못한다.

- 컬렉션이나 맵 타입으로 오토와이어링
    - 스프링에서는 인터페이스를 구현한 빈을 컬렉션이나(Collection)이나 맵(Map) 타입에 담에서 가져오는 방법도 제공한다.

### 2.1.6 컴포넌트 스캔
클래스 로더(Class Loader)를 스캔하면서 특정 클래스를 찾아 DI컨테이너에 등록하는 방법
- 기본설정으러 컴포넌트 스캔하기
- 다음과 같은 애너테이션이 붙은 클래스가 탐생 대상이 되고 탐색된 컴포넌트는 DI 컨테이너에 등록
    ```
        - @Component (org.springframework.stereotype.Component)
        - @Controller (org.springframework.stereotype.Controller)
        - @Service (org.springframework.stereotype.Service)
        - @Repository (org.springframework.stereotype.Repository)
        - @Configuration (org.springframework.context.annotation.Configuration)
        - @RestController (org.springframework.web.bind.annotation.RestController)
        - @ControllerAdvice (org.springframework.web.bind.annotation.ControllerAdvice)
        - @ManagedBean (javax.annotation.ManagedBean)
        - @Bamed (javax.inject.Named)
    ```
- 자바 기반에서는 @ComponentScan 애너테이션 사용
- XML 기반에서는 &lt;context:component-scan&gt; 요소 사용
- 클래스 로더위에서 애너테이션이 붙은 클래스를 찾아야하기때문에 탐색 범위가 넓고 시간도 오래걸림
    - 애플리케이션 기동시간을 오래걸리게하는 원인


#### 스캔대상에서 가장 많이 활용되는 4가지
|애너테이션|설명|
|---------|----|
|@Controller|MVC 패턴에서의 C, 즉 컨틀롤러(Controller) 역할을 하는 컴포넌트에 붙이는 애너테이션, 클라이언트에서 오는 요청을 받고, 비지니스 로직의 처리 결과를 응답으로 돌려보내는 기능을 한다. 이때 실제 비지니스로직은 @Service가 붙은 컴포넌트에 처리하도록 위임한다.|
|@Service|비지니스 로직(service)을 처리하는 컴포넌트에 붙이는 애너테이션이다. 컨트롤러에서 받은 입력 데이터를 활용해 비지니스 로직을 실행하는 기능을 한다. 이때 영속적으로 보관해야 하는 데이터가 있따면 @Repository가 붙은 컴포넌트에서 처리하도록 위임한다.|
|@Repository|영속적인 데이터 처리를 수행하는 컴포넌트에 붙이는 애너테이션이다. ORM(Object-Relational Mapping) 관련 라이브러리를 활요앻 데이터의 CRUD를 처리하는 기능을한다.|
|@Component|위의 세 경우에 해당하지 않는 컴포넌트(유틸리티 클래스나 기타 자원 클래스 등)에 붙이는 애너네이션|



추가로 다른 컴포넌트를 더 포함하고 싶다면 필터를 적용하는 압버으로 스캔 범위를 커스터마이징 할 수 있다.
 - 애너테이션을 활용한 필터(ANNOTATION)
 - 할당 가능한 타입을 활용한 필터(ASSIGNABLE_TYPE)
 - 정규 표현식 패턴을 활용한 필터(REGEX)
 - AspectJ 패턴을 활용한 필터(ASPECTJ)

    
### 2.1.7 빈 스코프
 - DI 컨테이너는 빈의 의존관계뿐 아니라, 생존 기간(Bean scope)도 관리
 - DI 컨테이너가 관리하는 빈은 기본적으로 싱글톤으로 만들어짐

#### 스프링 프레임워크에서 사용 가능한 스코프 
|스코프|설명|
|-----|----|
|singleton|DI 컨테이너를 기동 할 때 인스턴스가 하나 만들어지고, 이후부터는 그 인스턴스를 공유하는 방식이다. 기본 스코프이기 때문에 별도로 스코프를 지정하지 않았다면 singleton으로 간주한다.|
|prototype|DI 컨테이너에 빈을 요청할 때마다 새로운 빈 인스턴스가 만들어진다. 멀티 스레드 환경에서 오동작이 발생하지 않아야하는(thread-safe) 빈이라면 singleton 스코프가 아닌 prototype을 사용해야 한다.
|request|HTTP 요청이 들어올 때마다 새로운 빈 인스턴스가 만들어진다. 웹 애플리케이션을 만들때만 사용할 수 있다.
|session|HTTP 세션이 만들어 질때마다 새로운 빈 인스턴스가 만들어진다. 웹 애플리케이션을 만들 때만 사용할 수 있다.
|global session|포틀릿(portlet) 환경에서 글로벌 HTTP 세션이 만들어질 때마다 새로운 빈 인스턴스가 만들어진다. 포틀릿을 사용한 웹 애플리케이션을 만들 때만 사용할 수있다.
|application|서블릿 컨텍스트(Servlet Context)가 만들어질 때마다 빈 인스턴스가 만들어진다. 웹 애플리케이션을 만들때만 사용할 수 있다.
|custom|스코프 이름을 직접 정할 수 있고 정의한 규칙에 따라 빈 인스턴스를 만들 수 있다.

- 스코프 설정
    - 자바 기반 : @Scope 애너테이션에 스코프 이름을 지정 ex) @Scope("prototype")
    - XML 기반 : &lt;bean&gt; 속성에 스코프 지정
- 다른스코프의 빈 주입
    - 스코프는 빈의 생존기간을 의미 => 빈간 스코프가 다르다. == 각 빈의 수명이 다르다.
    - 다른 스코프의 빈주입 사용시
        - @Lookup 애너테이션
            - DI컨테이너는 메서드를 바이트 코드로 형태로 만드는 기능이 있다. DI 컨테이너가 빈을 LookUP 하는 메서드를 만들다음, 그 메서드를 의존할 빈에게 주입하게되는데, 이 기능을 룩업 메서드 인젝션(Lookup Method Injection)이라고 한다. 
            - DI 컨테이너에게 룩업을 대행하게 하고 싶은 메서드에 @Lookup 애너테이션을 붙여줌
            - final, private으로 지정하거나, 매개변수 지정하면 X
            - @Lookup 애너테이션에는 value속성에 빈을 이름을 지정 할 수 있다. 지정하지 않으면 반환값 타입을 보고 룩업 대상 빈을 찾음
        - 스코프트 르락시
            - 기존 빈을 프락시로 감싼후, 이 프락시를 다른 빈에 주입하고, 주입받은 빈에서 이 프락시의 메서드를 호출하면 프락시 내부적으로 DI 컨테이너에서 빈을 룩업하고 룩업된 빈의 메서드를 실행하는 방식 
            - 보통 request 스코프나 session 스코프같이 수명이 짧은 빈을 singleton 스코프와 같은 사애적으로 수명이 긴 빈에 주입할때 사용
            -자바기반 설정시 @Scope 애너테이션을 붙인 다음 proxyMode 속성에 프록시를 만드는방법을 지정하면된다.
            - proxyMode 속성
                - ScopedProxyMode.INTERFACES
                    - JDK의 동적 프락시(java.lang.reflect.Proxy)를 사용해 인터페이스 기반의 프락시를 만든다.
                - ScopedProxyMode.TARGET_CLASS
                    - 스프링 프레임워크에 내장된 GCLIB을 사용해 서브클래스 기반의 프락시를 만든다.
            - XML 기반 설정시  &lt;aop:scoped-proxy&gt;요소를 사용한다.
            - 스프링프레임워크 공식문서에는 request, session, globalSession 스코프에서 스코프트 프락시를 사용하고, prototype 스코프에 대해서는 룩업 메서드 인젝션을 사용하도록 안내하고 있다.
            - prototype 스코프에서 스코프트 프락시를 사용하지 못하는 것은 아니지만, 주입된 필드에서 프락시 안에있는 메서드를 한번더 호출하기때문에 매번 새로운 인스턴스가 만들어질 때마다 가 각 프락시의 메서드가 반복해서 호출됨으로 효율성 측면에서 바람직하지 않다는 점을 감안해야한다.
 - 커스텀 스코프
    - 사용자가 직접 정의한 커스텀 스코프를 만들 수 있다.
    - Scope 인터페이스를 구현
    - CustomScopeConfigurer에 자신이 만든 스코프를 스코프명과 함께 설정

### 2.1.8 빈의 생명 주기
 1. initialzation (빈의 초기화 단계)
 2. activation (빈의 사용 단계)
 3. destruction (빈의 종료단계)

 #### 초기화 단계
 1. 빈 설정 읽기 및 보완
    1) 빈 설정 정보 읽어오기
    2) 빈 설정 정보 보완 처리
 2. 빈 생성 및 의존 관계 해결
    1) Contructor Injection 
    2) Field Injection
    3) Setter Injection
 3. 빈 생성 후 초기화 작업
    1) Bean Post Processor 전처리
    2) Bean Post Processor 초기화 처리
    3) Bean Post Processor 후처리


#### 빈 설정 정보 읽기 및 보완
1. 빈의 생성하는데 필요 한 정보를 수집(자바 Configuration, XML, 애너테이션 커포넌트 스캔중) 정보만 불러오는 단계로 빈을 생서하지 않는다.
2. Bean Factory Post Processor(BFPP)를 사용해 빈의 정보를 보완
    - org.springframework.beans.config.BeanFactoryPostProcessor를 구연한  클래스가 수행
    - Property placehoder는 이시점에 실제 프로퍼티 값으로 치환
#### 빈 생성 및 의존 관계 해결
빈의 정보를 읽고,빈 인스턴스를 성공적으로 생성했다면 의존 관계를 해결하기 위한 의존성 주입을 한다. 실제 수행되는순서는 다음과 같다.
1. 생성자 기반 의존성 주입
2. 필드 기반 의존성주입
3. 세터 기반 의존성 주입

#### 빈 생성 후 초기화 작업(bean initialization callback)
 빈 샌성 후의 초기화 작업(Post Construct)이 수행된다.
 크게 전처리, 초기화 처리, 후처리로 구분된다.
 초기화 부분의 처리 순서
 ```
  - 애너테이션 기반 : @PostConstruct애너테이션이 붙은 메서드
  - InitializingBean 인터페이스를 구현한 경우, afterPropertiesSet 메서드
  - 자바 기반의 설정의 경우 @Bean의 initMethod 속성에 지정한 메서드
  - XML 기반 설정을 사용하는 경우, <bean> 요소의 init-method 속성에 지정한 메서드
 ```
 빈이 생성이된 후에 이뤄지는 초기화는 빈을 생성할 때 해주는 초기화와 큰 차이가 있는데, 그것은 의존성 주입이 끝난 필드 값을 초기화에 활용 할 수있다는 점.
 전처리와 후처리는 BeanPostProcessor(BPP) 인터페이스(org.springframework.beans.factory.config.BeanPostProcessor)의 메서드를 통해 실행된다. 
```
public interface BeanPostProcessr {
    // 전처리
    Object postProcessBeforeInitialization(Object bean, String beanName);
    // 전처리
    Object postProcessAfterInitialization(Object bean, String beanName);
}
```
@PostConstruct 애너테이션을 활동하는 경우 반환 값이 void 이고 메서드 변수는 없어야한다. 같은 처리로는 InitializingBean 인터페이스를 구현하다음 afterPropertiesSet 메서드로 대체 할수도 있다. 또, 어떠한 이유로 @PostContruct 애너테이션이나, intializingBean인터페이스를 구현하지 못하는 경우 @Bean애너테이션에 initMehtod 속성에 Method명을 지정하거나, xml의경우 &lt;bean&gt;에 init-method속성에 메서드명을 지정하면된다.

### 종료 단계
스프링 프레임워크에서는 빈이 파괴되기 전에 전처리(PreDestory) 할 수 있는 방법을 제공한다.
#### 빈이 파괴되기 전에 전처리 수행
빈 생성후 초기화(PostConstruct)와 동작 방식이나 구조는 대칭, 동작은 반대로 
전처리 순서
 ```
  - 애너테이션 기반 : @PreDestroy애너테이션이 붙은 메서드
  - DisposableBean 인터페이스를 구현한 경우, destory 메서드
  - 자바 기반의 설정의 경우 @Bean의 destoryMethod 속성에 지정한 메서드
  - XML 기반 설정을 사용하는 경우, <bean> 요소의 destory-method 속성에 지정한 메서드
 ```
 빈 파괴의 전의 전처리(Pre Destory) 작업은 prototype 스코프의 빈에서는 동작하지 않는다.

 ### DI 컨테이너 종료
 ConfigurableApplicationContext 인터페이스 ApplicationContext 인터페이스를 확장한 서브 인터페이스로, 우리가 사용하는 DI 컨테이너가 바로 ConfigurableApplicationContext 인터페이스의 구현체다. 이 이넡페이스는 close라는 메서드가 있고 이것을 호출하면 DI 컨테이너가 종료된다.  


 
### 2.1.9 빈 설정 분할
DI 컨테이너에서 고나리하는 빈이 많아지면 많아질수록 설정 내용도 많아져서 관리기 어려움
빈 섲렁 법위를 명학히, 가독송도 높이기 위해 목적에 맞게 분할 하는 것이 좋음.
- 자바기반 설정의 분할
    - @Import 애너테이션 활용
- XML 기반 설정
    - &lt;import&gt; 요소를 사용

### 2.1.10 프로파일별 설정 구현
설정 파일을 특정 환경이나 목적에 맞게 선택적으로 사용 할 수있도록 그룹 화 할 수있으며, 이기능을 profile 이라고한다.
#### 프로파일 정의 
 - 자바기반
    - @Profile
    - @Profile({"development", "stating"}), @Profile("production") 과 같이 사용. @Profile("!production") 같이 부정형으로도 표현가능.
 - XML 기반
    - &lt;beans&gt; 요소의 profile 속성 사용

별도로 프로파일이 지정되어있지않으면, 모든 프로파일에서 사용가능


#### 프로파일 선택
프포파일 설정을 정리했다면, 어떤 프로파일을 선택해야할지 알려줘야한다. 이 정보는 자바의 시스템 프로퍼티를 통해 전달할 수있는데, 자바 애플리케이션을 실행할때 명령행 옵션으로 spring.profiles.active라는 프로퍼티 값과 사용할 프로퍼티파일 이름을 지정하면된다.

자바 명령행 옵션으로 프로파일 지정
> -Dspring.profiles.active=production

환경 변수로 프로파일 지정하는 방법
> export SPRING_PROFILES_ACTIVE=production

web.xml 프로파일을 지정하는 방법
> &lt;context-param&gt;<br>
> &nbsp;&nbsp;&nbsp;&nbsp; &lt;param-name&gt;spring.profiles.active&lt;/param-name&gt;<br>
> &nbsp;&nbsp;&nbsp;&nbsp; &lt;param-value&gt;production&lt;/param-value&gt;<br>
> &lt;/context-param&gt;

웹 애플리케이션이라면 xml에 default설정후에 spring.profiles.active로 지정해 기본 프로파일을 덮어쓰면 된다.


### 2.1.11 JSR 330
자바 표준 사양중에서는 DI와 관련해서 JSR 330(Dependency Injection for Java)라는 것이 있다. 이 사양에서는 DI기능을 사용하기 위한 API(주로 애너테이션)가 정의되어있다.
JSR 330 관련 클래스가 클래스패스 상에 있다면 DI 컨테이너가 컴포넌트 스캔을 할때 JSR 330에서 정의한 애너테이션을 붙인 빈도 관리 대상으로 포함시킨다. 때문에 DI 관련 섲렁 자체는 스프링 프레임워크에 의존하지 않고 자바 표준 클래스만으로도 표현 할 수 있다.

JSR 330과 스프링 프레임워크 애너테이션 비교

|스프링 프레임워크|JSR 330    |설명                                                                                   |
|---------------|-----------|---------------------------------------------------------------------------------------|
|@Autowired     |@Inject    |@Inject에는 필수 체크 속성(requied)이 없다.|
|@Component     |@Named     |스프링 프레임워크에서는 singleton이 기본 스코프지만 JSR 330에서는 prototype이 기본 스코프다.|
|@Qualifier     |@Named     |@Named가 @Compoent와 @Qualifier의 기능을 겸한다.|
|@Scope         |@Scope     |JSR 330의 @Scope는 커스텀 스코프를 정의하기 위한 메타 애너테이션이기 때문에 @Scope 애너테이션으로 스코프를  지정하지 못한다. 기본적으로 구현되어 제공되는 스코프는 @Singleton이 유일하다. |

스프링 프레임워크와 JSR330에서 사용하는 기본 스코프가 서로 다르다.
스프링 프레임워크에서 컴포넌트 스캔을 할때 JSR 330 사양에 맞춰 기본 스코프를 prototype 스코프로 바꿔주려면 다음과같이 설정한다.
@Component애너테이션에 scopeResolver=jsr330ScopeMetadataResolver.class를 추가한다.
단 기본 스코프가 바뀌는 것은 의도치 않은  오동작을 유발할 수 있으으로 굳이 자바 표준 API를 고집하는게 아니라면 스프링 애너테이션으로 통일하는것이 존다.







## 2.2. AOP
공통적이고 반복적으로 필요로 하는 처리 내용을 횡단관심사(Cross-Cutting Concern)
대표적인 횡단관심사
 - 보안
 - 로깅
 - 트랜잭션 관리
 - 모니터링
 - 캐시 처리
 - 예외처리
 
 
 횡단 관심사에 해당하는 부분을 분리해서 한곳으로 모으는 것을 <B>횡단 관심사의 분리(Separation Of Cross-Cutting Concerns)</B> 이를 실천하는 방법을 관점 지향 프로그래밍이라고한다.

### 2.2.1 AOP의 개요
관점 지향 프로그래밍을 의미하는 약어(Aspoect Oriented Programming)
AOP를 활용하면 DI를 활용하면 공통적인 기능을 외부에서 집어넣을 수 있다, 달리 말하면 애플리케이션 코드에서 공통적인 기능을 분리해 내는 것

#### AOP의 개념
- 애스펙트(Aspect)
    - AOP의 단위가 되는 횡단 관심사에 해당한다. AOP의 예로 자주 언급되는 '로그를 출력한다.', '예외를 처리한다.', '트랜잭션을 관리한다'와 같은 관심사가 애스펙트다.
- 조인 포인트
    - 횡단 관심사가 실행될 지점이나 시점(메서드 실행이나 예외 발생 등)을 말한다. 조인 포인트는 AOP를 구현한 라이브러리에 따라 사양이 다를 수 있는데 참고로 스프링 프레임워크의 AOP에서는 메서드 단위로 조인 포인트를 잡는다.
- 어드바이스(Advice)
    - 특정 조인 포인트에서 실행되는 코드로, 횡단 관심사를 실제로 구현해서 처리하는 부분이다. 어드바이스에서는 Around, Before, After등 여러 유형이 있다.
- 포인트 컷(Point Cut)
    - 수많은 조인 포인트 중엣 실제로 어드바이스를 적용할 곳을 선별하기 위한 표현식(expression)을 말한다. 일종의 조인 포인트 그룹이라고 볼수 도 있다. 스프링 AOP에서는 포인트컷을 정의할 때 XML 기반 설정 방식으로 빈 정의 파일을 만들거나, 애너테이션 기반 설정방식으로 소스코드에 주석 형태로 정의한다.
- 위빙(Weaving)
    - 애플리케이션 코드의 적절한 지점에 애스펙트를 적용하는 것을 말한다. AOP 구현 라이브러리에 따라 위빙하는 시점이 다를 수있는데, 컴파일 시점이나 클래스 로딩 시점, 실행 시점 등의 다양한 위밍 시점이 있다. 참고로 스프링 AOP는 기본적으로 실행 지점에 위빙한다.
- 타깃(Target)
    - AOP 처리에 의해 처리 흐름에 변화가 생긴 객체를 말한다. 어드바이스드 오브젝트(Advised Obejct)라고도 한다.

#### 스프링 프레임워크에서 지원하는 어드바이스 유형

|어드바이스|설명|
|---------|----|
|Before|조인 포인트 전에 실행된다. 예외가 발생하는 경우메나 제외하고 항상 실행된다.|
|After Returning|조인 포인트가 정상적으로 종료한 후에 실행된다. 예외가 발생하면 실해되지 않는다.|
|After Throwing|조인 포인트에서 예외가 발생했을 때 실행된다. 예외가 발생하지 않고 정상적으로 종료하면 실행되지 않는다.|
|After|조인 포인트에서 처리가 완료된 후 실행된다. 예외 발생이나 정상종료 여부와 상관없이 항상 실행된다.
|Around|조인 포인트 전후에 실행된다.|

### 2.2.2 스프링 AOP
스프링 프레임워크 안에는 AOP를 지원하는 모듈로 스프링 AOP가 포함되어있다. 스프링 aOP에는 DI컨테이너에서 관리하는 빈들을 타깃(target)으로 어드바이스를 적용하는 기능이 있는데, 조인 포인트에 어드바이스를 적용하는 방버은 프락시 객체를 만들어서 대체하는 방법을 쓴다.
스프링 AOP에는 AspectJ라는 AOP 프레임워크가 포함되어있다. AspectJ는 애스펙트와 어드바이스를 정의하기 위한 애너테이션이나 포인트컷 표현 언어(Pointcut Expression Language), 위빙메커니즘을 제공하는 역할을 한다.

애스펙트구현
```
    @Aspect
    @Commponent
    public class XxxAspect{
        @Before(excution(* *..*ServiceImpl.*(..))")
        public void xxxx(JoinPoint jp){
            System.out.println("Method Start " + jp.getSingature());
        }
    }
```

애스펙트 동작설정
 - 자바기반 : @EnableAspectAutoProxy 애너테이션을 사용한다.
 - XML 기반 : &lt;aop:aspectj-autoproxy&gt; 요소를 사용한다.

### 2.2.3. 자바기반 설정 방식에서의 어드바이스의 정의

#### Before
@Before 애너테이션을 붙인 다음 포인트컷 표현식을 추가하면된다. 전달되는 인수를 통해 실행 중인 메서드의 정부를 구할 수 있다.

#### After Retruning
@AfterReturing 애너테이션을 붙인 다음 포인트컷 표현식을 추가하면된다, 실행시점은 대상메서드가 정상적으로 종료한 후다. 정상적으로 종료한 메서드의 반환값을 구할 수 다. @AfterRetuning 애너테이션의 returing 속성에 반환값을 받을 매개변수의 이름을 지정하면 된다.

#### After Throwing
@AfterThrowing 애너테이션을 붙인 다음 포인트컷 표현식을 추가하면된다, 예외가 발생해서 비정상적으로 종료될때 실행된다. 발생한 예외가 무엇인지 알고 싶다면 @After Throwing 애너테이션의 throwing 속성에 예외를 받을 매개변수의 이름을 지정하면 된다. After Throwing 어드바이스에서는 예외를 다시 던져 전파(propagation)하는 것이 가능하다. After Thowing 어드바이스는 예외를 외부로 던져지는 것을 막지 못한다.

#### After
@After 애너테이션을 붙인 다음 포인트컷 표현식을 추가하면된다. 메서드가 정상 종료 여부나 예외 발생 여부와 상관없이 무조건 실행된다.

#### Around
@Around 애너테이션을 붙인 다음 포인트컷 표현식을 추가하면된다. 메서드의 실행 전과 후의 처리 물론, 포인트컷이 적용된 대상 메서드 자체도 실행할수있다.

### 2.2.4. XML기반 설정 방식에서의 어드바이스의 정의
애너테이션을 사용하지 안는 애스펙트 클래스를 만든다음, XML 파일에서 AOP를 설정하면된다.

### 2.2.5 포인트컷 표현식
표현식을 이용한 조인 포인트 선택 기능은 AspectJ가 제공하며, 스프링 AOP는 AspectJ가 제공하는 포인텃 표현식 상당수를 지원한다.
포인트컷은 일치시킬 패턴에 따라 지시자(designator)의 형식이 달라진다.

#### 메서드명으로 조인포인트 선택

```
    execution[지시자](*[반환값] com.example.domain[패키지].*Service[클래스타입].find[메서드]*(..[인수])) 
```
포인트컷에서 사용되는 와일드 카드

|와일드카드|설명|
|---------|----|
|*|기본적으로 임의의 문자열을 의미한다. 패키지를 펴현할 때는 의의 패키지 1개 계층을 의미한다. 메서드의 매개변수를 표현할 때는 임의의 변수 1개를 의미한다.|
|..| 패키지를 표현할때는 임의의 패키지 0개 이상 계층을 의미한다. 메서드의 매개변수를 표현할 때는 임의의 인수 0개 이상을 의미한다.
|+| 클래스 뒤에 붙여 쓰며, 해당 클래스와 해당 클래스의 서브클래스, 혹은 구현 클래스 모두를 의미한다.

#### 타입으로 조인포인트 선택
within 지시자를 사용해 타입정보로 조인 포인트를 선택 할 수있다. 클래스명의 패턴만 사용한다.

```
    within(com.example.service..*)
```

#### 그 밖의 기타 바업으로 조인 포인트 선택
- bean
- annotation
- within

#### 네임드 포인트컷 활용
포인트컷에 이름을 붙여두면 나중에 그 임음으로 포인트컷을 재사용할수있다. 이렇게 이름이 붙여진 포인트컷을 네임드 포인트(Named Pointcut) 컷이라고한다. 
네인드 포인트컷은 @Pointcut 애너테이션으로 정의할 수있는데, 이 애너테이션이 붙은 메서드 이름을 포인트컷의 이름이 된다. 메서드의 반환값은 void로 한다.

#### 어드바이스 대상 객체와 인수정보 가저오기
JoinPoint 타입의 인수를 활용하면 어드바이스 대상 객체나 메서드를 호출할 때 전달된 인수의 정보를 가져올 수있다. getTarget메서드를 이요하면 프락시가 입혀지기 전의 원본 대상 객체 정보를, getThis 메서드를 이용하면 프락시를 getArgs 메서드를 이용하면 인수정보를 가져올 수 있다. 단 JoinPoint 인터페이스의 메서드는 반환값이 Object타입이기때문에 실제로 사용하기전에는 형변환을 해야한다. 그래서 이 과정에서 타입이 호환되지 않으면 ClassCastException이 발생할 수있다. 이럴때는 포인트컷 지시자인 target이나 this, args 등을 활용해 대상 객체나 인수를 어드바이스 메서드에 파라메터로 바로 바인딩하면 된다. 이 방법을 사용하면 getTarget메서드나 getArgs 메서드의 결과를 형변활 필요가 없어지고 타입이 맞지 않는경우는 자연스럽게 어드바이스 대상에서 제외된다. 결국 타입 불일치로 인한 오동작을 미연에 방지할 수있는데 이런 상황을 'type safe(타입 안전)' 이라고한다.


#### 스프링 프로젝트에서 활용되는 AOP 기능
- 트랜잭션관리 
    - 트랜젝이 필요한 메서드에 @Transactional 애너테이션을 지정하면 복잡한 트랜잭션 관리를 스프링 프레임워크가 대신 해준다.
- 인가 
    - 스프링 시큐리티에서 제공하는 인가 기능을 AOP 형태로 적용할 수있다. ex) @PreAuthrize("hasRole('ADMIN')") 
- 캐싱
    - 메서드에 @cacheable 애너테이션을 지정하면 메서드의 매개변수등을 키로 사용해 실행결과를 캐시로 관리 할 수있다.
- 비동기처리
    - 비동기 처리를 하고싶은 메서드에 @Async 애너테이션을 붙여주고 반환값으로 ComletableFuture 타입의 값이나 DeferrendResult 타입으로 반환가게 만들면 해당 메서드는 AOP 방식으로 별도의 스레드에서 실행될 수있다. 스레득 관리는 스프링 프레임워크가 처리해준다.
- 재처리
    - 메서드에 @Retryable 애너테이션을 붙여두면 해당 메서드가 정상적으로 처리되 않은 경우, 원하는 조건을 만족 할때까지 재처리하게 된다.








## 2.3. 데이터 바인딩과 형 변환
 - Data Binding (데이터 바인딩) : 자바 객체의 프로퍼티에 외부에서 입력된 값을 설정 하는 과정 
 - Bean(빈) : DI 컨테이너에서 관리되면 애플리케이션을 구성하는 컴포넌트 역할의 자바 객체 
 - JavaBeans(자바빈즈) : 데이터 바인딩이나 프로퍼티 관점에서 다뤄지는 객체

 ### 2.3.1 String 값에 대한 데이터 바인딩
 데이터를 바인딩 하는 기능은 DataBinder 클래스가 제공하는데, HTTP를 사용하는 환경에서는 서블릿 API에 맞게 커스터마이징된 ServletRequestDataBinder를 사용하면된다.

 ### 2.3.2 스프링의 형 변환
 데이터 바인딩을 할때는 자바빈즈 프로퍼티 타입에 맞게 입력된 문자열 값을 형 변환 해야한다.

 - 빈을 조작하거나 래핑
    - 빈의 프로퍼티를 읽거나 원래 있던 빈을 조작하고 래핑하는 방법제공
 - 형변환
    - Converter인터페이스등의 구현 클래스를 활용해 형변환, String 외타입에 대해서도 형 변환가능
 - 필드 값의 표현 형식 포매팅
    - Formatter 인터페이스를등의 구현 클래스를 활용해 형변환, Local을 고려한 국제화 기능 제공


### 2.3.3 PropertyEditor 활용
스프링 프레임워크는 다양한 형태의 propertyEditor 구현을 제공, 프리미티브 타입이나, 레퍼, URL과 같은 타입도 무리없이 설정 제공

### 2.3.4 Conversion 서비스 활용
형 변환(Type Conversion)을 하거나 필드 값의 표현 형식을 포매팅하는 기능을 ConversionService 인터페이스를 통해 제공 DefaultFormatingConversionService를 사용하는게 일반적, ConversionService를 이용하려면 먼저 DI 커넽이너에 등록해야하고, 빈의 ID 가 'conversionService'가 괴도록 정의해야한다.

### 2.3.5 포매팅용 애너테이션 활용
DefaultFormmatingCoinversionService를 활용하면 형변환이 필요한 곳에 다음과같은 애너테이션을 붙인다음, 형변환시 적용할 포맷형식을 정할 수 있다.
- @DateTimeFormat 애너테이션 (ex @DateTimeForamt(pattern ="yyyy/MM/dd")) 
- @NumberFormat 애너테이션

### 2.3.6 형 변환 방식의 커스터마이징 (P. 100)
직접형 변환 기능을 만들어 사용 가능 Coverter를 구현해  ConversionService에 추가해야함.

### 2.3.7 필드 포매팅 방식의 커스터마이징(P. 103)
필드 값을 다양한 형식으로 표현하는 포매팅 기능을 제공

## 2.4 프로퍼티관리
스프링 프레임워크는 각종 설정 정보를 효율적으로 다룰 수 있는 관리 메커니즘 제공

### 2.4.1 빈정의 시 프로퍼티 활용
각종 설정 값을 소스에 직접 하드코딩하는 대신 @Value 애너테이션을 활용하면 별도로 분리된 프로퍼티 값을 소스에 주입해 사용 할 수 있다.
자바 기반 설정을 사용한다면 @PropertySource 애너테이션으로 위치 지정 가능하다. 프로퍼티의 키 값으로 표현되는 '${프로퍼티 키}' 부분은 나중에 다른 갑스로 치환될 자리라는 의미로 플레이스 홀더(placeholder)라고한다. 만양 같은이름의 프로퍼티가 JVM이나 환경변수, 프로퍼티 파일에 중복되 설정되있으면 우선순위는 다음과같다.
1. JVM 시스템 프로퍼티
2. 환경변수
3. 프로퍼티 파일

플레이스홀더이는 기본값을 줄 수 있다. '${프로퍼티티 키 : 기본값}'

### 2.4.2 빈구현 과정에서 프로퍼티 응용
특정 값을 하드코딩하고 싶지 않을 때 활용하면된다.


## 2.5 스프링 표현 언어
SpEL : Spring Expression Language 스프링 프레임워크가 제공하는 표현 언어

### 2.5.1 SpEL 설정
Spring-expression을 의존라이브러리로 추가한다. 

### 2.5.2 SpEL API의 개요
SpEL과 관련된 인터페이스 가운데 사용자가 직접 사용할 마한 주요 인터페이스로는 ExpressionParser와 Expression두가지가 있다.
 - ExpressionParser 인터페이스는 문자열 형태인 표혀신을 분석 Expression 객체를 생성하기 위한 메서드를 제공
 - Expression 인터페이스는 분석된 표현식의 내용을 실행하기 위한 메서드 제공

### 2.5.3 빈 정의 시 SpEL 활용
SpEL은 XML 기반이나 애터네이션 기반 빈 설정방식에도 사용할 수 있고 '#{표현식}'과 같은 형태로 표기한다.

### 2.5.4 SpEL에서 쓸 수있는 표현식 유형
#### 리터럴 값
- 문자열이나 수치 갑, 부울 값, 날짜나 시간과 같은 리터럴 값의 표현을 지원한다. 문자의 리터럴 값을 표현하는 경우에는 작은따옴표를 사용해 '문자열'과같은 형식으로 표현한다.
#### 객체생성
- 프리미티브 타입을 제외하면 FQCN을 사용한다.
    - List를 생성하는 경우 : "{값(, ...)}"
        - 예) "{1,2,3}"
    - Map을 생성하는 경우 : "{키:값(, ...)}"
        - 예) "{name : '홍길동', joinYear: 2017}"
    - 배열을 생성하는 경우 : "new 타입[인덱스]" 또는 "new 타입[]{값(,...)}" 형식
        - 예) "new int[]{1,2,3}"
    - 임의의 객체를 생성하는 경우: "new 타입(....)"
        - 예) "new com.exampleFileUploadHelper()"
#### 프로퍼티 참조
 - 자바빈즈의 프로퍼티에 접근하기 위한 표현을 지원한다. 기본적으로 프로퍼티의 이름을 명시하면 되는데, 중첩된 객체의 프로퍼티나, 컬랙션이나 배열 안에 포함된 요소, 혹은 Map 안에 담긴 요소등에서도 접근 할 수 있다.
#### 메서드 호출
 - 자바객체의 메서드 호출을 위한 표현을 지원한다.
#### 타입
 - 타입을 의미하는 표현인 T(타입의 FQCN)을 지원한다. 이 표현은 상수를 나타내거나 static 메서드를 호출할 때 사용된다.
#### 변수 참조
 - 변수의 개념이 있어서 변수에 접근하기 위한 표현인 '#변수명'을 지원한다. 스프링 시큐리티에서 메서드의 인가 기능등에 활용 될 수있다.
#### 빈 참조
 - DI 컨테이너에서의 빈을 참조하기 위한 표현으로 '@빈 이름'을 지원한다. 스프링 MVC가 제공하는 JSP 태그 라이브러리 중에서 &lt;spring:eval&gt; 요소의 expressionm 속성에서 사용할 수 있다.
#### 연산자
 - 표준 관계 연산자와, 삼항연산자, Elvis연산자 산술연산자, 인스턴스를 비교하는 intanceof, 정규 표현식으로 일치 여부를 화긴하는 matches를 지원한다.
#### 탬플릿
 - 텍스트 본문 안에 표현식을 집어 넣어 텍스트에 표현식의 결과를 표시하게 만드는 템플릿 기능을 지원한다. '#{표현식}'과 같은 방식으로 기재하면된다.
#### 컬렉션
 - 컬렉션안에서 조건과 일치하는 요소를 추출하기 위한 표현(Collection Selection)이나 컬렉션 안에 있는 요소가 가진 특정 프로퍼티의 갑슬 추출하기 위한 표현(Collection Projection)을 지원한다.

## 2.6 리소스 추상화 
스프링 프레임워크의 리소스 추상화 기능을 활용하면 구체적인 위치 정보를 직접 다루지 않더라도 리소스에 접근 할 수있다.
 
### 2.6.1 Resource 인터페이스와 구현 클래스 (Page. 116)
Resource 인터페이스와 WriteableResource 인터페이스 제공한다. 구현 클래스는 직접 골라 사용해도 되지만, 스프링 프레임워크는 위치를 보고 적절한 구현클래스를 선택할 수 있는 기능을 제공한다.(ResourceLoader Interface)

### 2.6.2 ResourceLoader 인터페이스와 구현 클래스 (Page. 117)
Resource객체를 생성하는 과정을 추상화하기 위해 ResourceLoader 인터페이스를 제공한다. DI컨테이너를 구성하는 다양한 ApplicationContext 인터페이스의 구현 클래스는 모두 이 인터페이스를 구현하고 있다.

### 2.6.3 Resource 인터페이스를 활용한 리소스 접근 (Page. 118)
Resource 인터페이스를 활용한 다양한 접근 방법소개. (URL, File 등) 스프링 프레임워크상의 프로퍼티 기능을 활용해 Resource 객체 주입 가능.

### 2.6.4 XML 파일에서 리소스 지정
XML 기반 설정 방식에서 빈을 정의할 때 프로퍼티 파일을 참조하거나 또 다른 빈 정의 파일을 포함하는 과정에서 Resource 인터페이스의 구현 클래스가 사용된다.

## 2.7 메시지 관리 

### 2.7.1 MessageSource 인터페이스와 구현 클래스(Page. 122)
스프링프레임워크는 외부에서 메시지 정보를 가져오는 기능을 제공하는데, 그 핵심이 MessageSource 인터페이스 이다. MessageSource는 메시지 정보의 출처를 추상화하기 위한 것으로, 어딘가에 있을 메시지 정보를 가져오기 위해 getMessage 메서드를 제공한다.

### 2.7.2 MessageSource 사용
스프링프에임워크는 다양한 형태의 MessageSource 구현 클래스를 제공한다.
#### MessageSource의 Bean 정의
ResouceBundleMessageSource를 사용한 예
```
    @bean 
    public MessageSource messageSource(){
        ResouceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        //  클래스패스상에 있는 프로퍼티 파일읠 확장자를 제외하고 지정
        messageSource.setBasenames("messages");
        return messageSource;
    }
```
#### 메시지 정의
```
    # 님, 안녕하세요.
    welcome.message=\ub2d8, \uc548\ub155\ud558\uc138\uc694.
```
영문이나, 숫자와 같이 ASCII 코드로 표현할수 없는 한글과 같은 문자를 메시지에 사용하고 싶다면 Unicode를 사용하되, 아스키 코드에서 사용 가능한 문자료 표현해야한다.
JDK에는 native2ascii라는 툴이 있다.
 
#### MessageSource의 API 활용
DI 컨테이너에 등록된 MessageSource를 주입 받은 다음 getMessage 메서드를 호출하면된다.

#### MessageSourceResolvable의 활용
메시지에 포함할 인수 값조차 프로퍼티 파일에서 읽어오고 싶은경우에는 DefaultMessageSourceResolvable 클래스를 활용한다.



### 2.7.3 프로퍼티 파일을 UTF-8로 인코딩
프로퍼티 파일에 메시지를 저장할 때 유니코드를 아스키 문자열로 변환한 문자열을 사용했는데, 프로퍼티 파일이 ISO-8859-1로 인코딩되는 것이 사양에 규정돼 있기 때문이다.
```
    ResouceBundleMessageSource messageSource = new ResourceBundleMessageSource();
    messageSource.setBasenames("messages");
    messageSource.setDefaultEncoding("UTF-8");
    return messageSource;
```

### 2.7.4 다국어 지원하기(Page. 127)
MessageResouce의 구현 클래스는 국가별로 메시지의 언어를 다르게 적용할 수있는데 국제화 기능을 갖추고있다. 언어마다 프로퍼티를 따로만들고 Java SE의 resouceBundle 사양에 지원하는 로캘을 선택하면 된다.
