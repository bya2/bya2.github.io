---
title: 성능 측정을 위해 Performance API 사용

categories:
  - Node.js
tags: [Javascript, Node.js, "Performnace API"]

toc: true
toc-sticky: true

date: 2021-09-05
last_modified_at: 2021-09-05
---

Node.js에서 Performance API를 이용해서 성능 측정의 정확도 늘리기

# Date.now를 사용하면 되지 않을까?

> 시스템 시간을 기반으로 한 Date를 기준으로 실제 사용자를 모니터링하는 것은 적절치 않다. 대부분의 시스템은 정기적으로 시간을 동기화 하는 데몬을 실행한다. 그리고 그 시계는 15분 내지 20분 마다 몇 ms 씩 조정되는 것이 일반적이다. 따라서 그 속도에서 측정된 10 초간격의 1% 정도가 부정확할 것이다.

> Perhaps less often considered is that Date, based on system time, isn't ideal for real user monitoring either. Most systems run a daemon that regularly synchronizes the time. It is common for the clock to be tweaked a few milliseconds every 15-20 minutes. At that rate about 1% of 10 second intervals measured would be inaccurate.

출처 > [DevelopersGoogleWeb](https://developers.google.com/web/updates/2012/08/When-milliseconds-are-not-enough-performance-now)


# Install

```js
const t0 = performance.now();
```

ReferenceError: performance is not defined
{: .notice}

??? 뭘까요.

검색 > [stackoverflow](https://stackoverflow.com/questions/46436943/referenceerror-performance-is-not-defined-when-using-performance-now)

> 난 이것이 프론트엔드에 태그된 것으로 알고 있지만, (나처럼) node.js에서의 해결 방법을 찾고 있는 사람이 있다면 너는 먼저 perf_hooks 모듈을 require할 필요가 있다 (node 8.5+부터 이용가능).

> I know this is tagged front-end but if anyone comes across this looking for a node.js solution (like me), you need to first require performance from the perf_hooks module (available in node 8.5+).


Node 문서 > [Node.js documentation](https://nodejs.org/api/perf_hooks.html#perf_hooks_performance_measurement_apis)

> 이 모듈은 W3C Web Performance APIs의 하위 집합 구현과 Node.js별 성능 측정을 위한 추가 APIs를 제공한다.

> This module provides an implementation of a subset of the W3C Web Performance APIs as well as additional APIs for Node.js-specific performance measurements.


> 현재 Node.js 인스턴스에서 성능 메트릭스을 수집하는 데 사용할 수 있는 Object다.

> An object that can be used to collect performance metrics from the current Node.js instance. It is similar to window.performance in browsers.

...  
그렇다고 합니다!

모듈을 설치하고 불러옵시다.

```sh
$ npm i perf_hooks
```
```js
const performance = require('pref_hooks');
```

# Performance.now

> 현재 high resolution 밀리초 타임스탬프를 반환한다. 여기서 0은 현재 노드 프로세스의 시작을 나타낸다.

> Returns the current high resolution millisecond timestamp, where 0 represents the start of the current node process.

Performance API는 `performance.now()`를 통해서 DOMHighResTimeStamp에 접근할 수 있게 해줍니다.

```js
const t0 = performance.now();
...
const t1 = performance.now();
console.log(t1 - t0);
```

이렇게 사용하면 되겠네요!

# Performance.mark, Performance.measure

## Performance.mark

([name, [, options]])

> Performance 타임라인에 새로운 `PerformanceMark` 항목을 생성한다. `PerformanceMark`는 `performanceEntry.entryType`이 항상 `'mark'`이고, `PerformanceEntry.duration`이 항상 `0`인 `PerformanceEntry`의 하위 클래스다. Performance 마크는 Performance 타임라인에서 특정 중요한 순간을 표시하는데 사용된다.

> Creates a new PerformanceMark entry in the Performance Timeline. A PerformanceMark is a subclass of PerformanceEntry whose performanceEntry.entryType is always 'mark', and whose performanceEntry.duration is always 0. Performance marks are used to mark specific significant moments in the Performance Timeline.

```js
performance.mark("squirrel");
performance.mark("squirrel");
performance.mark("monkey");
performance.mark("monkey");
performance.mark("dog");
performance.mark("dog");


// Get all of the PerformanceMark entries.
const allEntries = performance.getEntriesByType("mark");
console.log(allEntries.length);
// 6

// Get all of the "monkey" PerformanceMark entries.
const monkeyEntries = performance.getEntriesByName("monkey");
console.log(monkeyEntries.length);
// 2

// Clear out all of the marks.
performance.clearMarks();
```

## Performance.measure

(name, [, startMarkOrOptions[, endMark]])

> Performance 타임라인에서 새로운 `PerformanceMeasure` 항목을 생성한다. `PerformanceMeasure`은 `performanceEntry.entryType`이 항상 `'measure'`이고, `performanceEntry.duration`이 `startmark`와 `endmark` 이후 경과된 밀리초 수를 측정하는 `PerformanceEntry`의 하위 클래스다.

> `startmark` 인수는 Performance 타임라인에서 `PerformanceMark`를 식별하거나 `PerformanceNodeTiming` 클래스에서 제공되는 타임스탬프 속성들을 식별할 수 있다. 만약 명명된 `startmark`가 없다면, 오류가 발생한다.

> 선택적으로 사용할 수 있는 `endmark` 인수는 Performance 타임라인에서 `PerformanceMark`를 식별하거나 `PerformanceNodeTiming` 클래스에서 제공되는 타임스탬프 속성들을 식별할 수 있다. 매개변수가 전달되지 않으면 `endmark`는 performance.now()가 되고, 매개변수가 전달됬을 때 명명된 `endmark`가 없다면 오류가 발생한다.

> Creates a new PerformanceMeasure entry in the Performance Timeline. A PerformanceMeasure is a subclass of PerformanceEntry whose performanceEntry.entryType is always 'measure', and whose performanceEntry.duration measures the number of milliseconds elapsed since startMark and endMark.

> The startMark argument may identify any existing PerformanceMark in the Performance Timeline, or may identify any of the timestamp properties provided by the PerformanceNodeTiming class. If the named startMark does not exist, an error is thrown.

> The optional endMark argument must identify any existing PerformanceMark in the Performance Timeline or any of the timestamp properties provided by the PerformanceNodeTiming class. endMark will be performance.now() if no parameter is passed, otherwise if the named endMark does not exist, an error will be thrown.

...
전부 그렇다고 합니다!

## Code

Mozila에 작성되어있는 Performance API 사용법과는 여러모로 달랐습니다.

Node는 PerformanceObserver의 인스턴스를 생성해서 observe 메서드를 통해 값을 측정하는 것 같습니다.

```js
const { PerformanceObserver, performance } = require('perf_hooks');

const obs = new PerformanceObserver(items => {
  console.log(items.getEntries()[0].duration);
  performance.clearMarks();
});

obs.observe({ entryTypes: ['measure'] });
```
1
```js
const arr = [1, 2, 3, 4, 5];

performance.mark('A');
arr.map(el => {
  performance.mark('B');
  performance.measure('A to B', 'A', 'B');
});
```
```
0.0353
49.0011
49.5013
49.8932
50.3025
50.7167
```
2
```js
performance.mark('A');
const arr = [];
for (let i=0; i<10000; i+=1) {
  arr[i] =1;
}
performance.mark('B');
performance.measure('A to B', 'A', 'B');
```
```
1.396
```

이런 방식으로 사용하면 되겠습니다!

# 참고

[yceffort님의 블로그](https://yceffort.kr/2020/12/measuring-performance-of-javascript-functions)  
[Nodejs.org](https://nodejs.org/dist./v10.17.0/docs/api/perf_hooks.html)


