---
layout: post
title: URLSearchParam (URL주소로 부터 파라미터 값 얻어오기)
categories: [share]
date: 2020-03-06 09:46 +0900
---

js에서 URL에 있는 파라미터 정보를 얻을려면 어떻게 해야 하나?
URLSearchParams를 구현하는 객체는 for .. of 를 통해서 직접 사용 가능하다.

```js
let queryString = window.location.search;
const urlParams = new URLSearchParams(queryString);
const userId = urlParams.get("userId");
```

## 참고

[https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)
