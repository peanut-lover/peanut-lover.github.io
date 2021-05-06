---
title: "Java 컴파일부터 동작까지 한눈에"
date: 2021-05-06 00:00:00 -0400
categories: java jvm jit
---

# 목차

- 1. JVM 이란?
- 2. JVM 구성 요소

# 1. JVM 이란?

> Reference
>
> https://dzone.com/articles/jvm-architecture-explained

Java Virtual Machine 은 자바 바이트코드(.class)을 실행하는 프로그램입니다. 즉, 자바 프로그램이 실행할 수 있는 런타임 환경을 제공합니다. 흔히 우리가 알고있는 JRE(Java Runtime Environment)가 JVM의 구현체를 가지고 있습니다.

자바 컴파일러(javac)에 의해 생성된 자바 바이트 코드는 기계어가 아니기 때문에 OS에서 바로 실행할 수 없습니다. JVM은 OS가 java byte code를 이해할 수 있도록 해석해줍니다. JVM의 해석을 거치기 때문에 C언어와 같은 네이티브언어에 비해 느렸지만 **JIT(Just-In-Time) Compiler** 를 구현해 해당 문제를 극복했습니다.

Byte Code는 JVM 위에서 OS상관없이 실행됩니다. 이런 점이 Java의 가장 큰 장점이라고 할수 있습니다.
OS에 종속적이지 않고 Java 파일 하나만 만들면 어느 디바이스든 JVM 위에서 실행 할 수 있습니다.

그리고 JVM은 크게 세 가지의 구성으로 이루어집니다.
[![JVM Image](/assets/image/JVM-Architecture.png)](https://dzone.com/articles/jvm-architecture-explained)

# 2. JVM 구성 요소

구성 요소들은 한 번정도는 깊게 이해하는 것이 좋을 것 같아 큰 단락으로 정리하겠습니다.

- ClassLoader
- Runtime Data Area
- Execution Engine
- Java Native Interface
- Java Native Libraries

## 자바 클래스 로더 (class loader)

클래스를 메모리에 올리는 동적 클래스 로딩 기능은 ClassLoader에 의해 이루어집니다.

즉, 자바 클래스들은 한번에 메모리에 올라오지않고 어플리케이션에 의해 필요할 때 동적으로 메모리에 올라오게됩니다.
클래스 로딩은 3단계로 이루어집니다.

> Loading -> Linking -> Initializaion

### 1) Loading

모든 클래스들이 하나의 클래스 로더에 의해 로딩되지않습니다. 클래스의 종류와 경로에 따라 어떤 클래스로더에 의해 로드될지 결정됩니다. 만약 클래스가 발견되지 않는다면 NoClassDefFoundError 와 ClassNotFoundException이 발생하게 됩니다.

- BootStrap ClassLoader
  - Its job is to load the first pure Java ClassLoader. Bootstrap ClassLoader loads classes from the location rt.jar.
- Extension ClassLoader
  - It loads files from **jre/lib/ext** directory or any other directory pointed by the system property java.ext.dirs.
- Application ClassLoader
  - It loads the Application type classes found in the environment variable CLASSPATH, -classpath or -cp command line option.

### 2) Linking

1. Verify
   Bytecode verifier는 생성된 자바 바이트 코드가 적절한지를 검증합니다.
2. Prepare
   모든 static 변수의 메모리가 할당되어지고 **default 값들이** 할당됩니다.
3. Resolve
   모든 Symbolic memory reference가 Method Area에 있는 original references로 대체됩니다.

** Method Area는 아래 Runtime Data Area에서 설명할 예정. **

### 3) Initialization

Class Loading의 마지막 단계입니다. 모든 Static 변수들이 original 값들로 할당되고 static block이 실행 됩니다.

```java
  static class Student {
    // Static Block
    static {
      System.out.println("static...1");
    }
  }
```

## Runtime Data Areas

Runtime Data Area는 메모리에 올라와있는 자바 프로그램입니다.
크게 5개의 주요 구성요소로 나뉘어져있습니다.

- **Method Area** : static 변수들을 포함해서 클래스 수준의 모든 데이터가 이곳에 저장될 것입니다. JVM마다 오직 하나의 method area가 있으며 공유되는 리소스들입니다.

- **Heap Area** : 모든 Objects와 그에 상응하는 instance 변수 그리고 배열들이 이곳에 저장됩니다. 물론 JVM마다 하나의 Heap Area가 존재합니다. Method areas 와 Heap areas는 여러 스레드들간에 공유되는 메모리입니다. 즉, 이 곳에 저장된 데이터들은 스레드에 안전하지 않습니다.

- **Stack Area** : 각각의 스레드를 위해 분리된 런타임 스택이 생성됩니다. 메소드 호출마다, Stack Frame이라 불리는 하나의 Entry가 Stack memory가 생성됩니다. stack area는 스레드에 안전하지만 공유되는 리소스가 아닙니다. 또, Stack Frame은 3가지 Subentities로 나뉘어집니다.

  - Local Variable Array
  - Operand stack
  - Frame data

- **PC Register** : 각 스레드는 현재 실행중인 상태 정보를 저장하는 개별적인 PC Register들을 가질 것이고 진행이 되면 지속적으로 갱신됩니다.

- **Native Method stacks** : Native Method Stack 는 native method 정보를 가지고 있습니다. 각 스레드는 개별의 native method stack 이 생성될 것입니다.

## Execution Engine (중요)

Runtime Data Area에 할당된 Java ByteCode를 Execution Engine이 실행시킵니다.
The Execution Engine 은 bytecode 를 읽고 한줄 한줄 실행시킵니다.

- Interpreter

  - interpreter는 bytecode를 빨게 해석하지만 느리게 실행한다. interpreter의 단점은 하나의 메소드를 여러번 호출할 때, 매번 새로운 해석이 요구된다는 것이다.

- JIT Compiler

  - JIT Compiler는 interpreter의 단점을 없애준다. Execution Engine은 interpreter가 bytecode를 변환하는데 사용하지만 **반복되는 코드를 발견했을 때** 전체 bytecode를 컴파일하고 그것을 native code로 바꾸는 JIT compiler를 사용한다. 여기서 생성된 native code는 반복되는 메소드 호출에 대해 성능을 개선하기위해 직접적으로 사용됩니다.

- Garbage Collector
  - 참조되어 있지 않는 Objects들을 모으고 제거하는 역할을 합니다. Garbage Collection은 `System.gc()` 를 호출하므로 발생합니다. 하지만 실행이 보장되지는 않습니다.

## Java Native Interface (JNI)

JNI 는 Native Method Libraries와 상화작용하며 Execution Engin을 위해 Native Libraries를 제공합니다.

## Native Method Libraries

Execution Engine에서 필요한 Native Libraries의 모음입니다.
