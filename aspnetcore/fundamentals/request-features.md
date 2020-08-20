---
title: ASP.NET Core의 요청 기능
author: ardalis
description: ASP.NET Core용 인터페이스에 정의된 HTTP 요청 및 응답과 관련된 웹 서버 구현 세부 사항에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: 3b5c929519407de5dc582c10a86745efddc8a38a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634516"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="f2c45-103">ASP.NET Core의 요청 기능</span><span class="sxs-lookup"><span data-stu-id="f2c45-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="f2c45-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f2c45-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f2c45-105">HTTP 요청 및 응답과 관련된 웹 서버 구현의 세부 사항은 인터페이스를 통해서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="f2c45-106">이러한 인터페이스는 서버 구현 및 미들웨어에서 애플리케이션의 호스팅 파이프라인을 만들고 수정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="f2c45-107">기능 인터페이스</span><span class="sxs-lookup"><span data-stu-id="f2c45-107">Feature interfaces</span></span>

<span data-ttu-id="f2c45-108">ASP.NET Core는 `Microsoft.AspNetCore.Http.Features`에서 서버가 지원하는 기능을 식별하기 위해 사용하는 다수의 HTTP 기능 인터페이스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="f2c45-109">다음 기능 인터페이스는 요청을 처리하고 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="f2c45-110">`IHttpRequestFeature`는 프로토콜, 경로, 쿼리 문자열, 헤더 및 본문을 포함하여 HTTP 요청의 구조를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="f2c45-111">`IHttpResponseFeature`는 상태 코드, 헤더 및 응답 본문을 포함하여 HTTP 응답 구조를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="f2c45-112">`IHttpAuthenticationFeature`는 `ClaimsPrincipal`을 기반으로 사용자를 식별하고 인증 처리기를 지정하기 위한 지원을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="f2c45-113">`IHttpUpgradeFeature`는 서버가 프로토콜을 전환하려는 경우 사용할 추가 프로토콜을 지정하는 클라이언트를 허용하는 [HTTP 업그레이드](https://tools.ietf.org/html/rfc2616.html#section-14.42)에 대한 지원을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="f2c45-114">`IHttpBufferingFeature`는 요청 및/또는 응답의 버퍼링을 사용하지 않도록 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="f2c45-115">`IHttpConnectionFeature`는 로컬과 원격 주소 및 포트에 대한 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="f2c45-116">`IHttpRequestLifetimeFeature`는 연결을 중단하는 기능 또는 클라이언트 연결 끊김과 같이 요청이 너무 일찍 종료되었는지를 감지하는 기능에 대한 지원을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="f2c45-117">`IHttpSendFileFeature`는 파일을 비동기적으로 보내는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="f2c45-118">`IHttpWebSocketFeature`는 웹 소켓을 지원하기 위한 API를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="f2c45-119">`IHttpRequestIdentifierFeature`는 요청을 고유하게 식별하기 위해 구현할 수 있는 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="f2c45-120">`ISessionFeature`는 사용자 세션을 지원하기 위한 `ISessionFactory` 및 `ISession` 추상화를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="f2c45-121">`ITlsConnectionFeature`는 클라이언트 인증서를 검색하기 위한 API를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="f2c45-122">`ITlsTokenBindingFeature`는 TLS 토큰 바인딩 매개 변수 작업을 위한 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="f2c45-123">`ISessionFeature`는 서버 기능이 아니지만 `SessionMiddleware`로 구현됩니다([애플리케이션 상태 관리](app-state.md) 참조).</span><span class="sxs-lookup"><span data-stu-id="f2c45-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="f2c45-124">기능 컬렉션</span><span class="sxs-lookup"><span data-stu-id="f2c45-124">Feature collections</span></span>

<span data-ttu-id="f2c45-125">`HttpContext`의 `Features` 속성은 현재 요청에 사용 가능한 HTTP 기능을 가져오고 설정하기 위한 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="f2c45-126">기능 컬렉션은 요청 컨텍스트 내에서도 변경할 수 있기 때문에, 미들웨어를 사용하여 콜렉션을 수정하고 추가 기능에 대한 지원을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="f2c45-127">미들웨어 및 요청 기능</span><span class="sxs-lookup"><span data-stu-id="f2c45-127">Middleware and request features</span></span>

<span data-ttu-id="f2c45-128">서버가 기능 컬렉션을 만드는 동안 미들웨어는 이 컬렉션에 추가할 수 있고 컬렉션의 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="f2c45-129">예를 들어 `StaticFileMiddleware`는 `IHttpSendFileFeature` 기능에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="f2c45-130">이는 해당 기능이 존재하는 경우 실제 경로에서 요청된 고정 파일을 보내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="f2c45-131">그렇지 않으면 느린 대체 메서드를 사용하여 파일을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="f2c45-132">사용 가능한 경우 `IHttpSendFileFeature`는 운영 체제가 파일을 열고 네트워크 카드로 직접 커널 모드 복사를 수행할 수 있게 해줍니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="f2c45-133">또한 미들웨어는 서버에 의해 설정된 기능 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="f2c45-134">기존 기능을 미들웨어로 대체할 수 있으므로, 미들웨어가 서버의 기능을 향상할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="f2c45-135">컬렉션에 추가된 기능은 다른 미들웨어에서 즉시 사용하거나 나중에 요청 파이프라인의 기본 애플리케이션 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="f2c45-136">사용자 지정 서버 구현 및 특정 미들웨어의 향상 기능을 결합하면 애플리케이션에 필요한 정확한 기능 집합을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="f2c45-137">이를 통해 서버를 변경하지 않고 누락된 기능을 추가할 수 있으며, 최소한의 기능만 노출되도록 하여 공격 영역을 제한하고 성능을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="f2c45-138">요약</span><span class="sxs-lookup"><span data-stu-id="f2c45-138">Summary</span></span>

<span data-ttu-id="f2c45-139">기능 인터페이스는 특정 요청이 지원할 수 있는 특정 HTTP 기능을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="f2c45-140">서버는 기능 컬렉션 및 해당 서버에서 지원하는 초기 기능 집합을 정의하지만, 미들웨어를 사용하여 이러한 기능을 향상할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f2c45-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2c45-141">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f2c45-141">Additional resources</span></span>

* [<span data-ttu-id="f2c45-142">서버</span><span class="sxs-lookup"><span data-stu-id="f2c45-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="f2c45-143">미들웨어</span><span class="sxs-lookup"><span data-stu-id="f2c45-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f2c45-144">OWIN(Open Web Interface for .NET)</span><span class="sxs-lookup"><span data-stu-id="f2c45-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
