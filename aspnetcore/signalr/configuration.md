---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core 앱을 구성 하는 방법을 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: fc0e6398884bb5c3b806a587a8a361d7f279461f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625559"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="cb8fb-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb8fb-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-105">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb8fb-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb8fb-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cb8fb-108">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb8fb-109">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb8fb-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb8fb-111">자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cb8fb-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="cb8fb-113">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb8fb-114">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-115">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cb8fb-116">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="cb8fb-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cb8fb-117">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb8fb-118">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-118">MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-119">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb8fb-120">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-121">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb8fb-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-122">Configure server options</span></span>

<span data-ttu-id="cb8fb-123">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb8fb-124">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-124">Option</span></span> | <span data-ttu-id="cb8fb-125">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-125">Default Value</span></span> | <span data-ttu-id="cb8fb-126">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb8fb-127">30초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-127">30 seconds</span></span> | <span data-ttu-id="cb8fb-128">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb8fb-129">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb8fb-130">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-131">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-131">15 seconds</span></span> | <span data-ttu-id="cb8fb-132">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb8fb-133">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-134">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-135">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-135">15 seconds</span></span> | <span data-ttu-id="cb8fb-136">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb8fb-137">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb8fb-138">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb8fb-139">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="cb8fb-139">All installed protocols</span></span> | <span data-ttu-id="cb8fb-140">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-140">Protocols supported by this hub.</span></span> <span data-ttu-id="cb8fb-141">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb8fb-142">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb8fb-143">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cb8fb-144">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cb8fb-145">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cb8fb-146">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-146">32 KB</span></span> | <span data-ttu-id="cb8fb-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cb8fb-148">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="cb8fb-149">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb8fb-150">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb8fb-151">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb8fb-152">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="cb8fb-153">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb8fb-154">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-154">Option</span></span> | <span data-ttu-id="cb8fb-155">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-155">Default Value</span></span> | <span data-ttu-id="cb8fb-156">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb8fb-157">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-157">32 KB</span></span> | <span data-ttu-id="cb8fb-158">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cb8fb-159">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb8fb-160">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb8fb-161">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb8fb-162">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-162">32 KB</span></span> | <span data-ttu-id="cb8fb-163">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cb8fb-164">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb8fb-165">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-165">All Transports are enabled.</span></span> | <span data-ttu-id="cb8fb-166">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb8fb-167">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-167">See below.</span></span> | <span data-ttu-id="cb8fb-168">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb8fb-169">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-169">See below.</span></span> | <span data-ttu-id="cb8fb-170">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="cb8fb-171">0</span><span class="sxs-lookup"><span data-stu-id="cb8fb-171">0</span></span> | <span data-ttu-id="cb8fb-172">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="cb8fb-173">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="cb8fb-174">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb8fb-175">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-175">Option</span></span> | <span data-ttu-id="cb8fb-176">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-176">Default Value</span></span> | <span data-ttu-id="cb8fb-177">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb8fb-178">90 초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-178">90 seconds</span></span> | <span data-ttu-id="cb8fb-179">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb8fb-180">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb8fb-181">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb8fb-182">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-182">Option</span></span> | <span data-ttu-id="cb8fb-183">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-183">Default Value</span></span> | <span data-ttu-id="cb8fb-184">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb8fb-185">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-185">5 seconds</span></span> | <span data-ttu-id="cb8fb-186">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb8fb-187">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb8fb-188">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb8fb-189">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-189">Configure client options</span></span>

<span data-ttu-id="cb8fb-190">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb8fb-191">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb8fb-192">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-192">Configure logging</span></span>

<span data-ttu-id="cb8fb-193">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb8fb-194">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb8fb-195">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-196">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb8fb-197">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb8fb-198">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb8fb-199">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb8fb-200">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb8fb-201">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb8fb-202">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cb8fb-203">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cb8fb-204">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cb8fb-205">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-205">The following table lists the available log levels.</span></span> <span data-ttu-id="cb8fb-206">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cb8fb-207">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cb8fb-208">String</span><span class="sxs-lookup"><span data-stu-id="cb8fb-208">String</span></span>                      | <span data-ttu-id="cb8fb-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cb8fb-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cb8fb-210">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cb8fb-211">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cb8fb-212">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb8fb-213">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb8fb-214">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb8fb-215">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb8fb-216">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb8fb-217">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb8fb-218">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb8fb-219">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-219">Configure allowed transports</span></span>

<span data-ttu-id="cb8fb-220">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb8fb-221">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb8fb-222">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-222">All transports are enabled by default.</span></span>

<span data-ttu-id="cb8fb-223">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb8fb-224">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb8fb-225">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cb8fb-226">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cb8fb-227">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-228">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb8fb-229">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-229">Configure bearer authentication</span></span>

