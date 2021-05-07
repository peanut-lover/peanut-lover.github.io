---
title: "Spring IoC 컨테이너"
date: 2021-05-06 03:03:00 -0400
categories: java spring
---

스프링 IoC Container 와 Bean 관리에 관해 정리해보겠습니다.

# IoC(Inversion Of Control)?

IoC가 무엇이냐는 질문에 답하기는 말로 정말 어렵다.

소프트웨어 개발에 대한 제어권을 프레임워크로 역전 혹은 Dependency Injection을 할 수 있는 구조 등등 많은 표현으로 알고 있어왔다.

그러던 괜찮은 자료를 찾아 요약해놨습니다.
[Ioc 관련 링크](https://web.archive.org/web/20040413042810/http://www.betaversion.org/~stefano/linotype/news/38/)

> This inversion of control is sometimes named the Hollywood principle, “Do not call us, we call You”.

Ioc는 "우리에게 전화하지마, 우리가 너에게 전화할께" 헐리우드 이론으로 불렸다.

이 문구가 IoC 개념을 이해한느데 적절하고 보며

> Martin Fowler renames it Dependency Injection,

마틴 파울러가 DI으로 이름을 다시 바꿨다고 한다.

결국, 사람마다 정의가 다를 수 있지만 IoC와 Dependency Injection을 같은 개념으로 바라봐도 크게 문제가 될 것 같지 않을 것 같습니다.

## Dependency Injection

SOLID 원칙 중 D에 해당하는 DIP(Dependency Injection Principle)는 Component간의 직접적인 의존성을 끊을 수 있다.

일반적인 프로그래밍 방식을 보면

```java
public class UserService {
  private final UserRepository userRepository;
  public UserService() {
    this.userRepository = new CustomUserRepository();
  }
}
```

위와 같이 구체적인 CustomUserRepository 클래스의 생성자에 의존적입니다. 만약 CustomRepository가 아닌 새로운 구현체를 적용하고 싶거나 생성자의 형태가 바뀐다면 의존성이 존재하는 모든 소스 코드를 고쳐야 될 것입니다.

DI가 적용된 코드를 살펴보겠습니다.

```java
public class UserService {
  private final UserRepository userRepository;
  public UserService(UserRepository userRepository) {
    this.userRepository = userRepository;
  }
}
```

위와 같이 구체적인 구현을 UserService를 생성하는 쪽으로 책임을 위임함으로써 UserService 와 UserRepository의 구현체와의 의존성이 사라지게됩니다. 이렇게 의존성을 위에서 주입시켜주는 것을 DI라고 합니다.

# 스프링 IoC Container

Bean 설정 소스로 부터 Bean 정의를 읽어들이고, Bean을 구성하고 제공합니다.

스프링 IoC 컨테이너의 역할은

- Bean 인스턴스 생성
- Bean 간의 의존 관계 설정
- Bean Injection

이 된다.

스피링 IoC Container의 Root Interface는 `BeanFactory`가 되며 어플리케이션 컴포넌트의 중앙 저장소입니다. 스프링에서는 ApplicationContext가 BeanFactory interface를 구현하고 있어서 IoC컨테이너 역할 을 할 수 있습니다.

# 컴포넌트 스캔

Bean 정의와 Bean간의 의존성을 수동으로 등록해주는 방법은 설정이 귀찮고 복잡해진다. 그래서 나온 방법이 Component Auto Scan이다.

적용할 Class 의 상단에 `@ComponentScan` 어노테이션을 적용하면 해당 클래스의 패키지 이하의 모든 클래스 중 `@Component` 어노테이션을 사용한 클래스를 빈으로 자동으로 등록해준다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
  ...
}
```

Spring Boot 프로젝트에서는 `SpringBootApplication` Annotation에 `ComponentScan` Annotation이 적용되어있다. 즉, `@SpringBootApplication`이 적용된 Class 파일을 기준으로 하위 패키지들은 컴포넌트 탐색 대상이 된다.

컴포넌트 스캔을 활용해서 Bean을 등록할 경우, 어플리케이션 시작시 Reflection을 사용하기 때문에 (Annotation 정보를 얻는 과정에 Reflection을 활용하게 됨) 조금의 성능저하가 있습니다.

하지만 어플리케이션 실행 순간에만 생기는 속도 지연이기 때문에 서비스 전체에 큰 영향을 주지는 않습니다.

그리고 직접 추가하는 방법도 있지만 사용성이 좋지 않기 때문에 추천되는 방식은 아닙니다.

# @Autowired

Autowired 어노테이션을 사용해서 Injection을 IoC Container에게 요청 할 수 있다.

Bean의 주입을 변수임을 나타내는 Annotation입니다. 각 변수선어, 생성자, Setter에도 적용할 수 있습니다.

# Bean의 스코프

Bean의 스코프는 Singleton, ProtoType 두 가지 종류가 있습니다.

Spring에서 Bean Scope를 설정하는 방법은 `@Scope("prototype")`와 같은 어노테이션을 활용합니다.

default 값은 singleton입니다.

```java
@Component @Scope("prototype")
public class Proto {

}
```

위와 같이 명시적으로 Scope를 prototype을 지정하면 Injection할 때마다 새로운 Bean이 생성된다.

주의할 점은 singleton scope의 component에서 prototype scope의 component를 참조할 경우, 아무런 설정을 해주지 않는다면 singletone Bean은 항상 같은 prototype scope를 들고 있게된다.

이를 해결하기위해서는 bean의 직접 참조가 아닌 Proxy를 활용해 해결할 수 있다.

```java
@Component @Scope("prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class Proto {

}
```

위와 같이 Proxy Mode를 설정해주면 Singleton Bean에서 매번 새로운 Prototype Bean을 얻을 수 있다.
