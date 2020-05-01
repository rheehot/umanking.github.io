---
layout: post
title: Java Comparable vs Compartor 사용방법
categories: [Java]
date: 2020-03-10 08:18 +0900
---
> 자바 Comparable과 Comparator에 대해서 알아보자.

## 일반적인 정렬
일반적인 primitive 타입 정렬은 java에서 제공하는 메서드를 통해서 쉽게 할 수 있다.
```java
public static void main(final String[] args) {
    final int[] intArray = {1, 5, 3, 7, 8, 3};
    Arrays.sort(intArray);
    System.out.println(Arrays.toString(intArray));

    final List<String> list = Arrays.asList("a", "c", "d", "e", "b", "f");
    Collections.sort(list);
    System.out.println(list);
}
```
결과 
```shell
[1, 3, 3, 5, 7, 8]
[a, b, c, d, e, f]
```

> Arrays.sort(), Collections.sort()의 내부 구현이 어떻게 되어 있는지는 추가로 학습 


## 객체 정렬? 
이번에는 객체를 정렬하려면 어떻게 해야 할까? 그냥 생각해보면 기준이 되는 값이 없기 때문에 판단할 수 없겠구나 라는 생각이 든다.
간단한 User클래스를 만들어 보자.

```java
public class User{

    private final int id;
    private final String name;
    private final int age;

    public User(final int id, final String name, final int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    // getter 생략

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

3명의 유저를 만들고 배열 sort메서드를 호출하면 다음과 같이 `cannot be cast to Comparable` class cast 예외가 발생한다.
```java
public static void main(final String[] args) {
    final User andrew = new User(1, "andrew", 32);
    final User berry = new User(2, "berry", 26);
    final User robert = new User(3, "robert", 28);
    final User[] users = {andrew, berry, robert};

    Arrays.sort(users);

    System.out.println(Arrays.toString(users));
}
```


```shell
Exception in thread "main" java.lang.ClassCastException: com.example.demo.comparator.User cannot be cast to java.lang.Comparable
	at java.util.ComparableTimSort.countRunAndMakeAscending(ComparableTimSort.java:320)
	at java.util.ComparableTimSort.sort(ComparableTimSort.java:188)
	at java.util.Arrays.sort(Arrays.java:1246)
	at com.example.demo.comparator.ComparableTest.main(ComparableTest.java:27)
```


## 방법1 - Comparable 구현
다시 User클래스로 돌아가서, Comparable인터페이스를 구현한다.
```java
public class User implements Comparable {

    private final int id;
    private final String name;
    private final int age;

    // 생략

    @Override
    public int compareTo(Object o) {
        if (o instanceof User) {
            User user = (User) o;
            return this.age - user.age;
        }
        return 0;
    }
```
해당 인터페이스를 구현하면, 반드시 compareTo라는 메서드를 오버라이딩으로 구현해줘야 한다. 메서드 안의 내용은 해당 객체의 어떤 필드를 기준으로 정렬을 할 것인지 구현한다. 나이순으로 정렬 하도록 작성했다. 다시 메인 메서드를 실행시키면 다음과 같다. 

```shell
[User{id=2, name='berry', age=26}, User{id=3, name='robert', age=28}, User{id=1, name='andrew', age=32}]
```
age순으로 오름차순 정렬이 되었다.
하지만, Comparable의 단점은 클래스당 하나의 compareTo 메서드를 오버라이딩 하기 때문에 특정 정렬 기준만을 충족시킨다. 다이나믹하게 여러 가지 비교자를 만들어서 주입할 수 는 없을까? 


## 방법2 - Comparator 구현
Comparator는 Functional Interface로 하나의 추상메서드를 가지고 있다. 그리고 익명 함수 이기도 하다. 결국 람다식으로 표현이 가능하며, 정렬을 할때, 2번째 인자로 해당 Comparator를 넘길 수 있다. 기존의 Comparable 인터페이스 구현 부를 삭제하고 다양한 Comparaotr를 만들어 보자.
```java
public class User {

    private final int id;
    private final String name;
    private final int age;

    public User(final int id, final String name, final int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    // 생략

    public static Comparator<User> nameComparator = (o1, o2) -> o1.name.compareTo(o2.name);

    public static Comparator<User> ageComparator = (o1, o2) -> o1.age - o2.age;
```
User클래스에 nameComparator, ageComparator를 각각 만들고, 람다식을 통해서 작성했다.
실제 사용은 다음과 같이 2번째 인자로 원하는 Comparator를 넘긴다.

```java
public static void main(final String[] args) {
        final User andrew = new User(1, "andrew", 32);
        final User berry = new User(2, "berry", 26);
        final User robert = new User(3, "robert", 28);
        
        final User[] users = {andrew, berry, robert};
        // 나이순으로 정렬
        Arrays.sort(users,  User.ageComparator);
        System.out.println(Arrays.toString(users));

        final List<User> userList = Arrays.asList(andrew, berry, robert);
        // 이름순으로 정렬 
        Collections.sort(userList, User.nameComparator);
        System.out.println(userList);
    }
}
```

```shell
[User{id=2, name='berry', age=26}, User{id=3, name='robert', age=28}, User{id=1, name='andrew', age=32}]
[User{id=1, name='andrew', age=32}, User{id=2, name='berry', age=26}, User{id=3, name='robert', age=28}]
```
나이순으로, 이름순으로 정렬됨을 확인 할 수 있다.

## 더 생각해 볼 것
자주 사용하는 Comparator를 저렇게 클래스안에 넣는 것도 좋은 방법이고, 사실 사용하는 쪽에서 다음과 같이 넘겨줄 수 도 있다.
```java
Arrays.sort(users,  (o1, o2) -> o1.age - o2.age);
```
하지만 요구 사항이 바뀌는 경우를 생각해보자. 나이순이 아니라, 어떤 특정한 조건으로 새롭게 정렬 한다고 했을 때 기존의 ageCompartor로 구현했더라면 이름과 내부 구현만 바꾸면 사용하는 쪽 코드를 전혀 손대지 않아도 된다.
하지만 일일이 클라이언트에서 넘기는 형태였다면?? 모든 관련된 코드를 전부 찾아야 한다. 그래서 첫번째 특정 클래스내에 자주 사용하는 Compartor를 구현해 놓는 게 훨씬 좋다고 생각한다.