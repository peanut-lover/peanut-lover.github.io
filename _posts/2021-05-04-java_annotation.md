---
title: "Java Annotation 정리"
date: 2021-05-05 08:00:00 -0400
categories: java
---

해당 글은 Java Annotation에 익숙해지기 위해 [Oracle Annotation](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)를 번역 및 정리하는 목적에 있습니다.

Annotation은 JDK5부터 등장했으면 공식 문서에서의 정의는 아래와 같다.

> Annotations, a form of metadata, provide data about a program that is not part of the program itself. Annotations have no direct effect on the operation of the code they annotate.

어노테이션들은 메타데이터의 형태이며 프로그램 그 자체의 영역이 아닌 프로그램에 대한 데이터를 제공합니다. 어노테이션들은 *코드의 동작에 직접적인 영향*을 끼치진 않는다.

위 문장으로 어노테이션이 무엇이냐는 질문에 답하기는 어렵기 때문에 자세히 알아보겠습니다.

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

## Annotation 적용 수 있는 곳

Java SE 8 릴리즈 이전의 어노테이션들은 클래스, 필드, 메소드 그리고 그외 다른 요소를 선언할 때만 사용될 수 있습니다.

하지만 Java SE 8 릴리즈 후,아래 예제처럼 어노테이션들은 어떠한 타입 사용에서도 적용될 수 있습니다.

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

위와 같은 annotation의 형태를 `type annotation`이라고 부릅니다. Type Annotation은 더욱 강한 타입 Checking을 지원하기 위해서 만들어졌습니다. 대신 Java SE 8 는 type checking framework를 제공하지는 않지만 자바 컴파일러와 함께 사용되는 하나 이상의 pluggable module을 구현해서 type checking framework를 작성하는 것을 가능하게 합니다.

예를 들어 NullPointerException의 발생을 피하기 위해 여러분이 특정 변수에 결코 null이 할당되는 것을 원하지않는다면 Null이 할당될 가능성이 있는지 체크하는 custom plug-in을 작성할 수 있습니다.

```java
@NonNull String str;
```

NonNull module을 포함해서 코드를 컴파일할 때, 컴파일러는 Null할당 가능성을 체크해서 경고를 출력할 것입니다.

## Java SE 8 에서 제공해주는 Annotations

- @Deprecated
- @Override
- @SuppressWarnings
- @SafeVarargs
- @FuncationalInterface : Java SE 8 부터 지원.

# 사용자 정의 어노테이션

어노테이션을 선언할 때는 class 가 아닌 @interface 를 통해 선언을 할 수 있습니다.

추가로 어노테이션에 적용 가능한 메타 어노테이션 또한 존재합니다.
자세한 사용법은 문서를 참조하기 바랍니다.[Predifined Annotation Doc](https://docs.oracle.com/javase/tutorial/java/annotations/predefined.html)

- @Retention
- @Documented
- @Target
- @Inherited
- @Repeatable : Java SE 8 부터 지원.

간단히 Method에 적용 가능하고 Runtime시기까지 정보가 유지되는 Annotation을 만들어보겠습니다.

```java
package study;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface UserAnnotation {
    int number();
    String text() default "This is first annotation";
}
```

아주 간단히 `Method` 적용가능하고 `런타임동작`까지 어노테이션 정보가 유지되는 Annotation을 선언했습니다. 그렇기에 아래와 같은 코드에서 Reflection 통해 런타임에 해당 어노테이션 정보를 조회할 수 있습니다.

Reflection을 통해 중복되는 코드를 줄일 수 있지만 성능적인 측면에서 추천되는 방식은 아닌 것 같습니다.

```java

import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) {
        new Main().checkAnnotations(Main.class);
    }
    @UserAnnotation(number = 1, text = "Annotation is applied!")
    public void testMethod1() { }
    @UserAnnotation(number = 2)
    public void testMethod2() { }

    public void checkAnnotations(Class useClass) {
        Method[] methods = useClass.getDeclaredMethods();
        for(Method tempMethod: methods) {
            UserAnnotation annotation = tempMethod.getAnnotation(UserAnnotation.class);
            if(annotation != null) {
                int number = annotation.number();
                String text = annotation.text();
                System.out.println(tempMethod.getName() + "() : number=" + number + " text=" + text);

            } else {
                System.out.println(tempMethod.getName() + "() : annotation is null.");
            }
        }
    }
}
```

## 어노테이션의 활용 사례

개인적으로 Annotation은 정말 잘쓰고 있다는 느낌은 준 라이브러리가 있습니다. 바로 Lombok입니다. Lombok에 관한 자세한 내용은 다른 글로 정리하겠습니다.

# 어노테이션 프로세싱

어노테이션 프로세싱은 compile time 에 발생하며 javac 동작시 특정 어노테이션을 탐색하고 처리하기 위한 방법입니다.

어노테이션 프로세서는 input으로 자바코드를 받고 output으로 파일(주로 java file)을 생성합니다. 즉, 상황에 맞는 자바코드를 생성할 수 있다는 것을 의미합니다. 하지만 기존에 존재하는 자바 class를 수정할 수는 없습니다.

전체적인 과정은 아래와 같습니다.

1. 어노테이션 클래스들을 작성
2. 어노테이션 처리하는 클래스(annotation parser class)들을 작성
3. 프로젝트에 어노테이션을 적용
4. 컴파일을 시작하면 어노테이션 처리 클래스들 어노테이션에 대한 처리를 시작
5. 그리고 자동으로 생성된 클래스들은 Build folder 추가될 것입니다.
6. 처리되지않은 Annotation Processor가 남아있다며 4번부터 다시 반복

Annotation Parser class는 프로젝트를 컴파일할 때만 오직 필요하다는 것을 알아야합니다. 배포시 어플리케이션에는 포함될 필요가 없습니다.

[Annotation Processing Example](https://hannesdorfmann.com/annotation-processing/annotationprocessing101/)

> ## Reference
>
> [어노테이션 공식문서](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)
>
> https://medium.com/@jintin/annotation-processing-in-java-3621cb05343a
>
> https://hannesdorfmann.com/annotation-processing/annotationprocessing101/
