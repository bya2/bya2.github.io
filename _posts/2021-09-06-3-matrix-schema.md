---
title: \[P:Simple-crawling\] Array of Arrays of Object

categories:
  - Simple-crawling
tags:
  - [Node.js, Mongoose, Schema]

toc: true
toc-sticky: true

date: 2021-09-06
last_modified_at: 2021-09-06
---

Mongoose의 배열 안의 배열 안의 객체로 이루어진 스키마 작성에 대해 정리

# 문제

스크랩할 데이터가 많은 것도 문제지만, 그 데이터를 종류 별로 분류해서 객체로 만드는 것도 문제입니다.

일단 데이터를 요청하고 받아오고 분류해서 객체로 만들어 봤더니 이런 모양이 나와버렸습니다.
```js
{
  [
    {
      ...
      platform: [
        [
          {
            ...
          },
          ...
        ],
        ...
      ]
    },
    ...
  ]
}
```

조금 복잡할 순 있지만, 문제가 되는 부분은 아래와 같습니다.

```js
platform: [
  [
    {
      ...
    }
  ],
  ...
]
```

일단 이걸 객체라고 만들어 놓긴 했는데, Mongoose의 스키마로 어떻게 작성하고 모델을 만들지 처음엔 감이 안잡혔습니다.  
이중 배열에 그 안에 여러 개의 객체가 들어있는데다가, 그 객체의 4가지 형태로 프로퍼티가 각각 달랐습니다.

# 해결

이를 해결하는 방법은 다음과 같습니다.

```js
const matrixSchema = new mongoose.Schema({
  ...
})

const okSchema = new mongoose.Schema({
  ...
  platform: [[matrixSchema]],
})
```

# 참고 자료

[Mongoose issues](https://github.com/Automattic/mongoose/issues/1361)  
[StackOverflow](https://stackoverflow.com/questions/52944240/how-to-create-array-of-arrays-of-objects-schema-in-mongoose-js)

