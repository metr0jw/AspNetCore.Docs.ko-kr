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
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393875"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="7708d-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7708d-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7708d-105">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7708d-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7708d-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7708d-108">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7708d-109">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="7708d-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7708d-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7708d-111">자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7708d-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="7708d-113">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7708d-114">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7708d-115">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7708d-116">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="7708d-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7708d-117">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7708d-118">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-118">MessagePack serialization options</span></span>

<span data-ttu-id="7708d-119">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7708d-120">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-121">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7708d-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-122">Configure server options</span></span>

<span data-ttu-id="7708d-123">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7708d-124">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-124">Option</span></span> | <span data-ttu-id="7708d-125">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-125">Default Value</span></span> | <span data-ttu-id="7708d-126">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7708d-127">30초</span><span class="sxs-lookup"><span data-stu-id="7708d-127">30 seconds</span></span> | <span data-ttu-id="7708d-128">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7708d-129">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7708d-130">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7708d-131">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-131">15 seconds</span></span> | <span data-ttu-id="7708d-132">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7708d-133">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-134">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-135">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-135">15 seconds</span></span> | <span data-ttu-id="7708d-136">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7708d-137">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7708d-138">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7708d-139">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7708d-139">All installed protocols</span></span> | <span data-ttu-id="7708d-140">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-140">Protocols supported by this hub.</span></span> <span data-ttu-id="7708d-141">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7708d-142">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7708d-143">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7708d-144">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7708d-145">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7708d-146">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-146">32 KB</span></span> | <span data-ttu-id="7708d-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="7708d-148">1</span><span class="sxs-lookup"><span data-stu-id="7708d-148">1</span></span> | <span data-ttu-id="7708d-149">각 클라이언트가 큐에 대기 하기 전에 병렬로 호출할 수 있는 최대 허브 메서드 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="7708d-150">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7708d-151">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="7708d-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7708d-152">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7708d-153">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7708d-154">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7708d-155">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7708d-156">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-156">Option</span></span> | <span data-ttu-id="7708d-157">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-157">Default Value</span></span> | <span data-ttu-id="7708d-158">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7708d-159">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-159">32 KB</span></span> | <span data-ttu-id="7708d-160">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7708d-161">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7708d-162">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7708d-163">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7708d-164">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-164">32 KB</span></span> | <span data-ttu-id="7708d-165">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7708d-166">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7708d-167">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-167">All Transports are enabled.</span></span> | <span data-ttu-id="7708d-168">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7708d-169">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-169">See below.</span></span> | <span data-ttu-id="7708d-170">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7708d-171">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-171">See below.</span></span> | <span data-ttu-id="7708d-172">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="7708d-173">0</span><span class="sxs-lookup"><span data-stu-id="7708d-173">0</span></span> | <span data-ttu-id="7708d-174">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="7708d-175">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="7708d-176">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="7708d-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7708d-177">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-177">Option</span></span> | <span data-ttu-id="7708d-178">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-178">Default Value</span></span> | <span data-ttu-id="7708d-179">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7708d-180">90 초</span><span class="sxs-lookup"><span data-stu-id="7708d-180">90 seconds</span></span> | <span data-ttu-id="7708d-181">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7708d-182">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7708d-183">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="7708d-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7708d-184">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-184">Option</span></span> | <span data-ttu-id="7708d-185">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-185">Default Value</span></span> | <span data-ttu-id="7708d-186">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7708d-187">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-187">5 seconds</span></span> | <span data-ttu-id="7708d-188">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7708d-189">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7708d-190">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7708d-191">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-191">Configure client options</span></span>

<span data-ttu-id="7708d-192">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7708d-193">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7708d-194">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-194">Configure logging</span></span>

<span data-ttu-id="7708d-195">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7708d-196">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7708d-197">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-198">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7708d-199">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7708d-200">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7708d-201">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="7708d-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7708d-202">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7708d-203">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7708d-204">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="7708d-205">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7708d-206">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7708d-207">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-207">The following table lists the available log levels.</span></span> <span data-ttu-id="7708d-208">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7708d-209">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7708d-210">String</span><span class="sxs-lookup"><span data-stu-id="7708d-210">String</span></span>                      | <span data-ttu-id="7708d-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7708d-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7708d-212">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="7708d-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7708d-213">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="7708d-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="7708d-214">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7708d-215">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="7708d-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7708d-216">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7708d-217">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7708d-218">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7708d-219">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7708d-220">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7708d-221">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-221">Configure allowed transports</span></span>

<span data-ttu-id="7708d-222">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7708d-223">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7708d-224">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-224">All transports are enabled by default.</span></span>

<span data-ttu-id="7708d-225">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7708d-226">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7708d-227">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="7708d-228">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7708d-229">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7708d-230">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7708d-231">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-231">Configure bearer authentication</span></span>

