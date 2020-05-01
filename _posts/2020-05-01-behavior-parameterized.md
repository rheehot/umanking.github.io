---
layout: post
title: "Modern Java in Action - #2 동작파리미터화 코드 전달하기"
date: 2020-05-01 15:04 +0900
categories: [java]
tags: [Java, ModernJavaInAction]
# published: false
---

농부가 재고량을 파악하는 애플리케이션이 있다고 생각해봅니다.

빨간색 사과만을 찾는 요구사항이 생겼습니다.

하지만 요구 사항은 늘 변경되기 때문에,

다음날 빨간색 사과 && 무게가 150g 이상인 것들만 필요하다고 요구사항이 변경되었습니다.

이런 다양한 요구 사항을 만족 시키기 위해서 변화는 부분을 추상화로 빼서, 파라미터를 통해서 넘기는 것을 **동작 파라미터화** 입니다.

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
