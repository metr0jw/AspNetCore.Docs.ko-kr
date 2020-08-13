---
title: ASP.NET Core의 요청 기능
author: ardalis
description: ASP.NET Core용 인터페이스에 정의된 HTTP 요청 및 응답과 관련된 웹 서버 구현 세부 사항에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/request-features
ms.openlocfilehash: 4b6b34724bc848578293e586da12ead63c96cfbd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016793"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core의 요청 기능

작성자: [Steve Smith](https://ardalis.com/)

HTTP 요청 및 응답과 관련된 웹 서버 구현의 세부 사항은 인터페이스를 통해서 정의됩니다. 이러한 인터페이스는 서버 구현 및 미들웨어에서 애플리케이션의 호스팅 파이프라인을 만들고 수정하는 데 사용됩니다.

## <a name="feature-interfaces"></a>기능 인터페이스

ASP.NET Core는 `Microsoft.AspNetCore.Http.Features`에서 서버가 지원하는 기능을 식별하기 위해 사용하는 다수의 HTTP 기능 인터페이스를 정의합니다. 다음 기능 인터페이스는 요청을 처리하고 응답을 반환합니다.

`IHttpRequestFeature`는 프로토콜, 경로, 쿼리 문자열, 헤더 및 본문을 포함하여 HTTP 요청의 구조를 정의합니다.

`IHttpResponseFeature`는 상태 코드, 헤더 및 응답 본문을 포함하여 HTTP 응답 구조를 정의합니다.

`IHttpAuthenticationFeature`는 `ClaimsPrincipal`을 기반으로 사용자를 식별하고 인증 처리기를 지정하기 위한 지원을 정의합니다.

`IHttpUpgradeFeature`는 서버가 프로토콜을 전환하려는 경우 사용할 추가 프로토콜을 지정하는 클라이언트를 허용하는 [HTTP 업그레이드](https://tools.ietf.org/html/rfc2616.html#section-14.42)에 대한 지원을 정의합니다.

`IHttpBufferingFeature`는 요청 및/또는 응답의 버퍼링을 사용하지 않도록 메서드를 정의합니다.

`IHttpConnectionFeature`는 로컬과 원격 주소 및 포트에 대한 속성을 정의합니다.

`IHttpRequestLifetimeFeature`는 연결을 중단하는 기능 또는 클라이언트 연결 끊김과 같이 요청이 너무 일찍 종료되었는지를 감지하는 기능에 대한 지원을 정의합니다.

`IHttpSendFileFeature`는 파일을 비동기적으로 보내는 메서드를 정의합니다.

`IHttpWebSocketFeature`는 웹 소켓을 지원하기 위한 API를 정의합니다.

`IHttpRequestIdentifierFeature`는 요청을 고유하게 식별하기 위해 구현할 수 있는 속성을 추가합니다.

`ISessionFeature`는 사용자 세션을 지원하기 위한 `ISessionFactory` 및 `ISession` 추상화를 정의합니다.

`ITlsConnectionFeature`는 클라이언트 인증서를 검색하기 위한 API를 정의합니다.

`ITlsTokenBindingFeature`는 TLS 토큰 바인딩 매개 변수 작업을 위한 메서드를 정의합니다.

> [!NOTE]
> `ISessionFeature`는 서버 기능이 아니지만 `SessionMiddleware`로 구현됩니다([애플리케이션 상태 관리](app-state.md) 참조).

## <a name="feature-collections"></a>기능 컬렉션

`HttpContext`의 `Features` 속성은 현재 요청에 사용 가능한 HTTP 기능을 가져오고 설정하기 위한 인터페이스를 제공합니다. 기능 컬렉션은 요청 컨텍스트 내에서도 변경할 수 있기 때문에, 미들웨어를 사용하여 콜렉션을 수정하고 추가 기능에 대한 지원을 추가할 수 있습니다.

## <a name="middleware-and-request-features"></a>미들웨어 및 요청 기능

서버가 기능 컬렉션을 만드는 동안 미들웨어는 이 컬렉션에 추가할 수 있고 컬렉션의 기능을 사용할 수 있습니다. 예를 들어 `StaticFileMiddleware`는 `IHttpSendFileFeature` 기능에 액세스합니다. 이는 해당 기능이 존재하는 경우 실제 경로에서 요청된 고정 파일을 보내는 데 사용됩니다. 그렇지 않으면 느린 대체 메서드를 사용하여 파일을 보냅니다. 사용 가능한 경우 `IHttpSendFileFeature`는 운영 체제가 파일을 열고 네트워크 카드로 직접 커널 모드 복사를 수행할 수 있게 해줍니다.

또한 미들웨어는 서버에 의해 설정된 기능 컬렉션에 추가할 수 있습니다. 기존 기능을 미들웨어로 대체할 수 있으므로, 미들웨어가 서버의 기능을 향상할 수 있습니다. 컬렉션에 추가된 기능은 다른 미들웨어에서 즉시 사용하거나 나중에 요청 파이프라인의 기본 애플리케이션 자체에서 사용할 수 있습니다.

사용자 지정 서버 구현 및 특정 미들웨어의 향상 기능을 결합하면 애플리케이션에 필요한 정확한 기능 집합을 구성할 수 있습니다. 이를 통해 서버를 변경하지 않고 누락된 기능을 추가할 수 있으며, 최소한의 기능만 노출되도록 하여 공격 영역을 제한하고 성능을 개선할 수 있습니다.

## <a name="summary"></a>요약

기능 인터페이스는 특정 요청이 지원할 수 있는 특정 HTTP 기능을 정의합니다. 서버는 기능 컬렉션 및 해당 서버에서 지원하는 초기 기능 집합을 정의하지만, 미들웨어를 사용하여 이러한 기능을 향상할 수 있습니다.

## <a name="additional-resources"></a>추가 자료

* [서버](xref:fundamentals/servers/index)
* [미들웨어](xref:fundamentals/middleware/index)
* [OWIN(Open Web Interface for .NET)](xref:fundamentals/owin)