<span data-ttu-id="7708d-232">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7708d-233">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7708d-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7708d-234">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7708d-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7708d-235">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7708d-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7708d-236">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7708d-237">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7708d-238">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7708d-239">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7708d-240">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7708d-241">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7708d-242">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-243">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-244">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-244">Option</span></span> | <span data-ttu-id="7708d-245">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-245">Default value</span></span> | <span data-ttu-id="7708d-246">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7708d-247">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-248">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-248">Timeout for server activity.</span></span> <span data-ttu-id="7708d-249">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-250">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-251">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7708d-252">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-252">15 seconds</span></span> | <span data-ttu-id="7708d-253">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-254">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-255">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-256">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-257">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-257">15 seconds</span></span> | <span data-ttu-id="7708d-258">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-259">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-260">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7708d-261">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7708d-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7708d-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-263">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-263">Option</span></span> | <span data-ttu-id="7708d-264">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-264">Default value</span></span> | <span data-ttu-id="7708d-265">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7708d-266">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-267">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-267">Timeout for server activity.</span></span> <span data-ttu-id="7708d-268">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7708d-269">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-270">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7708d-271">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-272">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-273">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-274">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-275">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-275">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-276">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-276">Option</span></span> | <span data-ttu-id="7708d-277">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-277">Default value</span></span> | <span data-ttu-id="7708d-278">설명</span><span class="sxs-lookup"><span data-stu-id="7708d-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7708d-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7708d-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7708d-280">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-281">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-281">Timeout for server activity.</span></span> <span data-ttu-id="7708d-282">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-283">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-284">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7708d-285">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-285">15 seconds</span></span> | <span data-ttu-id="7708d-286">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-287">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-288">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-289">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7708d-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7708d-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7708d-291">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-292">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-293">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-294">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7708d-295">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-295">Configure additional options</span></span>

<span data-ttu-id="7708d-296">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-297">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-298">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-298">.NET Option</span></span> |  <span data-ttu-id="7708d-299">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-299">Default value</span></span> | <span data-ttu-id="7708d-300">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7708d-301">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7708d-302">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-303">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-304">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7708d-305">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-305">Empty</span></span> | <span data-ttu-id="7708d-306">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7708d-307">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-307">Empty</span></span> | <span data-ttu-id="7708d-308">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7708d-309">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-309">Empty</span></span> | <span data-ttu-id="7708d-310">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7708d-311">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-311">5 seconds</span></span> | <span data-ttu-id="7708d-312">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-312">WebSockets only.</span></span> <span data-ttu-id="7708d-313">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7708d-314">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7708d-315">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-315">Empty</span></span> | <span data-ttu-id="7708d-316">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7708d-317">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7708d-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7708d-318">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="7708d-319">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7708d-320">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7708d-321">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7708d-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7708d-322">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7708d-323">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7708d-324">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7708d-325">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7708d-326">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7708d-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-328">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-328">JavaScript Option</span></span> | <span data-ttu-id="7708d-329">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-329">Default Value</span></span> | <span data-ttu-id="7708d-330">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7708d-331">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="7708d-332">모든 HTTP 요청과 함께 전송 되는 헤더의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="7708d-333">브라우저에서 헤더 보내기가 Websocket 또는 스트림에 대해 작동 하지 않습니다 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> .</span><span class="sxs-lookup"><span data-stu-id="7708d-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7708d-334">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7708d-335">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-336">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-337">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="7708d-338">CORS 요청과 함께 자격 증명을 보낼지 여부를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="7708d-339">Azure App Service는 cookie 고정 세션에 s를 사용 하 고 올바르게 작동 하려면이 옵션을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="7708d-340">CORS에 대 한 자세한 SignalR 내용은을 참조 하십시오 <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="7708d-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-341">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-341">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-342">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-342">Java Option</span></span> | <span data-ttu-id="7708d-343">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-343">Default Value</span></span> | <span data-ttu-id="7708d-344">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7708d-345">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7708d-346">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-347">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-348">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7708d-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7708d-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7708d-350">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-350">Empty</span></span> | <span data-ttu-id="7708d-351">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7708d-352">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7708d-353">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7708d-354">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7708d-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7708d-355">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7708d-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7708d-356">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7708d-357">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7708d-358">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7708d-359">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7708d-360">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7708d-361">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="7708d-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7708d-362">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7708d-363">자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7708d-364">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="7708d-365">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7708d-366">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7708d-367">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7708d-368">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="7708d-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7708d-369">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7708d-370">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-370">MessagePack serialization options</span></span>

<span data-ttu-id="7708d-371">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7708d-372">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-373">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7708d-374">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-374">Configure server options</span></span>

