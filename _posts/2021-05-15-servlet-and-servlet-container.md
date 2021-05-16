---
title: "Servlet 과 Servlet Container"
date: 2021-05-15 08:00:00 -0400
categories: java tomcat
---

# Servlet 이란?

Servlet은 사용자의 요청을 처리해서 동적인 컨텐츠를 응답으로 주기 위한 프로그램이며 **Java EE 의 표준 API** 중 하나 입니다.

`javax.servlet.Servlet`

Servlet 표준 속에서 요구되는 중요한 점은 전체 데이터 처리 과정 속에서 오직 특정 부분만 다룬다는 것입니다. 예를들어 Servlet 은 결코 특정 포트를 listen 하지않고 클라이언트와 직접적인 커뮤니케이션(소켓 통신 etc)을 하지 않으며 외부 리소스 접근에 관한 관리에 대한 책임이 없습니다.

오히려 이런 작업들은 Tomcat, 즉 Servlet Container에 의해 관리됩니다.

이러한 방식은 작성한 Servlet이 다양한 환경에서 재사용할 수 있게 해주고 다른 요소들의 수정과 개별적인 개발을 가능하게 해줍니다.

# Servlet Container 이란?

유연한 설정과 여러 기술을 지원하는 상호운용성이 톰켓을 많은 상황에서 웹 어플리케이션 서버로써 사용할 수 있게 해줬더라도, 핵심적으로 톰켓은 **자바 서블릿 컨테이너**입니다.

톰캣은 Java Servlet과 JSP API의 구현을 활용해서, 클라이언트로부터 요청을 받을 수 있고, 요청에 대한 알맞은 처리를 하기위해 동적으로 Container에 의해 관리되는 자바 클래스를 컴파일해서 사용자에게 결과를 돌려줄 수 있습니다.

자바 서블릿 표준은 다른 주요 자바 웹 기술들과 상호운용을 위해 디자인 되었기 때문에 Tomcat Server에 올려진 Servlet은 톰캣이 이용할 수 있게 해준 어떠한 리소스든 사용할 수 있습니다.

톰켓의 XML 설정 파일은 매우 세밀한 리소스 접근 제어를 가능하게하고, 낮은 결합도를 유지하며, 쉬운 배포를 가능하게 합니다.

## Servlet Life Cycle in Servlet Container

서블릿 컨테이너에서 서블릿가 어떻게 사용되는지 간략히 정리해보겠습니다.

톰캣에서 동작하는 전형적인 서블릿의 Life cycle은 다음과 같습니다.

1. 톰캣이 가지고 있는 Connector 중 하나를 통해서 request를 전달받습니다.

2. 톰캣은 해당 request를 처리하기위해 적절한 Engine에 매핑합니다. 여기서 Engine들은 정확한 Engine을 탐색하기 위해 범위를 좁혀주는 Hosts and Servers과 같은 요소들 속에 포함된 개념입니다.

3. 일단 요청이 적절한 서블릿에 매핑되면, 톰캣은 서블릿이 로드되었는지 체크합니다. 만약 로드되어 있지않다면 톰캣은 JVM에 의해 실행될 수 있게 서블릿 인스턴스를 생성합니다.

4. 톰캣은 init 메소드를 호출함으로써 서블릿을 초기화합니다. The servlet includes code that is able to read Tomcat configuration files and act accordingly, as well as declare any resources it might need, so that Tomcat can create them in an orderly, managed fashion.

5. 일단 서블릿이 초기화되면, 톰캣은 **서블릿 의 service 메소드를 호출**해서 request를 처리하고, 응답을 반환할 것입니다.

6. 서블릿 Life cycle 동안, 톰캣과 서블릿은 다양한 상태 변화들에 대해서 서블릿을 모니터링할 수 있는 listener 클래스를 사용해서 소통할 수 있습니다. 톰캣은 다양한 방식으로 상태변화를 저장, 조회할 수 있고 다른 서블릿들도 그것들에 접근하는 것을 가능하게 하며 다양한 컴포넌트에 의해 접근되고 유지될 수 있는 상태도 만들 수 있습니다.

