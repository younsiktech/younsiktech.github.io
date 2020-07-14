---
layout: post
title:  "스프링 - 인코딩 - UTF-8 설정"
date:   2020-06-18 14:11:43
categories: spring
tags: spring encoding utf8
---

#### 한글을 올바르게 표현하는 방법

GET와 POST 방식이 다르다.

- GET
  - Reason
    - 데이터가 GET방식에서는 요청정보 Header의 URI에 포함되어 전달된다.
    - 서블릿의 영역 밖에 존재한다.
    - URI에 대해 인코딩 처리 작업
  - Solution
    - 톰캣 server.xml에서 utf-8 설정
- POST
  - Reason
    - 데이터가 POST방식에서는 요청정보 Body에 포함되어 전달된다.
    - 서블릿에서 어느 정도 컨트롤이 가능하다.
  - Solution
    - CharacterEncodingFilter에서 UTF-8 설정
    - JSP 파일에 UTF-8 설정 + 서블릿 UTF-8 설정

#### Html 설정 (필수)

``` html
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Home</title>
</head>
```

- head 태그 영역을 설정한다.
  - 최신 IDE(통합 개발 환경)를 사용한다면 기본적인 인코딩 방식은 UTF-8로 설정이 되어있다.
  - 하지만 Windows 운영체제는 기본 인코딩 값으로 여전히 euc-kr방식을 사용하기 때문에, 해당 페이지의 인코딩 방식(utf-8)이 무엇인지 브라우저가 알 수 있게 선언해야 한다.
- HTML5의 경우

``` html
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
```

- XHTML의 경우

``` html
<meta charset="utf-8">
```

#### Tomcat server.xml 설정 (필수)

``` xml
<Connector
    connectionTimeout="20000"
    port="8080"
    protocol="HTTP/1.1"
    redirectPort="8443"
    URIEncoding="UTF-8"
/>

<Connector
    port="8009"
    protocol="AJP/1.3"
    redirectPort="8443"
    URIEncoding="UTF-8"
/>
```

GET 방식으로 전달된 질의 문자열들은 URI에 포함되어 전달되기 때문에 URI에 대해 인코딩 처리 작업을 해야한다.

서버마다 기본적으로 URI 인코딩 문자코드가 정해져 있으며 톰캣8 버전에서는 UTF-8 문자코드가 기본값으로 적용된다.

서버에서 직접 URI를 UTF-8로 인코딩하는 과정이 필요하므로 Servers(아파치 톰캣)의 해당 프로젝트 config 폴더 > server.xml 파일 > URIEncoding="UTF-8" property를 추가한다.

#### 스프링 WebApplicationInitializer 구현하여 설정하는 경우 (선택 1)

``` java
public class MyWebAppInitializer implements WebApplicationInitializer {
     @Override
     public void onStartup(ServletContext servletContext) throws ServletException {
           FilterRegistration charEncodingFilterReg = servletContext.addFilter("CharacterEncodingFilter", CharacterEncodingFilter.class);
           charEncodingFilterReg.setInitParameter("encoding", "UTF-8");
           charEncodingFilterReg.setInitParameter("forceEncoding", "true");
           charEncodingFilterReg.addMappingForUrlPatterns(null, true, "/*");
     }
}

```

서블릿 3.0에서는 web.xml 없이 웹 애플리케이션 설정을 초기화하는 기능을 제공하는데 스프링은 해당 기능을 사용하는 WebApplicationInitializer 인터페이스를 이용하여 초기화할 때의 동작을 정의할 수 있다.

#### 스프링 web.xml 설정하는 경우 (선택 2)

브라우저 —> 요청정보 Body 데이터 —UTF8 인코딩 —> Java 단에서의 데이터 처리가 필요하다.

- 이유
  - CharacterEncodingFilter
    - HTTP상에서 주고 받는 데이터의 헤더값을 UTF-8로 인코딩
  - Servlet에서의 request.setCharacterEncoding("utf-8");
    - POST로 인코딩 데이터를 받는 Servlet에서의 request.setCharacterEncoding("utf-8"); 처리와 동일한 기능을 수행한다.
    - 즉, 이 Filter를 설정하면 POST 요청을 보내는(Submit) 모든 Controller(Servlet) 마다 request.setCharacterEncoding("utf-8"); 내용을 추가하지 않아도 된다.
  - 또한 DB에서 불러올 한글 데이터들의 한글 깨짐 현상을 해결한다.