<span data-ttu-id="7708d-375">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7708d-376">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-376">Option</span></span> | <span data-ttu-id="7708d-377">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-377">Default Value</span></span> | <span data-ttu-id="7708d-378">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7708d-379">30초</span><span class="sxs-lookup"><span data-stu-id="7708d-379">30 seconds</span></span> | <span data-ttu-id="7708d-380">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7708d-381">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7708d-382">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7708d-383">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-383">15 seconds</span></span> | <span data-ttu-id="7708d-384">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7708d-385">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-386">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-387">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-387">15 seconds</span></span> | <span data-ttu-id="7708d-388">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7708d-389">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7708d-390">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7708d-391">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7708d-391">All installed protocols</span></span> | <span data-ttu-id="7708d-392">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-392">Protocols supported by this hub.</span></span> <span data-ttu-id="7708d-393">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7708d-394">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7708d-395">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7708d-396">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7708d-397">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7708d-398">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-398">32 KB</span></span> | <span data-ttu-id="7708d-399">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="7708d-400">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7708d-401">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="7708d-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7708d-402">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7708d-403">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7708d-404">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7708d-405">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7708d-406">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-406">Option</span></span> | <span data-ttu-id="7708d-407">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-407">Default Value</span></span> | <span data-ttu-id="7708d-408">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7708d-409">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-409">32 KB</span></span> | <span data-ttu-id="7708d-410">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7708d-411">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7708d-412">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7708d-413">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7708d-414">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-414">32 KB</span></span> | <span data-ttu-id="7708d-415">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7708d-416">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7708d-417">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-417">All Transports are enabled.</span></span> | <span data-ttu-id="7708d-418">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7708d-419">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-419">See below.</span></span> | <span data-ttu-id="7708d-420">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7708d-421">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-421">See below.</span></span> | <span data-ttu-id="7708d-422">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="7708d-423">0</span><span class="sxs-lookup"><span data-stu-id="7708d-423">0</span></span> | <span data-ttu-id="7708d-424">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="7708d-425">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="7708d-426">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="7708d-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7708d-427">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-427">Option</span></span> | <span data-ttu-id="7708d-428">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-428">Default Value</span></span> | <span data-ttu-id="7708d-429">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7708d-430">90 초</span><span class="sxs-lookup"><span data-stu-id="7708d-430">90 seconds</span></span> | <span data-ttu-id="7708d-431">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7708d-432">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7708d-433">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="7708d-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7708d-434">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-434">Option</span></span> | <span data-ttu-id="7708d-435">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-435">Default Value</span></span> | <span data-ttu-id="7708d-436">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7708d-437">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-437">5 seconds</span></span> | <span data-ttu-id="7708d-438">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7708d-439">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7708d-440">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7708d-441">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-441">Configure client options</span></span>

<span data-ttu-id="7708d-442">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7708d-443">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7708d-444">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-444">Configure logging</span></span>

<span data-ttu-id="7708d-445">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7708d-446">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7708d-447">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-448">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7708d-449">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7708d-450">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7708d-451">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="7708d-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7708d-452">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7708d-453">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7708d-454">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="7708d-455">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7708d-456">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7708d-457">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-457">The following table lists the available log levels.</span></span> <span data-ttu-id="7708d-458">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7708d-459">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7708d-460">String</span><span class="sxs-lookup"><span data-stu-id="7708d-460">String</span></span>                      | <span data-ttu-id="7708d-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7708d-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7708d-462">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="7708d-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7708d-463">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="7708d-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="7708d-464">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7708d-465">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="7708d-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7708d-466">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7708d-467">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7708d-468">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7708d-469">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7708d-470">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7708d-471">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-471">Configure allowed transports</span></span>

<span data-ttu-id="7708d-472">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7708d-473">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7708d-474">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-474">All transports are enabled by default.</span></span>

<span data-ttu-id="7708d-475">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7708d-476">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7708d-477">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="7708d-478">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7708d-479">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7708d-480">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7708d-481">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-481">Configure bearer authentication</span></span>

<span data-ttu-id="7708d-482">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7708d-483">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7708d-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7708d-484">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7708d-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7708d-485">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7708d-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7708d-486">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7708d-487">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7708d-488">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7708d-489">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7708d-490">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7708d-491">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7708d-492">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-493">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-494">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-494">Option</span></span> | <span data-ttu-id="7708d-495">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-495">Default value</span></span> | <span data-ttu-id="7708d-496">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7708d-497">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-498">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-498">Timeout for server activity.</span></span> <span data-ttu-id="7708d-499">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-500">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-501">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7708d-502">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-502">15 seconds</span></span> | <span data-ttu-id="7708d-503">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-504">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-505">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-506">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-507">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-507">15 seconds</span></span> | <span data-ttu-id="7708d-508">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-509">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-510">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7708d-511">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7708d-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7708d-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-513">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-513">Option</span></span> | <span data-ttu-id="7708d-514">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-514">Default value</span></span> | <span data-ttu-id="7708d-515">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7708d-516">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-517">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-517">Timeout for server activity.</span></span> <span data-ttu-id="7708d-518">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7708d-519">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-520">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7708d-521">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-522">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-523">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-524">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-525">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-525">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-526">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-526">Option</span></span> | <span data-ttu-id="7708d-527">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-527">Default value</span></span> | <span data-ttu-id="7708d-528">설명</span><span class="sxs-lookup"><span data-stu-id="7708d-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7708d-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7708d-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7708d-530">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-531">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-531">Timeout for server activity.</span></span> <span data-ttu-id="7708d-532">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-533">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-534">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7708d-535">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-535">15 seconds</span></span> | <span data-ttu-id="7708d-536">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-537">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-538">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-539">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7708d-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7708d-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7708d-541">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-542">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-543">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-544">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7708d-545">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-545">Configure additional options</span></span>

