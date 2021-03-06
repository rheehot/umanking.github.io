---
layout: post
title: "[Java]  wait와 notify메서드"
date: 2019-08-06 17:31:34
categories: [Java]
tags: [Java]
redirect_from:
  - 2019/08/06/java-object-method/
  - java/java-object-method/
---

> Java Thread wait() , notify(), notifyAll() 메서드에 대해서 알아보자.

쓰레드 클래스의 메서드가 아닌, 자바 최상위 클래스인 Object 클래스의 존재하는 메서드.

| 메서드       | 기능                                                           | 비고                            |
| --------- | ------------------------------------------------------------ | ----------------------------- |
| wait      | lock를 소유한 스레드가 자신의 제어권을 양보하고 WAITING 또는 TIMED_WAITING상태에서 대기 | 호출하는 스레드가 반드시 고유 락을 갖고있다야 한다. |
| notify    | WAIT 상태에 빠져있는 다른 스레드를 다시 RUNNABLE 상태로 변경시키는 역할               | 호출하는 스레드가 반드시 고유 락을 갖고있다야 한다. |
| notifyAll | 잠들어 있던 모든 스레드를 깨운다                                           | 호출하는 스레드가 반드시 고유 락을 갖고있다야 한다. |

호출하는 쪽 스레드가 synchronized 블록내에서 실행(고유락을 획득한) 되지 않으면 IllegalMonitorStateException 이 발생한다.잠든 스레드 하나만 깨우도록 제어하기 어렵기 때문에 보통은 `notifyAll()`을 사용해서 모두 깨우고, 깨어난 스레드들은 락을 획득하기 위해 경쟁한다. 락을 획득한 스레드만이 wait()함수를 리턴시키고 그 다음 로직을 수행한다.
