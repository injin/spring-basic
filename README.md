# spring-basic
[인프런] 스프링 핵심 원리 - 기본편  
[목차 바로가기](./INDEX.md)

## 1. 객체 지향 설계와 스프링

- 좋은 객체 지향 설계의 5가지 원칙 (SOLID)

    
    📌 SRP : 단일 책임 원칙(single responsibility principle)
    OCP : 개방-폐쇄 원칙 (Open/closed principle)
    LSP : 리스코프 치환 원칙 (Liskov substitution principle)
    ISP : 인터페이스 분리 원칙 (Interface segregation principle)
    DIP : 의존관계 역전 원칙 (Dependency inversion principle)
    

- 객체지향의 핵심은 다형성. 즉, 역할과 구현을 분리
- 다형성 만으로는 구현 객체를 변경할 때 클라이언트 코드도 함께 변경된다
- **다형성 만으로는 OCP, DIP를 지킬 수 없다**

---

## 3. 스프링 핵심 원리 이해2 - 객체 지향 원리 적용

- 관심사의 분리. 클라이언트는 추상(인터페이스)에만 의존
- 관심사의 분리 : 객체를 생성하고 연결하는 역할(AppConfig)과 실행하는 역할(OrderServiceImpl)이 명확히 분리
- 구성영역 (AppConfig) vs 사용영역 (OrderServiceImpl)

### 3.8 IOC, DI, 그리고 컨테이너

- 의존관계는 정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계 둘을 분리해서 생각해야 한다.
- **정적인 클래스 의존 관계**는 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. 즉, 애플리케이션을 실행하지 않아도 분석할 수 있다.
- **동적인 객체 인스턴스 의존 관계**는 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계다
- 애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서  
  클라이언트와 서버의 실제 의존관계가 연결 되는 것을 **의존관계 주입**이라 한다.
- AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을
  IoC 컨테이너 또는 **DI 컨테이너**라 한다.

### 3.9 Spring으로 전환하기

- 전환 전 : 개발자가 직접 객체를 생성하고 DI

```java
AppConfig appConfig = new AppConfig();
MemberService memberService = appConfig.memberService();
```