<span data-ttu-id="7708d-546">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-547">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-548">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-548">.NET Option</span></span> |  <span data-ttu-id="7708d-549">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-549">Default value</span></span> | <span data-ttu-id="7708d-550">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7708d-551">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7708d-552">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-553">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-554">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7708d-555">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-555">Empty</span></span> | <span data-ttu-id="7708d-556">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7708d-557">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-557">Empty</span></span> | <span data-ttu-id="7708d-558">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7708d-559">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-559">Empty</span></span> | <span data-ttu-id="7708d-560">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7708d-561">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-561">5 seconds</span></span> | <span data-ttu-id="7708d-562">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-562">WebSockets only.</span></span> <span data-ttu-id="7708d-563">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7708d-564">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7708d-565">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-565">Empty</span></span> | <span data-ttu-id="7708d-566">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7708d-567">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7708d-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7708d-568">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="7708d-569">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7708d-570">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7708d-571">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7708d-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7708d-572">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7708d-573">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7708d-574">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7708d-575">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7708d-576">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7708d-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-578">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-578">JavaScript Option</span></span> | <span data-ttu-id="7708d-579">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-579">Default Value</span></span> | <span data-ttu-id="7708d-580">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7708d-581">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7708d-582">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7708d-583">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-584">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-585">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-586">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-586">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-587">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-587">Java Option</span></span> | <span data-ttu-id="7708d-588">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-588">Default Value</span></span> | <span data-ttu-id="7708d-589">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7708d-590">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7708d-591">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-592">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-593">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7708d-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7708d-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7708d-595">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-595">Empty</span></span> | <span data-ttu-id="7708d-596">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7708d-597">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7708d-598">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7708d-599">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7708d-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7708d-600">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7708d-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7708d-601">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7708d-602">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7708d-603">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7708d-604">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7708d-605">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7708d-606">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="7708d-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7708d-607">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7708d-608">자세한 내용은 [ 설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7708d-609">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="7708d-610">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7708d-611">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7708d-612">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7708d-613">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="7708d-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7708d-614">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7708d-615">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-615">MessagePack serialization options</span></span>

<span data-ttu-id="7708d-616">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7708d-617">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-618">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7708d-619">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-619">Configure server options</span></span>

<span data-ttu-id="7708d-620">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7708d-621">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-621">Option</span></span> | <span data-ttu-id="7708d-622">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-622">Default Value</span></span> | <span data-ttu-id="7708d-623">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7708d-624">30초</span><span class="sxs-lookup"><span data-stu-id="7708d-624">30 seconds</span></span> | <span data-ttu-id="7708d-625">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7708d-626">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7708d-627">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7708d-628">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-628">15 seconds</span></span> | <span data-ttu-id="7708d-629">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7708d-630">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-631">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-632">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-632">15 seconds</span></span> | <span data-ttu-id="7708d-633">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7708d-634">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7708d-635">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7708d-636">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7708d-636">All installed protocols</span></span> | <span data-ttu-id="7708d-637">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-637">Protocols supported by this hub.</span></span> <span data-ttu-id="7708d-638">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7708d-639">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7708d-640">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7708d-641">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7708d-642">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7708d-643">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-643">32 KB</span></span> | <span data-ttu-id="7708d-644">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="7708d-645">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7708d-646">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="7708d-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7708d-647">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7708d-648">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7708d-649">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7708d-650">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7708d-651">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-651">Option</span></span> | <span data-ttu-id="7708d-652">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-652">Default Value</span></span> | <span data-ttu-id="7708d-653">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7708d-654">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-654">32 KB</span></span> | <span data-ttu-id="7708d-655">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7708d-656">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7708d-657">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7708d-658">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7708d-659">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-659">32 KB</span></span> | <span data-ttu-id="7708d-660">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7708d-661">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7708d-662">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-662">All Transports are enabled.</span></span> | <span data-ttu-id="7708d-663">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7708d-664">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-664">See below.</span></span> | <span data-ttu-id="7708d-665">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7708d-666">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-666">See below.</span></span> | <span data-ttu-id="7708d-667">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7708d-668">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="7708d-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7708d-669">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-669">Option</span></span> | <span data-ttu-id="7708d-670">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-670">Default Value</span></span> | <span data-ttu-id="7708d-671">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7708d-672">90 초</span><span class="sxs-lookup"><span data-stu-id="7708d-672">90 seconds</span></span> | <span data-ttu-id="7708d-673">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7708d-674">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7708d-675">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="7708d-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7708d-676">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-676">Option</span></span> | <span data-ttu-id="7708d-677">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-677">Default Value</span></span> | <span data-ttu-id="7708d-678">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7708d-679">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-679">5 seconds</span></span> | <span data-ttu-id="7708d-680">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7708d-681">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7708d-682">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7708d-683">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-683">Configure client options</span></span>

