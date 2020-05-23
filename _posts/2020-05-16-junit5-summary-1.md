---
layout: post
title: "JUnit5 요약 #1"
subtitle: "JUnit5 사용하기"
date: 2020-05-16 11:27 +0900
categories: [JUnit] 
tags: [JUnit]
---
<!-- ![](https://miro.medium.com/max/881/1*J8sjpKQJswCKiPUYVefbgQ.jpeg) -->
> 해당 포스팅은 인프런의 백기선님 강의를 기반으로 요약정리한 내용입니다.!


## JUnit5 개요

-   junit platform : launcher 같은 역할
-   vintage: junit3,4 구현체
-   jupitor: junit5 구현체, 목성(주피터)이 행성의 5번째임



## Junit5 사용하기 

-   Spring Boot 2.2.x 버전대 이상을 사용하면 JUnit5 모듈이 자동으로 들어간다.
-   Spring Boot를 사용하지 않는다면 다음과 같이 의존성을 추가한다.

    ```xml
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.5.2</version>
        <scope>test</scope>
    </dependency>
    ```

## Junit 테스트명 DisplayName

`@DiaplayName("주문 성공 테스트")`  와 같은 형태로 junit 실행후 하단에 표시되는 report 에 한글로 표시된다. 더이상 메서드 이름을 한글로 작성하지 않아도 된다. (junit5) 부터는



## Junit Assertions 

### assertEquals

```java
public static void assertEquals(Object expected, Object actual) {
    AssertEquals.assertEquals(expected, actual);
}
```

크게 중요한건 아니지만, 첫번째 파라미터는 expected 값을, 두번째 파라미터는 실제 값을 넣어주는 게  Junit API를 이해하고 사용하는 사람에게 조금 더 와 닿지 않을까? 

> ✅ assertEquals의 세번째 파라미터값이 String message 도 있지만, Supplier<String> 도 존재한다. 어떤 차이인가?
>
> ![](https://user-images.githubusercontent.com/28615416/82105675-89ea3d00-9757-11ea-80bf-6e7a51c646c3.png)
>
> 해당 메서드는 테스트가 실패하는 케이스에 세번째 파라미터값으로 입력한 message내용이 출력된다.
>
> 그냥 문자열을 입력한 경우에는 매번 해당 연산을 수행한다.(실패하든, 성공하든 상관없이) 
> 하지만 Supplier<String> 으로 람다식으로 해서 넘기게 된다면, 실제 실패하는 경우(lazy evaluation)을 통해서 실행된다. 그래서 문자열이 모든 테스트에 존재하는 경우라면, Supplier를 사용하는 것이 효율적이다!! 

### assertAll

보통 assertEquals()로 검증하는 코드가 여러개로 검증을 하고, 그 중에서 몇개가 실패하더라도 최초의 실패건 하나만 리포팅이 된다. 하지만 assertAll을 통해서 람다식으로(Executable) 모든 검증 코드를 넘기게 되면, 총 몇건이 실패 했는지 알 수 있다.

```java
assertAll(
    () -> assertEquals(StudyStatus.COMPLETE, study.getStudyStatus(), "초기 상태값은 DRAFT 여야 합니다."),
    () -> assertTrue(study.getLimit() > 0, "스터디 참여 제한은 0 보다 커야 합니다.")
);
```

2건다 실패한 것에 대해서 명확하게 리포팅해준다.

![](https://user-images.githubusercontent.com/28615416/82106522-8ad19d80-975c-11ea-937f-3d9d92324c4b.png)



### assertThrows

```java
public Study(int limit) {
    if (limit < 0) {
        throw new IllegalArgumentException("limit 은 0보다는 커야 합니다.");
    }
    this.limit = limit;
}
```

다음과 같이 limit이 0보다 작은 경우 예외가 발생하는 상황에서 다음과 같이 테스트 할 수 있다.

```java
assertThrows(IllegalArgumentException.class, () -> new Study(-10));
```

첫번째 파라미터는 기대되는 exception의 타입, 두번째는 Executable 로 어떤 실행을 했을때를 넘긴다. 

다음과 같이 해당 Exception을 받아서 메세지도 동일한지 테스트 할 수 있다. 

```java
IllegalArgumentException e = assertThrows(IllegalArgumentException.class, () -> new Study(-10));
assertEquals("limit 은 0보다는 커야 합니다.", e.getMessage());
```

### assertTimeout

```java
assertTimeout(Duration.ofMillis(100), () -> {
    new Study(1);
    Thread.sleep(300);
});
```

100 millieSeconds를 기대했지만, 실제 300milieSeconds가 나오기 때문에 위 케이스는 실패한다.



## 조건에 따른 테스트 

#### programming 방법

```java
assumeTrue(System.getenv().get("HOME").equals("/Users/andrew"));
// 아래 로직 실행


```

assumeTrue 파라미터 값 조건에 맞으면 그 다음 로직이 실행된다.

```java
assumingThat(System.getenv().get("HOME").equals("/Users/andrew"), () -> {
    // 실행할 부분
});

assumingThat(System.getenv().get("TEST_ENV").equals("andrew"), () -> {
    // 실행할 부분
});
```

assumingThat를 통해서 코드 블럭으로 지정할 수 있다. 



#### annotaion 기반 

```java
@Test
@EnabledOnJre(JRE.JAVA_8)
@EnabledOnOs(OS.MAC)
@EnabledIfEnvironmentVariable(named = "TEST_ENV", matches = "andrew")
void test(){
    // .. 
}
```

다음과 같이 어노테이션 기반으로도, 원하는 조건들을 설정할 수 있다. 



## Tag & Filtering

@Tag 어노테이션으로 특정 테스트를 그루핑할 수 있다.

```java
@Test
@Tag("fast")
void study_test1() {
    
}

@Test
@Tag("slow")
void study_test2() {

}
```

- 1) intellij 에서 Edit Configuration - Test Kind를 Tags 로 바꾸고, expression 에 `fast` or `slow` 위에서 입력했던 Tag값을 넣고 실행하면 해당 Tagging 되어있는 테스트 케이스만 동작함 

- 2) pom.xml 에서 profile 에 따른 test 실행을 변경하기. 

  - local 환경에서는 fast 붙은것만 테스트 실행
  - production (CI) 환경에서는 fast, slow 전체 테스트 실행

  ```xml
  <profiles>
        <!-- 로컬 환경에서는 fast만 테스트 -->  
          <profile>
              <id>default</id>
              <activation>
                  <activeByDefault>true</activeByDefault>
              </activation>
              <build>
                  <plugins>
                      <plugin>
                          <artifactId>maven-surefire-plugin</artifactId>
                          <configuration>
                              <groups>
                                  fast
                              </groups>
                          </configuration>
                      </plugin>
                  </plugins>
              </build>
          </profile>
      
    <!-- ci 환경에서는 전체 테스트 -->  
          <profile>
              <id>production</id>
              <build>
                  <plugins>
                      <plugin>
                          <artifactId>maven-surefire-plugin</artifactId>
                          <configuration>
                              <groups>
                                  fast | slow
                              </groups>
                          </configuration>
                      </plugin>
                  </plugins>
              </build>
          </profile>
      </profiles>
  ```

  설정후에 `mvn test` 혹은 production 환경에서 테스트 하고 싶다면 `mvn test -Pproduction` 입력



## Custom Tag (Composed Annotation)

반복적으로 작성해야 하는 어노테이션같은 경우, composed (합성)을 통해서 커스텀한 태그를 제공할 수 있다. 위에서 살펴본 fast, slow 로 나눠지는 tag를 반복해서 작성해야 하는 불편함이 있다.

```java
@Retention(RetentionPolicy.SOURCE)
@Target(ElementType.METHOD)
@Test
@Tag("fast")
public @interface FastTest {
}

@Retention(RetentionPolicy.SOURCE)
@Target(ElementType.METHOD)
@Test
@Tag("slow")
public @interface SlowTest {
}
```

다음과 같이 사용할 수 있다.

```java
@FastTest
void study_test1() {
}

@SlowTest
void study_test2() {

}
```



## ParameterizedTest (테스트 반복하기)

파라미터로 넘긴값을 객체에 매핑해서 사용하려면 어떻게 해야 할까? 

```java
@ParameterizedTest
@ValueSource(ints = {10, 20, 30})
void test1(@ConvertWith(StudyConverter.class) Study study) {
    System.out.println(study.getLimit());
}
```

SimpleArgumentConverter를 구현한 StudyConverter 를 작성하고 `@ConvertWith` 어노테이션으로 넘겨준다. 

```java
// single argument
static class StudyConverter extends SimpleArgumentConverter {
    @Override
    protected Object convert(Object source, Class<?> aClass) throws ArgumentConversionException{
        assertEquals(Study.class, aClass, "Can only converter to Study");
        return new Study(Integer.parseInt(source.toString()));
    }
}
```

단일 값인 경우가 아니라 여러개의 파라미터를 갖는 값인 경우에는 어떻게 할까? 

```java
// 생성자
public Study(int limit, String name) {
    this.limit = limit;
    this.name = name;
}
```



@CsvSource를 통해서 `,`  (콤마)를 통해서 해당 값들을 넘겨줄 수 있다. 이번에는 ArgumentsAggregator 를 구현해야 한다.

```java
@ParameterizedTest
@CsvSource(value = {"10, 자바", "20, 스프링"})
void test2(@AggregateWith(StudyAggregator.class) Study study) {
    System.out.println(study);
}
```

다음과 같이 ArgumentsAggregator 를 구현한 StudyAggregator를 @AggregateWith 으로 넘긴다.

```java
// multiple argument - using argument accessor
static class StudyAggregator implements ArgumentsAggregator {
    @Override
    public Object aggregateArguments(ArgumentsAccessor argumentsAccessor, ParameterContext parameterContext) throws ArgumentsAggregationException {
        return new Study(argumentsAccessor.getInteger(0), argumentsAccessor.getString(1));
    }
}
```


## 테스트 순서 

기본 Junit Test는 메서드 실행당 새로운 인스턴스를 만든다. Junit5 에서는 Class당 테스트 인스턴스를 만들 수 있도록 해당 기능을 제공한다. 다음과 같이 클래스 레벨에 작성할 수 있다.

```java
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
```

테스트 순서는 Junit에서 구현한 알고리즘에 따라서 순서가 정해진다. 순서를 명확히 해야 할때는 클래스 레벨에 @TestMethodOrder의 MethodOrderer를 어떤걸로 할지 넘겨준다. 실제 메서드 별로 `@Order` 애노테이션을 달아서 순서를 지정해줄 수 있다.

```java
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
```

> ❗️주의사항, `@Order` 애노테이션은 스프링에서 제공하는 것도 있다. 이것은 빈의 순서를 정의 하는 것이므로, 반드시 junit에서 제공하는 `@Order`어노테이션을 사용해야 한다! 