<span data-ttu-id="cb8fb-230">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb8fb-231">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb8fb-232">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="cb8fb-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb8fb-233">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb8fb-234">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb8fb-235">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cb8fb-236">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb8fb-237">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb8fb-238">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb8fb-239">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb8fb-240">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-241">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-242">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-242">Option</span></span> | <span data-ttu-id="cb8fb-243">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-243">Default value</span></span> | <span data-ttu-id="cb8fb-244">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb8fb-245">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-246">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-246">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-247">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-248">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-249">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-250">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-250">15 seconds</span></span> | <span data-ttu-id="cb8fb-251">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-252">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-253">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-254">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-255">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-255">15 seconds</span></span> | <span data-ttu-id="cb8fb-256">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-257">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-258">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb8fb-259">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-261">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-261">Option</span></span> | <span data-ttu-id="cb8fb-262">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-262">Default value</span></span> | <span data-ttu-id="cb8fb-263">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb8fb-264">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-265">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-265">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-266">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb8fb-267">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-268">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb8fb-269">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-270">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-271">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-272">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-273">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-273">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-274">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-274">Option</span></span> | <span data-ttu-id="cb8fb-275">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-275">Default value</span></span> | <span data-ttu-id="cb8fb-276">설명</span><span class="sxs-lookup"><span data-stu-id="cb8fb-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb8fb-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb8fb-278">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-279">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-279">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-280">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-281">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-282">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb8fb-283">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-283">15 seconds</span></span> | <span data-ttu-id="cb8fb-284">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-285">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-286">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-287">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb8fb-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb8fb-289">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-290">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-291">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-292">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb8fb-293">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-293">Configure additional options</span></span>

<span data-ttu-id="cb8fb-294">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-295">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-296">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-296">.NET Option</span></span> |  <span data-ttu-id="cb8fb-297">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-297">Default value</span></span> | <span data-ttu-id="cb8fb-298">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-299">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb8fb-300">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-301">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-302">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb8fb-303">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-303">Empty</span></span> | <span data-ttu-id="cb8fb-304">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb8fb-305">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-305">Empty</span></span> | <span data-ttu-id="cb8fb-306">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb8fb-307">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-307">Empty</span></span> | <span data-ttu-id="cb8fb-308">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb8fb-309">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-309">5 seconds</span></span> | <span data-ttu-id="cb8fb-310">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-310">WebSockets only.</span></span> <span data-ttu-id="cb8fb-311">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb8fb-312">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb8fb-313">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-313">Empty</span></span> | <span data-ttu-id="cb8fb-314">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb8fb-315">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb8fb-316">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb8fb-317">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb8fb-318">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb8fb-319">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="cb8fb-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb8fb-320">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb8fb-321">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb8fb-322">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb8fb-323">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb8fb-324">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-326">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-326">JavaScript Option</span></span> | <span data-ttu-id="cb8fb-327">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-327">Default Value</span></span> | <span data-ttu-id="cb8fb-328">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb8fb-329">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="cb8fb-330">모든 HTTP 요청과 함께 전송 되는 헤더의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="cb8fb-331">브라우저에서 헤더 보내기가 Websocket 또는 스트림에 대해 작동 하지 않습니다 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cb8fb-332">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb8fb-333">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-334">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-335">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="cb8fb-336">CORS 요청과 함께 자격 증명을 보낼지 여부를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="cb8fb-337">Azure App Service는 cookie 고정 세션에 s를 사용 하 고 올바르게 작동 하려면이 옵션을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="cb8fb-338">CORS에 대 한 자세한 SignalR 내용은을 참조 하십시오 <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-339">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-339">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-340">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-340">Java Option</span></span> | <span data-ttu-id="cb8fb-341">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-341">Default Value</span></span> | <span data-ttu-id="cb8fb-342">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-343">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb8fb-344">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-345">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-346">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb8fb-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb8fb-348">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-348">Empty</span></span> | <span data-ttu-id="cb8fb-349">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb8fb-350">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb8fb-351">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb8fb-352">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb8fb-353">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cb8fb-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb8fb-354">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-355">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb8fb-356">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb8fb-357">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cb8fb-358">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb8fb-359">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb8fb-360">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb8fb-361">자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cb8fb-362">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="cb8fb-363">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb8fb-364">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-365">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cb8fb-366">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="cb8fb-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cb8fb-367">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb8fb-368">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-368">MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-369">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb8fb-370">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-371">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb8fb-372">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-372">Configure server options</span></span>