<span data-ttu-id="7708d-684">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7708d-685">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7708d-686">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-686">Configure logging</span></span>

<span data-ttu-id="7708d-687">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7708d-688">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7708d-689">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-690">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7708d-691">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7708d-692">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7708d-693">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="7708d-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7708d-694">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7708d-695">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7708d-696">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="7708d-697">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7708d-698">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7708d-699">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-699">The following table lists the available log levels.</span></span> <span data-ttu-id="7708d-700">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7708d-701">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7708d-702">String</span><span class="sxs-lookup"><span data-stu-id="7708d-702">String</span></span>                      | <span data-ttu-id="7708d-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7708d-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7708d-704">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="7708d-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7708d-705">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="7708d-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="7708d-706">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7708d-707">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="7708d-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7708d-708">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7708d-709">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7708d-710">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7708d-711">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7708d-712">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7708d-713">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-713">Configure allowed transports</span></span>

<span data-ttu-id="7708d-714">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7708d-715">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7708d-716">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-716">All transports are enabled by default.</span></span>

<span data-ttu-id="7708d-717">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7708d-718">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7708d-719">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="7708d-720">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7708d-721">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7708d-722">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7708d-723">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-723">Configure bearer authentication</span></span>

<span data-ttu-id="7708d-724">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7708d-725">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7708d-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7708d-726">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7708d-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7708d-727">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7708d-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7708d-728">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7708d-729">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7708d-730">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7708d-731">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7708d-732">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7708d-733">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7708d-734">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-735">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-736">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-736">Option</span></span> | <span data-ttu-id="7708d-737">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-737">Default value</span></span> | <span data-ttu-id="7708d-738">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7708d-739">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-740">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-740">Timeout for server activity.</span></span> <span data-ttu-id="7708d-741">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-742">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-743">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7708d-744">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-744">15 seconds</span></span> | <span data-ttu-id="7708d-745">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-746">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-747">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-748">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-749">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-749">15 seconds</span></span> | <span data-ttu-id="7708d-750">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-751">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-752">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7708d-753">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7708d-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7708d-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-755">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-755">Option</span></span> | <span data-ttu-id="7708d-756">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-756">Default value</span></span> | <span data-ttu-id="7708d-757">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7708d-758">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-759">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-759">Timeout for server activity.</span></span> <span data-ttu-id="7708d-760">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7708d-761">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-762">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7708d-763">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-764">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-765">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-766">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-767">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-767">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-768">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-768">Option</span></span> | <span data-ttu-id="7708d-769">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-769">Default value</span></span> | <span data-ttu-id="7708d-770">설명</span><span class="sxs-lookup"><span data-stu-id="7708d-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7708d-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7708d-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7708d-772">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-773">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-773">Timeout for server activity.</span></span> <span data-ttu-id="7708d-774">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-775">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-776">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7708d-777">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-777">15 seconds</span></span> | <span data-ttu-id="7708d-778">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-779">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-780">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-781">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7708d-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7708d-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7708d-783">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-784">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-785">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-786">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7708d-787">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-787">Configure additional options</span></span>

<span data-ttu-id="7708d-788">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-789">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-790">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-790">.NET Option</span></span> |  <span data-ttu-id="7708d-791">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-791">Default value</span></span> | <span data-ttu-id="7708d-792">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7708d-793">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7708d-794">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-795">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-796">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7708d-797">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-797">Empty</span></span> | <span data-ttu-id="7708d-798">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7708d-799">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-799">Empty</span></span> | <span data-ttu-id="7708d-800">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7708d-801">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-801">Empty</span></span> | <span data-ttu-id="7708d-802">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7708d-803">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-803">5 seconds</span></span> | <span data-ttu-id="7708d-804">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-804">WebSockets only.</span></span> <span data-ttu-id="7708d-805">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7708d-806">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7708d-807">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-807">Empty</span></span> | <span data-ttu-id="7708d-808">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7708d-809">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7708d-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7708d-810">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="7708d-811">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7708d-812">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7708d-813">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7708d-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7708d-814">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7708d-815">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7708d-816">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7708d-817">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7708d-818">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7708d-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-820">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-820">JavaScript Option</span></span> | <span data-ttu-id="7708d-821">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-821">Default Value</span></span> | <span data-ttu-id="7708d-822">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7708d-823">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7708d-824">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7708d-825">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-826">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-827">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-828">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-828">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-829">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-829">Java Option</span></span> | <span data-ttu-id="7708d-830">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-830">Default Value</span></span> | <span data-ttu-id="7708d-831">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7708d-832">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7708d-833">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-834">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-835">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7708d-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7708d-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7708d-837">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-837">Empty</span></span> | <span data-ttu-id="7708d-838">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7708d-839">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7708d-840">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7708d-841">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7708d-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7708d-842">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7708d-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7708d-843">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7708d-844">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7708d-845">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7708d-846">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7708d-847">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="7708d-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7708d-848">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7708d-849">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="7708d-850">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="7708d-851">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7708d-852">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7708d-853">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7708d-854">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-854">MessagePack serialization options</span></span>

