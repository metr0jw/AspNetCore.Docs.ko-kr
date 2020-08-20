---
title: IIS가 있는 Windows에서 ASP.NET Core 호스팅
author: rick-anderson
description: Windows Server IIS(인터넷 정보 서비스)에서 ASP.NET Core 앱을 호스팅하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: 1338ecd6205a351b54a58522b1713a6d70e2d22a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634750"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="a88e4-103">IIS가 있는 Windows에서 ASP.NET Core 호스팅</span><span class="sxs-lookup"><span data-stu-id="a88e4-103">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a88e4-104">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="a88e4-105">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="a88e4-106">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="a88e4-106">Supported operating systems</span></span>

<span data-ttu-id="a88e4-107">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="a88e4-108">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-108">Windows 7 or later</span></span>
* <span data-ttu-id="a88e4-109">Windows Server 2012 R2 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="a88e4-110">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="a88e4-111">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a88e4-112">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="a88e4-113">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a88e4-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="a88e4-114">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="a88e4-114">Supported platforms</span></span>

<span data-ttu-id="a88e4-115">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="a88e4-116">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="a88e4-117">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="a88e4-118">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="a88e4-119">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="a88e4-120">32비트(x86)용으로 게시된 앱은 해당 IIS 애플리케이션 풀에 대해 32비트를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-120">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="a88e4-121">자세한 내용은 [IIS 사이트 만들기](#create-the-iis-site) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-121">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="a88e4-122">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-122">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="a88e4-123">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-123">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="a88e4-124">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="a88e4-124">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="a88e4-125">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="a88e4-125">In-process hosting model</span></span>

<span data-ttu-id="a88e4-126">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-126">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="a88e4-127">요청이 나가는 네트워크 트래픽을 동일한 머신에 다시 반환하는 네트워크 인터페이스인 루프백 어댑터를 통해 프록시되지 않기 때문에 In-Process 호스팅이 Out-of-Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-127">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="a88e4-128">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-128">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a88e4-129">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="a88e4-129">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="a88e4-130">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-130">Performs app initialization.</span></span>
  * <span data-ttu-id="a88e4-131">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-131">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="a88e4-132">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="a88e4-132">Calls `Program.Main`.</span></span>
* <span data-ttu-id="a88e4-133">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-133">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="a88e4-134">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-134">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="a88e4-136">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-136">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="a88e4-137">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-137">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="a88e4-138">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-138">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="a88e4-139">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-139">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="a88e4-140">IIS HTTP 서버에서 요청을 처리한 후:</span><span class="sxs-lookup"><span data-stu-id="a88e4-140">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="a88e4-141">해당 요청은 ASP.NET Core 미들웨어 파이프라인으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-141">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="a88e4-142">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-142">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="a88e4-143">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-143">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="a88e4-144">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-144">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="a88e4-145">In Process 호스팅은 기존 앱에 대해 옵트인(opt in)됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-145">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="a88e4-146">ASP.NET Core 웹 템플릿은 In Process 호스팅 모델을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-146">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="a88e4-147">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(*w3wp.exe* 또는 *iisexpress.exe*) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-147">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="a88e4-148">성능 테스트의 결과 .NET Core 앱 in-process를 호스팅하는 것이 앱 out-of-process 및 [Kestrel](xref:fundamentals/servers/kestrel)에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-148">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a88e4-149">단일 실행 파일로 게시된 앱은 In Process 호스팅 모델을 통해 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-149">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="a88e4-150">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="a88e4-150">Out-of-process hosting model</span></span>

<span data-ttu-id="a88e4-151">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-151">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="a88e4-152">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-152">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="a88e4-153">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-153">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a88e4-154">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-154">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="a88e4-156">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-156">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="a88e4-157">드라이버는 웹 사이트의 구성된 포트에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-157">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="a88e4-158">구성된 포트는 일반적으로 80(HTTP) 또는 443(HTTPS)입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-158">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="a88e4-159">모듈은 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-159">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="a88e4-160">임의 포트는 80 또는 443이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-160">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="a88e4-161">ASP.NET Core 모듈은 시작 시 환경 변수를 통해 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-161">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="a88e4-162"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-162">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="a88e4-163">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-163">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="a88e4-164">이 모듈은 HTTPS 전달을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-164">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="a88e4-165">HTTPS를 통해 IIS에서 수신하는 경우에도 요청은 HTTP를 통해 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-165">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="a88e4-166">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-166">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a88e4-167">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a88e4-168">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-168">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="a88e4-169">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-169">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="a88e4-170">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-170">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="a88e4-171">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-171">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="a88e4-172">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-172">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="a88e4-173">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="a88e4-173">Enable the IISIntegration components</span></span>