<span data-ttu-id="cb8fb-373">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb8fb-374">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-374">Option</span></span> | <span data-ttu-id="cb8fb-375">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-375">Default Value</span></span> | <span data-ttu-id="cb8fb-376">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb8fb-377">30초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-377">30 seconds</span></span> | <span data-ttu-id="cb8fb-378">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb8fb-379">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb8fb-380">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-381">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-381">15 seconds</span></span> | <span data-ttu-id="cb8fb-382">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb8fb-383">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-384">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-385">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-385">15 seconds</span></span> | <span data-ttu-id="cb8fb-386">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb8fb-387">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb8fb-388">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb8fb-389">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="cb8fb-389">All installed protocols</span></span> | <span data-ttu-id="cb8fb-390">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-390">Protocols supported by this hub.</span></span> <span data-ttu-id="cb8fb-391">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb8fb-392">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb8fb-393">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cb8fb-394">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cb8fb-395">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cb8fb-396">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-396">32 KB</span></span> | <span data-ttu-id="cb8fb-397">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cb8fb-398">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="cb8fb-399">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb8fb-400">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb8fb-401">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb8fb-402">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="cb8fb-403">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb8fb-404">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-404">Option</span></span> | <span data-ttu-id="cb8fb-405">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-405">Default Value</span></span> | <span data-ttu-id="cb8fb-406">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb8fb-407">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-407">32 KB</span></span> | <span data-ttu-id="cb8fb-408">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cb8fb-409">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb8fb-410">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb8fb-411">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb8fb-412">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-412">32 KB</span></span> | <span data-ttu-id="cb8fb-413">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cb8fb-414">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb8fb-415">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-415">All Transports are enabled.</span></span> | <span data-ttu-id="cb8fb-416">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb8fb-417">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-417">See below.</span></span> | <span data-ttu-id="cb8fb-418">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb8fb-419">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-419">See below.</span></span> | <span data-ttu-id="cb8fb-420">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="cb8fb-421">0</span><span class="sxs-lookup"><span data-stu-id="cb8fb-421">0</span></span> | <span data-ttu-id="cb8fb-422">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="cb8fb-423">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="cb8fb-424">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb8fb-425">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-425">Option</span></span> | <span data-ttu-id="cb8fb-426">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-426">Default Value</span></span> | <span data-ttu-id="cb8fb-427">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb8fb-428">90 초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-428">90 seconds</span></span> | <span data-ttu-id="cb8fb-429">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb8fb-430">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb8fb-431">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb8fb-432">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-432">Option</span></span> | <span data-ttu-id="cb8fb-433">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-433">Default Value</span></span> | <span data-ttu-id="cb8fb-434">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb8fb-435">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-435">5 seconds</span></span> | <span data-ttu-id="cb8fb-436">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb8fb-437">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb8fb-438">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb8fb-439">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-439">Configure client options</span></span>

<span data-ttu-id="cb8fb-440">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb8fb-441">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb8fb-442">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-442">Configure logging</span></span>

<span data-ttu-id="cb8fb-443">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb8fb-444">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb8fb-445">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-446">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb8fb-447">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb8fb-448">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb8fb-449">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb8fb-450">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb8fb-451">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb8fb-452">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cb8fb-453">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cb8fb-454">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cb8fb-455">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-455">The following table lists the available log levels.</span></span> <span data-ttu-id="cb8fb-456">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cb8fb-457">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cb8fb-458">String</span><span class="sxs-lookup"><span data-stu-id="cb8fb-458">String</span></span>                      | <span data-ttu-id="cb8fb-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cb8fb-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cb8fb-460">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cb8fb-461">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cb8fb-462">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb8fb-463">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb8fb-464">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb8fb-465">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb8fb-466">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb8fb-467">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb8fb-468">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb8fb-469">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-469">Configure allowed transports</span></span>

<span data-ttu-id="cb8fb-470">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb8fb-471">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb8fb-472">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-472">All transports are enabled by default.</span></span>

<span data-ttu-id="cb8fb-473">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb8fb-474">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb8fb-475">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cb8fb-476">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cb8fb-477">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-478">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb8fb-479">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-479">Configure bearer authentication</span></span>

<span data-ttu-id="cb8fb-480">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb8fb-481">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb8fb-482">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="cb8fb-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb8fb-483">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb8fb-484">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb8fb-485">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cb8fb-486">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb8fb-487">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb8fb-488">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb8fb-489">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb8fb-490">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-491">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-492">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-492">Option</span></span> | <span data-ttu-id="cb8fb-493">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-493">Default value</span></span> | <span data-ttu-id="cb8fb-494">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb8fb-495">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-496">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-496">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-497">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-498">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-499">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-500">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-500">15 seconds</span></span> | <span data-ttu-id="cb8fb-501">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-502">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-503">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-504">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-505">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-505">15 seconds</span></span> | <span data-ttu-id="cb8fb-506">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-507">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-508">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb8fb-509">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-511">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-511">Option</span></span> | <span data-ttu-id="cb8fb-512">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-512">Default value</span></span> | <span data-ttu-id="cb8fb-513">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb8fb-514">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-515">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-515">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-516">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb8fb-517">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-518">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb8fb-519">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-520">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-521">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-522">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-523">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-523">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-524">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-524">Option</span></span> | <span data-ttu-id="cb8fb-525">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-525">Default value</span></span> | <span data-ttu-id="cb8fb-526">설명</span><span class="sxs-lookup"><span data-stu-id="cb8fb-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb8fb-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb8fb-528">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-529">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-529">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-530">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-531">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-532">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb8fb-533">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-533">15 seconds</span></span> | <span data-ttu-id="cb8fb-534">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-535">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-536">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-537">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb8fb-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb8fb-539">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-540">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-541">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-542">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb8fb-543">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-543">Configure additional options</span></span>

