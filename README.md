# spring-basic
[인프런] 스프링 핵심 원리 - 기본편

## 4. 스프링 컨테이너와 스프링 빈
### 4.2 컨테이너에 등록된 모든 빈 조회
- ApplicationContextInfoTest.java
### 4.3 스프링 빈 조회 - 기본
- ApplicationContextBasicFindTest.java
### 4.4 스프링 빈 조회 - 동일 타입이 둘 이상
- ApplicationContextSameBeanFindTest.java
### 4.7 다양한 설정 형식 지원 - 자바코드, XML
- XmlAppContext.java
- src/main/resources/appConfig.xml
### 4.8 스프링 빈 설정 메타정보 - BeanDefinition
- BeanDefinitionTest.java

---
## 5. 싱글톤 컨테이너
### 5.1 웹 애플리케이션과 싱글톤
- SingletonTest.java : pureContainer 메소드
### 5.2 싱글톤패턴
- SingletonService.java
- SingletonTest.java : singletonServiceTest 메소드
### 5.3 싱글톤 컨테이너
- SingletonTest.java : springContainer 메소드
### 5.5 @Configuration과 싱글톤
- SingletonTest.java : configurationDeep 메소드

---
## 6. 컴포넌트 스캔
### 6.1 컴포넌트 스캔과 의존관계 자동 주입 시작하기
- AutoAppConfig.java
- AutoAppConfigTest.java




