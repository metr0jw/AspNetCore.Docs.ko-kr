---
title: gRPC와 프로세스 간 통신
author: jamesnk
description: 프로세스 간 통신에 gRPC를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945523"
---
# <a name="inter-process-communication-with-grpc"></a>gRPC와 프로세스 간 통신

작성자: [James Newton-King](https://twitter.com/jamesnk)

클라이언트와 서비스 간의 gRPC 호출은 일반적으로 TCP 소켓을 통해 전송됩니다. TCP는 네트워크를 통해 통신하는 데 유용하지만, 클라이언트와 서비스가 동일한 머신에 있는 경우에는 [IPC(프로세스 간 통신)](https://wikipedia.org/wiki/Inter-process_communication)가 더 효율적입니다. 이 문서에서는 IPC 시나리오에서 사용자 지정 전송에서 gRPC를 사용하는 방법을 설명합니다.

## <a name="server-configuration"></a>서버 구성

사용자 지정 전송은 Kestrel에서 지원됩니다. Kestrel은 *Program.cs*에서 구성됩니다.

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

위의 예제는 다음과 같습니다.

* `ConfigureKestrel`에서 Kestrel의 엔드포인트를 구성합니다.
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>을 호출하여 지정된 경로를 포함한 [UDS(Unix 도메인 소켓)](https://en.wikipedia.org/wiki/Unix_domain_socket)를 수신 대기합니다.

Kestrel은 UDS 엔드포인트에 대한 지원을 기본 제공합니다. UDS는 Linux, macOS 및 [최신 버전의 Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)에서 지원됩니다.

## <a name="client-configuration"></a>클라이언트 구성

`GrpcChannel`은 사용자 지정 전송을 통한 gRPC 호출을 지원합니다. 채널을 만들 때 사용자 지정 `ConnectionFactory`가 포함된 `SocketsHttpHandler`로 구성할 수 있습니다. 팩터리를 사용하면 클라이언트가 사용자 지정 전송을 통해 연결한 다음 해당 전송을 통해 HTTP 요청을 보낼 수 있습니다.

> [!IMPORTANT]
> `ConnectionFactory`는 .NET 5 릴리스 후보 1의 새로운 API입니다.

Unix 도메인 소켓 연결 팩터리의 예는 다음과 같습니다.

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

사용자 지정 연결 팩터리를 사용하여 채널을 만듭니다.

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

위의 코드를 사용하여 만든 채널은 Unix 도메인 소켓을 통해 gRPC 호출을 보냅니다.
