---
title: \[Simple-crawling\] Loop와 Scope-chain

categories:
  - Simple-crawling
tags:
  - [Javascript, Loop, Scope-chain]

toc: true
toc-sticky: true

date: 2021-09-05
last_modified_at: 2021-09-05
---

# Problem

별 생각없이 반복문을 사용하다가, 스코프 체인에 대해서 알게 되고 나서 정말 많은 고민을 했습니다.

- let과 const에 따른 블록 레벨 스코프
- 스코프 체인에 의해 연결된 여러 활성화 객체와 전역 객체
- 실행 문맥의 정의

등등등....

# About JS loop performace

이에 대한 답을 얻기 위해, 먼저 반복문의 성능 차이에 대해서 조사했습니다.

## Each loop

![loop_performance](https://user-images.githubusercontent.com/61080445/132125095-658886c8-cfe2-40c5-a66b-4fc5884c8079.PNG)

출처: [shlrur님의 블로그](https://shlrur.github.io/develog/2018/10/18/about-loop-performance/)
{: .text-center}

> 실험 결과는 대체적으로 while >= for > for of > forEach(native) > forEach(loadsh) > each(underscore) 의 순으로 집계되었습니다.

> While과 for가 가장 높은 성능을 기록하였고, 그 다음이 for of, 그리고 나머지 3가지인 forEach(native), forEach(lodash) 그리고 each(underscore)가 비슷한 성능을 기록하였습니다.

> While과 for는 가장 단순한 로직을 가지는 loop인 만큼 가장 높은 성능을 기록하였습니다. 단순한 로직이지만, 대상 array에 index를 사용해서 접근해야 하는 단점을 가지고 있습니다.

Loop(for, while) 간에 성능 차이는 메서드나 함수에 따라 조금씩 다를 수 있지만, 그리 큰 차이를 보이지 않는다는 걸 알 수 있었습니다.

성능이 그리 큰 차이를 보이지 않는다면 다음 문제로 넘어가야죠.

## For Loop, Array, Scope chain

그렇다면...

루프에서 배열을 사용할 때 '스코프 체인'을 적용해보도록 하겠습니다.

```js
const len = 10000;
```
1
```js
const arr =[];
const t0 = performance.now();
for (let i=0; i<len; i+=1) {
  arr[i] = 1;
}
const t1 = performance.now();
console.log(t1 - t0, 'milliseconds');
```
```
1.3417999744415283 milliseconds
```
2
```js
const arr2 = [];
const t2 = performance.now();
for (let i=0, tmpArr=arr2; i<len; i+=1) {
  tmpArr[i] = 1;
}
const t3 = performance.now();
console.log(t3 - t2, 'milliseconds');
```
```
0.31219983100891113 milliseconds
```

어느정도의 오차는 있지만, 대략 이런 결과를 출력합니다.    
스코프 체인을 통해 활성화 객체와 전역 객체를 이동하는 것만으로도 몇 배의 차이가 발생합니다.  

여기서 제가 헷갈려서 더 알아봐야할 점은 루프가 인데스마다 실행 문맥을 생성하느냐, 아니면 하나의 실행 문맥을 유지하고 가져올 변수가 블록 밖에 있을 때 스코프 체인을 통해 활성화 객체와 전역 객체를 오고가느냐입니다.

저는 일단 후자로 이해하고 있습니다. 그 점이 루프를 더 매력적으로 만들어주고, 성능이 이해가 가니까요.


# Apply

마지막으로, 위 내용에 대해서 활용을 해보겠습니다.

현재 개인 프로젝트를 작성하고 있는데, 배열 내의 요소들을 반복문을 통해 다른 함수의 매개변수로 사용할 필요가 있었습니다.

작은 프로젝트고 데이터도 그리 많지 않아서 유의미한 차이를 보이진 않겠지만, 더욱 완전한 코드를 작성하기 위해서 다음과 같은 코드를 작성해봤습니다.

1
```js
const arr = [1, 2, 3, 0];

for (let a=arr.shift(), tmpArr=[...arr]; tmpArr.length > 0; a=tmpArr.shift()) {
  console.log({arr, tmpArr, a});
}
```
```
{ arr: [ 2, 3, 0 ], tmpArr: [ 2, 3, 0 ], a: 1 }
{ arr: [ 2, 3, 0 ], tmpArr: [ 3, 0 ], a: 2 }
{ arr: [ 2, 3, 0 ], tmpArr: [ 0 ], a: 3 }
```

2
```js
const arr = [1, 2, 3, 0];

for (let a=arr.shift(), tmpArr=arr; tmpArr.length > 0; a=tmpArr.shift()) {
  console.log({arr, tmpArr, a});
}
```
```
{ arr: [ 2, 3, 0 ], tmpArr: [ 2, 3, 0 ], a: 1 }
{ arr: [ 3, 0 ], tmpArr: [ 3, 0 ], a: 2 }
{ arr: [ 0 ], tmpArr: [ 0 ], a: 3 }
```

원본 데이터를 유지하느냐, 안하느냐의 차이입니다.

목적에 맞게 사용하면 될 것 같습니다.

여기서 tmpArr.length를 변수에 할당해서 사용할 순 없으니, 더 고민해볼 여지는 남은 것 같군요.

루프마다 객체에서 프로퍼티를 가져오는 시간을 해결해야하니까요!