<span data-ttu-id="a88e4-174">`CreateHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-174">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="a88e4-175">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/generic-host#default-builder-settings>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-175">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="a88e4-176">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-176">IIS options</span></span>

<span data-ttu-id="a88e4-177">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="a88e4-177">**In-process hosting model**</span></span>

<span data-ttu-id="a88e4-178">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-178">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="a88e4-179">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-179">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="a88e4-180">옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-180">Option</span></span>                         | <span data-ttu-id="a88e4-181">기본값</span><span class="sxs-lookup"><span data-stu-id="a88e4-181">Default</span></span> | <span data-ttu-id="a88e4-182">설정</span><span class="sxs-lookup"><span data-stu-id="a88e4-182">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a88e4-183">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-183">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a88e4-184">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-184">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a88e4-185">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-185">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a88e4-186">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-186">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a88e4-187">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-187">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="a88e4-188">`HttpContext.Request` 및 `HttpContext.Response`에 대해 동기 I/O가 허용되는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-188">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="a88e4-189">`HttpRequest`의 최대 요청 본문 크기를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-189">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="a88e4-190">IIS 자체에는 `IISServerOptions`에 설정된 `MaxRequestBodySize` 앞에 처리되는 `maxAllowedContentLength` 한도가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-190">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="a88e4-191">`MaxRequestBodySize`를 변경해도 `maxAllowedContentLength`에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-191">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="a88e4-192">`maxAllowedContentLength`를 늘리려면 *web.config*에 항목을 추가하여 `maxAllowedContentLength`를 더 높은 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-192">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="a88e4-193">자세한 내용은 [구성](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-193">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="a88e4-194">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="a88e4-194">**Out-of-process hosting model**</span></span>

<span data-ttu-id="a88e4-195">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-195">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="a88e4-196">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-196">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="a88e4-197">옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-197">Option</span></span>                         | <span data-ttu-id="a88e4-198">기본값</span><span class="sxs-lookup"><span data-stu-id="a88e4-198">Default</span></span> | <span data-ttu-id="a88e4-199">설정</span><span class="sxs-lookup"><span data-stu-id="a88e4-199">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a88e4-200">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-200">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a88e4-201">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-201">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a88e4-202">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-202">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a88e4-203">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-203">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a88e4-204">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-204">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="a88e4-205">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-205">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a88e4-206">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="a88e4-206">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a88e4-207">[IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 다음을 전달하도록 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-207">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="a88e4-208">체계(HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a88e4-208">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="a88e4-209">요청이 시작된 원격 IP 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-209">Remote IP address where the request originated.</span></span>

<span data-ttu-id="a88e4-210">[IIS 통합 미들웨어](#enable-the-iisintegration-components)는 전달된 헤더 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-210">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="a88e4-211">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-211">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="a88e4-212">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-212">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="a88e4-213">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="a88e4-213">web.config file</span></span>

<span data-ttu-id="a88e4-214">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-214">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a88e4-215">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-215">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="a88e4-216">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-216">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="a88e4-217">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-217">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a88e4-218">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-218">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="a88e4-219">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-219">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="a88e4-220">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-220">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="a88e4-221">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-221">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="a88e4-222">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-222">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="a88e4-223">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-223">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="a88e4-224">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-224">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="a88e4-225">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-225">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="a88e4-226">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="a88e4-226">web.config file location</span></span>

<span data-ttu-id="a88e4-227">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-227">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="a88e4-228">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-228">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="a88e4-229">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-229">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="a88e4-230">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-230">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="a88e4-231">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-231">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="a88e4-232">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-232">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="a88e4-233">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="a88e4-233">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="a88e4-234">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="a88e4-234">Transform web.config</span></span>

<span data-ttu-id="a88e4-235">게시할 때 *web.config*를 변환해야 하는 경우 <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-235">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="a88e4-236">구성, 프로필 또는 환경을 기반으로 하는 환경 변수를 설정하려면 게시할 때 *web.config*를 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-236">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="a88e4-237">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-237">IIS configuration</span></span>

<span data-ttu-id="a88e4-238">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="a88e4-238">**Windows Server operating systems**</span></span>

<span data-ttu-id="a88e4-239">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-239">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="a88e4-240">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-240">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="a88e4-241">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-241">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="a88e4-243">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-243">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="a88e4-244">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-244">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="a88e4-246">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-246">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a88e4-247">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-247">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="a88e4-248">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-248">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a88e4-249">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-249">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a88e4-250">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-250">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a88e4-251">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-251">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a88e4-252">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-252">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="a88e4-253">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-253">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a88e4-254">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-254">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a88e4-255">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-255">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="a88e4-256">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-256">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="a88e4-257">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="a88e4-257">**Windows desktop operating systems**</span></span>

<span data-ttu-id="a88e4-258">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-258">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a88e4-259">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-259">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="a88e4-260">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-260">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="a88e4-261">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-261">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="a88e4-262">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-262">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="a88e4-263">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-263">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a88e4-264">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-264">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="a88e4-265">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-265">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a88e4-266">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-266">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="a88e4-267">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-267">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a88e4-268">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-268">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a88e4-269">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-269">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a88e4-270">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-270">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a88e4-271">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-271">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="a88e4-272">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-272">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a88e4-273">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-273">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a88e4-274">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-274">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="a88e4-276">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-276">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a88e4-277">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-277">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="a88e4-278">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-278">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a88e4-279">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-279">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a88e4-280">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-280">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="a88e4-281">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-281">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="a88e4-282">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="a88e4-282">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="a88e4-283">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-283">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="a88e4-284">직접 다운로드(현재 버전)</span><span class="sxs-lookup"><span data-stu-id="a88e4-284">Direct download (current version)</span></span>

<span data-ttu-id="a88e4-285">다음 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-285">Download the installer using the following link:</span></span>

[<span data-ttu-id="a88e4-286">현재 .NET Core 호스팅 번들 설치 관리자(직접 다운로드)</span><span class="sxs-lookup"><span data-stu-id="a88e4-286">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="a88e4-287">이전 버전의 설치 관리자</span><span class="sxs-lookup"><span data-stu-id="a88e4-287">Earlier versions of the installer</span></span>

<span data-ttu-id="a88e4-288">이전 버전의 설치 관리자를 가져오려면:</span><span class="sxs-lookup"><span data-stu-id="a88e4-288">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="a88e4-289">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-289">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="a88e4-290">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-290">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="a88e4-291">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-291">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="a88e4-292">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-292">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="a88e4-293">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-293">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="a88e4-294">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-294">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="a88e4-295">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-295">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="a88e4-296">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-296">Run the installer on the server.</span></span> <span data-ttu-id="a88e4-297">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-297">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="a88e4-298">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-298">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="a88e4-299">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-299">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="a88e4-300">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-300">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="a88e4-301">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-301">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="a88e4-302">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-302">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="a88e4-303">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-303">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="a88e4-304">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-304">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="a88e4-305">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-305">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="a88e4-306">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-306">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="a88e4-307">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="a88e4-307">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="a88e4-308">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-308">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="a88e4-309">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-309">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="a88e4-310">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-310">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="a88e4-311">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-311">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="a88e4-312">새 호스팅 번들을 설치하여 공유 프레임워크를 업그레이드한 후, 시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-312">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="a88e4-313">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-313">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="a88e4-314">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-314">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="a88e4-315">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-315">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="a88e4-316">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-316">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="a88e4-317">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-317">The preferred method is to use WebPI.</span></span> <span data-ttu-id="a88e4-318">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-318">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="a88e4-319">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="a88e4-319">Create the IIS site</span></span>

1. <span data-ttu-id="a88e4-320">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-320">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="a88e4-321">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-321">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="a88e4-322">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-322">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="a88e4-323">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-323">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="a88e4-324">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-324">Right-click the **Sites** folder.</span></span> <span data-ttu-id="a88e4-325">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-325">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="a88e4-326">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-326">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="a88e4-327">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-327">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="a88e4-329">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-329">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a88e4-330">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-330">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="a88e4-331">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-331">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a88e4-332">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-332">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="a88e4-333">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="a88e4-333">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a88e4-334">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-334">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="a88e4-335">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-335">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="a88e4-336">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-336">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="a88e4-337">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-337">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="a88e4-339">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-339">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="a88e4-340">ASP.NET Core에서는 데스크톱 CLR(.NET CLR)을 로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-340">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="a88e4-341">.NET Core에 대한 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-341">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="a88e4-342">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-342">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="a88e4-343">*ASP.NET Core 2.2 이상*:</span><span class="sxs-lookup"><span data-stu-id="a88e4-343">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="a88e4-344">[In-Process 호스팅 모델](#in-process-hosting-model)을 사용하는 32비트 SDK로 게시된 32비트(x86) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트의 애플리케이션 풀을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-344">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="a88e4-345">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-345">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="a88e4-346">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-346">Select the app's Application Pool.</span></span> <span data-ttu-id="a88e4-347">**작업** 사이드바에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-347">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="a88e4-348">**32비트 애플리케이션 사용**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-348">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="a88e4-349">[In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-349">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="a88e4-350">IIS 관리자에서 **연결** 사이드바의 **애플리케이션 풀**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-350">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="a88e4-351">앱의 애플리케이션 풀을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-351">Select the app's Application Pool.</span></span> <span data-ttu-id="a88e4-352">**작업** 사이드바에서 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-352">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="a88e4-353">**32비트 애플리케이션 사용**을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-353">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="a88e4-354">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-354">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="a88e4-355">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-355">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="a88e4-356">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-356">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="a88e4-357">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-357">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="a88e4-358">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-358">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="a88e4-359">앱 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-359">Deploy the app</span></span>

<span data-ttu-id="a88e4-360">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-360">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="a88e4-361">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-361">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="a88e4-362">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-362">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="a88e4-363">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-363">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="a88e4-364">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-364">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="a88e4-366">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-366">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="a88e4-367">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-367">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="a88e4-368">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-368">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="a88e4-369">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="a88e4-369">Alternatives to Web Deploy</span></span>

<span data-ttu-id="a88e4-370">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-370">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="a88e4-371">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-371">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="a88e4-372">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="a88e4-372">Browse the website</span></span>

<span data-ttu-id="a88e4-373">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-373">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="a88e4-374">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-374">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="a88e4-375">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-375">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="a88e4-377">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="a88e4-377">Locked deployment files</span></span>

<span data-ttu-id="a88e4-378">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-378">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="a88e4-379">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-379">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="a88e4-380">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-380">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="a88e4-381">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-381">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="a88e4-382">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-382">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="a88e4-383">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-383">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="a88e4-384">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-384">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="a88e4-385">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-385">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="a88e4-386">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="a88e4-386">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="a88e4-387">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="a88e4-387">Data protection</span></span>

<span data-ttu-id="a88e4-388">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-388">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="a88e4-389">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-389">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="a88e4-390">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-390">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="a88e4-391">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-391">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="a88e4-392">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-392">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="a88e4-393">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-393">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="a88e4-394">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-394">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="a88e4-395">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-395">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="a88e4-396">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-396">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="a88e4-397">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="a88e4-397">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="a88e4-398">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-398">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="a88e4-399">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-399">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="a88e4-400">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-400">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="a88e4-401">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-401">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="a88e4-402">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-402">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="a88e4-403">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-403">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="a88e4-404">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-404">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="a88e4-405">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-405">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="a88e4-406">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-406">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="a88e4-407">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-407">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="a88e4-408">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-408">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="a88e4-409">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="a88e4-409">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="a88e4-410">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-410">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="a88e4-411">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-411">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="a88e4-412">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-412">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="a88e4-413">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-413">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="a88e4-414">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-414">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="a88e4-415">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-415">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="a88e4-416">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-416">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="a88e4-417">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-417">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="a88e4-418">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-418">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="a88e4-419">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-419">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="a88e4-420">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-420">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="a88e4-421">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-421">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="a88e4-422">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="a88e4-422">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="a88e4-423">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-423">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="a88e4-424">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-424">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="a88e4-425">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-425">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="a88e4-426">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-426">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="a88e4-427">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-427">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="a88e4-428">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-428">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="a88e4-429">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-429">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="a88e4-430">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="a88e4-430">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="a88e4-431">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-431">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="a88e4-432">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-432">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="a88e4-433">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="a88e4-433">Virtual Directories</span></span>

<span data-ttu-id="a88e4-434">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-434">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="a88e4-435">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-435">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="a88e4-436">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="a88e4-436">Sub-applications</span></span>

<span data-ttu-id="a88e4-437">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-437">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="a88e4-438">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-438">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="a88e4-439">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-439">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="a88e4-440">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-440">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="a88e4-441">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-441">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="a88e4-442">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-442">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="a88e4-443">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-443">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="a88e4-444">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-444">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="a88e4-445">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-445">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="a88e4-446">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-446">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="a88e4-447">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-447">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="a88e4-448">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-448">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="a88e4-449">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-449">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="a88e4-450">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-450">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="a88e4-451">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-451">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="a88e4-452">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-452">Select **OK**.</span></span>

<span data-ttu-id="a88e4-453">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-453">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="a88e4-454">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-454">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="a88e4-455">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-455">Configuration of IIS with web.config</span></span>

<span data-ttu-id="a88e4-456">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-456">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="a88e4-457">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-457">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="a88e4-458">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-458">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="a88e4-459">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-459">For more information, see the following topics:</span></span>

* [<span data-ttu-id="a88e4-460">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="a88e4-460">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="a88e4-461">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-461">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="a88e4-462">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="a88e4-462">Configuration sections of web.config</span></span>

<span data-ttu-id="a88e4-463">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-463">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="a88e4-464">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-464">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="a88e4-465">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-465">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="a88e4-466">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="a88e4-466">Application Pools</span></span>

<span data-ttu-id="a88e4-467">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-467">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="a88e4-468">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-468">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="a88e4-469">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-469">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="a88e4-470">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-470">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="a88e4-471">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-471">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="a88e4-472">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-472">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="a88e4-473">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="a88e4-473">Application Pool Identity</span></span>

<span data-ttu-id="a88e4-474">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-474">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="a88e4-475">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-475">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="a88e4-476">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-476">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="a88e4-478">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-478">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="a88e4-479">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-479">Resources can be secured using this identity.</span></span> <span data-ttu-id="a88e4-480">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-480">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="a88e4-481">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-481">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="a88e4-482">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-482">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="a88e4-483">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-483">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="a88e4-484">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-484">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="a88e4-485">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-485">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="a88e4-486">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-486">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="a88e4-487">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-487">Select the **Check Names** button.</span></span> <span data-ttu-id="a88e4-488">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-488">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="a88e4-489">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-489">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="a88e4-490">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-490">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="a88e4-491">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-491">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="a88e4-493">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-493">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="a88e4-495">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-495">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="a88e4-496">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-496">Provide additional permissions as needed.</span></span>

<span data-ttu-id="a88e4-497">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-497">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="a88e4-498">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-498">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="a88e4-499">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-499">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a88e4-500">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="a88e4-500">HTTP/2 support</span></span>

<span data-ttu-id="a88e4-501">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-501">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="a88e4-502">In-Process</span><span class="sxs-lookup"><span data-stu-id="a88e4-502">In-process</span></span>
  * <span data-ttu-id="a88e4-503">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-503">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a88e4-504">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="a88e4-504">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="a88e4-505">Out of Process</span><span class="sxs-lookup"><span data-stu-id="a88e4-505">Out-of-process</span></span>
  * <span data-ttu-id="a88e4-506">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-506">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a88e4-507">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-507">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="a88e4-508">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="a88e4-508">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a88e4-509">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-509">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="a88e4-510">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-510">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="a88e4-511">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-511">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="a88e4-512">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-512">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a88e4-513">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-513">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="a88e4-514">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-514">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="a88e4-515">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="a88e4-515">CORS preflight requests</span></span>

<span data-ttu-id="a88e4-516">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-516">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="a88e4-517">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-517">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="a88e4-518">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-518">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="a88e4-519">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="a88e4-519">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="a88e4-520">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="a88e4-520">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="a88e4-521">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-521">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="a88e4-522">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-522">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="a88e4-523">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="a88e4-523">Application Initialization Module</span></span>

<span data-ttu-id="a88e4-524">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="a88e4-524">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="a88e4-525">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-525">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="a88e4-526">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-526">The request triggers the app to start.</span></span> <span data-ttu-id="a88e4-527">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="a88e4-527">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="a88e4-528">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-528">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="a88e4-529">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="a88e4-529">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="a88e4-530">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-530">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="a88e4-531">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-531">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="a88e4-532">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-532">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="a88e4-533">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-533">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="a88e4-534">**역할 및 기능 추가 마법사**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-534">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="a88e4-535">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-535">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="a88e4-536">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-536">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="a88e4-537">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-537">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="a88e4-538">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="a88e4-538">Using IIS Manager:</span></span>

  1. <span data-ttu-id="a88e4-539">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-539">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="a88e4-540">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-540">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="a88e4-541">기본 **시작 모드**는 **OnDemand**입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-541">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="a88e4-542">**시작 모드**를 **AlwaysRunning**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-542">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="a88e4-543">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-543">Select **OK**.</span></span>
  1. <span data-ttu-id="a88e4-544">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-544">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="a88e4-545">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-545">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="a88e4-546">기본 **미리 로드 사용** 설정은 **False**입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-546">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="a88e4-547">**미리 로드 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-547">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="a88e4-548">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-548">Select **OK**.</span></span>

* <span data-ttu-id="a88e4-549">*web.config*를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-549">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="a88e4-550">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="a88e4-550">Idle Timeout</span></span>

<span data-ttu-id="a88e4-551">‘앱 호스팅 In Process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="a88e4-551">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="a88e4-552">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-552">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="a88e4-553">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-553">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="a88e4-554">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-554">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="a88e4-555">기본 **유휴 시간 제한(분)** 은 **20**분입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-555">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="a88e4-556">**유휴 시간 제한(분)** 을 **0**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-556">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="a88e4-557">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-557">Select **OK**.</span></span>
1. <span data-ttu-id="a88e4-558">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-558">Recycle the worker process.</span></span>

<span data-ttu-id="a88e4-559">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-559">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="a88e4-560">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-560">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="a88e4-561">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-561">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="a88e4-562">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="a88e4-562">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="a88e4-563">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="a88e4-563">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="a88e4-564">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="a88e4-564">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="a88e4-565">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="a88e4-565">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="a88e4-566">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="a88e4-566">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="a88e4-567">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="a88e4-567">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="a88e4-568">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="a88e4-568">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="a88e4-569">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-569">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="a88e4-570">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a88e4-570">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="a88e4-571">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="a88e4-571">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="a88e4-572">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="a88e4-572">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="a88e4-573">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a88e4-573">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a88e4-574">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-574">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="a88e4-575">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-575">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="a88e4-576">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="a88e4-576">Supported operating systems</span></span>

<span data-ttu-id="a88e4-577">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-577">The following operating systems are supported:</span></span>

* <span data-ttu-id="a88e4-578">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-578">Windows 7 or later</span></span>
* <span data-ttu-id="a88e4-579">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-579">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a88e4-580">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-580">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="a88e4-581">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-581">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a88e4-582">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-582">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="a88e4-583">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a88e4-583">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="a88e4-584">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="a88e4-584">Supported platforms</span></span>

<span data-ttu-id="a88e4-585">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-585">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="a88e4-586">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-586">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="a88e4-587">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-587">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="a88e4-588">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-588">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="a88e4-589">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-589">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="a88e4-590">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-590">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="a88e4-591">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-591">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="a88e4-592">호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="a88e4-592">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="a88e4-593">In-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="a88e4-593">In-process hosting model</span></span>

<span data-ttu-id="a88e4-594">In-Process 호스팅을 사용하면 ASP.NET Core 앱은 IIS 작업자 프로세스와 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-594">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="a88e4-595">In Process 호스팅은 다음과 같은 이유로 Out of Process 호스팅보다 향상된 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-595">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="a88e4-596">요청은 루프백 어댑터를 통해 프록시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-596">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="a88e4-597">루프백 어댑터는 나가는 네트워크 트래픽을 동일한 컴퓨터로 다시 반환하는 네트워크 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-597">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="a88e4-598">IIS는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)를 사용하여 프로세스 관리를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-598">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a88e4-599">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="a88e4-599">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="a88e4-600">앱 초기화를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-600">Performs app initialization.</span></span>
  * <span data-ttu-id="a88e4-601">[CoreCLR](/dotnet/standard/glossary#coreclr)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-601">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="a88e4-602">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="a88e4-602">Calls `Program.Main`.</span></span>
* <span data-ttu-id="a88e4-603">IIS 네이티브 요청의 수명을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-603">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="a88e4-604">In-process 호스팅 모델은 .NET Framework를 대상으로 하는 ASP.NET Core 앱을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-604">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="a88e4-605">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 In-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-605">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![In Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-inprocess.png)

<span data-ttu-id="a88e4-607">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-607">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="a88e4-608">드라이버는 웹 사이트의 구성된 포트[일반적으로 80(HTTP) 또는 443(HTTPS)]에서 IIS로 네이티브 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-608">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="a88e4-609">ASP.NET Core 모듈에서는 기본 요청을 수신하고 IIS HTTP Server(`IISHttpServer`)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-609">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="a88e4-610">IIS HTTP 서버는 네이티브 요청을 관리형 요청으로 변환하는 IIS의 In-process 서버를 구현한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-610">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="a88e4-611">IIS HTTP Server가 요청을 처리하면 해당 요청이 ASP.NET Core 미들웨어 파이프라인에 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-611">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a88e4-612">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-612">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a88e4-613">앱의 응답은 IIS HTTP 서버를 통해 IIS로 다시 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-613">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="a88e4-614">IIS는 요청을 시작한 클라이언트에 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-614">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="a88e4-615">In-process 호스팅은 기존 앱에 대한 옵트인(opt-in) 기능이지만 [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿은 기본적으로 모든 IIS 및 IIS Express 시나리오에 대해 In-Process 호스팅 모델로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-615">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="a88e4-616">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 메서드를 호출하여 [CoreCLR](/dotnet/standard/glossary#coreclr)을 부팅하고 IIS 작업자 프로세스(*w3wp.exe* 또는 *iisexpress.exe*) 내에서 앱을 호스트함으로써 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-616">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="a88e4-617">성능 테스트의 결과 .NET Core 앱을 in-process로 호스트하는 것이 앱을 out-of-process로 호스트하고 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 요청을 프록시하는 것보다 훨씬 높은 요청 처리량을 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-617">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="a88e4-618">Out-of-Process 호스팅 모델</span><span class="sxs-lookup"><span data-stu-id="a88e4-618">Out-of-process hosting model</span></span>

<span data-ttu-id="a88e4-619">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 ASP.NET Core 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-619">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="a88e4-620">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-620">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="a88e4-621">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-621">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a88e4-622">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-622">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Out-of-Process 호스팅 시나리오에서 ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="a88e4-624">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-624">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="a88e4-625">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-625">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="a88e4-626">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-626">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="a88e4-627">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 확장은 `http://localhost:{PORT}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-627">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="a88e4-628">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-628">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="a88e4-629">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-629">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="a88e4-630">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-630">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a88e4-631">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-631">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a88e4-632">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-632">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="a88e4-633">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-633">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="a88e4-634">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-634">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="a88e4-635">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-635">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="a88e4-636">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-636">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="a88e4-637">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="a88e4-637">Enable the IISIntegration components</span></span>

