---
layout: post
title: "Modern Java in Action - #2 동작파리미터화 코드 전달하기"
date: 2020-05-01 15:04 +0900
categories: [java]
tags: [java]
published: false
---

변화하는 요구사항은 소프트웨어 엔지니어링에서 피할 수 없는 문제다. 예를 들어 농부가 재고목록을 조사를 쉽게 할 수 있도록 돕는 애플리케이션이 있다고 가정하자. 농부는 이렇게 말할 것이다. "녹색 사과를 모두 찾고 싶어요" 또, 하룻밤을 자고 일어났더니 '150그램 이상인 사과를 모두 찾고 싶어요' 또 하룻밤 자고 일어났더니 '150그램 이상이면서 녹색인 사과를 찾을 수 있으면 좋겠네요' 라고 말한다.

이렇게 시시각각 변하는 사용자 요구사항에 어떻게 대응해야 할까?

**동작 파라미터화를 이용하면 자주 바뀌는 요구사항에 요과적으로 대응 할 수 있다.**
동작 파라미터화란 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미한다. 이 코드 블록은 나중에 프로그램에서 호출한다.
즉, 코드 블록의 실행은 나중으로 미뤄진다.

### 첫번째 시도: 녹색 사과 필터링

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (GREEN.equals(apple.getColor())) {
            result.add(apple);
        }
    }
    return result;
}
```

```java
public static List<Apple> filterByColorAndWeight(List<Apple> list, Color color, int standardWeight) {
        List<Apple> result = new ArrayList<>();

        for (Apple apple : list) {
            if (apple.getColor().equals(color)) {
                if (apple.getWeight() >= standardWeight) {
                    result.add(apple);
                }
            }
        }
        return result;
    }

public static void main(String[] args){
    // 사용하는 코드
    List<Apple> redApple = filterByColorAndWeight(inventories, Color.RED, 150);
}
```

## 지금까지 상황

지금까지의 상황은 늘 비즈니스 세계에서 일어납니다. 요구사항이 지속적으로 변경되고, 기존의 레거시를 건드리지 않고(테스트 케이스가 존재하지 않는다면) 파라미터 하나 추가해서 중복된 로직이 발생하는 메서드가 여기저기 생겨납니다. 지금 상황에서 어떻게 할 수 있을까요?
파라미터에 넘기는 조건들(Apple의 속성값)을 추상화 시켜서, 런타임에 주입하는 `전략패턴`을 활용해서 조건(함수)를 통째로 넘길 수 있습니다. 이를 `동작 파리미터화` 라고 합니다.
