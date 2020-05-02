---
layout: post
title: "[이펙티브 자바 3판] 아이템20.추상 클래스보다는 인터페이스를 우선하라"
categories: [effective java]
tags: [effective java]
date: 2020-03-03 19:59 +0900
---

![effective java image](https://user-images.githubusercontent.com/28615416/75598228-81ca1c00-5add-11ea-9319-e949af4e07cd.png){:.postImage}

자바가 제공하는 다중 구현 메커니즘은 `인터페이스와` `추상 클래스` 두가지다. 두 메커니즘 모두 인스턴스 메서드를 구현 형태로 제공할 수 있다. 둘의 가장 큰 차이는 추상은 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다는 점이다. 반면 인터페이스가 선언한 메서드를 모두 정의하고 그 일반규약을 잘 지킨 클래스라면 다른 어떤 클래스를 상속했든 같은 타입으로 취급된다.

인터페이스는 믹스인 정의에 안성맞춤이다.

> 믹스인이란 클래스가 구현할 수 있는 타입으로 믹스인을 구현한 클래스에 원래의 '주된 타입'외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 준다.
> 예컨대 Comparable은 자신을 구현한 클래스의 인스턴스들끼리는 순서를 정할 수 있다고 선언하는 믹스인 인터페이스다. 이처럼 대상 타입의 주된 기능에 선택적 기능을 '혼합'한다고 해서 믹스인이라 부른다.

인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있다. 타입을 계층적으로 정의하면 수많은 개념을 구조적으로 잘 표현할 수 있지만, 현실은 계층을 엄격하게 구분하기 힘들다. 예를 들면 Singer 인터페이스 Songwriter 인터페이스

```java
public interface Singer {
    AudioClip sing(Song s);
}
```

```java
public interface Songwriter {
    Song compose(int chartPosition);
}
```

이 코드 처럼 타입을 인터페이스로 정의하면 가수겸 노래 작곡하는 새로운 메서드를 추가한 제 3의 인터페이스를 정의할 수 도 있다.

```java
public interface SingerSongwriter extends Singer, Songwriter {
    AudioClip strum();
    void actSensitive();
}
```

이렇게 만들어둔 인터페이스가 결정적인 도움을 줄 수도 있다. 만약 같은 구조를 클래스로 만들려면 가능한 조합 전부를 각각의 클래스로 정의한 고도비만 계층 구조가 만들어질 것이다. 속성이 n개면 지원해야할 조합의 수는 2^n개가 된다. 흔히 조합 폭발이라 부른다. 거대한 클래스 계층구조에는 공통 기능을 정의해놓은 타입이 없으니, 자칫 매개변수 타입만 다른 메서드들을 수없이 많이 가진 거대한 클래스를 낳을 수 있다.

래퍼클래스 관용구와 함께 사용하면 인터페이스는 기능을 향상 시키고 안전하고 강력한 수단이 된다.

인터페이스의 메서드 중 구현 방법이 명백한 것이 있다면, 그 구현을 디폴드 메서드로 제공해 프로그래머들이 구현 하면 된다.
디폴트 메서드에도 제약이 있다. equals와 hashCode같은 Object의 메서드를 정의하고 있지만 이들은 디폴트 메서드로 제공해서는 안된다.

## 골격구현 클래스

한편, 인터페이스와 추상 골격 구현 클래스를 함께 제공하는 식으로 인터페이스와 추상 클래스의 장점을 모두 취하는 방법도 있다. 인터페이스로 타입을 정의하고, 필요하면 디폴트 메서드 몇개도 함께 제공한다. 그리고 골격 구현 클래스는 나머지 메서드들까지 구현한다. 이렇게 해두면 단순히 골격 구현을 확장하는 것만으로 이 인터페이스를 구현하는 데 필요한 일이 대부분 완료된다. 바로 템플릿 메서드 패턴이다.

관례상 인터페이스 이름이 Interface라면 그 골격 구현 클래스의 이름은 AbstractInterface로 짓는다. 좋은 예로 컬렉션 프레임워크의 AbstractCollection, AbstractSet, 등등이 각각의 바로 핵심 컬렉션 인터페이스의 골격 구현이다.

예를 보자. 다음 코드는 완벽히 동작하는 List구현체를 반환하는 저적 팩터리 메서드로, AbstractList 골격 구현으로 활용했다.

```java
static List<Integer> intArrayAsList(int[] a){
    Objects.requiredNonNull(a);

    return new AbstractList<Integer>(){
        @Override public Integer get(int i){
            return a[i]
        }

        @Override public Integer set(int i, Integer val){
            int oldVal = a[i];
            a[i] = val;
            return oldVal;
        }

        @Override public int size(){
            return a.length;
        }
    }
}
```

골격 구현 클래스의 아름다움은 추상클래스 처럼 구현을 도와주는 동시에 추상 클래스로 타입을 정의할 때 따라오는 심각한 제약에서 자유롭다는 점에 있다.

## 골격 구현 작성

골격 구현 작성은(지루하지만) 상대적으로 쉽다. 가장 먼저 인터페이스를 잘 살펴 다른 메서드들의 구현에 사용되는 기반 메서드들을 선정한다. 이 기반 메서드들은 골격 구현에서는 추상 메서드가 될 것이다. 그 다음으로 기반 메서드들을 사용해 직접 구현할 수 있는 메서드를 모두 디폴트 메서드로 제공한다. (단, equals 와 hashCode 같은 Object메서드는 디폴트 메서드로 제공하면 안된다는 사실을 항상 유념하자) 만약 인터페이스의 메서드 모두가 기반 메서드와 디폴트 메서드가 된다면 골격 구현 클래스를 별도로 만들 이유는 없다. 기반 메서드나 디폴트 메서드로 만들지 못한 메서드가 남아 있다면, 이 인터페이스를 구현하는 골격 구현 클래스를 하나 만들어 남은 메서드들을 작성해 넣는다. 골격 구현 클래스에는 필요하면 public이 아닌 필드와 메서드를 추가 해도 된다.

## 골격 구현 예

간단한 예로 Map.Entry 인터페이스를 살펴보자. getKey, getValue는 확실히 기반 메서드이며, 선택적으로 setValue도 포함할 수 있다. 이 인터페이스는 equals 와 hashCode의 동작 방식도 정의해놨다. Object 메서드들은 디폴트 메서드로 제공해서는 안되므로, 해당 메서드들은 모두 골격 구현 클래스에 구현한다.

```java
public abstract class AbstractMapEntry<K, V> implements Map.Entry<K,V>{

    // 변경 가능한 엔트리는 이 메서드를 반드시 재정의해야 한다.
    @Override public V setValue(V value){
        throw new UnsupportedOperationException();
    }

    // 생략.....
    // equals, hashCdoe, toString 구현
}
```

## 핵심 정리

일반적으로 다중 구현용 타입으로 인터페이스가 가장 적합하다. 복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 고려해보자. 골격 구현은 '가능한 한' 인터페이스의 디폴트 메서드로 제공하여 그 인터페이스를 구현한 모든 곳에서 활용하는 것이 좋다. '가능한 한'이라고 한 이유는 인터페이스에 걸려 있는 구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문이다.