<span data-ttu-id="cb8fb-544">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-545">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-546">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-546">.NET Option</span></span> |  <span data-ttu-id="cb8fb-547">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-547">Default value</span></span> | <span data-ttu-id="cb8fb-548">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-549">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb8fb-550">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-551">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-552">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb8fb-553">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-553">Empty</span></span> | <span data-ttu-id="cb8fb-554">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb8fb-555">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-555">Empty</span></span> | <span data-ttu-id="cb8fb-556">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb8fb-557">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-557">Empty</span></span> | <span data-ttu-id="cb8fb-558">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb8fb-559">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-559">5 seconds</span></span> | <span data-ttu-id="cb8fb-560">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-560">WebSockets only.</span></span> <span data-ttu-id="cb8fb-561">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb8fb-562">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb8fb-563">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-563">Empty</span></span> | <span data-ttu-id="cb8fb-564">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb8fb-565">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb8fb-566">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb8fb-567">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb8fb-568">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb8fb-569">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="cb8fb-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb8fb-570">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb8fb-571">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb8fb-572">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb8fb-573">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb8fb-574">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-576">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-576">JavaScript Option</span></span> | <span data-ttu-id="cb8fb-577">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-577">Default Value</span></span> | <span data-ttu-id="cb8fb-578">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb8fb-579">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cb8fb-580">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb8fb-581">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-582">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-583">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-584">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-584">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-585">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-585">Java Option</span></span> | <span data-ttu-id="cb8fb-586">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-586">Default Value</span></span> | <span data-ttu-id="cb8fb-587">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-588">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb8fb-589">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-590">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-591">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb8fb-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb8fb-593">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-593">Empty</span></span> | <span data-ttu-id="cb8fb-594">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb8fb-595">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb8fb-596">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb8fb-597">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb8fb-598">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cb8fb-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb8fb-599">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-600">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb8fb-601">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb8fb-602">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cb8fb-603">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb8fb-604">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb8fb-605">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb8fb-606">자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cb8fb-607">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="cb8fb-608">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb8fb-609">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-610">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cb8fb-611">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="cb8fb-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cb8fb-612">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb8fb-613">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-613">MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-614">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb8fb-615">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-616">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb8fb-617">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-617">Configure server options</span></span>

<span data-ttu-id="cb8fb-618">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb8fb-619">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-619">Option</span></span> | <span data-ttu-id="cb8fb-620">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-620">Default Value</span></span> | <span data-ttu-id="cb8fb-621">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb8fb-622">30초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-622">30 seconds</span></span> | <span data-ttu-id="cb8fb-623">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb8fb-624">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb8fb-625">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-626">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-626">15 seconds</span></span> | <span data-ttu-id="cb8fb-627">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb8fb-628">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-629">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-630">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-630">15 seconds</span></span> | <span data-ttu-id="cb8fb-631">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb8fb-632">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb8fb-633">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb8fb-634">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="cb8fb-634">All installed protocols</span></span> | <span data-ttu-id="cb8fb-635">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-635">Protocols supported by this hub.</span></span> <span data-ttu-id="cb8fb-636">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb8fb-637">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb8fb-638">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cb8fb-639">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cb8fb-640">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cb8fb-641">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-641">32 KB</span></span> | <span data-ttu-id="cb8fb-642">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cb8fb-643">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="cb8fb-644">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb8fb-645">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb8fb-646">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb8fb-647">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="cb8fb-648">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb8fb-649">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-649">Option</span></span> | <span data-ttu-id="cb8fb-650">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-650">Default Value</span></span> | <span data-ttu-id="cb8fb-651">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb8fb-652">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-652">32 KB</span></span> | <span data-ttu-id="cb8fb-653">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cb8fb-654">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb8fb-655">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb8fb-656">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb8fb-657">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-657">32 KB</span></span> | <span data-ttu-id="cb8fb-658">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cb8fb-659">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb8fb-660">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-660">All Transports are enabled.</span></span> | <span data-ttu-id="cb8fb-661">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb8fb-662">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-662">See below.</span></span> | <span data-ttu-id="cb8fb-663">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb8fb-664">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-664">See below.</span></span> | <span data-ttu-id="cb8fb-665">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cb8fb-666">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb8fb-667">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-667">Option</span></span> | <span data-ttu-id="cb8fb-668">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-668">Default Value</span></span> | <span data-ttu-id="cb8fb-669">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb8fb-670">90 초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-670">90 seconds</span></span> | <span data-ttu-id="cb8fb-671">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb8fb-672">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb8fb-673">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb8fb-674">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-674">Option</span></span> | <span data-ttu-id="cb8fb-675">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-675">Default Value</span></span> | <span data-ttu-id="cb8fb-676">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb8fb-677">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-677">5 seconds</span></span> | <span data-ttu-id="cb8fb-678">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb8fb-679">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb8fb-680">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb8fb-681">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-681">Configure client options</span></span>

