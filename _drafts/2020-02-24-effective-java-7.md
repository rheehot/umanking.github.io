---
layout: post
title: "[이펙티브 자바] 아이템7.다 쓴 객체 참조를 해제하라"
description: 이펙티브 자바, 아이템7.다 쓴 객체 참조를 해제하라
categories: [effective java]
tags: [effective java]
date: 2020-02-24 20:27 +0900
---

![effective java image](https://user-images.githubusercontent.com/28615416/75598228-81ca1c00-5add-11ea-9319-e949af4e07cd.png){:.postImage}

<!-- TOC -->

- [아이템7. 다 쓴 객체 참조를 해제하라](#아이템7-다-쓴-객체-참조를-해제하라)
  - [해법?](#해법)
  - [그렇다면 매번 null 처리하나?](#그렇다면-매번-null-처리하나)
  - [Best방법](#best방법)
  - [null 처리는 언제 하나? 메모리 누수의 원인 3가지](#null-처리는-언제-하나-메모리-누수의-원인-3가지)
  - [핵심정리](#핵심정리)
  - [추가학습](#추가학습)

<!-- /TOC -->

# 아이템7. 다 쓴 객체 참조를 해제하라

자바 처럼 가비지 컬렉터를 갖춘 언어로 넘어오면 프로그래머의 삶이 훨씬 평안해진다.
다 쓴 객체를 알아서 회수해 가니 말이다. 그래서 자칫 메모리 관리에 더 이상 신경쓰지 않아도 된다고 오해 할 수 있다. 절대 아니다.

## 해법?

해법은 간단하다. 해당 참조를 다 썼을 때 null(참조해제)하면 된다.

## 그렇다면 매번 null 처리하나?

그럴 필요도 없고, 바람직하지도 않다. 그리고 프로그램을 필요 이상으로 지저분하게 만든다.
**객체 참조를 null 처리하는 일은 예외적인 경우여야 한다.**

## Best방법

가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것이다.
여러분이 변수의 범위를 최소가 되게 정의했다면(아이템57) 이 일은 자연스럽게 이뤄진다.

> 아이템57, 지역변수의 범위를 최소화 하라.

## null 처리는 언제 하나? 메모리 누수의 원인 3가지

### 1. 자기 메모리를 직접 관리 하는 클래스 - 메모리 누수의 주의해야 함

원소를 다 사용한 즉시, 그 원소가 참조한 객체들을 다 null 처리해야 한다.

### 2. 캐시 - 메모리 누수의 주의해야 함

객체 참조를 캐시에 넣고, 이 사실을 잊은 채 한참 그냥 놔두는 일이다.

### 2-1. 해법1

캐시 외부에서 키(key)를 참조하는 동안만(값이 아니라) 엔트리가 살아 있는 캐시가 필요한 상황이라면 `WeakHashMap`을 사용해 캐시를 만들자.

### 2-2. 해법2

캐시를 만들때 보통은 캐시 엔트리의 유효 기간을 정확히 정의하기가 어렵기 때문에
시간이 지날수록 엔트리의 가치를 떨어뜨리는 방식을 흔히 사용한다.
이런 방식에서는 쓰지 않는 엔트리를 이따금 청소해줘야 한다.
(Scheduled threadPoolExecutor 같은) 백그라운드 스레드를 활용하거나 캐시에 새 엔트리를 추가할 때 부수 작업으로 수행하는 방법이 있다.

`LinkedHashMap`은 `revmoeEldestEntry` 메서드를 써서 후자의 방식으로 처리한다.
더 복잡한 캐시를 만들고 싶다면 java.lang.ref 패키지를 직접 활용해야 한다.

### 3. 리스너, 콜백

리스너, 콜백이라 부르는 것이다. 클라이언트가 콜백을 등록만 하고 명확히 해지 하지 않는다면, 뭔가 조치해주지 않는 한 콜백은 계속 쌓여 간다.
이럴때 콜백을 약한참조(weak reference)로 저장하면 GC가 즉시 수거해 간다. 예를 들어 `WeakHashMap`에 키로 저장하면된다.

## 핵심정리

메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다.
이런 누수는 철저한 코드리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견할 수 있따.
그래서 이런 종류의 문제는 `예방법`을 익혀두는 것이 매우 중요함

## 추가학습

- [WeakHashMap](https://docs.oracle.com/javase/8/docs/api/java/util/WeakHashMap.html)
- [LinkedHashMap](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedHashMap.html)
- [removeEldestEntry](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedHashMap.html#removeEldestEntry-java.util.Map.Entry-)
- [Heap profiler](https://dzone.com/articles/java-memory-model-simplified)
