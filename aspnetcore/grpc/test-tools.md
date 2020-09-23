---
title: gRPC 도구를 사용하여 서비스 테스트
author: jamesnk
description: gRPC 도구를 사용하여 서비스를 테스트하는 방법을 알아봅니다. gRPCurl은 gRPC 서비스와 상호 작용하기 위한 명령줄 도구입니다. gRPCui는 대화형 웹 UI입니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594375"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="e299a-105">gRPC 도구를 사용하여 서비스 테스트</span><span class="sxs-lookup"><span data-stu-id="e299a-105">Test services with gRPC tools</span></span>

<span data-ttu-id="e299a-106">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e299a-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e299a-107">개발자가 클라이언트 앱을 빌드하지 않고도 서비스를 테스트할 수 있도록 하는 도구를 gRPC에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="e299a-108">[gRPCurl](https://github.com/fullstorydev/grpcurl)은 gRPC 서비스와의 상호 작용을 제공하는 명령줄 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="e299a-109">[gRPCui](https://github.com/fullstorydev/grpcui)는 gRPC에 대한 대화형 웹 UI를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="e299a-110">이 문서에서는 다음 작업의 수행 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-110">This article discusses how to:</span></span>

* <span data-ttu-id="e299a-111">gRPCurl 및 gRPCui를 다운로드하고 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="e299a-112">gRPC ASP.NET Core 앱을 사용하여 gRPC 리플렉션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="e299a-113">`grpcurl`를 사용하여 gRPC 서비스를 검색하고 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="e299a-114">`grpcui`를 사용하여 브라우저를 통해 gRPC 서비스와 상호 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="e299a-115">gRPCurl 정보</span><span class="sxs-lookup"><span data-stu-id="e299a-115">About gRPCurl</span></span>

<span data-ttu-id="e299a-116">gRPCurl은 gRPC 커뮤니티에서 만든 명령줄 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="e299a-117">해당 기능은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-117">Its features include:</span></span>

* <span data-ttu-id="e299a-118">스트리밍 서비스를 포함하여 gRPC 서비스 호출.</span><span class="sxs-lookup"><span data-stu-id="e299a-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="e299a-119">[gRPC 리플렉션](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)을 사용하여 서비스 검색.</span><span class="sxs-lookup"><span data-stu-id="e299a-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="e299a-120">gRPC 서비스 나열 및 설명.</span><span class="sxs-lookup"><span data-stu-id="e299a-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="e299a-121">안전한(TLS) 서버 및 안전하지 않은(일반 텍스트) 서버에서 작동.</span><span class="sxs-lookup"><span data-stu-id="e299a-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="e299a-122">`grpcurl`다운로드 및 설치에 대한 자세한 내용은 [gRPCurl GitHub 홈페이지](https://github.com/fullstorydev/grpcurl#installation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e299a-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="e299a-123">gRPC 리플렉션 설정</span><span class="sxs-lookup"><span data-stu-id="e299a-123">Setup gRPC reflection</span></span>

<span data-ttu-id="e299a-124">`grpcurl`에서 서비스를 호출하려면 먼저 서비스의 Protobuf 계약을 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="e299a-125">이때 다음과 같은 두 가지 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-125">There are two ways to do this:</span></span>

* <span data-ttu-id="e299a-126">gRPC 리플렉션을 사용하여 서비스 계약을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="e299a-127">명령줄 인수에 *.proto* 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="e299a-128">gRPC 리플렉션 및 서비스 검색에서는 gRPCurl을 사용하기가 더 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="e299a-129">gRPC ASP.NET Core는 [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) 패키지에서 gRPC 리플렉션을 기본적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="e299a-130">앱에서 리플렉션을 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="e299a-131">`Grpc.AspNetCore.Server.Reflection` 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="e299a-132">*Startup.cs*에서 리플렉션을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="e299a-133">`AddGrpcReflection` - 리플렉션을 사용하는 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="e299a-134">`MapGrpcReflectionService` - 리플렉션 서비스 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="e299a-135">`grpcurl` 사용</span><span class="sxs-lookup"><span data-stu-id="e299a-135">Use `grpcurl`</span></span>

<span data-ttu-id="e299a-136">`-help` 인수는 `grpcurl` 명령줄 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="e299a-137">서비스 검색</span><span class="sxs-lookup"><span data-stu-id="e299a-137">Discover services</span></span>

<span data-ttu-id="e299a-138">`describe` 동사를 사용하여 서버에서 정의한 서비스를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="e299a-139">위의 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-139">The preceding example:</span></span>

* <span data-ttu-id="e299a-140">`localhost:5001` 서버에서 `describe` 동사를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="e299a-141">gRPC 리플렉션에서 반환한 서비스 및 메서드를 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="e299a-142">`Greeter`는 앱에 의해 구현된 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="e299a-143">`ServerReflection`은 `Grpc.AspNetCore.Server.Reflection` 패키지에 의해 추가된 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="e299a-144">`describe`를 서비스, 메서드 또는 메시지 이름과 결합하여 세부 정보를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="e299a-145">gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="e299a-145">Call gRPC services</span></span>

<span data-ttu-id="e299a-146">요청 메시지를 나타내는 JSON 인수와 함께 서비스 및 메서드 이름을 지정하여 gRPC 서비스를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="e299a-147">JSON은 Protobuf로 변환되어 서비스로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="e299a-148">위의 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-148">The preceding example:</span></span>

* <span data-ttu-id="e299a-149">`-d` 인수는 JSON으로 요청 메시지를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="e299a-150">이 인수는 서버 주소 및 메서드 이름 앞에 와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="e299a-151">`greeter.Greeter` 서비스에서 `SayHello` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="e299a-152">응답 메시지를 JSON으로 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="e299a-153">gRPCui 정보</span><span class="sxs-lookup"><span data-stu-id="e299a-153">About gRPCui</span></span>

<span data-ttu-id="e299a-154">gRPCui는 gRPC의 대화형 웹 UI입니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="e299a-155">gRPCurl을 기반으로 구축되고 gRPC 서비스를 검색하고 테스트는 하는 GUI를 제공하며, Postman과 같은 HTTP 도구와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="e299a-156">`grpcui` 다운로드 및 설치에 대한 자세한 내용은 [gRPCui GitHub 홈페이지](https://github.com/fullstorydev/grpcui#installation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e299a-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="e299a-157">`grpcui` 사용</span><span class="sxs-lookup"><span data-stu-id="e299a-157">Using `grpcui`</span></span>

<span data-ttu-id="e299a-158">상호 작용할 서버 주소에서 `grpcui`를 인수로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="e299a-159">이 도구는 대화형 웹 UI와 함께 브라우저 창을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="e299a-160">gRPC 서비스는 gRPC 리플렉션을 사용하여 자동으로 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="e299a-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui 웹 UI](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="e299a-162">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="e299a-162">Additional resources</span></span>

* [<span data-ttu-id="e299a-163">gRPCurl GitHub 홈페이지</span><span class="sxs-lookup"><span data-stu-id="e299a-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="e299a-164">gRPCui GitHub 홈페이지</span><span class="sxs-lookup"><span data-stu-id="e299a-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="e299a-165">Grpc.AspNetCore.Server.Reflection</span><span class="sxs-lookup"><span data-stu-id="e299a-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