이 기능에 대한 대표적인 예는 e-commerce 어플리케이션이 유저가 장바구니에 담아 놓았던 아이템들을 기억하고 결제 프로세스에 데이터를 넘길 수 있는 것입니다.

7. 마지막으로 톰캣은 서블릿을 제거하기위해 서블릿의 destory 메소드를 호출합니다. 해당 작업은 서블릿의 상태 변화에 의해 혹은 어플리케이션을 undeploy하기 위해 톰캣에 전달된 외부 명령에 의해, 아니면 server shutdown에 의해 발생합니다.

# Servlet이 필요한 이유

Servlet API가 필요한 이유는 두 가지 관점에서 바라볼 수 있습니다.

- 편리한 개발을 위한 Servlet API의 사용
- 프로세스 기반의 요청처리의 단점을 극복

## 편리한 Servlet API의 사용

우리가 Servlet API 혹은 Servlet Container를 사용하지 않는다면, Socket Connection관리, 주고 받는 Raw Data 파싱, session, cookie 관리, 요청에 관련된 thread관리를 직접해줘야할 것입니다.

추가로 잘 구현했다하더라도 서로 다른 인터페이스를 가진 외부 라이브러리와의 통합은 불가능에 가까울 것입니다.

이러한 번거로움을 해결하기위해 Java EE Platform의 표준으로 javax.servlet, javax.servlet.http 와 같은 패키지가 나오게 되었고 개발자는 각 벤더에서 만든 Servlet 컨테이너에 동일한 인터페이스를 가진 Servlet 개발에만 집중을 할 수 있게되었습니다.

## 프로세스 기반의 요청처리의 단점을 극복

이 내용을 이해하기 위해 과거의 기술을 한번 들여다볼 필요가 있습니다.

CGI(Common Gateway Interface)는 웹서버와 외부 프로그램 간의 데이터를 주고 받는 방법에 대한 규격입니다. 즉, Servlet과 마찬가지로 웹서버가 동적인 컨텐츠를 제공할 수 있는 방법입니다.

CGI 가 나온 초창기에는 C나 Perl로 CGI프로그램을 만들어 동적 컨텐츠를 제공했습니다. 즉, 각 요청당 하나의 프로세스가 해당 요청을 처리해줬습니다. CGI는 규격일 뿐이고 초기에 흔히 프로세스기반의 요청처리를 하는 어플리케이션을 CGI라 통상적으로 불리게 된 것같습니다.

하지만 위와 같이 프로세스로 매 요청을 처리할 때 프로세스가 메모리에 올라가고 내려오는데는 많은 자원을 잡아먹게됩니다.

이를 해결하기 위해 Servlet Container는 매 요청을 스레드를 통해 처리함으로써 프로세스 기반의 요청처리에 비해 서버의 자원을 덜 사용합니다.

# Servlet API 사용

주로 사용할 만한 Servlet API 대해 정리해보겠습니다.

- Servlet
- Sharing Information(Using Scope Objects)
- Filter
- Listener

https://docs.oracle.com/javaee/7/tutorial/servlets.htm#BNAFD

## HelloServlet

![httpServlet Image](/assets/image/httpServlet.png)

대부분 웹 서블릿은 [`javax.servlet.http.HttpServlet`](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServlet.html)을 상속받아 구현하게 됩니다.

최상위 root interface인 javax.servlet.Servlet의 init(), service(), destory() method가 Servlet Container가 동적으로 호출 될 것입니다.

그리고 저희는 간단히 HttpServlet 의 Method들을 Override해서 정의하면 요청에 대한 처리를 수행할 수 있습니다.

```java
package mypackage;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    @Override
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
        // Logging
        ServletContext context = getServletContext();
        context.log("HelloServlet.service() invoked!");

        res.setContentType("text/html");
        PrintWriter writer = res.getWriter();
        writer.println("<h1>Hello This is Sample Servlet!</h1>");
    }
}

```

위 예제 코드는 service를 간단히 테스트하기 위해 직접 Override 했지만 HttpServlet 클래스의 service 메소드의 default 구현은 HTTP Method에 따라 doGet(), doPost(), ... 을 호출하도록 분기로직이 있습니다. 실제 사용시 미리 정의되있는 doXXX() 사용하면 될 것 같습니다.

