---
title: Excution Context (2)
author: boooruim
date: 2024-03-20 00:35:00 +0900
categories: [JS]
tags: [js,excution context, scope, call stack,debugger]
pin: true
math: true
mermaid: true
image:
  path: /assets/img/2024-03-25-excution-context2/Thum.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Responsive rendering of Chirpy theme on multiple devices.
---


<!-- ![img-description](/assets/img/cat.png)
_Image Caption_ -->

<!-- ## Headings

# H1 - heading
{: .mt-4 .mb-0 } -->

<!-- #### H4 - heading
{: data-toc-skip='' .mt-4 } -->
<!-- markdownlint-restore -->

*[**이전 포스팅**](http://localhost:4000/posts/excution-context-1/)에서 이어지는 내용입니다.*

```html
<script>
  n0 = "n0";
  var v0 = "v0";
  let l0 = "l0";
  const c0 = "c0";
  console.log(v0, n0, l0, c0);
  console.log(window.v0, window.n0, window.l0, window.c0);
  function fn2() {
    n2 = "n2";
    console.log(n0, n1, n2);
    var v2 = "v2";
    console.log(v0, v2);
    // console.log(v1)
    let l2 = "l2";
    console.log(l0, l2);
    // console.log(l1);
    const c2 = "c2;";
    console.log(c0, c2);
    // console.log(c1);
  }
  function fn1() {
    n1 = "n1";
    var v1 = "v1";
    let l1 = "l1";
    const c1 = "c1";
    fn2();
  }
  fn1();
  console.log(n2);
  // console.log(v2, l2, c2);
</script>
```

---

**전역 컨텍스트에서의 Scope**는 Global과 Script로 나뉜다. 

![alt text](/assets/img/2024-03-20-excution-context1/Tab1.png)


line5까지 실행을 해보면 `n0`과 `var v0` 선언은 Global Scope, `let l0`과 `const c0`는 Script Scope에 선언이 된다.

이 둘의 차이는 line6~7의 결과를 보면 확인할 수 있다. 
```js
console.log(v0, n0, l0, c0); // "v0" "n0" "l0" "c0"
console.log(window.v0, window.n0, window.l0, window.c0); //"v0" "n0" undefined undefined
```
`n0`과 `var v0`은 전역 객체인 window의 프로퍼티가 되는 반면, let과 const로 선언한 `l0`과 `c0`은 window의 프로퍼티가 되지 않는 차이가 있다.

물론 4개의 변수 전부 전역 실행 컨텍스트에서 선언한 변수이기 때문에 Scope Chaining을 통해 다른 실행 컨텍스트에서 접근이 가능하다!

이제 line28의 `fn1()`의 내부로 들어가보자.

---

![alt text](/assets/img/2024-03-25-excution-context2/fn1-excution-context.png)

위와 같이 Call Stack의 anonymous 위에 fn1이라는 함수 실행 컨텍스트가 쌓인 것을 볼 수가 있다.\
즉 현재 실행 컨텍스트가 fn1이라는 얘기가 된다. 

또한 **함수 실행컨텍스트는 Local이란 Scope**를 추가로 갖는데 지역 스코프라해서 현재 컨텍스트에서만 접근 가능한 식별자의 범위를 의미한다. 즉 다른 실행 컨텍스트에서는 접근을 하지 못한다.

---

![alt text](/assets/img/2024-03-25-excution-context2/fn1.png)

위처럼 line22에 디버깅 커서가 있다는 것을 기준으로 콘솔창에 `v1`을 입력하면 `undefiend`이 출력된다.

아직 `var v1 = "v1"`이라는 선언문이 실행되기도 전인데 에러가 발생하질 않는다.\
다른 프로그래밍 언어에서는 상상도 할 수 없는 일이다!

자바스크립트에서 이것이 가능한 이유는 **호이스팅** 때문이다.


### 호이스팅

> 자바스크립트 엔진이 코드를 실행 후 식별자와 같은 정보를 미리 파악해놓는 것

실행 컨텍스트가 만들어지고 내부의 코드들이 실행되기 전에 자바스크립트 엔진은 현재 실행 컨텍스트를 먼저 스캔한 다음, 식별자들의 정보들을 특정 공간(environmentRecord)에 선언 및 저장해 둔다. 

이로인해 `var v1 = "v1"`코드가 실행되기 전에 `v1`을 출력해도 `"v1"`라는 값이 나오지는 않지만 접근은 가능하다. 하지만 `l1`과`c1`에 접근하면 ReferenceError가 발생한다.

![alt text](/assets/img/2024-03-25-excution-context2/error.png)


이러한 이유 때문에 let과 const으로 선언된 변수들은 호이스팅이 일어나질 않아서 Error가 발생한다고 생각할 수 있는데, **어떤 식별자든 모두 호이스팅이 일어난다.** 다만 `var`은 선언과 동시에 `undefined`로 초기화되는 반면에 `const`와 `let`은 선언만 될 뿐이다.

그래서 위 처럼 전체 코드와 관련없는 `x1`을 출력해보면, 같은 ReferenceError지만 `l1` `c1`의 `Cannot access from debugger`와는 다르게 `x1 is not defined`라고 뜨는 것을 확인할 수 있다.

이는 `l1`과 `c1`이 어딘가에는 선언이 되었지만 값이 아직 할당이 안되었기 때문에 접근을 못한다고 볼 수 있다.

### TDZ(Temporal Dead Zone)

![alt text](/assets/img/2024-03-25-excution-context2/TDZ.png)

let과 const의 경우 위와 같이 선언문 이전에 접근할 수 없는 구역을 TDZ(Temporal Dead Zone)이라고 한다.

---
![alt text](/assets/img/2024-03-25-excution-context2/fn1-excution-context.png)

본론으로 돌아와 Scope를 다시 보면 호이스팅을 통하여 `n1`만 Global Scope에 선언이 되고 나머지 변수들은 Local Scope에 선언이 되는 것을 확인할 수 있다.

분명 **전역 실행 컨텍스트에서의 `var` 선언은** Global Scope로 선언이 되었지만 **함수 실행 컨텍스트에서의 `var` 선언은** Local Scope에 선언되는 차이를 볼 수 있다.

마지막으로 line26의 `fn2()` 내부로 들어가보자.

---
![alt text](/assets/img/2024-03-25-excution-context2/fn2.png)

Call Stack에 fn2 함수 컨텍스트가 쌓이게 되고 호이스팅을 통해 `n2`는 Global Scope에, 나머지 변수들은 Local Scope에 선언이 된다.

그리고 지금, fn1에서 선언했던 `v1`을 콘솔창에 출력해보면 ReferenceError가 난다.

식별자가 존재 또는 접근 가능한 지는 어떻게 판단할까?

### Scope Chaining

전 포스팅에서 언급했듯이 자바스크립트 엔진은 식별자를 찾기위해 Scope Chaining을 함으로써 Local -> Script -> Global Scope 순으로 탐색한다. 결국 `v1`이라는 식별자는 Global Scope 까지 탐색을 해봐도 발견되지 못했기 때문에 ReferenceError가 발생하게 되는 것이다.
 
끝으로 함수들이 종료될 때 마다 Call Stack에서 하나씩 제거되고 글로벌 컨텍스트까지 제거되는 것이 곧 코드가 끝났다라는 의미가 된다.

## 마무리

![alt text](/assets/img/2024-03-25-excution-context2/summary.png)

위는 실행 컨텍스트의 식별자마다 선언되는 Scope를 생활코딩님이 잘 정리를 해논 것이다.

직접 중첩 함수를 짜고서 여러가지 실험을 하면서 느껴가면서 배우면 좋을 것 같다.

결국 반복이 생명이다.

## 참고

<https://www.youtube.com/watch?v=EWfujNzSUmw>