<span data-ttu-id="cb8fb-682">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb8fb-683">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb8fb-684">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-684">Configure logging</span></span>

<span data-ttu-id="cb8fb-685">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb8fb-686">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb8fb-687">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-688">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb8fb-689">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb8fb-690">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb8fb-691">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb8fb-692">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb8fb-693">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb8fb-694">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cb8fb-695">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cb8fb-696">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cb8fb-697">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-697">The following table lists the available log levels.</span></span> <span data-ttu-id="cb8fb-698">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cb8fb-699">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cb8fb-700">String</span><span class="sxs-lookup"><span data-stu-id="cb8fb-700">String</span></span>                      | <span data-ttu-id="cb8fb-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cb8fb-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cb8fb-702">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cb8fb-703">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cb8fb-704">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb8fb-705">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb8fb-706">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb8fb-707">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb8fb-708">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb8fb-709">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb8fb-710">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb8fb-711">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-711">Configure allowed transports</span></span>

<span data-ttu-id="cb8fb-712">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb8fb-713">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb8fb-714">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-714">All transports are enabled by default.</span></span>

<span data-ttu-id="cb8fb-715">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb8fb-716">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb8fb-717">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cb8fb-718">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cb8fb-719">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-720">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb8fb-721">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-721">Configure bearer authentication</span></span>

<span data-ttu-id="cb8fb-722">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb8fb-723">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb8fb-724">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="cb8fb-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb8fb-725">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb8fb-726">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb8fb-727">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cb8fb-728">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb8fb-729">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb8fb-730">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb8fb-731">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb8fb-732">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-733">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-734">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-734">Option</span></span> | <span data-ttu-id="cb8fb-735">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-735">Default value</span></span> | <span data-ttu-id="cb8fb-736">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb8fb-737">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-738">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-738">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-739">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-740">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-741">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-742">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-742">15 seconds</span></span> | <span data-ttu-id="cb8fb-743">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-744">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-745">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-746">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-747">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-747">15 seconds</span></span> | <span data-ttu-id="cb8fb-748">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-749">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-750">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb8fb-751">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-753">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-753">Option</span></span> | <span data-ttu-id="cb8fb-754">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-754">Default value</span></span> | <span data-ttu-id="cb8fb-755">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb8fb-756">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-757">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-757">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-758">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb8fb-759">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-760">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb8fb-761">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-762">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-763">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-764">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-765">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-765">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-766">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-766">Option</span></span> | <span data-ttu-id="cb8fb-767">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-767">Default value</span></span> | <span data-ttu-id="cb8fb-768">설명</span><span class="sxs-lookup"><span data-stu-id="cb8fb-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb8fb-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb8fb-770">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-771">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-771">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-772">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-773">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-774">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb8fb-775">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-775">15 seconds</span></span> | <span data-ttu-id="cb8fb-776">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-777">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-778">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-779">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb8fb-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb8fb-781">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-782">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-783">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-784">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb8fb-785">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-785">Configure additional options</span></span>

<span data-ttu-id="cb8fb-786">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-787">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-788">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-788">.NET Option</span></span> |  <span data-ttu-id="cb8fb-789">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-789">Default value</span></span> | <span data-ttu-id="cb8fb-790">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-791">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb8fb-792">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-793">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-794">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb8fb-795">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-795">Empty</span></span> | <span data-ttu-id="cb8fb-796">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb8fb-797">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-797">Empty</span></span> | <span data-ttu-id="cb8fb-798">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb8fb-799">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-799">Empty</span></span> | <span data-ttu-id="cb8fb-800">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb8fb-801">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-801">5 seconds</span></span> | <span data-ttu-id="cb8fb-802">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-802">WebSockets only.</span></span> <span data-ttu-id="cb8fb-803">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb8fb-804">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb8fb-805">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-805">Empty</span></span> | <span data-ttu-id="cb8fb-806">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb8fb-807">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb8fb-808">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb8fb-809">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb8fb-810">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb8fb-811">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="cb8fb-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb8fb-812">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb8fb-813">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb8fb-814">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb8fb-815">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb8fb-816">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-818">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-818">JavaScript Option</span></span> | <span data-ttu-id="cb8fb-819">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-819">Default Value</span></span> | <span data-ttu-id="cb8fb-820">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb8fb-821">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cb8fb-822">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb8fb-823">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-824">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-825">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-826">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-826">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-827">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-827">Java Option</span></span> | <span data-ttu-id="cb8fb-828">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-828">Default Value</span></span> | <span data-ttu-id="cb8fb-829">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-830">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb8fb-831">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-832">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-833">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb8fb-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb8fb-835">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-835">Empty</span></span> | <span data-ttu-id="cb8fb-836">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb8fb-837">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb8fb-838">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb8fb-839">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb8fb-840">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cb8fb-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb8fb-841">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-842">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb8fb-843">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb8fb-844">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cb8fb-845">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb8fb-846">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb8fb-847">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="cb8fb-848">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="cb8fb-849">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb8fb-850">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-851">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb8fb-852">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-852">MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-853">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb8fb-854">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-855">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb8fb-856">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-856">Configure server options</span></span>