추가로 편의를 위해 `@WebServlet` 어노테이션을 사용해서 서블릿 매핑 정보를 등록 했는데 Servlet 3.0 부터 지원되는 어노테이션이기 때문에 만약 어노테이션이 제대로 적용되지않는다면 **톰켓이 지원하는 Servlet의 버젼을 확인** 후 적용 가능하다면

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_7.xsd"
    version="2.7">
```

위와 같이 xml 의 version 정보를 아래와 같이 3.0 이상으로 변경해주면 제대로 적용됩니다.

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_3_0.xsd"
    version="3.0">
```

추가로 어노테이션을 활용하지않고 xml으로 servlet을 등록하고 싶다면

```xml
<servlet>
    <servlet-name>HelloServlet</servlet-name>
    <servlet-class>mypackage.HelloServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

위 내용을 web.xml에 적용하시면 가능합니다.

## HelloFilter

Servlet API 중 중요한 요소 중 하나로 [`javax.servlet.Filter`](https://docs.oracle.com/javaee/7/api/)가 있습니다.

![Servlet Filter Image](/assets/image/jee_filter.png)

Filter 또한 Servlet Container에 의해 사용되며 각 요청, 응답의 내용과 헤더를 변환할 수 있는 객체입니다. Filter는 어떠한 웹 리소스에도 적용할 수 있는 기능을 제공합니다.

잘 활용하면 Servlet 구현 자체에 의존성을 가지지 않는 기능을 추가하고 제거할 수 있어서 유연한 설계가 가능합니다.

해당 디자인을 잘 활용한 사례는 아래와 같습니다.

1. Authentication Filter
2. Logging and Auditing Filters
3. Image conversion Filters
4. Data compression Filters
5. Encrpytion Filters
6. Tokenizing Filters
7. Filters that trigger resource access events
8. XSL/T filters
9. MIME-type Chaine Filter

```java
package mypackage;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;
import java.io.PrintWriter;

@WebFilter(filterName = "HelloFilter",
        urlPatterns = {"/*"})
public class HelloFilter implements  Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // Logging
        ServletContext context = servletRequest.getServletContext();
        context.log("HelloFilter.doFilter() invoked!!");

        PrintWriter out=servletResponse.getWriter();
        out.print("filter is invoked before");
        filterChain.doFilter(servletRequest, servletResponse);
        out.print("filter is invoked after");
    }
}

```

Filter 자체를 정의하는 것은 Servlet과 유사합니다. doFilter method가 어떤 작업을 할지 정의할 수 있습니다.

doFilter의 매개변수로 넘어온 FilterChain을 통해 다음 Filter 혹은 Servlet를 수행하기 전후의 로직을 작성 할 수 있습니다.

HelloServlet에서와 만찬가지로 편의를 위해 `@WebFilter` 어노테이션을 활용했습니다.

xml을 통해서 등록을 하고 싶다면

```xml
...

<filter>
    <filter-name>HelloFilter</filter-name>
    <filter-class>mypackage.HelloFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HelloFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

```

위와 같이 web.xml을 수정하면 적용 가능합니다.

## Servlet Listener for Life Cycle event

Listener 는 Servlet Life Cycle과 관련있습니다.
Life Cycle 이벤트가 발생했을 때 호출되는 메소드를 가지고 있는 Listener Object를 정의함으로써
우리는 서블리의 Life cycle 에서 이벤트에 반응하고 모니터링할 수 있습니다. Listener objects를 사용하기 위해서, listener class를 정의해야합니다.

아래의 listener interface 를 구현함으로써 Listener 클래스를 만들수 있습니다.

listener method가 발생했을 때, event에 적절한 정보를 포함한 event를 함께 던져줍니다.
예를 들어 HttpSessionListener interface속의 메소드들은 HttpSession을 포함한 HttpSessionEvent를 던져줍니다.

Servlet Lifecycle Event와 그에 맞는 Listener가 궁금하다면 [링크](https://docs.oracle.com/javaee/7/tutorial/servlets002.htm#BNAFL)를 참조해주세요.

이번에도 간단한 Servlet Context 초기화시 발생하는 이벤트 Listener를 만들어 보겠습니다.

```java
package mypackage;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

