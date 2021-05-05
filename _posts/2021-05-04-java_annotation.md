---
title: "Java Annotation(작성 중...)"
date: 2021-05-05 08:00:00 -0400
categories: java
---

해당 글은 Java Annotation에 익숙해지기 위해 [Oracle Annotation](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)를 번역 및 정리하는 목적에 있습니다.

Annotation은 JDK5부터 등장했으면 공식 문서에서의 정의는 아래와 같다.

> Annotations, a form of metadata, provide data about a program that is not part of the program itself. Annotations have no direct effect on the operation of the code they annotate.

어노테이션들은 메타데이터의 형태이며 프로그램 그 자체의 영역이 아닌 프로그램에 대한 데이터를 제공합니다. 어노테이션들은 *코드의 동작에 직접적인 영향*을 끼치진 않는다.

위 문장으로 쉽게 무엇을 하는지 파악하기는 어려울 수 있으니 더 자세히 알아보겠습니다.

# Java Annotation의 개념

Annotation은 아래 3가지 형태로 사용됩니다. 다음과 같습니다.

- 컴파일러에게 정보를 제공
  - Annotation들은 error를 감지하거나 경고를 나타내기위해 컴파일러에 의해 사용될 수도 있다.
- 컴파일 단계 혹은 배포 단계에서의 처리
  - software tools들은 code, XML files 등등을 생산하기위해 Annotation정보를 처리할 수 있다.
- 런타임시에 처리
  - 어떤 Annotation들은 런타임에 처리되기 위해 이용할 수 있다.

# Annotation Basics

## The Format of an Annoation

간단한 형태로, Annotation은 다음과 같이 나타낸다.

```java
@Entity
```

`@` 문자는 컴파일러에게 다음으로 올 내용이 Annotation임을 나타낸다.

필요하다면 아래와 같이 각 어노테이션에 argument를 할당하는 것 또한 가능하다. (arugment가 하나라면 name은 생략 가능)

```java
@Author(
  name = "jihoson",
  date = "05/05/2021"
)
class MyClass { ... }
```

## Annotation 이 사용될 수 있는 곳

어노테이션들은 클래스, 필드, 메소드 그리고 그외 다른 요소를 선언할 때 사용될 수 있습니다.

Java SE 8 릴리즈에서는 아래 예제처럼 어노테이션들은 타입 사용에도 적용될 수 있습니다.

- 클래스 인스턴스 생성 표현:

```java
new @Interned MyObject();
```

- Type cast:

```java
myString = (@NonNull String) str;
```

- implements clause:

```java
class UnmodifiableList<T> implements @Readonly List<@Readonly T> { ... }
```

- Thrown exception declaration:

```java
void monitorTemperature() throws @Critical TemperatureException { ... }
```

위와 같은 annotation의 형태를 `type annotation`이라고 부릅니다.

# Java Annotaion의 활용

## 자주 사용되는 Annotation

> ## Reference
>
> [어노테이션 공식문서](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)
>
> [어노테이션 프로세싱](https://medium.com/@jintin/annotation-processing-in-java-3621cb05343a)
