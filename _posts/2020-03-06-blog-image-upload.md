---
layout: post
title: Github 페이지 이미지 업로드 하는 꼼수
categories: [share]
tags: [share]
date: 2020-03-06 12:01 +0900
---

보통 개발자들은 Github 페이지를 통해서 자신만의 개발 사이트를 구축하는데, 퍼블리슁한 [페이지가 1GB를 넘으면 안된다고 한다.](https://help.github.com/en/github/working-with-github-pages/about-github-pages#usage-limits)

그렇다면 보통 일반 HTML 페이지에서 자원을 많이 차지하는 건 당연히 `이미지` 일테니!
이미지 업로드를 망설였거나, 그래서 tistory로 갈아탄 분을 봄. 이만저만 신경써야 할 것이 (나중에 생긴다.)

## 어떻게 ?

너무나 민망한? 방법이지만, 저는 자신의 Repository에 Issue 에서 아무런 이슈를 만들고(그러라고 만든 이슈 탭이 아님에도 불구하고) 거기에 댓글에 이미지들을 첨부합니다.

![](https://user-images.githubusercontent.com/28615416/76046409-31eac980-5fa3-11ea-930d-57b452dc10db.png)

이게 좋았던 점은, 가끔씩 더 예쁜 블로그를 보다보면 테마를 종종 바꾸는데, 그럴경우 이미 작성되어있던 포스팅에 이미지 첨부가 URL이 아닌, 개인 `assets/images` 하위의 디렉토리에 위치한 이미지를 참조한다면 혹시나 path가 맞는지 확인해야 하고 그런 불편함이 있어서 저는 저 방식을 선호합니다!
