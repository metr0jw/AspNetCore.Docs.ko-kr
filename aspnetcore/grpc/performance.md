---
title: ASP.NET Core용 gRPC의 성능 모범 사례
author: jamesnk
description: 고성능 gRPC 서비스를 빌드하기 위한 모범 사례를 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876726"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="0b2c8-103">ASP.NET Core용 gRPC의 성능 모범 사례</span><span class="sxs-lookup"><span data-stu-id="0b2c8-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="0b2c8-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0b2c8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0b2c8-105">gRPC는 고성능 서비스를 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="0b2c8-106">이 문서에서는 gRPC에서 가능한 최상의 성능을 얻는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="0b2c8-107">채널 재사용</span><span class="sxs-lookup"><span data-stu-id="0b2c8-107">Reuse channel</span></span>

<span data-ttu-id="0b2c8-108">gRPC 호출 시 gRPC 채널을 재사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="0b2c8-109">채널을 재사용하면 기존 HTTP/2 연결을 통해 호출이 멀티플렉싱될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="0b2c8-110">gRPC 호출별로 새 채널을 생성하는 경우 완료하는 데 상당히 오랜 시간이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="0b2c8-111">호출할 때마다 HTTP/2 연결을 만들기 위해 클라이언트와 서버 간에 여러 번의 네트워크 왕복이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="0b2c8-112">소켓 열기</span><span class="sxs-lookup"><span data-stu-id="0b2c8-112">Opening a socket</span></span>
2. <span data-ttu-id="0b2c8-113">TCP 연결 설정</span><span class="sxs-lookup"><span data-stu-id="0b2c8-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="0b2c8-114">TLS 협상</span><span class="sxs-lookup"><span data-stu-id="0b2c8-114">Negotiating TLS</span></span>
4. <span data-ttu-id="0b2c8-115">HTTP/2 연결 시작</span><span class="sxs-lookup"><span data-stu-id="0b2c8-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="0b2c8-116">gRPC 호출하기</span><span class="sxs-lookup"><span data-stu-id="0b2c8-116">Making the gRPC call</span></span>

<span data-ttu-id="0b2c8-117">gRPC 호출 간에 채널을 공유하고 재사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="0b2c8-118">gRPC 클라이언트는 채널을 사용하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="0b2c8-119">gRPC 클라이언트는 경량 개체이며 캐시하거나 재사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="0b2c8-120">다양한 형식의 클라이언트를 포함하여 여러 gRPC 클라이언트를 한 채널에서 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="0b2c8-121">채널 및 채널에서 만든 클라이언트를 여러 스레드에서 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="0b2c8-122">채널에서 만든 클라이언트는 여러 개의 동시 호출을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="0b2c8-123">연결 동시성</span><span class="sxs-lookup"><span data-stu-id="0b2c8-123">Connection concurrency</span></span>

<span data-ttu-id="0b2c8-124">일반적으로 HTTP/2 연결에는 하나의 연결에서 한 번에 사용 가능한 [최대 동시 스트림(활성 HTTP 요청)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) 수에 한도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="0b2c8-125">기본적으로 대부분의 서버는 이 한도를 100개의 동시 스트림으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="0b2c8-126">gRPC 채널은 단일 HTTP/2 연결을 사용하고, 해당 연결에서 동시 호출이 멀티플렉싱됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="0b2c8-127">활성 호출 수가 연결 스트림 한도에 도달하면 추가 호출은 클라이언트에서 큐에 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="0b2c8-128">큐에 대기 중인 호출은 활성 호출이 완료될 때까지 대기한 후 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="0b2c8-129">부하가 높은 애플리케이션이나 장기 스트리밍 gRPC 호출에서는 이 한도로 인해 큐에 대기 중인 호출이 야기하는 성능 문제가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0b2c8-130">.NET 5에는 `SocketsHttpHandler.EnableMultipleHttp2Connections` 속성이 도입되었습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="0b2c8-131">`true`로 설정하면 동시 스트림 한도에 도달하는 경우 채널에서 추가 HTTP/2 연결이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="0b2c8-132">`GrpcChannel`이 만들어지면 내부 `SocketsHttpHandler`가 추가 HTTP/2 연결을 만들도록 자동으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="0b2c8-133">앱이 고유한 처리기를 구성하는 경우에는 `EnableMultipleHttp2Connections`를 `true`로 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="0b2c8-134">.NET Core 3.1 앱을 위한 몇 가지 해결 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="0b2c8-135">부하가 높은 앱 영역에 대해 별도의 gRPC 채널을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="0b2c8-136">예를 들어 `Logger` gRPC 서비스의 부하가 높을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="0b2c8-137">별도의 채널을 사용하여 앱에서 `LoggerClient`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="0b2c8-138">gRPC 채널의 풀을 사용합니다(예: gRPC 채널 목록 만들기).</span><span class="sxs-lookup"><span data-stu-id="0b2c8-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="0b2c8-139">`Random`은 gRPC 채널이 필요할 때마다 목록에서 채널을 선택하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="0b2c8-140">`Random`을 사용하면 호출이 여러 연결에서 무작위로 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0b2c8-141">서버에 대한 최대 동시 스트림 한도를 늘리는 것은 이 문제를 해결하는 또 다른 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="0b2c8-142">Kestrel에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>을 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="0b2c8-143">최대 동시 스트림 한도를 늘리지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="0b2c8-144">단일 HTTP/2 연결에서 스트림이 너무 많으면 새로운 성능 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="0b2c8-145">연결에 쓰려고 시도하는 스트림 간에 스레드 경합이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="0b2c8-146">연결 패킷 손실로 인해 TCP 계층에서 모든 호출이 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="0b2c8-147">연결 유지 ping</span><span class="sxs-lookup"><span data-stu-id="0b2c8-147">Keep alive pings</span></span>

