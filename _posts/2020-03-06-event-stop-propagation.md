---
layout: post
title: 이벤트 preventDefault와 stopPropagation의 차이
categoires: [Share]
date: 2020-03-06 09:57 +0900
---
자주 나오는데 정확히 몰라서 넘어갔던 event.preventDefault 와 event.stopPropagation에 대해서 알아보자.

`stopPropagation`은 위로 이벤트 버블링되는 것을 막는다. 
`preventDefault` 는 브라우저가 해당 이벤트에 대해 수행하는 것을 막는다. 그러니까 이 친구는 이벤트가 전파 될 수 있다. 

## preventDefault 

```js
$("#but").click(function (event) {
  event.preventDefault()
})
$("#foo").click(function () {
  alert("parent click event fired!")
})
```

```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
<div id="foo">
  <button id="but">button</button>
</div>
```
id가 but인 버튼을 클릭하면 어떤일이 발생하나, event.preventDefault()는 해당 이벤트 수행을 막지만, 버튼을 감싸고 있는 id가 foo인 div태그는 **클릭이벤트가 발생하여(상위로 전파?) 해당 얼럿이 뜨게 된다.**


## stopPropagation
```js
$("#but").click(function (event) {
  event.stopPropagation()
})
$("#foo").click(function () {
  alert("parent click event fired!")
})
```
```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
<div id="foo">
  <button id="but">button</button>
</div>
```
이 친구는 이벤트의 propagation(전파)를 막으므로, **아무일도 일어나지 않는다.**


## 이벤트 버블링, 캡쳐링
From quirksmode.org
```
Event capturing
When you use event capturing

               | |
---------------| |-----------------
| element1     | |                |
|   -----------| |-----------     |
|   |element2  \ /          |     |
|   -------------------------     |
|        Event CAPTURING          |
-----------------------------------
the event handler of element1 fires first, the event handler of element2 fires last.

Event bubbling
When you use event bubbling

               / \
---------------| |-----------------
| element1     | |                |
|   -----------| |-----------     |
|   |element2  | |          |     |
|   -------------------------     |
|        Event BUBBLING           |
-----------------------------------
the event handler of element2 fires first, the event handler of element1 fires last.

Any event taking place in the W3C event model is first captured until it reaches the target element and then bubbles up again.

                 | |  / \
-----------------| |--| |-----------------
| element1       | |  | |                |
|   -------------| |--| |-----------     |
|   |element2    \ /  | |          |     |
|   --------------------------------     |
|        W3C event model                 |
------------------------------------------
```
외국인들은 이벤트가 전달되는 것을 `fire` 한다고 하네. fire in the hole ! 이런 느낌이구나 


## 참고 
- [https://stackoverflow.com/questions/5963669/whats-the-difference-between-event-stoppropagation-and-event-preventdefault](https://stackoverflow.com/questions/5963669/whats-the-difference-between-event-stoppropagation-and-event-preventdefault)