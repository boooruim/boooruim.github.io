---
title: Excution Context (1)
author: boooruim
date: 2024-03-20 00:35:00 +0900
categories: [JS]
tags: [js,excution context, scope, call stack,debugger]
pin: true
math: true
mermaid: true
image:
  path: /assets/img/2024-03-20-excution-context1/Thum.png
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

## 실습환경 준비

생활코딩님의 [**강의**](https://www.youtube.com/watch?v=QtOF0uMBy7k)를 보면서 브라우저 환경에서의 debugger와 함께 학습하겠다.


아래의 HTML 코드를 VS Code의 Live Server를 통해 실습 환경을 준비한다.
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
코드를 한줄 한줄 실행하여 변화를 지켜보기 위해 브라우저의 디버거 기능을 간단히 알아보자

## 디버거 (Debugger)
**크롬 브라우저**를 기준으로 개발자 도구를 열면 Elements, Console, Sources 등 여러 탭들이 보이는데 Sources 탭을 눌러보자.

그러면 현재 웹 페이지의 JavaScript 코드가 보이게 될것이고 오른쪽에 아래와 같은 버튼 6개가 보인다.

![alt text](/assets/img/2024-03-20-excution-context1/DebugButton.png)


| 기능                                             | 설명          |
| :---------------------------------------------- | :--------------- |
| Pause script Excution / Resume script Excution  | 스크립트의 실행을 일시 중지 / 중지된 스크립트를 다시 시작|
| Step over next function call                    | 만약 다음 라인이 함수 호출이면 함수가 끝난 다음 라인으로 이동 |
| Step into next function call                    | 만약 다음 라인이 함수 호출이면 함수 내부로 진입|
| Step out of current function                    | 현재 라인으로부터 함수를 벗어난 직후의 라인으로 이동|
| Step                                            | 바로 다음 라인을 실행한다. |
| Deactivate breakpoints / Activate breakpoints   | 중단점들을 전부 비활성화 / 중단점들을 전부 활성화 |

글로는 이해하기 어려울 수 있으므로 기능들을 하나씩 눌러보면서 코드가 어떻게 실행되는 지 지켜보면 금방 알 수 있다.


그럼 이제 중단점을 line 2에 설정해두고 디버깅을 하면서 Excution Context에 대해 알아보자


<!-- Click the hook will locate the[^footnote] -->


## Excution Context

> Excution Context는 VariableEnvironment, LexicalEnvironment, Thisbinding으로 구성되어있고 이 안에서도 environmentRecord와 outerEnvironmentReference등의 개념[^footnote]들이 있지만 지금은 브라우저를 기준으로 눈에 보이는 것에만 초점을 두어 진행하겠다.
{: .prompt-info }

**Excution Context(실행 컨텍스트)란?**
>JavaScript 코드가 실행될 때 생성되며, 코드가 실행되는 데 필요한 정보가 포함된 환경을 의미

실행 컨텍스트는 스택(Stack) 형태로 관리되며, 코드 블록이 실행되는 동안에는 항상 스택의 최상위에 있는 컨텍스트가 현재 실행 컨텍스트가 된다.

실행 컨텍스트의 종류로 몇가지가 있지만 두 가지만 다뤄보겠다.

- **Global Execution Context**: 전역 코드가 실행될 때 생성되는 컨텍스트로, 전체코드의 실행 환경
- **Function Execution Context**: 함수가 호출될 때마다 생성되는 컨텍스트로, 해당 함수의 실행 환경

앞에서 언급한 스택은 브라우저 상에서 **Call Stack** 이라고 불리는 스택을 의미한다.

### Call Stack

![alt text](/assets/img/2024-03-20-excution-context1/CallStack.png)
> 현재 실행 중인 함수나 코드 블록의 정보를 저장하는 스택

브라우저에서 코드가 실행이 되면 Global Excution Context가 먼저 콜스택에 쌓이게 되고, 함수 호출문을 만나면 Function Excution Context가 그 위에 쌓이게 된다.

즉 콜 스택의 최상단에 있는 컨텍스트는 현재 실행 컨텍스트라고 볼 수 있다!

![alt text](/assets/img/2024-03-20-excution-context1/Tab1.png)

현재 실습을 기준으로 위처럼 Call Stack에 anonymous라는 것이 존재하는데 이것이 Global Excution Context를 나타낸다.

그 위에 있는 Scope에 대해서 알아보자.

### Scope

> 식별자(변수, 함수, 클래스 등)에 접근할 수 있는 유효 범위

즉 현재 실행 컨텍스트가 접근할 수 있는 식별자의 범위를 의미한다.

각 실행 컨텍스트의 Scope는 크게 Local, Script, Global로 나뉘고 Local은 지역 범위, Script와 Global은 전역 범위를 담당한다. _(아직 Script와 Global의 차이는 잘 모르겠다..)_

현재 실행 컨텍스트에서 식별자를 찾을 때 Local -> Script -> Global 순으로 찾는데 이러한 연결 자체를 Scope Chain 이라고 하고 연결을 통해 찾아가는 것을 Scope Chaining 이라고 한다.


### Global Excution Context

> 프로그램이 시작될 때 생성되는 Excution Context이며 전체 코드의 실행을 관리하는 환경

전역 실행 컨텍스트에서의 Scope를 Global Scope 라고하며 전역 변수와 함수가 정의되는 영역으로써 
어디서든(다른 컨텍스트) 접근할 수 있다.

또한 전역 실행 컨텍스트는 코드가 실행될 때 Global Object를 생성하는데 브라우저 환경에서는 window 객체이고, Node.js 환경에서는 global 객체이다.

### Function Excution Context

> 함수가 호출될 때마다 생성되는 Excution Context이며 해당 함수의 실행을 관리하는 환경

함수 실행 컨텍스트에서의 Scope를 Local Scope 라고하며 함수 내에서 선언된 변수와 함수는 함수 내부에서만 접근 가능할 수 있다.

>물론 Closuer[^fn-nth-2]라는 것을 통해 A라는 함수 컨텍스트 내의 지역변수를 B의 함수 컨텍스트 내에서 접근할 수 있다.
{: .prompt-info }

다음 포스팅에서 본격적으로 코드를 실행해가며 변화를 관찰해보자.


## 참고
[Excution Context]\
전체적인 흐름 파악용으로 쉬운 영어로 되어있어서 글을 이해하기엔 문제가 없었지만 다 읽어도 So..What..?\
<https://medium.com/@valentinog/javascript-what-is-the-execution-context-what-is-the-call-stack-bd23c78f10d1#b3cc>

[^footnote]: <https://techwell.wooritech.com/docs/languages/javascript/execution-context>

[^fn-nth-2]: <https://junilhwang.github.io/TIL/Javascript/Domain/Execution-Context/#_3-environmentrecord%E1%84%8B%E1%85%AA-hoisting-%E1%84%92%E1%85%A9%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B3%E1%84%90%E1%85%B5%E1%86%BC>