<span data-ttu-id="a88e4-638">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-638">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="a88e4-639">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-639">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="a88e4-640">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-640">IIS options</span></span>

<span data-ttu-id="a88e4-641">**In-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="a88e4-641">**In-process hosting model**</span></span>

<span data-ttu-id="a88e4-642">IIS 서버 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISServerOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-642">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="a88e4-643">다음 예제에서는 AutomaticAuthentication을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-643">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="a88e4-644">옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-644">Option</span></span>                         | <span data-ttu-id="a88e4-645">기본값</span><span class="sxs-lookup"><span data-stu-id="a88e4-645">Default</span></span> | <span data-ttu-id="a88e4-646">설정</span><span class="sxs-lookup"><span data-stu-id="a88e4-646">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a88e4-647">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-647">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a88e4-648">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-648">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a88e4-649">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-649">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a88e4-650">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-650">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a88e4-651">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-651">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="a88e4-652">**Out-of-process 호스팅 모델**</span><span class="sxs-lookup"><span data-stu-id="a88e4-652">**Out-of-process hosting model**</span></span>

<span data-ttu-id="a88e4-653">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-653">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="a88e4-654">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-654">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="a88e4-655">옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-655">Option</span></span>                         | <span data-ttu-id="a88e4-656">기본값</span><span class="sxs-lookup"><span data-stu-id="a88e4-656">Default</span></span> | <span data-ttu-id="a88e4-657">설정</span><span class="sxs-lookup"><span data-stu-id="a88e4-657">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a88e4-658">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-658">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a88e4-659">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-659">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a88e4-660">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-660">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a88e4-661">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-661">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a88e4-662">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-662">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="a88e4-663">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-663">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a88e4-664">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="a88e4-664">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a88e4-665">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-665">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="a88e4-666">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-666">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="a88e4-667">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-667">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="a88e4-668">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="a88e4-668">web.config file</span></span>

<span data-ttu-id="a88e4-669">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-669">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a88e4-670">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-670">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="a88e4-671">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-671">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="a88e4-672">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-672">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a88e4-673">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-673">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="a88e4-674">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-674">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="a88e4-675">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-675">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="a88e4-676">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-676">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="a88e4-677">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-677">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="a88e4-678">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-678">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="a88e4-679">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-679">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="a88e4-680">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-680">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="a88e4-681">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="a88e4-681">web.config file location</span></span>