<span data-ttu-id="7708d-855">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7708d-856">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-857">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7708d-858">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-858">Configure server options</span></span>

<span data-ttu-id="7708d-859">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7708d-860">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-860">Option</span></span> | <span data-ttu-id="7708d-861">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-861">Default Value</span></span> | <span data-ttu-id="7708d-862">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7708d-863">30초</span><span class="sxs-lookup"><span data-stu-id="7708d-863">30 seconds</span></span> | <span data-ttu-id="7708d-864">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7708d-865">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7708d-866">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7708d-867">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-867">15 seconds</span></span> | <span data-ttu-id="7708d-868">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7708d-869">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-870">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-871">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-871">15 seconds</span></span> | <span data-ttu-id="7708d-872">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7708d-873">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7708d-874">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7708d-875">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7708d-875">All installed protocols</span></span> | <span data-ttu-id="7708d-876">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-876">Protocols supported by this hub.</span></span> <span data-ttu-id="7708d-877">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7708d-878">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7708d-879">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="7708d-880">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7708d-881">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="7708d-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7708d-882">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7708d-883">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7708d-884">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7708d-885">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7708d-886">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-886">Option</span></span> | <span data-ttu-id="7708d-887">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-887">Default Value</span></span> | <span data-ttu-id="7708d-888">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7708d-889">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-889">32 KB</span></span> | <span data-ttu-id="7708d-890">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7708d-891">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7708d-892">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7708d-893">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7708d-894">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-894">32 KB</span></span> | <span data-ttu-id="7708d-895">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7708d-896">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7708d-897">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-897">All Transports are enabled.</span></span> | <span data-ttu-id="7708d-898">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7708d-899">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-899">See below.</span></span> | <span data-ttu-id="7708d-900">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7708d-901">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-901">See below.</span></span> | <span data-ttu-id="7708d-902">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7708d-903">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="7708d-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7708d-904">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-904">Option</span></span> | <span data-ttu-id="7708d-905">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-905">Default Value</span></span> | <span data-ttu-id="7708d-906">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7708d-907">90 초</span><span class="sxs-lookup"><span data-stu-id="7708d-907">90 seconds</span></span> | <span data-ttu-id="7708d-908">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7708d-909">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7708d-910">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="7708d-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7708d-911">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-911">Option</span></span> | <span data-ttu-id="7708d-912">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-912">Default Value</span></span> | <span data-ttu-id="7708d-913">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7708d-914">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-914">5 seconds</span></span> | <span data-ttu-id="7708d-915">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7708d-916">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7708d-917">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7708d-918">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-918">Configure client options</span></span>

<span data-ttu-id="7708d-919">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7708d-920">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7708d-921">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-921">Configure logging</span></span>

<span data-ttu-id="7708d-922">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7708d-923">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7708d-924">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-925">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7708d-926">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7708d-927">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7708d-928">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="7708d-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7708d-929">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7708d-930">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7708d-931">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7708d-932">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7708d-933">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="7708d-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7708d-934">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7708d-935">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7708d-936">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7708d-937">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7708d-938">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7708d-939">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-939">Configure allowed transports</span></span>

<span data-ttu-id="7708d-940">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7708d-941">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7708d-942">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-942">All transports are enabled by default.</span></span>

<span data-ttu-id="7708d-943">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7708d-944">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7708d-945">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7708d-946">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-946">Configure bearer authentication</span></span>

<span data-ttu-id="7708d-947">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7708d-948">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7708d-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7708d-949">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7708d-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7708d-950">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7708d-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7708d-951">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7708d-952">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7708d-953">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7708d-954">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7708d-955">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7708d-956">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7708d-957">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-958">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-959">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-959">Option</span></span> | <span data-ttu-id="7708d-960">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-960">Default value</span></span> | <span data-ttu-id="7708d-961">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7708d-962">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-963">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-963">Timeout for server activity.</span></span> <span data-ttu-id="7708d-964">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-965">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-966">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7708d-967">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-967">15 seconds</span></span> | <span data-ttu-id="7708d-968">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-969">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-970">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-971">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-972">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-972">15 seconds</span></span> | <span data-ttu-id="7708d-973">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-974">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-975">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7708d-976">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7708d-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7708d-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-978">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-978">Option</span></span> | <span data-ttu-id="7708d-979">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-979">Default value</span></span> | <span data-ttu-id="7708d-980">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7708d-981">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-982">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-982">Timeout for server activity.</span></span> <span data-ttu-id="7708d-983">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7708d-984">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-985">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7708d-986">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-987">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-988">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-989">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-990">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-990">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-991">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-991">Option</span></span> | <span data-ttu-id="7708d-992">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-992">Default value</span></span> | <span data-ttu-id="7708d-993">설명</span><span class="sxs-lookup"><span data-stu-id="7708d-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7708d-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7708d-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7708d-995">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-996">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-996">Timeout for server activity.</span></span> <span data-ttu-id="7708d-997">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-998">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-999">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7708d-1000">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-1000">15 seconds</span></span> | <span data-ttu-id="7708d-1001">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-1002">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-1003">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-1004">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7708d-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7708d-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7708d-1006">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7708d-1007">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7708d-1008">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7708d-1009">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7708d-1010">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1010">Configure additional options</span></span>

