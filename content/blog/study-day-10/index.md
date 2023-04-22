---
title: Study Day 10 **Filter**, Security, OAuth2
date: "2023-04-22"
description: "**Filter**, Security, OAuth2"
---


#### Filter?

Spring Framework에서 **Filter**는 HTTP 요청 및 응답을 가로채고,  
요청 및 응답을 수정하거나 처리하는 역할을 한다.  
**Filter**는 Servlet API에서 제공되며,  
Spring에서는 **Servlet Filter** 인터페이스를 구현하여 사용한다.

**Filter**는 Spring MVC 프레임워크에서 사용되어 요청 처리 전후에 다양한 작업을 수행할 수 있다.  
예를 들어, 요청의 인코딩을 변경하거나, 인증 및 권한 부여를 수행하거나,  
로깅 및 통계를 수집하는 등의 작업을 수행할 수 있다.

Spring Framework에서 **Filter**를 등록하려면,  
Servlet 기반 애플리케이션에서 등록하는 것과 유사한 방식으로 web.xml 파일에 등록하거나,  
Spring Boot에서는 @Bean 어노테이션을 사용하여 등록할 수 있다.

**Filter**는 **FilterChain** 객체를 통해 여러 개를 연결하여 사용할 수 있다.  
이 때, FilterChain은 **FilterChainProxy** 클래스를 통해 구현되며,  
필터 체인을 만들 때 순서를 지정할 수 있다.

``` java
public class EncodingFilter implements Filter {

    private String encoding;

    @Override
    public void init(FilterConfig FilterConfig) throws ServletException {
        encoding = FilterConfig.getInitParameter("encoding");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        request.setCharacterEncoding(encoding);
        response.setCharacterEncoding(encoding);
        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {
        // 필터 제거 시 수행할 작업
    }
}


@Configuration
public class AppConfig {

    @Bean
    public FilterRegistrationBean<EncodingFilter> encodingFilter() {
        FilterRegistrationBean<EncodingFilter> registration = new FilterRegistrationBean<>();
        registration.setFilter(new EncodingFilter());
        registration.setOrder(1); // 필터 순서
        registration.addUrlPatterns("/*"); // 필터 적용 URL 패턴
        registration.setName("encoding**Filter**"); // 필터 이름
        registration.setInitParameter("encoding", "UTF-8"); // 초기화 파라미터
        return registration;
    }
}
```

### interceptor / Filter 차이점?

Interceptor와 **Filter**는 모두 Spring MVC에서 요청 처리를 가로채고 처리할 수 있는 기능을 제공하는데,  
기능적으로 비슷한 역할을 한다.

![](./img/image.png) 
하지만 Interceptor는 **컨트롤러에 진입하기 전, 후**에 추가적인 작업을 수행하는 반면,  
**Filter**는 **DispatcherServlet에서 처리하기 전, 후**에 요청과 응답에 대한 필터링 작업을 수행한다.

또한 Interceptor는 HandlerInterceptor 인터페이스를 구현하고,  
preHandle, postHandle, afterCompletion 메서드를 제공하여  
각각 컨트롤러에 진입하기 전, 후, 완료 후에 수행할 작업을 구현할 수 있다.

![](./img/image1.png) 
반면, **Filter**는 Servlet Filter 인터페이스를 구현하고  
doFilter 메서드를 구현하여 요청 및 응답을 가로채고 처리한다.  
**Filter**는 Interceptor와 달리 컨트롤러에 진입하기 전에도 수행할 수 있으므로,  로깅, 인증, 인코딩 변경 등 다양한 작업을 수행할 수 있다.

따라서, Interceptor와 **Filter**는 목적이나 사용처에 따라 선택하여 사용할 수 있다.
![](./img/image2.png) 

### Security Filter?


![](./img/image3.png) 

Spring Security는 웹 보안을 제공하기 위해  
Spring Framework에서 제공하는 **Filter**와 유사한 역할을 하는 여러 개의 **Filter**를 제공한다.

**Spring Security의 Filter**는 웹 요청의 인증과 인가를 처리하기 위해 사용되며,   
인증 및 권한 부여 필터, 로그인 필터, 로그아웃 필터, 세션 관리 필터 등 다양한 종류가 있다.