<span data-ttu-id="a88e4-682">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-682">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="a88e4-683">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-683">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="a88e4-684">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-684">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="a88e4-685">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-685">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="a88e4-686">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-686">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="a88e4-687">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-687">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="a88e4-688">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="a88e4-688">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="a88e4-689">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="a88e4-689">Transform web.config</span></span>

<span data-ttu-id="a88e4-690">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-690">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="a88e4-691">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-691">IIS configuration</span></span>

<span data-ttu-id="a88e4-692">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="a88e4-692">**Windows Server operating systems**</span></span>

<span data-ttu-id="a88e4-693">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-693">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="a88e4-694">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-694">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="a88e4-695">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-695">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="a88e4-697">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-697">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="a88e4-698">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-698">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="a88e4-700">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-700">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a88e4-701">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-701">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="a88e4-702">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-702">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a88e4-703">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-703">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a88e4-704">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-704">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a88e4-705">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-705">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a88e4-706">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-706">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="a88e4-707">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-707">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a88e4-708">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-708">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a88e4-709">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-709">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="a88e4-710">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-710">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="a88e4-711">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="a88e4-711">**Windows desktop operating systems**</span></span>

<span data-ttu-id="a88e4-712">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-712">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a88e4-713">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-713">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="a88e4-714">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-714">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="a88e4-715">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-715">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="a88e4-716">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-716">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="a88e4-717">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-717">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a88e4-718">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-718">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="a88e4-719">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-719">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a88e4-720">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-720">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="a88e4-721">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-721">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a88e4-722">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-722">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a88e4-723">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-723">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a88e4-724">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-724">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a88e4-725">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-725">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="a88e4-726">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-726">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a88e4-727">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-727">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a88e4-728">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-728">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="a88e4-730">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-730">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a88e4-731">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-731">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="a88e4-732">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-732">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a88e4-733">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-733">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a88e4-734">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-734">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="a88e4-735">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-735">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="a88e4-736">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="a88e4-736">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="a88e4-737">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-737">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="a88e4-738">다운로드</span><span class="sxs-lookup"><span data-stu-id="a88e4-738">Download</span></span>

1. <span data-ttu-id="a88e4-739">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-739">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="a88e4-740">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-740">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="a88e4-741">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-741">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="a88e4-742">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-742">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="a88e4-743">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-743">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="a88e4-744">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-744">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="a88e4-745">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-745">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="a88e4-746">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-746">Run the installer on the server.</span></span> <span data-ttu-id="a88e4-747">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-747">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="a88e4-748">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-748">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="a88e4-749">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-749">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="a88e4-750">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-750">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="a88e4-751">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-751">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="a88e4-752">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-752">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="a88e4-753">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-753">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="a88e4-754">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-754">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="a88e4-755">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-755">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="a88e4-756">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-756">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="a88e4-757">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="a88e4-757">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="a88e4-758">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-758">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="a88e4-759">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-759">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="a88e4-760">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-760">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="a88e4-761">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-761">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="a88e4-762">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-762">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="a88e4-763">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-763">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="a88e4-764">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-764">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="a88e4-765">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-765">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="a88e4-766">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-766">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="a88e4-767">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-767">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="a88e4-768">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-768">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="a88e4-769">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-769">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="a88e4-770">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-770">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="a88e4-771">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-771">The preferred method is to use WebPI.</span></span> <span data-ttu-id="a88e4-772">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-772">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="a88e4-773">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="a88e4-773">Create the IIS site</span></span>

1. <span data-ttu-id="a88e4-774">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-774">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="a88e4-775">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-775">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="a88e4-776">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-776">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="a88e4-777">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-777">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="a88e4-778">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-778">Right-click the **Sites** folder.</span></span> <span data-ttu-id="a88e4-779">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-779">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="a88e4-780">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-780">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="a88e4-781">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-781">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="a88e4-783">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-783">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a88e4-784">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-784">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="a88e4-785">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-785">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a88e4-786">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-786">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="a88e4-787">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="a88e4-787">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a88e4-788">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-788">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="a88e4-789">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-789">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="a88e4-790">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-790">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="a88e4-791">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-791">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="a88e4-793">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-793">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="a88e4-794">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-794">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="a88e4-795">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-795">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="a88e4-796">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-796">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="a88e4-797">IIS 관리자의 **애플리케이션 풀**에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-797">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="a88e4-798">**32비트 애플리케이션 사용**을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-798">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="a88e4-799">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-799">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="a88e4-800">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-800">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="a88e4-801">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-801">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="a88e4-802">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-802">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="a88e4-803">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-803">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="a88e4-804">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-804">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="a88e4-805">앱 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-805">Deploy the app</span></span>

<span data-ttu-id="a88e4-806">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-806">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="a88e4-807">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-807">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="a88e4-808">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-808">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="a88e4-809">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-809">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="a88e4-810">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-810">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="a88e4-812">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-812">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="a88e4-813">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-813">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="a88e4-814">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-814">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="a88e4-815">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="a88e4-815">Alternatives to Web Deploy</span></span>

<span data-ttu-id="a88e4-816">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-816">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="a88e4-817">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-817">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="a88e4-818">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="a88e4-818">Browse the website</span></span>

<span data-ttu-id="a88e4-819">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-819">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="a88e4-820">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-820">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="a88e4-821">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-821">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="a88e4-823">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="a88e4-823">Locked deployment files</span></span>

<span data-ttu-id="a88e4-824">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-824">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="a88e4-825">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-825">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="a88e4-826">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-826">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="a88e4-827">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-827">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="a88e4-828">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-828">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="a88e4-829">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-829">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="a88e4-830">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-830">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="a88e4-831">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-831">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="a88e4-832">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="a88e4-832">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="a88e4-833">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="a88e4-833">Data protection</span></span>

<span data-ttu-id="a88e4-834">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-834">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="a88e4-835">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-835">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="a88e4-836">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-836">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="a88e4-837">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-837">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="a88e4-838">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-838">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="a88e4-839">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-839">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="a88e4-840">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-840">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="a88e4-841">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-841">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="a88e4-842">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-842">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="a88e4-843">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="a88e4-843">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="a88e4-844">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-844">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="a88e4-845">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-845">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="a88e4-846">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-846">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="a88e4-847">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-847">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="a88e4-848">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-848">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="a88e4-849">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-849">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="a88e4-850">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-850">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="a88e4-851">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-851">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="a88e4-852">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-852">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="a88e4-853">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-853">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="a88e4-854">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-854">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="a88e4-855">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="a88e4-855">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="a88e4-856">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-856">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="a88e4-857">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-857">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="a88e4-858">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-858">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="a88e4-859">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-859">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="a88e4-860">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-860">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="a88e4-861">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-861">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="a88e4-862">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-862">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="a88e4-863">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-863">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="a88e4-864">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-864">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="a88e4-865">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-865">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="a88e4-866">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-866">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="a88e4-867">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-867">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="a88e4-868">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="a88e4-868">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="a88e4-869">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-869">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="a88e4-870">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-870">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="a88e4-871">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-871">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="a88e4-872">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-872">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="a88e4-873">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-873">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="a88e4-874">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-874">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="a88e4-875">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-875">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="a88e4-876">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="a88e4-876">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="a88e4-877">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-877">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="a88e4-878">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-878">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="a88e4-879">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="a88e4-879">Virtual Directories</span></span>

<span data-ttu-id="a88e4-880">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-880">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="a88e4-881">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-881">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="a88e4-882">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="a88e4-882">Sub-applications</span></span>

<span data-ttu-id="a88e4-883">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-883">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="a88e4-884">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-884">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="a88e4-885">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-885">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="a88e4-886">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-886">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="a88e4-887">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-887">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="a88e4-888">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-888">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="a88e4-889">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-889">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="a88e4-890">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-890">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="a88e4-891">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-891">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="a88e4-892">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-892">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="a88e4-893">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-893">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="a88e4-894">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-894">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="a88e4-895">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-895">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="a88e4-896">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-896">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="a88e4-897">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-897">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="a88e4-898">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-898">Select **OK**.</span></span>

<span data-ttu-id="a88e4-899">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-899">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="a88e4-900">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-900">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="a88e4-901">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-901">Configuration of IIS with web.config</span></span>

<span data-ttu-id="a88e4-902">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-902">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="a88e4-903">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-903">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="a88e4-904">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-904">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="a88e4-905">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-905">For more information, see the following topics:</span></span>