<span data-ttu-id="cb8fb-857">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb8fb-858">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-858">Option</span></span> | <span data-ttu-id="cb8fb-859">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-859">Default Value</span></span> | <span data-ttu-id="cb8fb-860">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cb8fb-861">30초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-861">30 seconds</span></span> | <span data-ttu-id="cb8fb-862">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cb8fb-863">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cb8fb-864">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-865">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-865">15 seconds</span></span> | <span data-ttu-id="cb8fb-866">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb8fb-867">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-868">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-869">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-869">15 seconds</span></span> | <span data-ttu-id="cb8fb-870">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb8fb-871">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb8fb-872">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb8fb-873">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="cb8fb-873">All installed protocols</span></span> | <span data-ttu-id="cb8fb-874">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-874">Protocols supported by this hub.</span></span> <span data-ttu-id="cb8fb-875">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb8fb-876">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb8fb-877">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="cb8fb-878">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="cb8fb-879">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb8fb-880">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb8fb-881">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb8fb-882">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="cb8fb-883">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb8fb-884">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-884">Option</span></span> | <span data-ttu-id="cb8fb-885">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-885">Default Value</span></span> | <span data-ttu-id="cb8fb-886">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb8fb-887">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-887">32 KB</span></span> | <span data-ttu-id="cb8fb-888">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="cb8fb-889">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb8fb-890">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb8fb-891">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb8fb-892">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-892">32 KB</span></span> | <span data-ttu-id="cb8fb-893">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="cb8fb-894">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb8fb-895">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-895">All Transports are enabled.</span></span> | <span data-ttu-id="cb8fb-896">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb8fb-897">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-897">See below.</span></span> | <span data-ttu-id="cb8fb-898">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb8fb-899">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-899">See below.</span></span> | <span data-ttu-id="cb8fb-900">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cb8fb-901">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb8fb-902">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-902">Option</span></span> | <span data-ttu-id="cb8fb-903">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-903">Default Value</span></span> | <span data-ttu-id="cb8fb-904">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb8fb-905">90 초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-905">90 seconds</span></span> | <span data-ttu-id="cb8fb-906">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb8fb-907">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb8fb-908">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb8fb-909">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-909">Option</span></span> | <span data-ttu-id="cb8fb-910">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-910">Default Value</span></span> | <span data-ttu-id="cb8fb-911">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb8fb-912">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-912">5 seconds</span></span> | <span data-ttu-id="cb8fb-913">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb8fb-914">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb8fb-915">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb8fb-916">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-916">Configure client options</span></span>

<span data-ttu-id="cb8fb-917">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb8fb-918">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb8fb-919">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-919">Configure logging</span></span>

<span data-ttu-id="cb8fb-920">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb8fb-921">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb8fb-922">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-923">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb8fb-924">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb8fb-925">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb8fb-926">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb8fb-927">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb8fb-928">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb8fb-929">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-930">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb8fb-931">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb8fb-932">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb8fb-933">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb8fb-934">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb8fb-935">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb8fb-936">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb8fb-937">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-937">Configure allowed transports</span></span>

<span data-ttu-id="cb8fb-938">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb8fb-939">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb8fb-940">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-940">All transports are enabled by default.</span></span>

<span data-ttu-id="cb8fb-941">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb8fb-942">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cb8fb-943">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb8fb-944">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-944">Configure bearer authentication</span></span>

<span data-ttu-id="cb8fb-945">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb8fb-946">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb8fb-947">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="cb8fb-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb8fb-948">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb8fb-949">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb8fb-950">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cb8fb-951">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb8fb-952">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb8fb-953">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb8fb-954">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb8fb-955">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-956">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-957">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-957">Option</span></span> | <span data-ttu-id="cb8fb-958">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-958">Default value</span></span> | <span data-ttu-id="cb8fb-959">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb8fb-960">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-961">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-961">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-962">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-963">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-964">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-965">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-965">15 seconds</span></span> | <span data-ttu-id="cb8fb-966">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-967">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-968">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-969">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-970">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-970">15 seconds</span></span> | <span data-ttu-id="cb8fb-971">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-972">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-973">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cb8fb-974">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-976">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-976">Option</span></span> | <span data-ttu-id="cb8fb-977">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-977">Default value</span></span> | <span data-ttu-id="cb8fb-978">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb8fb-979">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-980">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-980">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-981">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb8fb-982">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-983">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cb8fb-984">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-985">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-986">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-987">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-988">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-988">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-989">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-989">Option</span></span> | <span data-ttu-id="cb8fb-990">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-990">Default value</span></span> | <span data-ttu-id="cb8fb-991">설명</span><span class="sxs-lookup"><span data-stu-id="cb8fb-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb8fb-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb8fb-993">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-994">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-994">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-995">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-996">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-997">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb8fb-998">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-998">15 seconds</span></span> | <span data-ttu-id="cb8fb-999">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-1000">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-1001">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-1002">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cb8fb-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cb8fb-1004">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-1005">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cb8fb-1006">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cb8fb-1007">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb8fb-1008">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1008">Configure additional options</span></span>

