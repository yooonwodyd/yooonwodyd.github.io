---
layout: post
title: "[Spring] DelegatingFilterProxy와 FilterChainProxy"
categories: 스프링_구현
tags: [spring]
---

> DelegatingFilterProxy와 FilterChainProxy가 무엇인지 설명하기 위해 다음의 작업을 거친다.
>
> 1. 서블릿 필터
> 2. 서블렛 컨테이너
> 3. DelegatingFilterProxy
> 3. FilterChainProxy





## 서블릿 필터
서블릿 필터는 ServletDispatcher에 도달하기 전 데이터를 알맞게 가공하고, 공통의 관심사를 처리하는 역할을 한다.<br>
(*java Servlet Filter is used to intercept the client request and do some pre-processing.*)


이를 통해 서블릿에서 인증(Authentication), 인가(Authorization), 비밀보장(Confidentiality), 데이터 무결성(Data Integrity)과 같은
보안 처리가 가능하다. 따라서 우리가 별도의 보안 처리를 하고 싶다면, 서블릿 필터를 만들어 추가하면 된다.

## 서블릿 컨테이너
이때, 보안을 위해 서블릿필터를 구현한다는 것은 무엇을 의미할까?

서블릿은 서블릿 컨테이너에서 관리되어진다.
**즉, 스프링 컨테이너에서 관리되어지지 않기에 스프링 관련 기술을 사용하지 못한다.**


Spring MVC를 공부해보았다면 이해가 쉬울 것이다. <br>
Spring MVC에서 우리는 비지니스 로직만을 구현하기 위해 서블릿과 컨트롤러를 따로 분리했다.
뿐만 아니라 이러한 방식은 스프링에서 제공하는 다양한 서블릿을 내가 구현하지 않고 쓸 수 있게 만들었다.

그리고 Spring 1.2부터 DelegatingFilterProxy가 나오면서 **서블릿 필터 역시 스프링에서 관리가 가능해졌다.**



## DelegatingFilterProxy 와 FilterChainProxy
> Proxy for a standard Servlet Filter, delegating to a Spring-managed bean that implements the Filter interface.

위는 스프링에서 정의하는 DelegatingFilterProxy이다.
DelegatingFilterProxy는 서블릿 컨테이너에서 관리되어지는 서블릿 필터다.
이름에서 알 수 있듯이 해당 필터는 보안을 직접 처리하지 않고, ApplicationContext에 존재하는 springSecurityFilterChain
을 찾아 책임을 위임한다.

이때 FilterChainProxy는 스프링이 제공하는 많은 필터들을 가지고 있고,
내가 사용하고 싶은 필터를 Bean으로 등록하고 커스텀함으로서 사용할 수 도있다.


## 책임 위임
책임을 위임한다는 말이 와닿지 않을 수 있다.
대표적인 서블릿 컨테이너인 톰캣을 예로 들어보자.
<br>
스프링부트의 경우 내장톰캣을 가지고 있기에 DelegatingFilterProxy 없이 FilterChain에서 필터를 등록할 수 있다.
하지만 내장톰캣이 없다면 어떻게 해야할까?

<br>

SpringContainer를 참조하는 DistpacherServlet을 서블릿 컨테이너에 등록한 뒤,
스프링MVC는 DelegatingFilterProxy를 xml에 추가함으로서 SpringContainer에 있는 빈을 참조할 수 있다.

이때 DelegatingFilterProxy는 Root Application Context를 통해 FilterChainProxy를 참조하는데,
root Application Context는 최상단에 있는 context로서
서로 다른 서블릿 컨텍스트에서 공유해야하는 Bean들을 등록해놓고 사용할 수 있기 때문이다.










