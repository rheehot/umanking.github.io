---
layout: post
title: "UTF-8, URL 인코딩?"
categories: [Share]
date: 2020-03-06 11:06 +0900
---
## 왜 URL을 인코딩 하는가? 
우선 URL encode에 대해서 알아보자. 자바에서는 URLEncoder.encode() 메서드를 통해서 해당 URL을 인코딩 할 수 있다. 그럼 왜 하는가? 
URL에는 ASCII로만 표현되기 때문에 한글, 특수 문자와 같은 것은 ASCII로 표현할 수 없다 
그렇기 때문에 인코딩을 통해서 브라우저가 읽을 수 있게 ASCII 형태로 변환 한다고 생각하면 된다.

| **Character** | From Windows-1252 | From UTF-8 |
| ------------- | ----------------- | ---------- |
| space         | %20               | %20        |
| !             | %21               | %21        |
| "             | %22               | %22        |
| #             | %23               | %23        |
| $             | %24               | %24        |
| %             | %25               | %25        |
| &             | %26               | %26        |
| '             | %27               | %27        |
| (             | %28               | %28        |
| )             | %29               | %29        |
| *             | %2A               | %2A        |
| +             | %2B               | %2B        |
| ,             | %2C               | %2C        |
| -             | %2D               | %2D        |
| .             | %2E               | %2E        |
| /             | %2F               | %2F        |




## UTF-8(유니코드) 이란? 
전세계의 다양한 문자들을 표현할 수 있도록 만들어 진것이 유니코드이고, 그 중에서 가장 힘을 얻은 것이 UTF-8이고 세계적인 표준이 되었다.


## 원래는 이것때문에 포스팅 한건데..
코드에서 보통 enc할때 보통 다음과 같이 아무렇지 않게 스트링 문자열 `"UTF-8"`을 사용한다.
비교적 짧아서 오타는 쉽게 판별할 수 있지만, 코드 상에 문자열이 들어가 있는 것이 좋은지 다시 한 번 고민해보자! 
 `java.nio`에서 이미 정의한 `StandardCharsets.UTF_8.name()` 이놈을 사용하자! 

## 참조
- [https://dololak.tistory.com/18](https://dololak.tistory.com/18)
- [https://www.w3schools.com/tags/ref_urlencode.asp](https://www.w3schools.com/tags/ref_urlencode.asp)
- [https://m.blog.naver.com/PostView.nhn?blogId=sangnam18&logNo=110092780243&proxyReferer=https%3A%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=sangnam18&logNo=110092780243&proxyReferer=https%3A%2F%2Fwww.google.com%2F)