- 해결법
  - 해당 filter를 매핑할 때 모든 URL에 대해 인코딩될 수 있도록 /*와 같이 url-pattern을 설정한다.
  - 주의!) 
    - Spring Security 설정이 있는 경우, 한글 필터 설정이 springSecurityFilterChain 앞에 위치해야 한다.

``` xml
<filter> 
    <filter-name>encodingFilter</filter-name> 
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class> 
    <init-param> 
    <param-name>encoding</param-name> 
    <param-value>UTF-8</param-value> 
    </init-param> 
    <init-param> 
    <param-name>forceEncoding</param-name> 
    <param-value>true</param-value> 
    </init-param> 
</filter> 
<filter-mapping> 
    <filter-name>encodingFilter</filter-name> 
    <url-pattern>/*</url-pattern> 
</filter-mapping> 
```

#### JSP 파일 설정 + 서블릿 설정

``` jsp
<%@
    page contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"
    language="java"
%>
<% request.setCharacterEncoding("UTF-8");%>
```

역할
- 응답정보 Body 데이터(JSP 내의 Java 코드 + HTML) —UTF8 인코딩 —> 브라우저
  - JSP의 인코딩 방식이 무엇인지 알 수 있게 선언하는 것
    - contentType: 서버에서 생성될 HTML의 charset에 대한 정보
    - pageEncoding: JSP 내의 Java 코드에 대한 charset에 대한 정보
  - jsp 파일에서의 한글 깨짐이 해결된다.
- Servlet에서의 response.setContentType("text/html;charset=UTF-8"); 처리와 유사한 기능을 수행한다.
  - dispatcher-servlet.xml에서의 설정은 Servlet 단에서의 설정을 의미한다.
  - jsp 파일 상의 page 설정은 JSP 단에서의 설정을 의미한다.
    - JSP 디폴트 contentType : ISO-8859-1
  - 아무리 Servlet에서 response.setContentType 결정해서 보내더라도 jsp page 자체의 contentType은 jsp spec에서 결정되므로 직접 기술해주지 않으면 ISO-8859-1로 설정된다. Servlet 단에서의 설정은 JSP가 아닌 텍스트 리턴 시에만 이용된다.
  - 따라서, 직접 .jsp page에 기술하는 것이 좋다.

``` java
@WebServlet("/loginServlet")
public class LoginServlet extends HttpServlet {
    // ... 생략
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        /* 요청정보 Body에 있는 문자열들을 인자값으로 지정한 문자코드로 인코딩한다. */
        request.setCharacterEncoding("UTF-8");
        //getParameter는 중복되지 않고 유일하게 하나만 넘어올 떄 사용된다.
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        /* 응답정보 문자열들을 인자값으로 지정한 문자코드로 인코딩한다.  */
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String htmlResponse = "<html>";
        htmlResponse += "<head><title>Query 문자열 한글 테스트</title></head>";
        htmlResponse += "<h2> your name is " + username + "<br/>";
        htmlResponse += "<h2> your password is " + password + "<br/>";
        out.println(htmlResponse);
    }
}
```

- request.setCharacterEncoding() 메서드 역할
  - 브라우저 —> 요청정보 Body 데이터 —UTF8 인코딩—> Java 단에서의 데이터 처리
    - 스프링 web.xml에 utf-8 설정하는 과정에 해당한다.
  - 한글 처리를 해주는 메서드는 HttpServletRequest의 상위 객체인 ServletRequest에서 제공하는 setCharacterEncoding() 메서드이다.
  - setCharacterEncoding(“UTF-8”) 메서드는 클라이언트가 전달한 요청정보 Body에 있는 데이터(문자열)들을 메서드 인자값으로 지정한 문자코드(UTF-8)로 인코딩해준다.
- request.setCharacterEncoding() 메서드 사용
  - Java 단에서 데이터를 받아오기 전((getParameter() 메서드)에 인코딩을 처리한다.
  - 즉, getParameter() 메서드 위에서 사용해야 한다

    ``` java
    request.setCharacterEncoding("UTF-8");
    request.getParameter("param");
    ```

- response.setContentType() 메서드 역할
  - 응답정보 Body 데이터(Text 데이터) —UTF8 인코딩—> 브라우저
    - 2번 [POST 방식] .jsp 파일에 utf-8 설정 과정에 해당한다.
  - response.setContentType(“text/html;charset=UTF-8”) 메서드는 클라이언트에 전달할 응답정보 Body에 있는 데이터(문자열)들을 메서드 인자값으로 지정한 문자코드(UTF-8)로 인코딩해준다.
- response.setContentType() 메서드 사용
  - html을 출력하는 메서드 위에서 사용해야 한다.

    ``` java
    response.setContentType("text/html;charset=UTF-8");
    ```