@WebListener(value = "This Listener is invoked by container.")
public class HelloServletContextListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext context = sce.getServletContext();
        context.log("HelloServletContextListener.contextInitailized() invoked!!");

        ServletContextListener.super.contextInitialized(sce);
    }
}
```

## Sharing Information in application

대부분의 web components는 각자의 역할을 수행하기 위해 다른 objects와 함께 작업을 합니다. 그래서 Servlet 기반으로 웹 서비스를 개발할 때도 마찬가지로 서로 공유가능한 Object들이 필요합니다.

그래서 각각의 Scope에서 사용가능한 Object들이 존재합니다.

- Web Context (Application)범위에서 공유되는 `javax.servlet.ServletContext`.

하나의 웹 Context에 속해있는 Web component들에서 모두 접근가능한 Object입니다. 추가로 ServletContext는 Servlet Container와 Servlet간에 접근을 허용해주는 Object이기도합니다. 예를들어 로그를 남기기위해서는 ServletContext를 이용할 수 있습니다.

- Session 범위에서 공유되는 `javax.servlet.http.HttpSession`.

하나의 세션에 속한 요청을 다루는 Web component들에서 접근이 가능합니다.
Client 의 상태를 유지하는데 사용됩니다. 예를 들어 사용자의 로그인 정보 유지

- Request 범위에서 공유되는 `javax.servlet.ServletRequest`

같은 요청을 다루는 범위에서 공유되는 객체입니다.

# Servlet Container 인 Tomcat의 기본 설정

톰캣은 내용이 방대하기 때문에 가장 중요한 설정인 `tomcat/config/server.xml`을 위주로 살펴보도록 하겠습니다.

톰캣의 구성요소는 아래와 같습니다.

- Service
- Connector
- Engine
- Host
- Context (Web application)

컨테이너를 위한 주요 설정파일인 `[tomcat path]/config/server.xml`를 살펴보며 각 구성요소에 대해 정리해보겠습니다.

## 초기 설정 config/server.xml

톰캣 아키텍쳐는 잘 정의된 규칙에 따라 결합된 일련의 요소들로 구성됩니다.

![Tomcat-Architecture Image](/assets/image/Tomcat-Architechture.jpeg)

각 서버의 구조는 server.xml에 의해 정의됩니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />
  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>
  <Service name="Catalina">
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    <Engine name="Catalina" defaultHost="localhost">
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>
      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
      </Host>
    </Engine>
  </Service>
</Server>
```

위 xml은 설치시 주석처리된 요소를 제거한 server.xml의 상태입니다.

## Context

우선 위 파일에서 보이지않는 Context에 대해 살펴보겠습니다.

공식 홈페이지의 용어 설명에 따르면

> Context - In a nutshell, a Context is a web application.

컨테이너라 불리는 톰캣의 구성요소의 그룹 가장 내부의 요소입니다. 하나의 웹 어플리케이션입니다.

어플리케이션은 로딩 중에 톰캣은 자동으로 Context를 인스턴스화하고 설정해줍니다.

설정 과정의 일부분으로, 톰캣은 물론 `[application folder]/WEB-INF/web.xml` 파일에 정의된 properties를 사용할 수 있는 상태로 만들어줍니다.

`[tomcat installation folder]/webapps`
webapps 디렉토리 아래로 여러 개의 컨텍스트가 위치합니다.

## Connector

Connector는 클라이언트와 communication하는 구성요소입니다. 톰캣은 다수의 connector들 가질 수 있습니다. 예를들어 Http traffic을 위한 `HTTP connector`와 Apache HTTPD server같은 또다른 웹서버와 Tomcat을 연결할 때 사용되는 AJP protocol을 구현하는 `AJP connector`가 있습니다.

톰캣의 default 설정은 HTTP 통신을 다루는 하나의 Connector를 포함합니다. 기본적으로 이 Connector는 8080 포트를 통해 오는 요청들을 기다립니다. 모든 어플리케이션을 위한 요청들은 하나의 Connector instance를 통해 전달됩니다. 매번 새로운 요청은 요청 기간 동안 Connector속에서 살아 남아 있는 새로운 thread의 인스턴스화를 발생시킵니다.