- 전환 후 : 스프링 컨테이너 사용

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
```

---

## 4. 스프링 컨테이너와 스프링 빈

### 4.1 스프링 컨테이너 생성

- `ApplicationContext` 를 스프링 컨테이너라 한다.
- `ApplicationContext` 는 인터페이스이다.
- `AnnotationConfigApplicationContext(AppConfig.class)` 는 구현체이다.  
  구현체의 종류는 다양 (GenericXmlApplicationContext 등)
- 스프링 컨테이너는 설정 정보를 참고해서 의존관계를 주입(DI)한다
- 빈 이름은 메소드명을 기본으로 하여 만들어 진다.
- 스프링 컨테이너는 크게 두개의 라이프 사이클로 생성된다.  
  스프링 빈 등록 ⇒ 의존관계 설정

### 4.2 컨테이너에 등록된 모든 빈 조회

- Role `ROLE_APPLICATION`: 직접 등록한 애플리케이션 빈
- Role `ROLE_INFRASTRUCTURE`: 스프링이 내부에서 사용하는 빈
- 이름, 타입으로 조회  
  같은 타입의 빈이 두개 이상 있으면 빈 이름을 지정하면 된다.

### 4.5 스프링 빈 조회 - 상속관계

- 부모 타입으로 조회하면, 자식 타입도 함께 조회한다
- 그래서 모든 자바 객체의 최고 부모인 Object 타입으로 조회하면, 모든 스프링 빈을 조회한다.
- (예제) 부모 타입으로 모두 조회하기

    ```java
    @Test
    @DisplayName("부모 타입으로 모두 조회하기 - Object")
    void findAllBeanByObjectType() {
        Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value=" +
                    beansOfType.get(key));
        }
    }
    ```


### 4.6 BeanFactory와 ApplicationContext

- `BeanFactory`는 스프링 컨테이너의 최상위 인터페이스다
- `ApplicationContext`는 BeanFactory의 기능을 상속받는다
- ApplicationContext는 빈 관리기능 + 편리한 부가 기능을 제공한다
- 스프링 컨테이너는 다양한 형식의 설정정보를 받아드릴 수 있게 유연하게 설계되었다. (자바코드, XML 등)

### 4.8 스프링 빈 설정 메타정보 - BeanDefinition

- 스프링은 어떻게 이런 다양한 설정 형식을 지원하는 것일까? 그 중심에는 `BeanDefinition` 이라는 추상화가 있다. 역할과 구현을 개념적으로 나눈 것
- 스프링 컨테이너는 자바 코드인지, XML인지 몰라도 된다. 오직 BeanDefinition만 알면 됨
- BeanDefinition 을 빈 설정 메타정보라 함
- 내부적으로 BeanDefinitionReader가 설정파일을 읽어서 BeanDefinition을 생성

---

## 5. 싱글톤 컨테이너

- 요청할 때마다 객체를 생성하면 메모리 낭비 ⇒ JVM당 하나의 객체만 생성하는 싱글톤 패턴 적용
- **스프링 컨테이너는 객체 인스턴스를 싱글톤으로 관리**
- **(주의) 싱글톤 빈은 무상태(stateless)로 설계해야 한다! (여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문)**  
  ⇒ 지역변수, 파라미터, ThreadLocal 등을 사용해야 함
- AppConfig의 자바 코드를 보면 분명히 각각 2번 new MemoryMemberRepository 호출해서 다른
  인스턴스가 생성되어야 하는데? 한 번만 호출된다. 비밀은?  
  ⇒ `@Configuration` 을 붙이면 바이트코드를 조작하는 CGLIB 기술을 사용해서 싱글톤을 보장  
  ⇒ 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 AppConfig 클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록한 것

---

## 6. 컴포넌트 스캔

- 스프링 빈을 등록할 때는 자바 코드의 @Bean이나 XML의 <bean> 등을 통해서 설정 정보에
  직접 등록할 스프링 빈을 나열 ⇒ 설정정보 길어짐
- 컴포넌트 스캔을 사용하려면 먼저 `@ComponentScan` 을 설정 정보에 붙여주면 된다.  
  기존의 AppConfig와는 다르게 @Bean으로 등록한 클래스가 하나도 없다.
- 컴포넌트 스캔은 이름 그대로 `@Component` 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다. @Component 를 붙여주자.

### 6.1 컴포넌트 스캔과 의존관계 자동주입 시작하기

- 생성자에 `@Autowired` 를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입한다
- 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입한다  
  `getBean(MemberRepository.class)` 와 동일하다고 이해하면 된다.


    📌 (빈 생성 방식 2가지)
       @Componet 는 컴포넌트 스캔방식, @Bean은 설정 정보에 직접 등록 방식으로 빈 생성



### 6.3 탐색위치와 기본 스캔 대상

- `@Component` : 컴포넌트 스캔에서 사용
- `@Controlller` : 스프링 MVC 컨트롤러에서 사용
- `@Service` : 스프링 비즈니스 로직에서 사용
- `@Repository` : 스프링 데이터 접근 계층에서 사용
- `@Configuration` : 스프링 설정 정보에서 사용

### 6.4 필터

- 컴포넌트 스캔에 포함 혹은 제외할 대상 지정
- (예시)

    ```java
    @ComponentScan(
    includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
    )
    ```

- Filter Type은 5가지 옵션이 있다 : 애노테이션(기본), 타입, AspectJ 패턴, 정규 표현식, 커스텀

### 6.5 중복 등록과 충돌

- 수동 빈 등록과 자동 빈 등록이 충돌 나면 수동이 우선권을 가져 override 된다 (단, 부트는 기본이 오류 발생)

---

## 7. 의존관계 자동 주입

### 7.1 다양한 의존 관계 주입 방법

1. **생성자 주입**  
   불변, 필수 의존관계에 사용. 생성자 호출시점에 딱 1번만 호출되는 것이 보장  
   생성자가 딱 1개만 있으면 @Autowired를 생략해도 자동 주입 된다  
2. **수정자 주입 (setter 주입)**  
   선택, 변경 가능성이 있는 의존관계에 사용  
3. **필드주입**  
   DI 프레임워크가 없으면 아무것도 할 수 없다. 사용하지 말자  
4. **일반 메서드 주입**  


    📌 (생성자 주입 + 롬복 `@RequiredArgsConstructor`) 을 사용하자. 불변을 유지할 수 있고, 누락을 방지하고, final 키워드를 사용해 오류를 컴파일 시점에 막아준다.


### 7.2 옵션 처리

자동 주입 대상을 옵션으로 처리하는 방법은 다음과 같다.

- `@Autowired(required=false)` : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 안됨
- `org.springframework.lang.@Nullable` : 자동 주입할 대상이 없으면 null이 입력된다.
- `Optional<>` : 자동 주입할 대상이 없으면 Optional.empty 가 입력된다.

### 7.5 @Autowired 필드 명, @Qualifier, @Primary

- 같은 타입의 빈이 두 개 이상인데 의존관계 주입을 하려고 하면 문제 발생
- 해결방법
    - `@Autowired` 필드 명 매칭 : 파라미터 명으로 빈 이름을 추가 매칭한다.
    - `@Qualifier` : 우선순위 가장 높다
    - `@Primary` 사용 : 코드가 깔끔

### 7.8 조회한 빈이 모두 필요할 때, List, Map

### 7.9 자동, 수동의 올바른 실무 운영 기준

- 업무로직은 자동 등록,
  기술 지원 로직은 수동 등록
- 다형성을 적극 활용하는 비즈니스 로직은 수동 등록을 고민해보자

---

## 8. 빈 생명주기 콜백

- 개발자는 (객체 생성 ⇒ 의존관계 주입) 이 완료된 시점을 어떻게 알 수 있을까?
- 스프링 빈의 라이프사이클  
  스프링 컨테이너 생성 ⇒ 스프링 빈 생성 ⇒ 의존관계 주입 ⇒ **초기화 콜백** ⇒ 사용 ⇒ **소멸전 콜백** ⇒ 스프링 종료
- 생명주기 콜백 지원방법 3가지
    - **인터페이스(InitializingBean, DisposableBean)**  
      : 스프링 전용 인터페이스에 의존. 거의 사용되지 않음  
    - **설정 정보에 초기화 메서드, 종료 메서드 지정**  
      : 외부 라이브러리에도 적용 가능  
      : 추론 기능이 있어 close, shutdown 이름의 메소드를 자동으로 호출해 줌 (inferred)  
    - **@PostConstruct, @PreDestroy 애노테이션 지원**  
      : 스프링에서 가장 권장하는 방법  

---

## 9. 빈 스코프

- 스코프란 빈이 존재할 수 있는 범위
- 싱글톤 vs 프로토타입 vs 웹스코프  
  **스코프의 핵심은 어느 시간만큼 컨테이너가 관리해주냐의 차이인 듯**  
- **싱글톤**: 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프
- **프로토타입**: 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프  
  미리 빈을 만들지 않고 클라이언트 요청이 오면 항상 새로운 빈을 생성해서 반환  
- 웹 관련 스코프  
  **request**: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프  
  **session**: 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프  
  **application**: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프  

### 9.2 프로토타입 스코프

- 클래스에 `@Scope("prototype")` 을 명시
- 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입 그리고 초기화까지만 관여. 종료 메서드가 호출되지 않음 ⇒ 클라이언트가 해야 함
- **싱글톤 빈과 사용 시 의존관계 주입받을 시 생성된 빈이 계속 유지되는 문제가 있다.  
  ⇒ 이를 해결하기 위해 ObjectProvider 또는 JSR330 Provider로 문제 해결  
  ⇒ 즉, 스콮이 다를 때 빈이 필요한 상황에서 Provider 사용**  
- Provider는 컨테이너에 빈을 대신 요청해서 반환해 주는 대리자 정도의 역할. **DL (Dependency Lookup)**

### 9.4 request 스코프 예제 만들기


    📌 스프링 부트는 웹 라이브러리가 없으면 지금까지 학습한`AnnotationConfigApplicationContext` 을 기반으로 애플리케이션을 구동한다. 웹 라이브러리가 추가되면 웹과 관련된 추가 설정과 환경들이 필요하므로`AnnotationConfigServletWebServerApplicationContext`를 기반으로 애플리케이션을 구동한다. ApplicationContext의 구현체 종류가 다름


- `@Scope(value = "request")` 를 사용하면 빈은 HTTP 요청 당 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸됨

### 9.6 스코프와 프록시

- `@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)`
- CGLIB라는 라이브러리로 내 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입한다.
- 가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.

---

## ○ 기타

- ApplicationContext 생성할 때, 파라미터로 여러 개의 설정정보 넘겨줘도 된다.
  예) `ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);`
- **객체의 생성과 초기화를 분리하자.** 생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다. 반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는등 무거운 동작을 수행한다. 따라서 둘을 명확하게 나누는 것이 유지보수 관점에서 좋다.
- **빈의 스코프에 대해 고민할 것**
- 싱글톤 빈을 동시 요청해도 괜찮나?  
  싱글톤 빈은 모든 쓰레드가 엑세스 할 수 있는 전역 영역인 힙 메모리에 올라감  
  클라이언트가 동시에 요청해도 각각 별도의 쓰레드가 생성되고 각 쓰레드에 대해 별도의 스택 메모리를 생성하기 때문에 변수를 덮어쓰지 않게 함. 다만, 싱글톤 빈은 Stateless하게 설계하는 것이 중요!!
- Spring이 CGLIB 사용해서 가짜 객체를 생성하는 예시
    - 싱글턴 빈을 만들 때
    - 스콮이 다른 가짜 객체를 만들어야될 때
- 스프링은 확장 포인트를 아는 것이 중요! 대부분의 것들을 인터페이스로 제공