Security **Filter**는 **FilterChainProxy** 클래스를 통해 구현되며,  
각 **Filter**는 **FilterChain**에서 순차적으로 실행된다.  
이 때, 각 **Filter**는 요청 처리를 중단할 수 있으며,  
중단된 경우 해당 요청에 대한 다음 단계의 처리를 하지 않는다

따라서, **Spring Security의 Filter**는 Spring Framework에서 제공하는 일반적인 **Filter**와 비슷한 역할을 하지만,  
보안과 관련된 특정한 작업을 수행하도록 설계되어 있다.



### OAuth2의 Filter Customize?

OAuth2를 구현하기 위해 Spring Security에서 제공하는  
**Filter**를 커스터마이징할 수 있다.  
이는 **Filter**를 커스텀하는 것과 비슷한 개념이다.

Spring Security에서 OAuth2 인증 방식을 사용하려면,  
OAuth2 인증 처리를 위한 **Filter**를 추가해야 한다.  
Spring Security는 OAuth2 인증을 처리하기 위해   
OAuth2AuthenticationProcessingFilter라는 **Filter**를 제공한다.  
이 **Filter**는 OAuth2 토큰을 사용하여 사용자 인증을 수행하고,  
인증 정보를 SecurityContext에 저장한다.

OAuth2AuthenticationProcessingFilter를 커스터마이징하려면,  
해당 **Filter**를 상속받아 필요한 메서드를 오버라이딩하고,  
필터의 동작을 커스터마이징할 수 있다.  
또는 OAuth2AuthenticationProcessingFilter를 직접 사용하지 않고,  
필요한 인증 방식에 맞는 **Filter**를 직접 구현하여 사용할 수도 있다.

따라서, OAuth2를 구현하기 위해 Spring Security에서 제공하는 **Filter**를 커스터마이징하는 것은  
**Filter**를 커스텀하는 것과 비슷한 개념이며,  
**Filter**의 동작을 원하는대로 커스터마이징하여 사용할 수 있다.


#### Token?

가장 널리 사용되는 토큰은 JSON Web Token(JWT)이다.  
JWT는 JSON 객체를 사용하여 정보를 암호화하고 서명하여 인증 정보를 보호한다.  
JWT는 쉽게 전송이 가능하며, 유연성과 확장성이 높아서  
인기 있는 토큰 기반 인증 방식이다.  
또한, JWT는 자체적으로 정보를 암호화하고 서명하므로,  
추가적인 인증 서버나 데이터베이스를 사용하지 않아도 되는 장점이 있다.

또한, OAuth 2.0에서는 **Access Token과 Refresh Token** 두 가지 타입의 토큰을 사용한다.  
**Access Token**은 API 호출을 인증하기 위해 사용되며,  
**Refresh Token**은 Access Token의 만료 시간이 지난 경우  
새로운 Access Token을 발급하기 위해 사용된다.  
OAuth 2.0은 JWT를 포함한 다양한 토큰 기반 인증 방식을 지원한다.

그 외에도, SAML Token, Kerberos Ticket 등 다양한 토큰이 있으며,  
각각의 토큰은 특정한 목적에 따라 사용된다.  
따라서, 사용하는 토큰은 상황에 따라 다르며,  
보안성과 확장성을 고려하여 선택해야 한다.



#### Security OAuth2 Filter Example
``` java
@Configuration
@EnableWebSecurity
public class SecurityConfig implements WebSecurityConfigurer<WebSecurity> {

    @Autowired
    private CustomOAuth2AccessTokenConverter customTokenConverter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/api/**").authenticated()
            .and()
            .addFilterAfter(customOAuth2AuthenticationProcessingFilter(), AbstractPreAuthenticatedProcessingFilter.class)
            .oauth2Login();
    }

    private OAuth2AuthenticationProcessingFilter customOAuth2AuthenticationProcessingFilter() {
        OAuth2AuthenticationProcessingFilter Filter = new OAuth2AuthenticationProcessingFilter();
        Filter.setAuthenticationManager(authenticationManagerBean());
        Filter.setStateless(false);
        Filter.setTokenServices(tokenServices());
        Filter.setAuthenticationSuccessHandler(oauth2AuthenticationSuccessHandler());
        return Filter;
    }

}
```

Security OAuth2 Filter Github  
https://github.com/parkgun6/SpringSecurity-Study/blob/master/src/main/java/org/geon/club/security/Filter/ApiCheckFilter.java