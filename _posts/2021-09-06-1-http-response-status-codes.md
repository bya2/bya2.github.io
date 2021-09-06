---
title: \[네트워크\] HTTP Response Status codes

categories:
  - Network
tags:
  - [http, response, status-codes]

toc: true
toc-sticky: true

date: 2021-09-06
last_modified_at: 2021-09-06
---

HTTP 응답 상태 코드에 대한 정리

# 응답 상태 코드

## 코드 구분

1. **Informational responses** (100-199)
2. **Successful responses** (200-299)
3. **Redirects** (300-399)
4. **Client errors** (400-499)
5. **Server errors** (500-599)

백 자리수마다 응답 상태 코드가 구분됩니다.

각 상태 코드의 정의에 대해서 알아보고, 자세한 설명을 모질라에 기술되어 있으므로 생략하겠습니다.

## Informational responses

`100`: **Continue** - 임시적인 응답, 상태 좋음

`101`: **Switching Protocol** - 서버에서 프로토콜 변경할 것임

`102`: **Processing** - 서버에서 요청을 수신하고 처리 중, 그러나 아직 제대로 된 응답 불가

`103`: **Early Hints** - 서버가 응답을 준비하는 동안, 사용자 에이전트가 사전 로딩을 시작할 수 있도록 함

## Successful responses

`200`: **OK** - 성공, 성공 의미는 HTTP 메소드에 따라 다름

`201`: **Created** - 성공, 새로운 리소스 생성

`202`: **Accepted** - 요청 수신, 그에 응한 행동 불가

`203`: **Non-Authoritative Information** - 메타 정보가 서버의 것과 불일치, 그러나 로컬이나 제 3의 복사본에서 모아짐

`204`: **No Content** - 응답 컨텐츠 없음, 헤더는 있음

`205`: **Reset Content** - 요청 완수 이후, 사용자 에이전트에게 요청을 보낸 문서 뷰 리셋

`206`: **Partial Content** - 

## Redirects

`301`: **Moved Permanently** - 요청한 리소스의 URI가 변경됨

`302`: **Found** - 요청한 리소스의 URI가 일시적으로 변경됨

## Client errors

`401`: **Unauthorized** - 비인증이므로 클라이언트는 스스로 인증해야함

`403`: **Forbidden** - 클라이언트는 콘텐츠에 접근할 권한 없음 (거절)

`404`: **Not Found** - 요청받은 리소스를 찾을 수 없음

## Server errors

`502`: **Bad Gateway** - 서버가 요청을 처리하는 데 필요한 응답을 얻으려 했으나 잘못된 응답을 수신

`503`: **Service Unavailable** - 요청을 처리할 준비가 되지 않음

# 마치며...

개인적으로 크롤링 관련 프로젝트를 만들 때 403 상태 코드가 많이 겪었습니다.

그래서 사용자 에이전트를 설정하고, 최대한 스크랩 범위를 줄여야했습니다. ㅠㅠ