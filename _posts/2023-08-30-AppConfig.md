---
layout: post
title: 2.스프링 컨테이너와 AppConfig
subtitle: Each post also has a subtitle
categories: 스프링_기초
tags: [test]
---


# 스프링 컨테이너의 역할은 무엇인가?


> 스프링 컨테이너는 @Configuration이 붙은 Config의 정보를 사용한다.



이때 스프링 컨테이너는 해당 클래스 내부에 @Bean이 붙은 메서드를

모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다.

그리고 그것을 우리는 **스프링 빈**이라고 부른다.





### 스프링 빈은 @Bean 이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다.


이때 스프링 빈은 applicationContext.getBean() 메서드를 사용해서 찾을 수 있다.



> 다음과 같이 컨테이너를 생성했을 때를 가정해보자.
```
AnnotationConfigApplicationContext ac = new
	  AnnotationConfigApplicationContext(AppConfig.class);
```



#### 1.스프링 컨테이너에서 스프링 빈을 찾는 가장 기본적인 조회 방법**


→ ac.getBean(빈이름, 타입)

→ ac.getBean(타입)

#### 2. 모든 빈 출력하기


→ ac.getBeanDefinitionNames() : 스프링에 등록된 모든 빈 이름을 조회한다.

→ ac.getBean() : 빈 이름으로 빈 객체(인스턴스)를 조회한다.

#### 3. 같은 타입의 빈 출력하기

→ ac.getBeansOfType() 을 사용하면 해당 타입의 모든 빈을 조회할 수 있다.





## 스프링 컨테이너에 하나의 인터페이스에 대해 등록된 빈이 여러개 있는 경우는 어떻게 할 수 있을 까?

예를 들어 어떠한 서비스가 레포지터리를 필요로 하는데, repository를 구현한 구현 클래스가 A,B 모두 있으며, 둘다 component로 등록되어 있다면 어떻게 될까?

보통 사용하는 기능이 바로 **@Primary, @Qualifier이다.**

이에 대한 설명은 다음과 같다.

다음은 생성자 자동주입의 예시이다.

```java
@Autowired
  public OrderServiceImpl(MemberRepository memberRepository,
                      @Qualifier("mainDiscountPolicy") DiscountPolicy
 discountPolicy) {
      this.memberRepository = memberRepository;
      this.discountPolicy = discountPolicy;
}
```

**@Qualifier 정리**1. @Qualifier끼리 매칭
2. 빈 이름 매칭
3. NoSuchBeanDefinitionException 예외 발생

@Primary의 경우, 원하는 컴포넨트에 붙이면 해당 클래스가 우선적으로 주입된다.