<span data-ttu-id="7708d-1011">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-1013">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1013">.NET Option</span></span> |  <span data-ttu-id="7708d-1014">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1014">Default value</span></span> | <span data-ttu-id="7708d-1015">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7708d-1016">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7708d-1017">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-1018">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-1019">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7708d-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1020">Empty</span></span> | <span data-ttu-id="7708d-1021">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7708d-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1022">Empty</span></span> | <span data-ttu-id="7708d-1023">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7708d-1024">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1024">Empty</span></span> | <span data-ttu-id="7708d-1025">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7708d-1026">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-1026">5 seconds</span></span> | <span data-ttu-id="7708d-1027">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1027">WebSockets only.</span></span> <span data-ttu-id="7708d-1028">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7708d-1029">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7708d-1030">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1030">Empty</span></span> | <span data-ttu-id="7708d-1031">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7708d-1032">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7708d-1033">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="7708d-1034">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7708d-1035">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7708d-1036">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7708d-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7708d-1037">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7708d-1038">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7708d-1039">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7708d-1040">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7708d-1041">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7708d-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-1043">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1043">JavaScript Option</span></span> | <span data-ttu-id="7708d-1044">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1044">Default Value</span></span> | <span data-ttu-id="7708d-1045">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7708d-1046">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7708d-1047">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7708d-1048">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-1049">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-1050">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-1051">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-1052">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1052">Java Option</span></span> | <span data-ttu-id="7708d-1053">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1053">Default Value</span></span> | <span data-ttu-id="7708d-1054">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7708d-1055">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7708d-1056">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-1057">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-1058">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7708d-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7708d-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7708d-1060">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1060">Empty</span></span> | <span data-ttu-id="7708d-1061">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7708d-1062">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7708d-1063">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7708d-1064">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7708d-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7708d-1065">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7708d-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7708d-1066">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7708d-1067">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7708d-1068">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7708d-1069">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7708d-1070">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7708d-1071">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7708d-1072">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="7708d-1073">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="7708d-1074">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7708d-1075">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7708d-1076">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7708d-1077">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1077">MessagePack serialization options</span></span>

<span data-ttu-id="7708d-1078">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7708d-1079">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-1080">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7708d-1081">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-1081">Configure server options</span></span>

<span data-ttu-id="7708d-1082">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7708d-1083">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1083">Option</span></span> | <span data-ttu-id="7708d-1084">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1084">Default Value</span></span> | <span data-ttu-id="7708d-1085">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="7708d-1086">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-1086">15 seconds</span></span> | <span data-ttu-id="7708d-1087">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7708d-1088">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-1089">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7708d-1090">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-1090">15 seconds</span></span> | <span data-ttu-id="7708d-1091">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7708d-1092">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7708d-1093">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7708d-1094">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="7708d-1094">All installed protocols</span></span> | <span data-ttu-id="7708d-1095">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="7708d-1096">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7708d-1097">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7708d-1098">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="7708d-1099">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7708d-1100">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="7708d-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7708d-1101">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7708d-1102">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7708d-1103">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7708d-1104">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7708d-1105">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1105">Option</span></span> | <span data-ttu-id="7708d-1106">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1106">Default Value</span></span> | <span data-ttu-id="7708d-1107">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7708d-1108">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-1108">32 KB</span></span> | <span data-ttu-id="7708d-1109">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7708d-1110">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7708d-1111">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7708d-1112">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7708d-1113">32KB</span><span class="sxs-lookup"><span data-stu-id="7708d-1113">32 KB</span></span> | <span data-ttu-id="7708d-1114">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7708d-1115">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7708d-1116">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1116">All Transports are enabled.</span></span> | <span data-ttu-id="7708d-1117">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7708d-1118">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1118">See below.</span></span> | <span data-ttu-id="7708d-1119">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7708d-1120">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1120">See below.</span></span> | <span data-ttu-id="7708d-1121">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7708d-1122">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7708d-1123">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1123">Option</span></span> | <span data-ttu-id="7708d-1124">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1124">Default Value</span></span> | <span data-ttu-id="7708d-1125">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7708d-1126">90 초</span><span class="sxs-lookup"><span data-stu-id="7708d-1126">90 seconds</span></span> | <span data-ttu-id="7708d-1127">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7708d-1128">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7708d-1129">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7708d-1130">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1130">Option</span></span> | <span data-ttu-id="7708d-1131">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1131">Default Value</span></span> | <span data-ttu-id="7708d-1132">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7708d-1133">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-1133">5 seconds</span></span> | <span data-ttu-id="7708d-1134">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7708d-1135">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7708d-1136">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7708d-1137">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-1137">Configure client options</span></span>

