---
title: "JAVA SE vs EE에 관하여"
date: 2021-05-04 20:12:00 -0400
categories: java
---

Oracle 홈페이지에서 JDK 혹은 JRE를 설치할 때 SE, EE를 선택해야하는 상황을 흔히 겪을 수 있습니다.
Standard Edition, Enterprise Edition이라는 이름을 통해서 알 수 있듯이 EE버젼은 기업용 (서비스용) 으로 사용되고 더 많을 기능을 제공할 것이라는 추측을 할 수는 있지만 좀더 자세히 알아보고 우리가 알아야할 것들이 무엇이 있는지를 알아보겠습니다.

# Java SE (Standard Edition)

흔히 자바 언어를 생각할 때, 대부분의 사람들은 JAVA SE API를 생각합니다. 그런 Java SE API는 자바 언어의 핵심 기능을 제공합니다. 여기서 말하는 핵심 기능은 Basic Type 부터 networking, security, database access, GUI, XML Parsing 까지 모두 해당됩니다.

그리고 Core API 이외에도 Java SE Platform는 Virtual Machine, Development tool, deployment tools, deployment technology 등등 자바 어플리케이션을 구현할때 흔히 사용되는 라이브러리들로 구성됩니다.

# Java EE (Enterprise Edition)

간단히 정의하면 Java EE는 자바로 웹개발을 할 수 있게 해주는 표준이며 **Java SE를 기반으로 확장된 개념**입니다.

Java EE platform은 large-scale, multi-tiered, scalable, reliable and secure network application을 개발하기위한 API와 런타임환경을 제공합니다.

## 1. Enterprise Application이란?

위와 같은 application들을 짧게 `Enterprise applications` 이라고 부르는데 그 이유는 큰 규모의 기업들이 마주치는 문제들을 해결하기 위해 설계되었기 때문입니다.

추가로 Enterprise applications 이 오직 거대한 조직, 기관, 정부에서만 유용한 것은 아니고 Enterprise application의 이점들은 **개인 개발자들과 작은 조직에게도 도움을 줄 수 있습니다.**

하지만 Enterprise Application을 더욱 강력하게 만드는 특징(보안, 신뢰성)들은 종종 **어플리케이션을 더욱 복잡하게** 만들수 있습니다.

Java EE Platform는 개발자들이 기능 개발에 집중할 수 있게 해주는 개발 모델, API, 런타임환경을 제공함으로써 앞서말한 Enterprise Application 개발에서 발생하는 복잡성을 줄여줍니다.

Java EE은 다양하고 중요한 개념들을 가지고 있기 때문에 더 자세한 내용은 추가로 다른 글에서 정리해보겠습니다.

## 2. Spring Framework 과 J2EE의 관계

![Spring JavaEE Image](/assets/image/JavaEE_Spring.png)

스프링은 2003년 초기 J2EE 표준의 복잡성에 대응하기위해 만들어졌습니다. 어떤 사람들은 JEE 와 Spring은 경쟁 구도를 가진다고 생각하지만 사실 스프링은 Java EE를 보완해줍니다. 그리고 Spring Programming Model은 Java EE platform 표준을 모두 수용하지는 않습니다. 오히려 EE 표준으로부터 주의깊게 특정 스펙들을 선택해서 적용해가고 있습니다.

- Servlet API
- WebSocket API
- Concurrency Utilities
- JSON Binding API
- Bean Validation
- JPA
- JMS

> ### Reference
>
> - [Oracle Java EE Overview](https://docs.oracle.com/javaee/6/firstcup/doc/gkhoy.html)
>
> - [Spring Framework](https://docs.spring.io/spring-framework/docs/current/reference/html/overview.html#overview)
>
> - [한국에서의 Spring](https://okky.kr/article/415474)
