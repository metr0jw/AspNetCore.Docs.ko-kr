---
title: ASP.NET Core가 있는 OWIN(Open Web Interface for .NET)
author: ardalis
description: ASP.NET Core에서 웹앱이 웹 서버에서 분리될 수 있도록 하는 OWIN(Open Web Interface for .NET)을 지원하는 방법을 알아봅니다.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
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
uid: fundamentals/owin
ms.openlocfilehash: e6069e5051530e88c43d99758d891b53c9f35233
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016897"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="badf8-103">ASP.NET Core가 있는 OWIN(Open Web Interface for .NET)</span><span class="sxs-lookup"><span data-stu-id="badf8-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="badf8-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="badf8-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="badf8-105">ASP.NET Core는 OWIN(Open Web Interface for .NET)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="badf8-106">OWIN을 사용하면 웹 앱을 웹 서버에서 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="badf8-107">미들웨어를 파이프라인에서 사용하고 요청 및 관련된 응답을 처리하기 위한 표준 방법을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="badf8-108">ASP.NET Core 애플리케이션 및 미들웨어는 OWIN 기반 애플리케이션, 서버 및 미들웨어와 상호 운용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="badf8-109">OWIN은 서로 다른 개체 모델이 있는 두 프레임워크를 함께 사용할 수 있도록 허용하는 분리 계층을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="badf8-110">`Microsoft.AspNetCore.Owin` 패키지는 두 개의 어댑터 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="badf8-111">ASP.NET Core에서 OWIN으로</span><span class="sxs-lookup"><span data-stu-id="badf8-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="badf8-112">OWIN에서 ASP.NET Core로</span><span class="sxs-lookup"><span data-stu-id="badf8-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="badf8-113">이렇게 하면 ASP.NET Core를 OWIN 호환 가능한 서버/호스트를 기반으로 호스팅하거나 다른 OWIN 호환 가능한 구성 요소를 ASP.NET Core를 기반으로 실행되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="badf8-114">이러한 어댑터를 사용하면 성능 비용이 수반됩니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="badf8-115">ASP.NET Core 구성 요소만을 사용하는 앱은 `Microsoft.AspNetCore.Owin` 패키지 또는 어댑터를 사용하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="badf8-116">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="badf8-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="badf8-117">ASP.NET Core 파이프라인에서 OWIN 미들웨어 실행</span><span class="sxs-lookup"><span data-stu-id="badf8-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="badf8-118">ASP.NET Core의 OWIN 지원은 `Microsoft.AspNetCore.Owin` 패키지의 일부로 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="badf8-119">이 패키지를 설치하여 OWIN 지원을 프로젝트로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="badf8-120">OWIN 미들웨어는 `Func<IDictionary<string, object>, Task>` 인터페이스 및 특정 키 설정(예: `owin.ResponseBody`)이 필요한 [OWIN 사양](https://owin.org/spec/spec/owin-1.0.0.html)을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="badf8-121">다음과 같은 간단한 OWIN 미들웨어는 "Hello World"를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="badf8-122">샘플 서명은 `Task`를 반환하고 OWIN에 필요한 `IDictionary<string, object>`를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="badf8-123">다음 코드는 `UseOwin` 확장 메서드로 `OwinHello` 미들웨어(위에 표시된)를 ASP.NET Core 파이프라인에 추가하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="badf8-124">OWIN 파이프라인 내에서 수행하도록 기타 작업을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="badf8-125">응답 헤더는 응답 스트림에 대한 최초 작성 전에 수정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="badf8-126">`UseOwin`에 대한 여러 번의 호출은 성능상의 이유로 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="badf8-127">OWIN 구성 요소는 함께 그룹화하는 경우 가장 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="badf8-128">OWIN 기반 서버에서 호스팅하는 ASP.NET Core 사용</span><span class="sxs-lookup"><span data-stu-id="badf8-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="badf8-129">OWIN 기반 서버는 ASP.NET Core 앱을 호스팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="badf8-130">이러한 서버는 [Nowin](https://github.com/Bobris/Nowin), .NET OWIN 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="badf8-131">이 문서에 대한 샘플에서는 Nowin을 참조하고 자체 호스팅 ASP.NET Core가 가능한 `IServer`를 만드는 데 사용하는 프로젝트를 추가했습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="badf8-132">`IServer`는 `Features` 속성 및 `Start` 메서드가 필요한 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="badf8-133">`Start`는 서버 구성 및 시작을 담당하며 이 경우 IServerAddressesFeature에서 구문 분석된 주소를 설정하는 일련의 흐름 API 호출을 통해 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="badf8-134">`_builder` 변수의 흐름 구성은 요청이 메서드의 앞부분에서 정의된 `appFunc`에 의해 처리되도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="badf8-135">이 `Func`는 들어오는 요청을 처리하도록 각 요청마다 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="badf8-136">Nowin 서버를 쉽게 추가하고 구성하도록 `IWebHostBuilder` 확장도 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="badf8-137">이 항목이 준비되어 있으면 이 사용자 지정 서버를 통해 *Program.cs*의 확장을 호출하여 ASP.NET Core 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="badf8-138">[ASP.NET Core 서버](xref:fundamentals/servers/index)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="badf8-139">OWIN 기반 서버에서 ASP.NET Core 실행 및 해당 WebSocket 지원 사용</span><span class="sxs-lookup"><span data-stu-id="badf8-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="badf8-140">ASP.NET Core에서 OWIN 기반 서버 기능을 활용할 수 있는 방법의 또 다른 예는 WebSocket과 같은 기능에 대한 액세스입니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="badf8-141">이전 예제에서 사용되는 .NET OWIN 웹 서버에는 ASP.NET Core 애플리케이션에서 활용할 수 있는 기본 제공되는 웹 소켓에 대한 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="badf8-142">다음 예제에서는 웹 소켓을 지원하고 WebSocket을 통해 서버에 전송된 모든 항목을 다시 표시하는 단순한 웹앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="badf8-143">이 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample)은 이전 것과 동일한 `NowinServer`를 사용하여 구성됩니다. 유일한 차이점은 애플리케이션이 해당 `Configure` 메서드에서 구성되는 방식에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="badf8-144">[간단한 websocket 클라이언트](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en)를 사용하는 테스트는 다음 애플리케이션을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![웹 소켓 테스트 클라이언트](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="badf8-146">OWIN 환경</span><span class="sxs-lookup"><span data-stu-id="badf8-146">OWIN environment</span></span>

<span data-ttu-id="badf8-147">`HttpContext`를 사용하여 OWIN 환경을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="badf8-148">OWIN 키</span><span class="sxs-lookup"><span data-stu-id="badf8-148">OWIN keys</span></span>

<span data-ttu-id="badf8-149">OWIN은 HTTP 요청/응답 교환 전체에서 정보를 전달하는 `IDictionary<string,object>` 개체에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="badf8-150">ASP.NET Core는 아래에 나열된 키를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="badf8-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="badf8-151">[기본 사양, 확장](https://owin.org/#spec) 및 [OWIN 키 지침 및 공통 키](https://owin.org/spec/spec/CommonKeys.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="badf8-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="badf8-152">요청 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="badf8-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="badf8-153">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-153">Key</span></span>               | <span data-ttu-id="badf8-154">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-154">Value (type)</span></span> | <span data-ttu-id="badf8-155">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="badf8-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="badf8-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="badf8-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="badf8-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="badf8-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="badf8-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="badf8-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="badf8-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="badf8-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="badf8-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="badf8-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="badf8-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="badf8-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="badf8-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="badf8-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="badf8-164">요청 데이터(OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="badf8-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="badf8-165">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-165">Key</span></span>               | <span data-ttu-id="badf8-166">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-166">Value (type)</span></span> | <span data-ttu-id="badf8-167">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="badf8-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="badf8-169">Optional</span><span class="sxs-lookup"><span data-stu-id="badf8-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="badf8-170">응답 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="badf8-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="badf8-171">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-171">Key</span></span>               | <span data-ttu-id="badf8-172">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-172">Value (type)</span></span> | <span data-ttu-id="badf8-173">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="badf8-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="badf8-175">Optional</span><span class="sxs-lookup"><span data-stu-id="badf8-175">Optional</span></span> |
| <span data-ttu-id="badf8-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="badf8-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="badf8-177">Optional</span><span class="sxs-lookup"><span data-stu-id="badf8-177">Optional</span></span> |
| <span data-ttu-id="badf8-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="badf8-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="badf8-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="badf8-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="badf8-180">기타 데이터(OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="badf8-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="badf8-181">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-181">Key</span></span>               | <span data-ttu-id="badf8-182">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-182">Value (type)</span></span> | <span data-ttu-id="badf8-183">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="badf8-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="badf8-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="badf8-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="badf8-186">공통 키</span><span class="sxs-lookup"><span data-stu-id="badf8-186">Common keys</span></span>

| <span data-ttu-id="badf8-187">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-187">Key</span></span>               | <span data-ttu-id="badf8-188">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-188">Value (type)</span></span> | <span data-ttu-id="badf8-189">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="badf8-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="badf8-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="badf8-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="badf8-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="badf8-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="badf8-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="badf8-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="badf8-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="badf8-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="badf8-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="badf8-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="badf8-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="badf8-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="badf8-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="badf8-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="badf8-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="badf8-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="badf8-199">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-199">Key</span></span>               | <span data-ttu-id="badf8-200">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-200">Value (type)</span></span> | <span data-ttu-id="badf8-201">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="badf8-202">sendfile.SendAsync</span></span> | <span data-ttu-id="badf8-203">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="badf8-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="badf8-204">요청당</span><span class="sxs-lookup"><span data-stu-id="badf8-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="badf8-205">불투명 v0.3.0</span><span class="sxs-lookup"><span data-stu-id="badf8-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="badf8-206">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-206">Key</span></span>               | <span data-ttu-id="badf8-207">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-207">Value (type)</span></span> | <span data-ttu-id="badf8-208">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="badf8-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="badf8-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="badf8-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="badf8-211">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="badf8-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="badf8-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="badf8-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="badf8-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="badf8-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="badf8-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="badf8-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="badf8-215">Key</span><span class="sxs-lookup"><span data-stu-id="badf8-215">Key</span></span>               | <span data-ttu-id="badf8-216">값(형식)</span><span class="sxs-lookup"><span data-stu-id="badf8-216">Value (type)</span></span> | <span data-ttu-id="badf8-217">설명</span><span class="sxs-lookup"><span data-stu-id="badf8-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="badf8-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="badf8-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="badf8-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="badf8-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="badf8-220">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="badf8-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="badf8-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="badf8-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="badf8-222">비-사양</span><span class="sxs-lookup"><span data-stu-id="badf8-222">Non-spec</span></span> |
| <span data-ttu-id="badf8-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="badf8-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="badf8-224">[RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) 5.5단계 참조</span><span class="sxs-lookup"><span data-stu-id="badf8-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="badf8-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="badf8-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="badf8-226">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="badf8-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="badf8-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="badf8-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="badf8-228">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="badf8-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="badf8-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="badf8-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="badf8-230">[대리자 시그니처](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) 참조</span><span class="sxs-lookup"><span data-stu-id="badf8-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="badf8-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="badf8-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="badf8-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="badf8-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="badf8-233">Optional</span><span class="sxs-lookup"><span data-stu-id="badf8-233">Optional</span></span> |
| <span data-ttu-id="badf8-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="badf8-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="badf8-235">Optional</span><span class="sxs-lookup"><span data-stu-id="badf8-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="badf8-236">추가 자료</span><span class="sxs-lookup"><span data-stu-id="badf8-236">Additional resources</span></span>

* [<span data-ttu-id="badf8-237">미들웨어</span><span class="sxs-lookup"><span data-stu-id="badf8-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="badf8-238">서버</span><span class="sxs-lookup"><span data-stu-id="badf8-238">Servers</span></span>](xref:fundamentals/servers/index)
