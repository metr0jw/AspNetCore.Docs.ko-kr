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
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a>ASP.NET Core용 gRPC의 성능 모범 사례

작성자: [James Newton-King](https://twitter.com/jamesnk)

gRPC는 고성능 서비스를 위해 설계되었습니다. 이 문서에서는 gRPC에서 가능한 최상의 성능을 얻는 방법을 설명합니다.

## <a name="reuse-channel"></a>채널 재사용

gRPC 호출 시 gRPC 채널을 재사용해야 합니다. 채널을 재사용하면 기존 HTTP/2 연결을 통해 호출이 멀티플렉싱될 수 있습니다.

gRPC 호출별로 새 채널을 생성하는 경우 완료하는 데 상당히 오랜 시간이 걸릴 수 있습니다. 호출할 때마다 HTTP/2 연결을 만들기 위해 클라이언트와 서버 간에 여러 번의 네트워크 왕복이 필요합니다.

1. 소켓 열기
2. TCP 연결 설정
3. TLS 협상
4. HTTP/2 연결 시작
5. gRPC 호출하기

gRPC 호출 간에 채널을 공유하고 재사용할 수 있습니다.

* gRPC 클라이언트는 채널을 사용하여 만듭니다. gRPC 클라이언트는 경량 개체이며 캐시하거나 재사용할 필요가 없습니다.
* 다양한 형식의 클라이언트를 포함하여 여러 gRPC 클라이언트를 한 채널에서 만들 수 있습니다.
* 채널 및 채널에서 만든 클라이언트를 여러 스레드에서 안전하게 사용할 수 있습니다.
* 채널에서 만든 클라이언트는 여러 개의 동시 호출을 수행할 수 있습니다.

## <a name="connection-concurrency"></a>연결 동시성

일반적으로 HTTP/2 연결에는 하나의 연결에서 한 번에 사용 가능한 [최대 동시 스트림(활성 HTTP 요청)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) 수에 한도가 있습니다. 기본적으로 대부분의 서버는 이 한도를 100개의 동시 스트림으로 설정합니다.

gRPC 채널은 단일 HTTP/2 연결을 사용하고, 해당 연결에서 동시 호출이 멀티플렉싱됩니다. 활성 호출 수가 연결 스트림 한도에 도달하면 추가 호출은 클라이언트에서 큐에 대기합니다. 큐에 대기 중인 호출은 활성 호출이 완료될 때까지 대기한 후 전송됩니다. 부하가 높은 애플리케이션이나 장기 스트리밍 gRPC 호출에서는 이 한도로 인해 큐에 대기 중인 호출이 야기하는 성능 문제가 발생할 수 있습니다.

::: moniker range=">= aspnetcore-5.0"

.NET 5에는 `SocketsHttpHandler.EnableMultipleHttp2Connections` 속성이 도입되었습니다. `true`로 설정하면 동시 스트림 한도에 도달하는 경우 채널에서 추가 HTTP/2 연결이 만들어집니다. `GrpcChannel`이 만들어지면 내부 `SocketsHttpHandler`가 추가 HTTP/2 연결을 만들도록 자동으로 구성됩니다. 앱이 고유한 처리기를 구성하는 경우에는 `EnableMultipleHttp2Connections`를 `true`로 설정하는 것이 좋습니다.

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

.NET Core 3.1 앱을 위한 몇 가지 해결 방법이 있습니다.

* 부하가 높은 앱 영역에 대해 별도의 gRPC 채널을 만듭니다. 예를 들어 `Logger` gRPC 서비스의 부하가 높을 수 있습니다. 별도의 채널을 사용하여 앱에서 `LoggerClient`를 만듭니다.
* gRPC 채널의 풀을 사용합니다(예: gRPC 채널 목록 만들기). `Random`은 gRPC 채널이 필요할 때마다 목록에서 채널을 선택하는 데 사용됩니다. `Random`을 사용하면 호출이 여러 연결에서 무작위로 분산됩니다.

> [!IMPORTANT]
> 서버에 대한 최대 동시 스트림 한도를 늘리는 것은 이 문제를 해결하는 또 다른 방법입니다. Kestrel에서는 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>을 사용하여 구성합니다.
>
> 최대 동시 스트림 한도를 늘리지 않는 것이 좋습니다. 단일 HTTP/2 연결에서 스트림이 너무 많으면 새로운 성능 문제가 발생합니다.
>
> * 연결에 쓰려고 시도하는 스트림 간에 스레드 경합이 발생합니다.
> * 연결 패킷 손실로 인해 TCP 계층에서 모든 호출이 차단됩니다.

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>연결 유지 ping

연결 유지 ping을 사용하여 비활성 기간 동안 HTTP/2 연결을 유지할 수 있습니다. 앱이 작업을 계속할 때 기존 HTTP/2 연결을 곧바로 사용할 수 있으면 연결을 다시 설정하느라 지연이 발생하지 않고 초기 gRPC 호출을 신속하게 수행할 수 있습니다.

연결 유지 ping은 <xref:System.Net.Http.SocketsHttpHandler>에서 구성됩니다.

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

앞의 코드는 비활성 기간 동안 60초마다 연결 유지 ping을 서버에 보내는 채널을 구성합니다. ping을 사용하면 서버와 사용 중인 모든 프록시가 비활성 상태로 인해 연결을 닫지 않습니다.

::: moniker-end

## <a name="streaming"></a>스트리밍

gRPC 양방향 스트리밍을 사용하면 고성능 시나리오에서 단항 gRPC 호출을 바꿀 수 있습니다. 양방향 스트림이 시작된 후에는 메시지를 스트리밍하는 것이 여러 개의 단항 gRPC 호출을 통해 메시지를 보내는 것보다 빠릅니다. 스트리밍된 메시지는 기존 HTTP/2 요청에서 데이터로 전송되며, 단항 호출별로 새 HTTP/2 요청을 만드는 오버헤드가 제거됩니다.

예제 서비스:

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

클라이언트 예:

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

성능상의 이유로 단항 호출을 양방향 스트리밍으로 대체하는 것은 고급 기법이며 많은 상황에서 적합하지 않습니다.

스트리밍 호출을 사용하는 것은 다음과 같은 경우에 적합합니다.

1. 높은 처리량이나 짧은 대기 시간이 필요한 경우
2. gRPC 및 HTTP/2가 성능 병목 상태로 식별된 경우
3. 클라이언트의 작업자가 gRPC 서비스를 사용하여 일반 메시지를 보내거나 받는 경우

단항 대신 스트리밍 호출을 사용할 때 추가 복잡성과 제한 사항에 유의하세요.

1. 서비스 또는 연결 오류로 인해 스트림이 중단될 수 있습니다. 오류가 발생하는 경우 스트림을 다시 시작하기 위한 논리가 필요합니다.
2. `RequestStream.WriteAsync`는 다중 스레딩에 안전하게 사용할 수 없습니다. 한 번에 하나의 메시지만 스트림에 쓸 수 있습니다. 여러 스레드의 메시지를 단일 스트림을 통해 보내려면 <xref:System.Threading.Channels.Channel%601> 같은 생산자/소비자 큐가 메시지를 마샬링해야 합니다.
3. gRPC 스트리밍 방법은 한 유형의 메시지를 수신하고 한 유형의 메시지 유형을 보내는 것으로 제한됩니다. 예를 들어 `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)`는 `RequestMessage`를 수신하고 `ResponseMessage`를 보냅니다. Protobuf가 `Any` 및 `oneof`를 사용하여 알 수 없는 메시지나 조건부 메시지를 지원하면 이러한 제한을 해결할 수 있습니다.