* [<span data-ttu-id="a88e4-906">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="a88e4-906">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="a88e4-907">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-907">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="a88e4-908">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="a88e4-908">Configuration sections of web.config</span></span>

<span data-ttu-id="a88e4-909">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-909">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="a88e4-910">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-910">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="a88e4-911">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-911">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="a88e4-912">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="a88e4-912">Application Pools</span></span>

<span data-ttu-id="a88e4-913">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-913">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="a88e4-914">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-914">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="a88e4-915">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-915">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="a88e4-916">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-916">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="a88e4-917">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-917">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="a88e4-918">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-918">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="a88e4-919">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="a88e4-919">Application Pool Identity</span></span>

<span data-ttu-id="a88e4-920">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-920">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="a88e4-921">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-921">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="a88e4-922">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-922">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="a88e4-924">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-924">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="a88e4-925">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-925">Resources can be secured using this identity.</span></span> <span data-ttu-id="a88e4-926">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-926">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="a88e4-927">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-927">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="a88e4-928">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-928">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="a88e4-929">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-929">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="a88e4-930">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-930">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="a88e4-931">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-931">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="a88e4-932">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-932">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="a88e4-933">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-933">Select the **Check Names** button.</span></span> <span data-ttu-id="a88e4-934">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-934">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="a88e4-935">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-935">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="a88e4-936">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-936">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="a88e4-937">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-937">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="a88e4-939">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-939">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="a88e4-941">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-941">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="a88e4-942">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-942">Provide additional permissions as needed.</span></span>

<span data-ttu-id="a88e4-943">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-943">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="a88e4-944">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-944">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="a88e4-945">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-945">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a88e4-946">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="a88e4-946">HTTP/2 support</span></span>

<span data-ttu-id="a88e4-947">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-947">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="a88e4-948">In-Process</span><span class="sxs-lookup"><span data-stu-id="a88e4-948">In-process</span></span>
  * <span data-ttu-id="a88e4-949">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-949">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a88e4-950">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="a88e4-950">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="a88e4-951">Out of Process</span><span class="sxs-lookup"><span data-stu-id="a88e4-951">Out-of-process</span></span>
  * <span data-ttu-id="a88e4-952">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-952">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="a88e4-953">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-953">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="a88e4-954">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="a88e4-954">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a88e4-955">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-955">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="a88e4-956">Out of Process 배포에서 HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-956">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="a88e4-957">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-957">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="a88e4-958">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-958">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a88e4-959">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-959">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="a88e4-960">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-960">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="a88e4-961">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="a88e4-961">CORS preflight requests</span></span>

<span data-ttu-id="a88e4-962">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-962">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="a88e4-963">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-963">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="a88e4-964">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-964">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="a88e4-965">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="a88e4-965">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="a88e4-966">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="a88e4-966">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="a88e4-967">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](#in-process-hosting-model) 또는 [Out-of-process](#out-of-process-hosting-model)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-967">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="a88e4-968">[유휴 시간 제한](#idle-timeout): 앱 호스팅 [In-process](#in-process-hosting-model)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-968">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="a88e4-969">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="a88e4-969">Application Initialization Module</span></span>

<span data-ttu-id="a88e4-970">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="a88e4-970">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="a88e4-971">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-971">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="a88e4-972">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-972">The request triggers the app to start.</span></span> <span data-ttu-id="a88e4-973">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="a88e4-973">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="a88e4-974">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-974">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="a88e4-975">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="a88e4-975">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="a88e4-976">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-976">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="a88e4-977">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능**을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-977">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="a88e4-978">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-978">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="a88e4-979">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-979">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="a88e4-980">**역할 및 기능 추가 마법사**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-980">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="a88e4-981">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-981">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="a88e4-982">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-982">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="a88e4-983">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-983">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="a88e4-984">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="a88e4-984">Using IIS Manager:</span></span>

  1. <span data-ttu-id="a88e4-985">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-985">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="a88e4-986">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-986">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="a88e4-987">기본 **시작 모드**는 **OnDemand**입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-987">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="a88e4-988">**시작 모드**를 **AlwaysRunning**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-988">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="a88e4-989">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-989">Select **OK**.</span></span>
  1. <span data-ttu-id="a88e4-990">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-990">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="a88e4-991">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-991">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="a88e4-992">기본 **미리 로드 사용** 설정은 **False**입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-992">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="a88e4-993">**미리 로드 사용**을 **True**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-993">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="a88e4-994">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-994">Select **OK**.</span></span>

* <span data-ttu-id="a88e4-995">*web.config*를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 *web.config* 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-995">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="a88e4-996">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="a88e4-996">Idle Timeout</span></span>

<span data-ttu-id="a88e4-997">‘앱 호스팅 In Process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="a88e4-997">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="a88e4-998">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-998">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="a88e4-999">**연결** 패널에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-999">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="a88e4-1000">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1000">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="a88e4-1001">기본 **유휴 시간 제한(분)** 은 **20**분입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1001">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="a88e4-1002">**유휴 시간 제한(분)** 을 **0**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1002">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="a88e4-1003">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1003">Select **OK**.</span></span>
1. <span data-ttu-id="a88e4-1004">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1004">Recycle the worker process.</span></span>

<span data-ttu-id="a88e4-1005">앱 호스팅 [Out of Process](#out-of-process-hosting-model)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1005">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="a88e4-1006">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1006">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="a88e4-1007">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1007">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="a88e4-1008">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="a88e4-1008">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="a88e4-1009">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="a88e4-1009">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="a88e4-1010">[애플리케이션 초기화 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="a88e4-1010">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="a88e4-1011">[애플리케이션 풀의 프로세스 모델 설정 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="a88e4-1011">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="a88e4-1012">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="a88e4-1012">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="a88e4-1013">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="a88e4-1013">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="a88e4-1014">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="a88e4-1014">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="a88e4-1015">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-1015">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="a88e4-1016">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a88e4-1016">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="a88e4-1017">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="a88e4-1017">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="a88e4-1018">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="a88e4-1018">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="a88e4-1019">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a88e4-1019">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a88e4-1020">IIS 서버에 ASP.NET Core 앱을 게시하는 방법에 대한 자습서 경험은 <xref:tutorials/publish-to-iis>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1020">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="a88e4-1021">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-1021">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="a88e4-1022">지원되는 운영 체제</span><span class="sxs-lookup"><span data-stu-id="a88e4-1022">Supported operating systems</span></span>

<span data-ttu-id="a88e4-1023">지원되는 운영 체제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1023">The following operating systems are supported:</span></span>

* <span data-ttu-id="a88e4-1024">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-1024">Windows 7 or later</span></span>
* <span data-ttu-id="a88e4-1025">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-1025">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a88e4-1026">[HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)는 IIS를 사용하는 역방향 프록시 구성에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1026">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="a88e4-1027">[Kestrel 서버](xref:fundamentals/servers/kestrel)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1027">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a88e4-1028">Azure에서 호스트하는 방법에 대한 자세한 내용은 <xref:host-and-deploy/azure-apps/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1028">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="a88e4-1029">문제 해결 지침은 <xref:test/troubleshoot>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1029">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="a88e4-1030">지원되는 플랫폼</span><span class="sxs-lookup"><span data-stu-id="a88e4-1030">Supported platforms</span></span>

<span data-ttu-id="a88e4-1031">32비트(x86) 또는 64비트(x64) 배포용으로 게시된 앱이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1031">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="a88e4-1032">앱이 다음과 같은 경우가 아닌 한 32비트(x86) .NET Core SDK를 포함한 32비트 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1032">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="a88e4-1033">64비트 앱에 사용할 수 있는 더 큰 가상 메모리 주소 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1033">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="a88e4-1034">더 큰 IIS 스택 크기가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1034">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="a88e4-1035">64비트 네이티브 종속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1035">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="a88e4-1036">64비트 앱을 게시하려면 64비트(x64) .NET Core SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1036">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="a88e4-1037">64비트 런타임이 호스트 시스템에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1037">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="a88e4-1038">ASP.NET Core는 기본 플랫폼 간 HTTP 서버인 [Kestrel 서버](xref:fundamentals/servers/kestrel)와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1038">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="a88e4-1039">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)를 사용하면 앱이 [Kestrel 서버](xref:fundamentals/servers/index#kestrel)를 사용하여 IIS 작업자 프로세스와 다른 별도의 프로세스에서(*Out-of-Process*) 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1039">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="a88e4-1040">ASP.NET Core 앱은 IIS 작업자 프로세스와 별도의 프로세스에서 실행되므로 이 모듈은 프로세스 관리를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1040">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="a88e4-1041">모듈은 첫 번째 요청이 들어올 때 ASP.NET Core 앱용 프로세스를 시작하고 종료되거나 충돌이 발생하면 앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1041">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="a88e4-1042">이는 [Windows Process Activation Service(WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)로 관리되는 In-Process로 실행되는 앱에서 볼 수 있는 동작과 기본적으로 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1042">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a88e4-1043">다음 다이어그램은 IIS, ASP.NET Core 모듈 및 Out-of-Process에 호스트된 앱 간의 관계를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1043">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 모듈](index/_static/ancm-outofprocess.png)

<span data-ttu-id="a88e4-1045">요청은 웹에서 커널 모드 HTTP.sys 드라이버로 도착합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1045">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="a88e4-1046">드라이버는 웹 사이트의 구성된 포트(일반적으로 80(HTTP) 또는 443(HTTPS))에서 IIS로 요청을 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1046">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="a88e4-1047">모듈은 포트 80 또는 443이 아닌 앱의 임의의 포트에서 Kestrel로 요청을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1047">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="a88e4-1048">모듈은 시작 시 환경 변수를 통해 포트를 지정하고 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)는 `http://localhost:{port}`에서 수신 대기하도록 서버를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1048">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="a88e4-1049">추가 검사가 수행되고 모듈에서 시작되지 않은 요청은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1049">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="a88e4-1050">모듈은 HTTPS 전달을 지원하지 않으므로 HTTPS를 통해 IIS에서 수신된 경우에도 HTTP를 통해 요청이 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1050">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="a88e4-1051">Kestrel이 모듈에서 요청을 선택한 후, 요청은 ASP.NET Core 미들웨어 파이프라인으로 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1051">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a88e4-1052">미들웨어 파이프라인은 요청을 처리하고 앱의 논리에 `HttpContext` 인스턴스로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1052">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a88e4-1053">IIS 통합에 의해 추가된 미들웨어는 체계, 원격 IP 및 경로 기준을 Kestrel에 요청을 전달하기 위한 계정으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1053">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="a88e4-1054">앱의 응답은 IIS로 다시 전달되고, 요청을 시작한 HTTP 클라이언트에 다시 푸시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1054">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="a88e4-1055">`CreateDefaultBuilder`는 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성하고 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)의 기본 경로 및 포트를 구성하여 IIS 통합을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1055">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="a88e4-1056">ASP.NET Core 모듈은 동적 포트를 생성하여 백 엔드 프로세스에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1056">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="a88e4-1057">`CreateDefaultBuilder`는 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1057">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="a88e4-1058">`UseIISIntegration`은 localhost IP 주소(`127.0.0.1`)의 동적 포트에서 수신 대기하도록 Kestrel을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1058">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="a88e4-1059">동적 포트가 1234인 경우 Kestrel은 `127.0.0.1:1234`에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1059">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="a88e4-1060">이 구성은 다음에서 제공된 다른 URL 구성을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1060">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="a88e4-1061">Kestrel의 수신 대기 API</span><span class="sxs-lookup"><span data-stu-id="a88e4-1061">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="a88e4-1062">[구성](xref:fundamentals/configuration/index)(또는 [명령줄 --urls 옵션](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="a88e4-1062">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="a88e4-1063">모듈을 사용하는 경우 `UseUrls` 호출 또는 Kestrel의 `Listen` API가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1063">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="a88e4-1064">`UseUrls` 또는 `Listen`을 호출하는 경우 Kestrel은 IIS 없이 앱을 실행할 때만 지정된 포트에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1064">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="a88e4-1065">ASP.NET Core 모듈 구성 지침은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1065">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="a88e4-1066">호스팅에 대한 자세한 내용은 [ASP.NET Core의 호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1066">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="a88e4-1067">애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-1067">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="a88e4-1068">IISIntegration 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="a88e4-1068">Enable the IISIntegration components</span></span>

<span data-ttu-id="a88e4-1069">`CreateWebHostBuilder`(*Program.cs*)에서 호스트를 빌드하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출하여 IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1069">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="a88e4-1070">`CreateDefaultBuilder`에 대한 자세한 내용은 <xref:fundamentals/host/web-host#set-up-a-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1070">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="a88e4-1071">IIS 옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-1071">IIS options</span></span>

| <span data-ttu-id="a88e4-1072">옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-1072">Option</span></span>                         | <span data-ttu-id="a88e4-1073">기본값</span><span class="sxs-lookup"><span data-stu-id="a88e4-1073">Default</span></span> | <span data-ttu-id="a88e4-1074">설정</span><span class="sxs-lookup"><span data-stu-id="a88e4-1074">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a88e4-1075">`true`인 경우 IIS 서버는 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1075">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a88e4-1076">`false`인 경우 서버는 `HttpContext.User`에 대한 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1076">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a88e4-1077">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1077">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a88e4-1078">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1078">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a88e4-1079">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1079">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="a88e4-1080">IIS 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Builder.IISOptions>에 대한 서비스 구성을 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1080">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="a88e4-1081">다음 예에서는 앱이 `HttpContext.Connection.ClientCertificate`를 채우는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1081">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="a88e4-1082">옵션</span><span class="sxs-lookup"><span data-stu-id="a88e4-1082">Option</span></span>                         | <span data-ttu-id="a88e4-1083">기본값</span><span class="sxs-lookup"><span data-stu-id="a88e4-1083">Default</span></span> | <span data-ttu-id="a88e4-1084">설정</span><span class="sxs-lookup"><span data-stu-id="a88e4-1084">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="a88e4-1085">`true`이면 [IIS 통합 미들웨어](#enable-the-iisintegration-components)가 [Windows 인증](xref:security/authentication/windowsauth)에 의해 인증된 `HttpContext.User`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1085">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a88e4-1086">`false`이면 미들웨어가 `HttpContext.User`에게 ID만 제공하고, `AuthenticationScheme`에서 명시적으로 요청될 때 챌린지에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1086">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a88e4-1087">IIS에서 Windows 인증은 `AutomaticAuthentication`이 작동하기 위해 사용하도록 설정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1087">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a88e4-1088">자세한 내용은 [Windows 인증](xref:security/authentication/windowsauth) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1088">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="a88e4-1089">로그인 페이지에서 사용자에게 나타나는 표시 이름을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1089">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="a88e4-1090">`true`면서 `MS-ASPNETCORE-CLIENTCERT` 요청 헤더가 있는 경우 `HttpContext.Connection.ClientCertificate`가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1090">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a88e4-1091">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="a88e4-1091">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a88e4-1092">전달된 헤더 미들웨어를 구성하는 [IIS 통합 미들웨어](#enable-the-iisintegration-components) 및 ASP.NET Core 모듈은 체계(HTTP/HTTPS) 및 요청이 시작된 원격 IP 주소를 전달하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1092">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="a88e4-1093">추가 프록시 서버 및 부하 분산 장치 외에도 호스팅되는 앱에 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1093">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="a88e4-1094">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1094">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="a88e4-1095">web.config 파일</span><span class="sxs-lookup"><span data-stu-id="a88e4-1095">web.config file</span></span>

<span data-ttu-id="a88e4-1096">*web.config* 파일은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1096">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a88e4-1097">*web.config* 파일을 만들고, 변하고, 게시하는 작업은 프로젝트를 게시할 때 MSBuild 대상(`_TransformWebConfig`)에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1097">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="a88e4-1098">이 대상은 웹 SDK 대상(`Microsoft.NET.Sdk.Web`)에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1098">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="a88e4-1099">SDK는 프로젝트 파일을 기반으로 해서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1099">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a88e4-1100">프로젝트에 *web.config* 파일이 없는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *‘인수’* 로 파일이 생성되어 [게시된 출력](xref:host-and-deploy/directory-structure)으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1100">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="a88e4-1101">프로젝트에 *web.config* 파일이 있는 경우, ASP.NET Core 모듈을 구성하기 위해 올바른 *processPath* 및 *인수*로 파일이 변환되어 게시된 출력으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1101">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="a88e4-1102">변환은 이 파일의 IIS 구성 설정을 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1102">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="a88e4-1103">*web.config* 파일은 활성 IIS 모듈을 제어하는 추가 IIS 구성 설정을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1103">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="a88e4-1104">ASP.NET Core 앱을 사용하여 요청을 처리할 수 있는 IIS 모듈에 대한 자세한 내용은 [IIS 모듈](xref:host-and-deploy/iis/modules) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1104">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="a88e4-1105">웹 SDK가 *web.config* 파일을 변환하지 못하게 하려면 프로젝트 파일의 **\<IsTransformWebConfigDisabled>** 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1105">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="a88e4-1106">웹 SDK가 파일을 변환하지 않도록 설정하는 경우 개발자가 *processPath* 및 *인수*를 수동으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1106">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="a88e4-1107">자세한 내용은 <xref:host-and-deploy/aspnet-core-module>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1107">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="a88e4-1108">web.config 파일 위치</span><span class="sxs-lookup"><span data-stu-id="a88e4-1108">web.config file location</span></span>

<span data-ttu-id="a88e4-1109">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 올바르게 설정하려면 배포된 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로(일반적으로 앱 기본 경로)에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1109">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="a88e4-1110">IIS에 제공되는 웹 사이트 실제 경로와 동일한 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1110">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="a88e4-1111">웹 배포를 사용하여 여러 앱을 게시하도록 설정하려면 앱의 루트에 *web.config* 파일이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1111">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="a88e4-1112">중요한 파일은 *\<assembly>assembly>.runtimeconfig.json*, *\<assembly>assembly>.xml*(XML 문서 주석) 및 *\<assembly>assembly>.deps.json*과 같은 앱의 실제 경로에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1112">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="a88e4-1113">*web.config* 파일이 있고 사이트가 정상적으로 시작되면 IIS는 요청되어도 이러한 중요한 파일을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1113">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="a88e4-1114">*web.config* 파일이 없거나, 이름이 잘못 지정되었거나, 정상적으로 시작되도록 사이트를 구성할 수 없는 경우 IIS에서 중요한 파일을 공개적으로 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1114">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="a88e4-1115">***web.config* 파일이 항상 배포에 있어야 하며, 올바르게 이름이 지정되고, 정상적으로 시작되도록 사이트를 구성할 수 있어야 합니다. 프로덕션 배포에서 *web.config* 파일을 제거하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1115">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="a88e4-1116">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="a88e4-1116">Transform web.config</span></span>

<span data-ttu-id="a88e4-1117">게시할 때 *web.config*를 변환해야 하는 경우(예: 구성, 프로필 또는 환경을 기반으로 환경 변수 설정) <xref:host-and-deploy/iis/transform-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1117">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="a88e4-1118">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-1118">IIS configuration</span></span>

<span data-ttu-id="a88e4-1119">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1119">**Windows Server operating systems**</span></span>

<span data-ttu-id="a88e4-1120">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1120">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="a88e4-1121">**관리** 메뉴 또는 **서버 관리자**의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1121">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="a88e4-1122">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1122">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="a88e4-1124">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1124">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="a88e4-1125">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1125">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="a88e4-1127">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1127">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a88e4-1128">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1128">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="a88e4-1129">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1129">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a88e4-1130">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1130">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a88e4-1131">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1131">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a88e4-1132">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1132">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a88e4-1133">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1133">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="a88e4-1134">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1134">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a88e4-1135">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1135">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a88e4-1136">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1136">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="a88e4-1137">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1137">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="a88e4-1138">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1138">**Windows desktop operating systems**</span></span>

<span data-ttu-id="a88e4-1139">**IIS 관리 콘솔** 및 **World Wide Web 서비스**를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1139">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a88e4-1140">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 차례로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1140">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="a88e4-1141">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1141">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="a88e4-1142">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1142">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="a88e4-1143">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1143">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="a88e4-1144">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1144">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="a88e4-1145">**World Wide Web 서비스**의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1145">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="a88e4-1146">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1146">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="a88e4-1147">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1147">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="a88e4-1148">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1148">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="a88e4-1149">자세한 내용은 [Windows 인증 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1149">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="a88e4-1150">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1150">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="a88e4-1151">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1151">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="a88e4-1152">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1152">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="a88e4-1153">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1153">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a88e4-1154">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1154">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="a88e4-1155">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1155">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="a88e4-1157">.NET Core 호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-1157">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="a88e4-1158">호스팅 시스템에 *.NET Core 호스팅 번들*을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1158">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="a88e4-1159">번들은 .NET Core 런타임, .NET Core 라이브러리 및 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1159">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="a88e4-1160">이 모듈을 통해 ASP.NET Core 앱을 IIS 배후에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1160">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a88e4-1161">IIS 이전에 호스팅 번들이 설치된 경우 번들 설치를 복구해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1161">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="a88e4-1162">IIS를 설치한 후 호스팅 번들 설치 프로그램을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1162">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="a88e4-1163">.NET Core의 64비트(x64) 버전을 설치한 후 호스팅 번들이 설치된 경우 SDK가 누락된 것처럼 보일 수 있습니다([ .NET Core SDK가 검색되지 않음](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="a88e4-1163">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="a88e4-1164">이 문제를 해결 하려면 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1164">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="a88e4-1165">다운로드</span><span class="sxs-lookup"><span data-stu-id="a88e4-1165">Download</span></span>

1. <span data-ttu-id="a88e4-1166">[.NET Core 다운로드](https://dotnet.microsoft.com/download/dotnet-core) 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1166">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="a88e4-1167">원하는 .NET Core 버전을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1167">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="a88e4-1168">**앱 실행 - 런타임** 열에서 원하는 .NET Core 런타임 버전의 행을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1168">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="a88e4-1169">**호스팅 번들** 링크를 사용하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1169">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="a88e4-1170">일부 설치 관리자는 EOL(수명 종료)에 도달한 릴리스 버전을 포함하고 Microsoft에서 더 이상 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1170">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="a88e4-1171">자세한 내용은 [지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1171">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="a88e4-1172">호스팅 번들 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-1172">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="a88e4-1173">서버에서 설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1173">Run the installer on the server.</span></span> <span data-ttu-id="a88e4-1174">관리자 명령 셸에서 설치 관리자를 실행할 때 다음 매개 변수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1174">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="a88e4-1175">`OPT_NO_ANCM=1`: ASP.NET Core 모듈 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1175">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="a88e4-1176">`OPT_NO_RUNTIME=1`: .NET Core 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1176">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="a88e4-1177">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1177">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="a88e4-1178">`OPT_NO_SHAREDFX=1`: ASP.NET 공유 프레임워크(ASP.NET 런타임) 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1178">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="a88e4-1179">서버에서 [SCD(자체 포함 배포)](/dotnet/core/deploying/#self-contained-deployments-scd)만 호스트하는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1179">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="a88e4-1180">`OPT_NO_X86=1`: x86 런타임 설치를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1180">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="a88e4-1181">32비트 앱을 호스팅하지 않음을 아는 경우 이 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1181">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="a88e4-1182">향후 32비트와 64비트 앱을 모두 호스트할 수 있는 기회가 있는 경우 이 매개 변수를 사용하지 않고 두 런타임을 모두 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1182">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="a88e4-1183">`OPT_NO_SHARED_CONFIG_CHECK=1`: 공유 구성(*applicationHost.config*)이 IIS 설치와 동일한 머신에 있는 경우 IIS 공유 구성 사용 선택을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1183">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="a88e4-1184">*ASP.NET Core 2.2 이상 호스팅 번들러 설치 관리자에 대해서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="a88e4-1184">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="a88e4-1185">자세한 내용은 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1185">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="a88e4-1186">시스템을 다시 시작하거나 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1186">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="a88e4-1187">IIS를 다시 시작하면 설치 관리자에서 변경된 시스템 PATH(환경 변수)의 내용이 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1187">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="a88e4-1188">호스팅 번들을 설치할 때 IIS에서 개별 사이트를 수동으로 중지하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1188">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="a88e4-1189">IIS가 다시 시작되면 호스트된 앱(IIS 사이트)이 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1189">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="a88e4-1190">앱은 [애플리케이션 초기화 모듈](#application-initialization-module-and-idle-timeout)을 포함하여 첫 번째 요청을 받으면 다시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1190">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="a88e4-1191">ASP.NET Core에서는 공유 프레임워크 패키지의 패치 릴리스에 대한 롤포워드 동작을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1191">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="a88e4-1192">IIS에서 호스트된 앱이 IIS를 통해 다시 시작되는 경우 앱은 첫 번째 요청을 받을 때 참조된 패키지의 최신 패치 릴리스와 함께 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1192">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="a88e4-1193">IIS를 다시 시작하지 않으면 작업자 프로세스가 재생되고 첫 번째 요청을 받을 때 앱이 다시 시작되고 롤포워드 동작을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1193">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="a88e4-1194">IIS 공유 구성에 대한 자세한 내용은 [IIS 공유 구성을 사용하는 ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1194">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="a88e4-1195">Visual Studio을 사용하여 게시할 때 웹 배포 설치</span><span class="sxs-lookup"><span data-stu-id="a88e4-1195">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="a88e4-1196">[웹 배포](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)를 사용하여 앱을 서버에 배포하는 경우 최신 버전의 웹 배포를 서버에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1196">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="a88e4-1197">웹 배포를 설치하려면 [WebPI(웹 플랫폼 설치 관리자)](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하거나 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=43717)에서 직접 설치 관리자를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1197">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="a88e4-1198">WebPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1198">The preferred method is to use WebPI.</span></span> <span data-ttu-id="a88e4-1199">WebPI는 호스팅 공급자에 대한 독립 실행형 설치 및 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1199">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="a88e4-1200">IIS 사이트 만들기</span><span class="sxs-lookup"><span data-stu-id="a88e4-1200">Create the IIS site</span></span>

1. <span data-ttu-id="a88e4-1201">호스팅 시스템에서 앱의 게시된 폴더 및 파일을 포함할 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1201">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="a88e4-1202">다음 단계에서는 폴더의 경로가 앱의 실제 경로로 IIS에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1202">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="a88e4-1203">앱의 배포 폴더 및 파일 레이아웃에 대한 자세한 내용은 <xref:host-and-deploy/directory-structure>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1203">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="a88e4-1204">IIS 관리자의 **연결** 패널에서 서버 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1204">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="a88e4-1205">**사이트** 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1205">Right-click the **Sites** folder.</span></span> <span data-ttu-id="a88e4-1206">상황에 맞는 메뉴에서 **웹 사이트 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1206">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="a88e4-1207">**사이트 이름**을 제공하고 **실제 경로**를 앱의 배포 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1207">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="a88e4-1208">**바인딩** 구성을 제공하고 **확인**을 선택하여 웹 사이트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1208">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[웹 사이트 추가] 단계에서 사이트 이름, 실제 경로 및 호스트 이름을 제공합니다.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="a88e4-1210">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1210">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a88e4-1211">최상위 와일드카드 바인딩은 보안 취약점에 앱을 노출시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1211">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="a88e4-1212">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1212">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a88e4-1213">와일드카드보다는 명시적 호스트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1213">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="a88e4-1214">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)에는 이러한 보안 위험이 없습니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="a88e4-1214">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a88e4-1215">자세한 내용은 [rfc7230 섹션-5.4](https://tools.ietf.org/html/rfc7230#section-5.4)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1215">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="a88e4-1216">서버 노드에서 **애플리케이션 풀**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1216">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="a88e4-1217">사이트의 앱 풀을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **기본 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1217">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="a88e4-1218">**애플리케이션 풀 편집** 창에서 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1218">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR 버전에 대해 관리 코드 없음 설정](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="a88e4-1220">ASP.NET Core는 별도의 프로세스에서 실행되며 런타임을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1220">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="a88e4-1221">ASP.NET Core는 데스크톱 CLR(.NET CLR) 로드에 관계없이 실행됩니다. .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1221">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="a88e4-1222">**.NET CLR 버전**을 **관리 코드 없음**으로 설정하는 것은 선택 사항이지만 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1222">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="a88e4-1223">*ASP.NET Core 2.2 이상*: [In-process 호스팅 모델](#in-process-hosting-model)을 사용하는 64비트(x64) [자체 포함된 배포](/dotnet/core/deploying/#self-contained-deployments-scd)의 경우 32비트(x86) 프로세스에 대해 앱 풀을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1223">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="a88e4-1224">IIS 관리자의 **애플리케이션 풀**에 있는 **작업** 사이드바에서 **애플리케이션 풀 기본값 설정** 또는 **고급 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1224">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="a88e4-1225">**32비트 애플리케이션 사용**을 찾아 값을 `False`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1225">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="a88e4-1226">이 설정은 [독립 프로세스 호스팅](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)에 배포된 앱에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1226">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="a88e4-1227">프로세스 모델 ID에 적절한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1227">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="a88e4-1228">애플리케이션 풀의 기본 ID(**프로세스 모델** >  **Identity** )가 **ApplicationPoolIdentity** 에서 다른 ID로 변경되면, 새 ID에 앱의 폴더, 데이터베이스 및 기타 필요한 리소스에 액세스하는 데 필요한 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1228">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="a88e4-1229">예를 들어 앱 풀에는 앱이 파일을 읽고 쓰는 폴더에 대한 읽기 및 쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1229">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="a88e4-1230">**Windows 인증 구성(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1230">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="a88e4-1231">자세한 내용은 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1231">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="a88e4-1232">앱 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-1232">Deploy the app</span></span>

<span data-ttu-id="a88e4-1233">[IIS 사이트 만들기](#create-the-iis-site) 섹션에서 설정한 IIS **실제 경로** 폴더에 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1233">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="a88e4-1234">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 배포를 위해 권장되는 메커니즘이지만, 프로젝트의 *게시* 폴더에서 호스팅 시스템의 배포 폴더로 앱을 이동하기 위한 옵션에는 여러 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1234">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="a88e4-1235">Visual Studio를 사용한 웹 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-1235">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="a88e4-1236">웹 배포에서 사용할 게시 프로필을 만드는 방법은 [ASP.NET Core 앱 배포용 Visual Studio 게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1236">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="a88e4-1237">호스팅 공급자가 게시 프로필을 제공하거나 게시 프로필을 만들 수 있도록 지원하는 경우 해당 프로필을 다운로드하고 Visual Studio **게시** 대화 상자를 사용하여 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1237">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![게시 대화 상자 페이지](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="a88e4-1239">Visual Studio 외부에서 웹 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-1239">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="a88e4-1240">[웹 배포](/iis/publish/using-web-deploy/introduction-to-web-deploy)는 Visual Studio 외부에서 명령줄을 통해 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1240">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="a88e4-1241">자세한 내용은 [웹 배포 도구](/iis/publish/using-web-deploy/use-the-web-deployment-tool)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1241">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="a88e4-1242">웹 배포에 대한 대안</span><span class="sxs-lookup"><span data-stu-id="a88e4-1242">Alternatives to Web Deploy</span></span>

<span data-ttu-id="a88e4-1243">수동 복사, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), [PowerShell](/powershell/) 등의 여러 방법 중 하나를 사용하여 앱을 호스팅 시스템으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1243">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="a88e4-1244">IIS에 ASP.NET Core 배포에 대한 자세한 내용은 [IIS 관리자를 위한 배포 리소스](#deployment-resources-for-iis-administrators) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1244">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="a88e4-1245">웹 사이트 찾아보기</span><span class="sxs-lookup"><span data-stu-id="a88e4-1245">Browse the website</span></span>

<span data-ttu-id="a88e4-1246">앱이 호스팅 시스템에 배포된 후 앱의 공용 엔드포인트 중 하나에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1246">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="a88e4-1247">다음 예제에서는 **포트** `80`에서 사이트가 `www.mysite.com`의 IIS **호스트 이름**에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1247">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="a88e4-1248">`http://www.mysite.com`에 대해 요청이 이루어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1248">A request is made to `http://www.mysite.com`:</span></span>

![IIS 시작 페이지가 로드된 Microsoft Edge 브라우저](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="a88e4-1250">배포 파일이 잠겨 있음</span><span class="sxs-lookup"><span data-stu-id="a88e4-1250">Locked deployment files</span></span>

<span data-ttu-id="a88e4-1251">앱이 실행 중이면 배포 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1251">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="a88e4-1252">잠긴 파일은 배포 중에 덮어쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1252">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="a88e4-1253">배포에서 잠긴 파일을 해제하려면 다음 방법 중 **하나**를 사용하여 앱 풀을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1253">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="a88e4-1254">웹 배포를 사용하고 프로젝트 파일에서 `Microsoft.NET.Sdk.Web`을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1254">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="a88e4-1255">*app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1255">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="a88e4-1256">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1256">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="a88e4-1257">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1257">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="a88e4-1258">서버의 IIS 관리자에서 앱 풀을 수동으로 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1258">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="a88e4-1259">PowerShell을 사용하여 *app_offline.htm*을 삭제합니다(PowerShell 5 이상 필요).</span><span class="sxs-lookup"><span data-stu-id="a88e4-1259">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="a88e4-1260">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="a88e4-1260">Data protection</span></span>

<span data-ttu-id="a88e4-1261">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1261">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="a88e4-1262">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1262">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="a88e4-1263">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1263">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="a88e4-1264">키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1264">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="a88e4-1265">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1265">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="a88e4-1266">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1266">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="a88e4-1267">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1267">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="a88e4-1268">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1268">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="a88e4-1269">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1269">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="a88e4-1270">**데이터 보호 레지스트리키 만들기**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1270">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="a88e4-1271">ASP.NET 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1271">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="a88e4-1272">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1272">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="a88e4-1273">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1273">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="a88e4-1274">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1274">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="a88e4-1275">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1275">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="a88e4-1276">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1276">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="a88e4-1277">기본적으로 데이터 보호 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1277">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="a88e4-1278">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1278">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="a88e4-1279">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1279">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="a88e4-1280">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다. 즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1280">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="a88e4-1281">자세한 내용은 [ASP.NET Core 데이터 보호 구성](xref:security/data-protection/configuration/overview)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1281">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="a88e4-1282">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1282">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="a88e4-1283">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1283">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="a88e4-1284">**사용자 프로필**을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1284">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="a88e4-1285">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1285">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="a88e4-1286">키는 *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1286">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="a88e4-1287">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1287">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="a88e4-1288">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1288">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="a88e4-1289">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1289">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="a88e4-1290">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1290">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="a88e4-1291">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1291">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="a88e4-1292">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1292">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="a88e4-1293">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1293">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="a88e4-1294">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1294">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="a88e4-1295">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1295">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="a88e4-1296">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1296">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="a88e4-1297">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1297">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="a88e4-1298">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1298">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="a88e4-1299">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1299">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="a88e4-1300">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1300">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="a88e4-1301">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1301">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="a88e4-1302">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1302">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="a88e4-1303">**데이터 보호에 대한 컴퓨터 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="a88e4-1303">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="a88e4-1304">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [컴퓨터 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1304">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="a88e4-1305">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1305">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="a88e4-1306">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="a88e4-1306">Virtual Directories</span></span>

<span data-ttu-id="a88e4-1307">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1307">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="a88e4-1308">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1308">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="a88e4-1309">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="a88e4-1309">Sub-applications</span></span>

<span data-ttu-id="a88e4-1310">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1310">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="a88e4-1311">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1311">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="a88e4-1312">하위 앱에는 ASP.NET Core 모듈이 처리기로 포함되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1312">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="a88e4-1313">하위 앱의 *web.config* 파일에 모듈이 처리기로 추가되면, 하위 앱을 찾으려고 할 때 잘못된 구성 파일을 참조하는 *500.19 내부 서버 오류*가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1313">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="a88e4-1314">다음 예제에서는 ASP.NET Core 하위 앱에 대해 게시된 *web.config* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1314">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="a88e4-1315">ASP.NET Core 앱 아래에 비ASP .NET Core 하위 앱을 호스팅하는 경우, 하위 앱의 *web.config* 파일에서 상속된 처리기를 명시적으로 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1315">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="a88e4-1316">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1316">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="a88e4-1317">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1317">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="a88e4-1318">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1318">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="a88e4-1319">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1319">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="a88e4-1320">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1320">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="a88e4-1321">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1321">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="a88e4-1322">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1322">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="a88e4-1323">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1323">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="a88e4-1324">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1324">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="a88e4-1325">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전**을 **관리 코드 없음**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1325">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="a88e4-1326">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1326">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="a88e4-1327">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1327">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="a88e4-1328">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀**에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1328">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="a88e4-1329">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1329">Select **OK**.</span></span>

<span data-ttu-id="a88e4-1330">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1330">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="a88e4-1331">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1331">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="a88e4-1332">web.config를 사용하여 IIS 구성</span><span class="sxs-lookup"><span data-stu-id="a88e4-1332">Configuration of IIS with web.config</span></span>

<span data-ttu-id="a88e4-1333">IIS 구성은 ASP.NET Core 모듈을 사용하여 ASP.NET Core 앱에 대해 작동하는 IIS 시나리오에서 *web.config*에 포함된 `<system.webServer>` 섹션의 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1333">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="a88e4-1334">예를 들어, IIS 구성은 동적 압축에 대해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1334">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="a88e4-1335">IIS가 동적 압축을 사용하도록 서버 수준에서 구성된 경우, 앱의 *web.config* 파일에 포함된 `<urlCompression>` 요소가 ASP.NET Core 앱에 대해 이를 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1335">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="a88e4-1336">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1336">For more information, see the following topics:</span></span>

* [<span data-ttu-id="a88e4-1337">\<system.webServer>의 구성 참조</span><span class="sxs-lookup"><span data-stu-id="a88e4-1337">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="a88e4-1338">격리된 앱 풀에서 실행되는 개별 앱에 대해 환경 변수를 설정하려면(IIS 10.0 이상에서 지원됨), IIS 참조 문서에서 [환경 변수 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 ‘AppCmd.exe 명령’ 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1338">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="a88e4-1339">web.config 구성 섹션</span><span class="sxs-lookup"><span data-stu-id="a88e4-1339">Configuration sections of web.config</span></span>

<span data-ttu-id="a88e4-1340">*web.config*에 있는 ASP.NET 4.x 앱의 구성 섹션은 ASP.NET Core 앱의 구성에 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1340">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="a88e4-1341">ASP.NET Core 앱은 다른 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1341">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="a88e4-1342">자세한 내용은 [구성](xref:fundamentals/configuration/index)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1342">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="a88e4-1343">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="a88e4-1343">Application Pools</span></span>

<span data-ttu-id="a88e4-1344">서버에서 여러 웹 사이트를 호스트하는 경우 각 앱을 해당 앱 풀에서 실행하여 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1344">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="a88e4-1345">이 구성은 IIS **웹 사이트 추가** 대화 상자의 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1345">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="a88e4-1346">**사이트 이름**을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1346">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="a88e4-1347">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1347">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="a88e4-1348">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="a88e4-1348">Application Pool Identity</span></span>

<span data-ttu-id="a88e4-1349">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1349">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="a88e4-1350">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1350">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="a88e4-1351">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 **ApplicationPoolIdentity** 를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1351">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="a88e4-1353">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1353">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="a88e4-1354">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1354">Resources can be secured using this identity.</span></span> <span data-ttu-id="a88e4-1355">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1355">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="a88e4-1356">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1356">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="a88e4-1357">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1357">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="a88e4-1358">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1358">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="a88e4-1359">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1359">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="a88e4-1360">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1360">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="a88e4-1361">**선택할 개체 이름 입력** 영역에 **IIS AppPool\\<app_pool_name>** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1361">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="a88e4-1362">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1362">Select the **Check Names** button.</span></span> <span data-ttu-id="a88e4-1363">*DefaultAppPool*의 경우 **IIS AppPool\DefaultAppPool**을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1363">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="a88e4-1364">**이름 확인** 단추를 선택하면 개체 이름 영역에 **DefaultAppPool** 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1364">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="a88e4-1365">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1365">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="a88e4-1366">개체 이름을 확인할 때는 **IIS AppPool\\<app_pool_name>** 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1366">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="a88e4-1368">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1368">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="a88e4-1370">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1370">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="a88e4-1371">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1371">Provide additional permissions as needed.</span></span>

<span data-ttu-id="a88e4-1372">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1372">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="a88e4-1373">*DefaultAppPool*을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1373">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="a88e4-1374">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1374">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a88e4-1375">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="a88e4-1375">HTTP/2 support</span></span>

<span data-ttu-id="a88e4-1376">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음 기본 요구 사항을 충족하는 Out of Process 배포에 대해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1376">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="a88e4-1377">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="a88e4-1377">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="a88e4-1378">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1378">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="a88e4-1379">대상 프레임워크: HTTP/2 연결은 IIS에 의해 완전히 처리되므로 Out of Process 배포에는 해당하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1379">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="a88e4-1380">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="a88e4-1380">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a88e4-1381">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1381">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="a88e4-1382">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1382">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a88e4-1383">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1383">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="a88e4-1384">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1384">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="a88e4-1385">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="a88e4-1385">CORS preflight requests</span></span>

<span data-ttu-id="a88e4-1386">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1386">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="a88e4-1387">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1387">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="a88e4-1388">OPTIONS 요청을 전달하도록 *web.config*에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a88e4-1388">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="a88e4-1389">IIS 관리자를 위한 배포 리소스</span><span class="sxs-lookup"><span data-stu-id="a88e4-1389">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="a88e4-1390">IIS 설명서</span><span class="sxs-lookup"><span data-stu-id="a88e4-1390">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="a88e4-1391">IIS에서 IIS 관리자 시작</span><span class="sxs-lookup"><span data-stu-id="a88e4-1391">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="a88e4-1392">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="a88e4-1392">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="a88e4-1393">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a88e4-1393">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="a88e4-1394">공식 Microsoft IIS 사이트</span><span class="sxs-lookup"><span data-stu-id="a88e4-1394">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="a88e4-1395">Windows Server 기술 콘텐츠 라이브러리</span><span class="sxs-lookup"><span data-stu-id="a88e4-1395">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="a88e4-1396">IIS의 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="a88e4-1396">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