<span data-ttu-id="0b2c8-148">연결 유지 ping을 사용하여 비활성 기간 동안 HTTP/2 연결을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="0b2c8-149">앱이 작업을 계속할 때 기존 HTTP/2 연결을 곧바로 사용할 수 있으면 연결을 다시 설정하느라 지연이 발생하지 않고 초기 gRPC 호출을 신속하게 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="0b2c8-150">연결 유지 ping은 <xref:System.Net.Http.SocketsHttpHandler>에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="0b2c8-151">앞의 코드는 비활성 기간 동안 60초마다 연결 유지 ping을 서버에 보내는 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="0b2c8-152">ping을 사용하면 서버와 사용 중인 모든 프록시가 비활성 상태로 인해 연결을 닫지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="0b2c8-153">스트리밍</span><span class="sxs-lookup"><span data-stu-id="0b2c8-153">Streaming</span></span>

<span data-ttu-id="0b2c8-154">gRPC 양방향 스트리밍을 사용하면 고성능 시나리오에서 단항 gRPC 호출을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="0b2c8-155">양방향 스트림이 시작된 후에는 메시지를 스트리밍하는 것이 여러 개의 단항 gRPC 호출을 통해 메시지를 보내는 것보다 빠릅니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="0b2c8-156">스트리밍된 메시지는 기존 HTTP/2 요청에서 데이터로 전송되며, 단항 호출별로 새 HTTP/2 요청을 만드는 오버헤드가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="0b2c8-157">예제 서비스:</span><span class="sxs-lookup"><span data-stu-id="0b2c8-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="0b2c8-158">클라이언트 예:</span><span class="sxs-lookup"><span data-stu-id="0b2c8-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="0b2c8-159">성능상의 이유로 단항 호출을 양방향 스트리밍으로 대체하는 것은 고급 기법이며 많은 상황에서 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="0b2c8-160">스트리밍 호출을 사용하는 것은 다음과 같은 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="0b2c8-161">높은 처리량이나 짧은 대기 시간이 필요한 경우</span><span class="sxs-lookup"><span data-stu-id="0b2c8-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="0b2c8-162">gRPC 및 HTTP/2가 성능 병목 상태로 식별된 경우</span><span class="sxs-lookup"><span data-stu-id="0b2c8-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="0b2c8-163">클라이언트의 작업자가 gRPC 서비스를 사용하여 일반 메시지를 보내거나 받는 경우</span><span class="sxs-lookup"><span data-stu-id="0b2c8-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="0b2c8-164">단항 대신 스트리밍 호출을 사용할 때 추가 복잡성과 제한 사항에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="0b2c8-165">서비스 또는 연결 오류로 인해 스트림이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="0b2c8-166">오류가 발생하는 경우 스트림을 다시 시작하기 위한 논리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="0b2c8-167">`RequestStream.WriteAsync`는 다중 스레딩에 안전하게 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="0b2c8-168">한 번에 하나의 메시지만 스트림에 쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="0b2c8-169">여러 스레드의 메시지를 단일 스트림을 통해 보내려면 <xref:System.Threading.Channels.Channel%601> 같은 생산자/소비자 큐가 메시지를 마샬링해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="0b2c8-170">gRPC 스트리밍 방법은 한 유형의 메시지를 수신하고 한 유형의 메시지 유형을 보내는 것으로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="0b2c8-171">예를 들어 `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)`는 `RequestMessage`를 수신하고 `ResponseMessage`를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="0b2c8-172">Protobuf가 `Any` 및 `oneof`를 사용하여 알 수 없는 메시지나 조건부 메시지를 지원하면 이러한 제한을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0b2c8-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