위 예시 server.xml의 Connector의 connectionTimeout attribute를 20,000으로 설정한 것은 세션이 5시간 33분 20초 동안 활성화되지않으면 종료됨을 의미합니다. redirectPort="8443" 설정은 SSL을 요구하는 요청들을 8443으로 redirected해주는 역할을 합니다.

## Host

Host는 톰캣 서버의 `www.yourdomain.com`같은 네트워크 이름과 관련있습니다. 하나의 host는 여러가지의 context들을 포함할 수 있다. 그리고 같은 서버 위에 여러가지의 hosts들을 정의할 수 있습니다. 예를 들어 만약 `yourdomian.com` 도메인을 등록했다면, 우리는 `w1.yourdomain.com` 나 `w1.yourdomain.com`과 같은 host 를 정의할 수 있습니다. 만약 DNS 서버에서 해당 도메인 이름을 컴퓨터 IP 주소와 매핑했다면 오직 인터넷을 통해서만 접근할 수 있음을 인지해야한다.

톰캣의 초기설정은 `localhost`라는 이름의 host를 포함 합니다. `localhost` 와 우리의 컴퓨터 사이의 관계는 `/etc/hosts` 파일에 작성된 내용에 의해 성립됩니다. (UNIX 계열의 OS는 DNS 서버보다 우선적으로 로컬에 있는 `/etc/hosts` 파일을 토대로 도메인 네임과 IP를 매핑을 시작합니다.)

Host 속성 `appBase`는 톰캣 설치 폴더 속의 application 디렉토리나타냅니다. 각 어플리케이션은 지정해놓은 디렉토리를 어플리케이션 경로로 인식합니다. 유일한 예외는 빈 문자와 매핑되는 ROOT path입니다.

초기설정의 localhost를 위한 base Directory는 `webapps`입니다. `[tomcat path]/webapps/ROOT` 속 어플리케이션은 빈 문자열에 의해 인식됩니다. 그래서 위 경로를 위한 접근 URL은 `http://localhost:8080/`이 됩니다. ROOT 이외에 디렉토리 `[tomcat path]/webapps/myapp`에 접근하려면 `http://localhost:8080/myapp` URL을 사용할 수 있습니다.

`unpackWARS="true"` 속성은 만약 `appBase`에 WAR file을 둔다면 톰캣이 자동으로 동일한 이름으로 압축을 해제한다는 것을 의미합니다. 만약 false로 설정되다면 어플리케이션은 WAR file을 통해서 직접적으로 실행 될 것입니다. 톰캣은 WAR file을 실행하기 위해 unzip이 필요하기 때문에 명백히 앞의 설정은 더 느리게 어플리케이션이 실행 시킬 것입니다.

마지막으로 `autoDeploy="true"` 속성은 만약 톰캣이 실행 중에 우리가 `appBase` 디렉토리 속에 어플리케이션을 둔다면, 그것은 자동으로 배포될 것이라는 것을 나타냅니다.

## Engine

하나의 Engine은 특정 Service에 대한 요청 처리 파이프라인을 나타냅니다. 하나의 Service가 다수의 Connecter를 가지고 있을 때, Engine은 해당 Connector들로 받은 요청들을 받고 처리해서 클라이언트에세 전달하기 위해 응답을 적절한 connector에 돌려줍니다.

하나의 Engine은 하나 이상의 Host를 포함해야합니다. 톰캣 초기설정은 host localhost를 포함하는 engine Catalina가 설정되있습니다. Catalina engine은 HTTP connector를 통해서 오는 모든 요청을 다루고 대응하는 응답으로 돌려줍니다. 그리고 매 요청을 요청 헤더에 포함된 정보를 기반으로 적절한 Host와 Context에 요청을 전달해줍니다.

## Service

Service 는 Server 속에서 하나의 Engine와 다수의 Connector들을 묶어주는 중개인 같은 요소입니다.
톰캣의 초기 설정은 HTTP, AJP connector 를 Catalina engine를 묶어주는 Catalina Service를 설정합니다. 참고로, Connector와 Engine은 Service 요소의 하위 요소입니다.