<span data-ttu-id="cb8fb-1009">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-1011">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1011">.NET Option</span></span> |  <span data-ttu-id="cb8fb-1012">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1012">Default value</span></span> | <span data-ttu-id="cb8fb-1013">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-1014">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb8fb-1015">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-1016">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-1017">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb8fb-1018">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1018">Empty</span></span> | <span data-ttu-id="cb8fb-1019">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb8fb-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1020">Empty</span></span> | <span data-ttu-id="cb8fb-1021">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb8fb-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1022">Empty</span></span> | <span data-ttu-id="cb8fb-1023">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb8fb-1024">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1024">5 seconds</span></span> | <span data-ttu-id="cb8fb-1025">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1025">WebSockets only.</span></span> <span data-ttu-id="cb8fb-1026">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb8fb-1027">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb8fb-1028">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1028">Empty</span></span> | <span data-ttu-id="cb8fb-1029">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb8fb-1030">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb8fb-1031">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb8fb-1032">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb8fb-1033">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb8fb-1034">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb8fb-1035">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb8fb-1036">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb8fb-1037">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb8fb-1038">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb8fb-1039">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-1041">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1041">JavaScript Option</span></span> | <span data-ttu-id="cb8fb-1042">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1042">Default Value</span></span> | <span data-ttu-id="cb8fb-1043">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb8fb-1044">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cb8fb-1045">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb8fb-1046">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-1047">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-1048">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-1049">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-1050">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1050">Java Option</span></span> | <span data-ttu-id="cb8fb-1051">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1051">Default Value</span></span> | <span data-ttu-id="cb8fb-1052">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-1053">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb8fb-1054">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-1055">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-1056">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb8fb-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb8fb-1058">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1058">Empty</span></span> | <span data-ttu-id="cb8fb-1059">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb8fb-1060">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb8fb-1061">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb8fb-1062">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb8fb-1063">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cb8fb-1064">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-1065">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cb8fb-1066">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cb8fb-1067">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cb8fb-1068">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cb8fb-1069">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cb8fb-1070">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="cb8fb-1071">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="cb8fb-1072">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cb8fb-1073">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-1074">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cb8fb-1075">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1075">MessagePack serialization options</span></span>

<span data-ttu-id="cb8fb-1076">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cb8fb-1077">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-1078">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cb8fb-1079">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1079">Configure server options</span></span>

<span data-ttu-id="cb8fb-1080">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cb8fb-1081">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1081">Option</span></span> | <span data-ttu-id="cb8fb-1082">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1082">Default Value</span></span> | <span data-ttu-id="cb8fb-1083">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-1084">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1084">15 seconds</span></span> | <span data-ttu-id="cb8fb-1085">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cb8fb-1086">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-1087">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cb8fb-1088">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1088">15 seconds</span></span> | <span data-ttu-id="cb8fb-1089">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cb8fb-1090">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cb8fb-1091">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cb8fb-1092">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1092">All installed protocols</span></span> | <span data-ttu-id="cb8fb-1093">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="cb8fb-1094">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cb8fb-1095">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cb8fb-1096">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="cb8fb-1097">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="cb8fb-1098">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cb8fb-1099">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cb8fb-1100">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cb8fb-1101">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="cb8fb-1102">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cb8fb-1103">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1103">Option</span></span> | <span data-ttu-id="cb8fb-1104">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1104">Default Value</span></span> | <span data-ttu-id="cb8fb-1105">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cb8fb-1106">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1106">32 KB</span></span> | <span data-ttu-id="cb8fb-1107">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="cb8fb-1108">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cb8fb-1109">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cb8fb-1110">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cb8fb-1111">32KB</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1111">32 KB</span></span> | <span data-ttu-id="cb8fb-1112">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="cb8fb-1113">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cb8fb-1114">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1114">All Transports are enabled.</span></span> | <span data-ttu-id="cb8fb-1115">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cb8fb-1116">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1116">See below.</span></span> | <span data-ttu-id="cb8fb-1117">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cb8fb-1118">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1118">See below.</span></span> | <span data-ttu-id="cb8fb-1119">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cb8fb-1120">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cb8fb-1121">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1121">Option</span></span> | <span data-ttu-id="cb8fb-1122">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1122">Default Value</span></span> | <span data-ttu-id="cb8fb-1123">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cb8fb-1124">90 초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1124">90 seconds</span></span> | <span data-ttu-id="cb8fb-1125">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cb8fb-1126">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cb8fb-1127">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cb8fb-1128">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1128">Option</span></span> | <span data-ttu-id="cb8fb-1129">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1129">Default Value</span></span> | <span data-ttu-id="cb8fb-1130">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cb8fb-1131">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1131">5 seconds</span></span> | <span data-ttu-id="cb8fb-1132">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cb8fb-1133">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cb8fb-1134">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cb8fb-1135">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1135">Configure client options</span></span>

