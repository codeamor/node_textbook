# 2.1 호출 스택, 이벤트 루프
## 1. 호출 스택

```javascript
function first() {
  second();
  console.log('첫 번째')
}
function second() {
  third();
  console.log('두 번째');
}
function third() {
  console.log('세 번째');
}
first();
```
> 위 코드의 순서?
- 세 번째 -> 두 번째 -> 첫 번째
> 쉽게 파악하는 법? -> 호출 스택을 그린다.

> 호출 스택 (함수의 호출, 자료구조의 스택)
- Anonymous은 가상의 전역 컨텍스트(항상 있다고 생각하는 게 좋습니다.)
- 함수 호출 순서대로 쌓이고, 역순으로 실행됩니다.
- 함수의 실행이 완료되면 스택에서 빠집니다.
- 선입선출 구조라서 스택이라고 불립니다.
- 호출 스택이 텅 비게 되면 자바스크립트가 실행 종료됩니다.

```javascript
function run() {
  console.log('3초 후 실행');
}
console.log('시작');
setTimeout(run, 3000);
console.log('끝');
}
```
> 위 코드의 순서?
- 시작 -> 끝 -> 3초 후 실행
- 호출 스택으로는 설명이 안되지만 이벤트 루프로 설명이 가능합니다.
(호출 스택, 백그라운드, 메모리, 태스크 큐, 콘솔창)
- 백그라운드에 위치한 코드는 다른 스레드가 실행하는데, 호출 스택이 있으면 멀티 스레딩이 됩니다.
> 그냥 다 백그라운드로 보내서 멀티 스레드로 구동시키면 좋지 않을까?
- 프로그래밍의 난이도가 대폭 상승하게 됩니다.
- 동시에 실행될 수 있는 코드엔 제한이 있고, 이를 제외한 코드들은 모두 동기적으로 처리가 됩니다.

- 호출 스택 / 백그라운드 / 메모리 / 태스크 큐 / 콘솔창 이해
```javascript
function oneMore() {
  console.log('one more');
}
function run() {
  console.log('run');
  setTimeout(() => {
    console.log('timer');
  }, 0)
  new Promise((resolve) => {
    resolve('hi');
  })
  .then(console.log);
  oneMore();
}

setTimeout(run, 5000);
```
- 호출 스택에 anonymous 가 깔림
- 호출 스택에 setTimeout 함수가 들어감
- 백그라운드에 타이머 생성
- 코드가 끝났으니 anonymous 가 호출 스택에서 제거됨
- 5초 뒤, 백그라운드의 run 함수가 태스크 큐에 들어감
- 호출 스택이 비어 있을 때, run 함수는 호출 스택으로 들어감
- 콘솔창에 run 출력
- 호출 스택의 최하단 run 위에 익명 함수를 가진 타이머가 들어감
- 백그라운드에 바로 익명 함수 타이머가 들어감
- 호출 스택에 new Promise와 내부의 resolve('hi')가 들어감
  - Promise 그 자체는 동기지만 then을 만나는 순간 비동기가 됨
- 백그라운드에 then을 만난 Promise로 인해 then, console.log(hi)가 들어감
- 호출 스택의 run 위에 oneMore()가 생성되고 그 안의 console.log가 호출 스택에 추가됨
- 콘솔창에 oneMore 함수 내부의 one more가 찍힘
- 호출 스택에서, oneMore 함수 내부의 console.log가 먼저 사라지고, oneMore 함수가 사라짐
- 호출 스택에서, run 함수가 사라짐
- 백그라운드에는 아직 익명 함수 타이머와 Promise의 then 내부의 코드가 남아 있음.
  - 백그라운드에서 먼저 실행되는 코드가 태스크 큐로 넘어감
- 백그라운드에서 누가 먼저 실행될지는 모르지만 두 개 모두 태스크 큐로 넘어가게 되면 Promise의 then이 익명 함수 타이머보다 먼저 호출 스택에 진입함.
<br>
  - **Promise의 then/catch, process.nextTick의 성질 -> 일반 타이머보다 우선순위가 높음**
- 호출 스택에 console.log('hi')가 먼저 들어가서 콘솔창에 찍힌 뒤 호출 스택에서 사라지고, 그 다음 익명 함수 타이머의 console.log('wow')가 찍히고 호출 스택에서 사라짐
<br>

`콘솔창 결과: run -> one more -> hi -> wow`


- 여기서 백그라운드는 JS가 아니라 C++ 혹은 OS 기반입니다.
- 백그라운드는 다른 언어로 된 V8 엔진이 구동하는 것이므로 자바스크립트(노드) 자체는 싱글 스레드입니다.





