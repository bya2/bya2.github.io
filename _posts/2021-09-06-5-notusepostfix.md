---
title: \[Loop\] 반복문과 후위 증감 연산자

categories:
  - Javascript
tags:
  - [javascript, loop]

toc: true
toc-sticky: true

date: 2021-09-06
last_modified_at: 2021-09-06
---
Loop에서 후위 증감 연산자를 사용하면 안되는 이유에 대해서 정리

# 개요

얼마 전, Loop문에서 for문과 while문의 성능이 훨씬 뛰어나다는 걸 알게되었습니다.

그만큼 제게 인덱스를 사용하면 for문과 while문은 그만큼 중요해졌는데, 별 생각 없이 관례처럼 인덱스에 쓰던 `후위 증감 연산자`가 루프문에 좋은 방법이 아니라는 소리를 듣게 되었습니다.

좋지 않은데, 이유는 대체 뭘까해서 찾아봤습니다.

# 이유

전위 증감과 후위 증감 연산자에 대해서 비교해보겠습니다.

**전위 증감**: 객체 자신을 증감시킨 뒤, 그 레퍼런스를 반환  
**후위 증감**: 객체를 복사하여 임시 객체를 만든 뒤, 객체를 증감시키고, 임시 객체를 반환
{: .notice}

후위 증감은 임시 객체를 만들기 때문에 복사하는 비용이 그만큼 더 듭니다.  

따라서 사본을 필요로 하지 않는 경우 메모리 상에 불필요한 낭비가 될 수 있습니다. 정수 복사 한 번쯤이야 별 거 아니지만, 루프가 수없이 돌아가다보면 그만큼 문제가 발생할 수 있기 때문입니다.

# 성능

각각 `i+=1`, `i++`, `++i` 순서대로 성능 테스트를 진행해보았습니다!  

복합 대입 연산자에 비해서 전위, 후위 증감 연산자가 훨씬 빠릅니다. 사본이 필요하지 않다면 전위 증감 연산자를 사용하는 게 맞겠군요.

```js
const len = 10000;
```

```js
// i+=1
performance.mark('A');
for (let i=0; i<len; i+=1) {
  arr[i] =1;
  arr.push(i);
}
performance.mark('B');
```
```
1.5471
```

```js
// i++
performance.mark('A');
for (let i=0; i<len; i++) {
  arr[i] =1;
  arr.push(i);
}
performance.mark('B');
```
```
0.6093
```

```js
// ++i
performance.mark('A');
for (let i=0; i<len; ++i) {
  arr[i] =1;
  arr.push(i);
}
performance.mark('B');
```
```
0.6268
```

# 참고 자료
[KLDP](https://kldp.org/node/154365)