<span data-ttu-id="cb8fb-1136">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cb8fb-1137">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cb8fb-1138">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1138">Configure logging</span></span>

<span data-ttu-id="cb8fb-1139">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cb8fb-1140">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cb8fb-1141">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cb8fb-1142">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cb8fb-1143">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cb8fb-1144">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cb8fb-1145">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cb8fb-1146">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cb8fb-1147">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cb8fb-1148">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cb8fb-1149">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cb8fb-1150">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cb8fb-1151">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cb8fb-1152">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cb8fb-1153">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cb8fb-1154">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cb8fb-1155">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cb8fb-1156">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1156">Configure allowed transports</span></span>

<span data-ttu-id="cb8fb-1157">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cb8fb-1158">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cb8fb-1159">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="cb8fb-1160">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cb8fb-1161">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cb8fb-1162">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1162">Configure bearer authentication</span></span>

<span data-ttu-id="cb8fb-1163">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cb8fb-1164">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cb8fb-1165">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cb8fb-1166">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cb8fb-1167">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cb8fb-1168">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cb8fb-1169">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cb8fb-1170">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cb8fb-1171">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cb8fb-1172">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cb8fb-1173">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-1175">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1175">Option</span></span> | <span data-ttu-id="cb8fb-1176">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1176">Default value</span></span> | <span data-ttu-id="cb8fb-1177">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cb8fb-1178">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-1179">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1179">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-1180">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-1181">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-1182">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cb8fb-1183">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1183">15 seconds</span></span> | <span data-ttu-id="cb8fb-1184">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-1185">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cb8fb-1186">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-1187">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="cb8fb-1188">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-1190">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1190">Option</span></span> | <span data-ttu-id="cb8fb-1191">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1191">Default value</span></span> | <span data-ttu-id="cb8fb-1192">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cb8fb-1193">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-1194">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1194">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-1195">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cb8fb-1196">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-1197">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-1198">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-1199">옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1199">Option</span></span> | <span data-ttu-id="cb8fb-1200">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1200">Default value</span></span> | <span data-ttu-id="cb8fb-1201">설명</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cb8fb-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cb8fb-1203">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cb8fb-1204">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1204">Timeout for server activity.</span></span> <span data-ttu-id="cb8fb-1205">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-1206">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cb8fb-1207">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 시간을 허용 하는 서버 값을 두 번 이상 표시 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cb8fb-1208">15초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1208">15 seconds</span></span> | <span data-ttu-id="cb8fb-1209">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="cb8fb-1210">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cb8fb-1211">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cb8fb-1212">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cb8fb-1213">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1213">Configure additional options</span></span>

<span data-ttu-id="cb8fb-1214">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cb8fb-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cb8fb-1216">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1216">.NET Option</span></span> |  <span data-ttu-id="cb8fb-1217">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1217">Default value</span></span> | <span data-ttu-id="cb8fb-1218">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-1219">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cb8fb-1220">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-1221">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-1222">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cb8fb-1223">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1223">Empty</span></span> | <span data-ttu-id="cb8fb-1224">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cb8fb-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1225">Empty</span></span> | <span data-ttu-id="cb8fb-1226">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cb8fb-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1227">Empty</span></span> | <span data-ttu-id="cb8fb-1228">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cb8fb-1229">5초</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1229">5 seconds</span></span> | <span data-ttu-id="cb8fb-1230">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1230">WebSockets only.</span></span> <span data-ttu-id="cb8fb-1231">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cb8fb-1232">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cb8fb-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1233">Empty</span></span> | <span data-ttu-id="cb8fb-1234">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cb8fb-1235">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cb8fb-1236">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="cb8fb-1237">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cb8fb-1238">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cb8fb-1239">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cb8fb-1240">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cb8fb-1241">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cb8fb-1242">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cb8fb-1243">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cb8fb-1244">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cb8fb-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cb8fb-1246">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1246">JavaScript Option</span></span> | <span data-ttu-id="cb8fb-1247">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1247">Default Value</span></span> | <span data-ttu-id="cb8fb-1248">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cb8fb-1249">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cb8fb-1250">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cb8fb-1251">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-1252">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-1253">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cb8fb-1254">Java</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="cb8fb-1255">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1255">Java Option</span></span> | <span data-ttu-id="cb8fb-1256">기본값</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1256">Default Value</span></span> | <span data-ttu-id="cb8fb-1257">Description</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cb8fb-1258">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cb8fb-1259">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cb8fb-1260">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cb8fb-1261">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cb8fb-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cb8fb-1263">Empty</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1263">Empty</span></span> | <span data-ttu-id="cb8fb-1264">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cb8fb-1265">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cb8fb-1266">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cb8fb-1267">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cb8fb-1268">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cb8fb-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
