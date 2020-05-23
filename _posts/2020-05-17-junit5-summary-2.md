---
layout: post
title: "JUnit5 요약 #2"
date: 2020-05-17 12:06 +0900
categories: [JUnit] 
tags: [JUnit]
---


## JUnit 확장하기 

- junit4 확장은 Runner, TestRule, MethodRule,.. 
- junit5 확장은 ExtensionWith 단 하나만 존재함

> 일반 테스트에서 특정 시간(1000 millies) 을 넘으면 @SlowTest 어노테이션을 붙이도록 만들어라

다음과 같이 BeforeTestExecutionCallback, AfterTestExecutionCallback 인터페이스를 구현한다.

```java
public class FindSlowTestExecution implements BeforeTestExecutionCallback, AfterTestExecutionCallback {    
    @Override
    public void beforeTestExecution(ExtensionContext extensionContext) {
    }

    @Override
    public void afterTestExecution(ExtensionContext extensionContext) {
    }
}
```

ExtensionContext.Store 를 만들기 위해서 해당 context에서 테스트 클래스 네임, 테스트 메서드 네임정보를 넘긴다. 
store에 시작 시간 정보를 기록하고, afterTestExecution 메서드에서 시간기록 차이(duration) 을 구한다. 그 차이값이 1000 millies 를 넘게 되면 로그를 찍는다.

```java
private static final long THRESHOLD = 1000L;

@Override
public void beforeTestExecution(ExtensionContext extensionContext) {
    String className = extensionContext.getRequiredTestClass().getName();
    String methodName = extensionContext.getRequiredTestMethod().getName();
    ExtensionContext.Store store = extensionContext.getStore(ExtensionContext.Namespace.create(className, methodName));
    store.put("START_TIME", System.currentTimeMillis());
}

@Override
public void afterTestExecution(ExtensionContext extensionContext) {
    String className = extensionContext.getRequiredTestClass().getName();
    String methodName = extensionContext.getRequiredTestMethod().getName();
    ExtensionContext.Store store = extensionContext.getStore(ExtensionContext.Namespace.create(className, methodName));
    Long startTime = store.remove("START_TIME", long.class);
    long duration = System.currentTimeMillis() - startTime;
    if (duration > THRESHOLD) {
        System.out.printf("Please consider mark method [%s] with @SlowTest\n",
                          extensionContext.getRequiredTestMethod().getName());
    }
}
```

사용하는 방법은 다음과 같다. 클래스 레벨에 선언한다.

```java
@ExtendWith(FindSlowTestExecution.class)
class StudyTest {
    
}
```

지금까지 살펴본 방법은 확장팩을 선언적으로 등록하는 방법이었다. 하지만 선언적으로 확장팩을 등록하는 방법은 이미 등록한 클래스를 수정하기가 어렵다. 종속적이다. 다음에는 프로그래밍적인 방법을 통해서 THRESHOLD 값을 어떻게 변경할 수 있는지 알아보자! 

```java
public class FindSlowTestExecution implements BeforeTestExecutionCallback, AfterTestExecutionCallback {
    
    private final long THRESHOLD;
	// 생성자 주입
    public FindSlowTestExecution(long THRESHOLD) {
        this.THRESHOLD = THRESHOLD;
    }
```

실제 사용하는 것은 @RegisterExtension으로 해당 클래스를 생성하는 방법이다. 

```java
class StudyTest {

    @RegisterExtension
    static FindSlowTestExecution findSlowTestExecution = new FindSlowTestExecution(1000);
    
}
```



## Junit4 -> Junit5 마이그레이션 

springboot 프로젝트 2.x.x 시작하게 되면 기본으로 junit5가 들어가고, junit-vintage-engine이 exlude 된다. 

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

vinatage 엔진은 junit 4의 구현체이고, jupitor가 junit5 구현체이다. 마이그레이션을 할 때는 exclude 된 junit-vintage-engine을 삭제 하면 junit4 기반으로 테스트를 할수도 있고, 기존의 작성된 junit4 기반의 테스트도 돌아간다. 하지만 spring에서 100% migration을 지원하지 않는다. 가령 Rule 관련된 것은 지원하지 않기 때문에 `junit-jupiter-migrationsuppor` 디펜던시를 추가해줘야 한다. 

