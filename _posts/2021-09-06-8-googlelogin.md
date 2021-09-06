---
title: \[Passport\] 2-Google Login, Logout

categories:
  - Node.js
tags:
  - [Passport, Federated-Identity, OAuth]

toc: true
toc-sticky: true

date: 2021-09-06
last_modified_at: 2021-09-06
---

Google에 Login, Logout할 때 어떤 원리로 이루어지는가에 대한 설명

# Federated Identity

`Federated Identity`는 일종의 로그인 방식입니다. 이는 OAuth와 연관되어 있습니다.

원리를 설명하기 위해 각 노드를 Resource owner, Resource server, Client로 나눕니다. 각 노드는 이러한 역할군을 가지고 있습니다.

Resource owner: User  
Client: My Service  
Resource server: Google, Facebook ...
{: .notice}

> `Resource owner`을 통해 `Resource server`의 `Access token`을 OAuth를 통해서 `Client`가 전달받으면, `Client`는 Access token을 통해서 `Resource owner`을 대신해서 `Resource server`에 접속할 수 있게 됩니다.

만약 제가 서비스를 하고 있는 서버가 있다면, 서버의 방문객을 통해서 Google과 같은 플랫폼에 접근할 수 있게 되는 것입니다.

> 그 때 `Access token`을 가지고 `Resource server`에게 `Resource server`상에서 `Resource owner`의 식별자가 무엇이냐고 묻습니다.  
그러면 `Access token`을 보고 `Resource server`는 `Resource owner`의 식별자가 '무엇'이다라고 API를 통해서 응답합니다.

이 사람의 Id는 '무엇'이다.
{: .notice}

> 그러면 `Client`는 식별자를 통해서 지금 접속해 있는 사용자가 누구인지 추적할 수 있게 됩니다. `Resource server` 상에서 `Resource owner`의 아이디 값을 이용하게 되는 겁니다.

그 때 서버는 플랫폼에서 발행된 '토큰'을 통해서 방문객의 식별자에 대해서 요청을 하고, 응답을 받아서 방문객이 누구인지 알 수 있게됩니다.

# 1. Content screen

> `Content screen`은 `Resource owner`가 `Resource server`에게 어떤 권한을 요청할 때 사용되는 화면입니다.

즉, 저희가 어떤 서버에서 구글과 같은 플랫폼 로그인하기 전 정보 제공을 위해 어떤 항목에 동의할 때 이용되는 화면입니다.

> `Content screen`을 통해 `Resource owner`가 권한을 승인하면, `Resource server`에게 신호가 갑니다.  
  `Resource server`는 최종적인 인증을 하기 위해 `Authorization code`를 만듭니다.
  이 `Authorization code`를 `Resource Owner`을 통해 `Client`에 전송합니다.

이 때 Client가 Authorization code의 값을 받을 주소를 정하는 것이 Authorized Redirect URLs이다.
clinetID: ...  
clientSecret: ...
{: .notice}

플랫폼은 최종적인 인증을 위해 `Authorizaion code`를 만들고 사용자를 통해 서버로 전송합니다.

# 2.

`Passport Strategy` 작성
구글 API에서 `json`(키) 다운받기

# 3.

어떤 기능들을 제공하기 위해 `Resource server`상에서의 어떤 기능이 필요한지를 결정해야합니다. 이것을 `Scope`라고 합니다.

Scope: Client가 Resource owner에게 요청할 Resource server에서의 기능들.  
ex) 캘린더, ...
{: .notice}

`routes`에서 `Resource owner`을 통해 `Resource server`로 인증할 때 필요한 주소를 만들어주는 코드를 작성합니다.

# 4.

`Resource server`에게 인증을 받을 때 로그인이 안되어있다면, 로그인을 먼저시켜야합니다.  

로그인 정보를 보내서 `Resource owner`의 식별자가 인증이되면 `Resource server`가 `Scope`에 대해서 묻습니다.

이제 `Resource owner`가 `Content screen`에서 권한에 대한 허용을 누르면, `Resource server`는 밑의 정보를 기록합니다.

1. Resource owner가 'user_id: 1'이라면, 'client_id: 1'에게 'scope: b, c'를 허용했다고 기록.  
2. 그 다음, Resource server가 Authorization code를 만든 후, Resource owner에게 전송.  
3. Header에 Redirection code를 담아서 Location 값을 부여.  
4. Location 값은 Redirection이므로, Resource owner은 자기도 모르게 Client에게 다시 접속.  
5. 따라서 Client에게 code가 전달되고, 이를 기록.  
{: .notice}

이제 API 활성화를 해야합니다.

`Client`는 `Resource server`에 접속해서 `ClientId`, `ClientSecret`, `Authorization code`를 직접 전달합니다. 이를 `Passport`가 개발자를 대신해서 자동으로 전달합니다.

`Resource server`는 이 값을 받고 `Authorization code`를 지우고 `Access token`을 발행합니다.

`Access token`은 'user_id: 1'은 'scope:b, c'에 대해서 'client_id: 1'에게 허용했다는 의미를 담고 있는 토큰입니다.

`Client`는 Google Plus API를 통해서 `Access token`을 이용해, `Resource server`의 프로필 작업, id와 같은 정보들을 요청하고, 그것을 응답받은 뒤, 그 결과를 callback 함수를 통해 전달할 것입니다.

# 5.

Facebook으로 로그인을 하든, Google로 로그인을 하든 간에 같은 사용자로 인식될 수 있게 하는 것이 기능 상 더 좋은 방법입니다.

많은 SNS들은 기본적으로 Email을 주지 않습니다. Email을 받기 위해서는 추가적인 Scope를 추가할 필요가 있습니다.

# 참고 자료

[생활코딩]()