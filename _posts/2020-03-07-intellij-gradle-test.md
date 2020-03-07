---
layout: post
title: "IntelliJ, Gradle 테스트가 느릴때"
categories: [Share]
date: 2020-03-07 11:28 +0900
---
Gradle로 테스트 케이스를 작성하는데, cleanTest, CompileJava ... 와 같이 여러개의 task를 실행해서 테스트 케이스 동작하는 게 느릴때 해결하는 방법에 대해서 알아보자.

![](https://user-images.githubusercontent.com/28615416/76135091-26f96d00-6067-11ea-8d54-24af3238cf11.png)

아주 간단한 테스트 케이스이고, Spring 전체 빈을 만들어서 주입하는 경우가 아니라, 정말 `유닛테스트` 이다.
당연히 매우 빠르게 동작하리라 기대했지만, 결과는 다음과 같이 여러가지 `task` 를 거친다.

![](https://user-images.githubusercontent.com/28615416/76135199-10074a80-6068-11ea-80b5-af6358a70fbe.png)


## 해결방법

![](https://user-images.githubusercontent.com/28615416/76135226-5492e600-6068-11ea-9f96-922bc2a43e31.png)

해결방법은 `설정` - `Build Tools` - `Gradle` - `Run tests using` 부분이 Gradle(Default) 되어 있을 것이다. 이 부분을 `IntelliJ IDEA` 로 변경하고 실행하면 다음과 같이 매우 빠르게 테스트라 동작한다.

![](https://user-images.githubusercontent.com/28615416/76135200-13023b00-6068-11ea-89a7-ea090c7357b9.png)