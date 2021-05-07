---
title: "Java Reflection 정리"
date: 2021-05-05 03:03:00 -0400
categories: java reflection
---

리플렉션이란 객체를 통해 클래스의 정보를 분석해 내는 프로그램 기법을 말한다. 투영, 반사라는 사전적 의미를 지니고 있다.

또 다른 말로 표현하면, Reflection은 런타임 중에 methods, classes, interfaces의 동작을 수정하고 살펴보기 위해 사용되는 API입니다.

- Reflection 을 위해 필요한 클래스들은 java.lang.reflect package에서 제공됩니다.
- Reflection 은 object가 어떤 class에 속하는 지에 대한 정보와 해당 클래스에 어떤 메소드가 있는지에 대한 정보를 제공합니다.
- Reflection을 통해 우리는 access specifier의 사용과 무관하게 런타임에 메소드를 발생할 수 있습니다.

![리플랙션 이미지](/assets/image/reflection.png)

# Reflection의 이점

- Extensibility Features

- Debugging and testing tools
  Debuffers는 리플렉션의 속성들을 private member들을 검증하기 위해 사할 수 있습니다.

# Reflection의 단점

- Performance Overhead:
  Reflective 명령들은 non-reflective 한 부분에 비해 더느린 성능을 하고 있습니ㅏㄷ. 그래서 성능에 민감한 어플리케이션에서 잦은 사용은 피해야합니다.
- Exposure of Internals: reflective한 코드는 추상화를 부수거나 플랫폼 변경에 의해 동작이 변경될 지도 모릅니다.

> Reflection
>
> https://www.geeksforgeeks.org/reflection-in-java/

# Reflection의 원리

Reflection이 가능한 이유는 ...
(작성)