Service 설정을 사용자가 수정하는 것은 흔하지 않습니다. 초기 설정으로 간단하고 충분합니다.

## Server

Server는 톰캣 instance 자체를 나타내고 최상위 요소입니다. 하나의 engine과 다수의 Connector를 포함하는 다수의 Service를 포함할 수 있습니다.

여기까지 주요 구성를 살펴봤습니다. 추가로 `server.xml` 속 남은 몇가지 개념을 소개하겠습니다.

## 그 외 요소

### Listener

Apache Tomcat에서의 Listener은 Servlet Lifecycele Listener와 비슷하지만 톰캣의 라이프 사이클 이벤트에 대한 처리를 해줄 수 있습니다.
Listener는 `org.apache.catalina.LifecycleListener` 를 구현한 자바 Object이며 특별한 이벤트들에 반응할 수 있습니다.

톰캣 초기 설정에 등록된 리스너들은 아래와 같습니다.

- AprLifecycleListener
- JasperListener
- JreMemoryLeakPreventionListener
- GlobalResourcesLifecylceListener
- ThreadLocalLeakPreventionListener

### Global Naming Resources

`GlobalNamingResource` 요소는 Server 내에 정의할 수 있습니다. server를 통해 접근할 수 있는 JNDI resource를 정의합니다. 초기 설정에 정의된 resource는 `conf/tomcat-users.xml`를 통해 정의된 user 와 password 메모리 기반 데이터 베이스가 존재합니다.

### Realm

Realm 요소는 어떤 Container 요소(Engine, Host, Context)에서도 나타날 수 있습니다. 해당 요소는 유저의 role과 password, users 데이터베이스를 나타냅니다. 이 요소의 목적은 Container 기반의 인증을 지원하는 것입니다.

`UserDatabaseRealm` 이외에, 다음 realm classes를 이용할 수 있습니다. `JDBCRealm`(JDBC driver를 통한 관계형 데이터 베이스와의 연결), `DataSourceRealm`, `JNDIRealm`, 그리고 `MemoryRealm`(메모리 속에 XML 파일을 올리는 것)

### Valve

Valve는 Container(Context, Host, or Engine) 속에 들어갔을 때, 모든 HTTP requests들을 어플리케이션에 도착하기전에 가로채는 역할을 하는 Interceptor입니다. 이러한 기능은 우리가 특정 어플리케이션으로 향하는 요청을 전처리할 수 있게 해줍니다.

여러가지의 valve의 사용 예시를 보이면

`RemoteAddrValve` valve는 source IP Address를 이용해서 요청을 선택적으로 allow or block할 수 있습니다.

```xml
<Valve className="org.apache.catalina.valves.RemoteAddrValve" block="192\.168.*" />
```

`RemoteHostValve` valve는 remote address filter처럼 동작하지만 client IP address가 대신 client host names를 이용합니다.

```xml
<Valve className="org.apache.catalina.valves.RemoteHostValve" deny=".*badweb\.com"/>
```

`RemoteDumperValve` 는 들어오는 요청의 자세한 내용을 로깅합니다. 디버깅에 용이합니다.

```xml
<Valve className="org.apahce.catalina.valves.RequestDumperValve" />
```

Host Container 속에 포함된 `single sign on` valve는 해당 host의 모든 어플리케이션에 대해 오직 한번의 인증을 요구하는 효과를 줍니다. 해당 벨브가 없다면 유저는 각 어플리케이션을 사용하기 전에 ID, Passowrd를 입력해야 할 것 입니다.

> # Reference
>
> https://www.mulesoft.com/tcat/tomcat-servlet
>
> https://www.tutorialspoint.com/servlets/servlets_overview.htm
>
> https://docs.oracle.com/javaee/7/tutorial/servlets.htm#BNAFD
>
> https://codeburst.io/understanding-java-servlet-architecture-b74f5ea64bf4
>
> https://howtodoinjava.com/tomcat/tomcats-architecture-and-server-xml-configuration-tutorial/
>
> https://pythonq.com/so/servlets/71499
>
> https://stackoverflow.com/questions/6719004/understanding-servlet-container