<span data-ttu-id="7708d-1138">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7708d-1139">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7708d-1140">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-1140">Configure logging</span></span>

<span data-ttu-id="7708d-1141">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7708d-1142">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7708d-1143">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7708d-1144">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7708d-1145">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7708d-1146">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7708d-1147">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7708d-1148">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7708d-1149">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7708d-1150">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7708d-1151">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7708d-1152">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="7708d-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7708d-1153">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7708d-1154">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7708d-1155">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7708d-1156">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7708d-1157">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7708d-1158">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-1158">Configure allowed transports</span></span>

<span data-ttu-id="7708d-1159">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7708d-1160">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7708d-1161">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="7708d-1162">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7708d-1163">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7708d-1164">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-1164">Configure bearer authentication</span></span>

<span data-ttu-id="7708d-1165">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7708d-1166">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7708d-1167">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="7708d-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7708d-1168">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7708d-1169">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7708d-1170">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7708d-1171">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7708d-1172">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7708d-1173">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7708d-1174">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="7708d-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7708d-1175">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-1177">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1177">Option</span></span> | <span data-ttu-id="7708d-1178">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1178">Default value</span></span> | <span data-ttu-id="7708d-1179">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7708d-1180">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-1181">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1181">Timeout for server activity.</span></span> <span data-ttu-id="7708d-1182">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-1183">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-1184">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7708d-1185">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-1185">15 seconds</span></span> | <span data-ttu-id="7708d-1186">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-1187">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7708d-1188">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-1189">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="7708d-1190">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7708d-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-1192">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1192">Option</span></span> | <span data-ttu-id="7708d-1193">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1193">Default value</span></span> | <span data-ttu-id="7708d-1194">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7708d-1195">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-1196">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1196">Timeout for server activity.</span></span> <span data-ttu-id="7708d-1197">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7708d-1198">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-1199">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-1200">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-1201">옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1201">Option</span></span> | <span data-ttu-id="7708d-1202">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1202">Default value</span></span> | <span data-ttu-id="7708d-1203">설명</span><span class="sxs-lookup"><span data-stu-id="7708d-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7708d-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7708d-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7708d-1205">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="7708d-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7708d-1206">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1206">Timeout for server activity.</span></span> <span data-ttu-id="7708d-1207">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-1208">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7708d-1209">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 시간을 허용 하는 서버 값을 두 번 이상 표시 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7708d-1210">15초</span><span class="sxs-lookup"><span data-stu-id="7708d-1210">15 seconds</span></span> | <span data-ttu-id="7708d-1211">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="7708d-1212">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7708d-1213">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7708d-1214">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7708d-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7708d-1215">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1215">Configure additional options</span></span>

<span data-ttu-id="7708d-1216">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7708d-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="7708d-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7708d-1218">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1218">.NET Option</span></span> |  <span data-ttu-id="7708d-1219">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1219">Default value</span></span> | <span data-ttu-id="7708d-1220">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7708d-1221">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7708d-1222">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-1223">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-1224">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7708d-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1225">Empty</span></span> | <span data-ttu-id="7708d-1226">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7708d-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1227">Empty</span></span> | <span data-ttu-id="7708d-1228">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7708d-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1229">Empty</span></span> | <span data-ttu-id="7708d-1230">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7708d-1231">5초</span><span class="sxs-lookup"><span data-stu-id="7708d-1231">5 seconds</span></span> | <span data-ttu-id="7708d-1232">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1232">WebSockets only.</span></span> <span data-ttu-id="7708d-1233">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7708d-1234">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7708d-1235">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1235">Empty</span></span> | <span data-ttu-id="7708d-1236">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7708d-1237">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7708d-1238">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="7708d-1239">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7708d-1240">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7708d-1241">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7708d-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7708d-1242">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7708d-1243">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7708d-1244">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7708d-1245">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7708d-1246">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7708d-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7708d-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7708d-1248">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1248">JavaScript Option</span></span> | <span data-ttu-id="7708d-1249">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1249">Default Value</span></span> | <span data-ttu-id="7708d-1250">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7708d-1251">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7708d-1252">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7708d-1253">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-1254">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-1255">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7708d-1256">Java</span><span class="sxs-lookup"><span data-stu-id="7708d-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="7708d-1257">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="7708d-1257">Java Option</span></span> | <span data-ttu-id="7708d-1258">기본값</span><span class="sxs-lookup"><span data-stu-id="7708d-1258">Default Value</span></span> | <span data-ttu-id="7708d-1259">Description</span><span class="sxs-lookup"><span data-stu-id="7708d-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7708d-1260">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7708d-1261">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7708d-1262">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="7708d-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7708d-1263">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="7708d-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7708d-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7708d-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7708d-1265">Empty</span><span class="sxs-lookup"><span data-stu-id="7708d-1265">Empty</span></span> | <span data-ttu-id="7708d-1266">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="7708d-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7708d-1267">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7708d-1268">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="7708d-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7708d-1269">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7708d-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7708d-1270">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7708d-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
