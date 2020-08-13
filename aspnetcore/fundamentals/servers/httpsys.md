---
title: ASP.NET Core에서 HTTP.sys 웹 서버 구현
author: rick-anderson
description: Windows의 ASP.NET Core에 대한 웹 서버인 HTTP.sys에 대해 알아봅니다. HTTP.sys 커널 모드 드라이버를 기반으로 한 HTTP.sys는 IIS 없이 인터넷에 대한 직접 연결에 사용될 수 있는 Kestrel에 대한 대안입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 384b6559b4ee6140da5cf785ffda3978aafbb132
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016767"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="7546b-104">ASP.NET Core에서 HTTP.sys 웹 서버 구현</span><span class="sxs-lookup"><span data-stu-id="7546b-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="7546b-105">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="7546b-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="7546b-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="7546b-107">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7546b-108">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-109">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="7546b-110">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="7546b-111">포트 공유</span><span class="sxs-lookup"><span data-stu-id="7546b-111">Port sharing</span></span>
* <span data-ttu-id="7546b-112">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="7546b-112">HTTPS with SNI</span></span>
* <span data-ttu-id="7546b-113">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="7546b-114">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="7546b-114">Direct file transmission</span></span>
* <span data-ttu-id="7546b-115">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="7546b-115">Response caching</span></span>
* <span data-ttu-id="7546b-116">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="7546b-117">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="7546b-117">Supported Windows versions:</span></span>

* <span data-ttu-id="7546b-118">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-118">Windows 7 or later</span></span>
* <span data-ttu-id="7546b-119">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7546b-120">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7546b-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="7546b-121">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-121">When to use HTTP.sys</span></span>

<span data-ttu-id="7546b-122">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="7546b-123">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="7546b-125">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="7546b-127">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="7546b-128">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7546b-129">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7546b-129">HTTP/2 support</span></span>

<span data-ttu-id="7546b-130">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7546b-131">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="7546b-132">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7546b-133">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7546b-134">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="7546b-135">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7546b-136">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="7546b-137">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="7546b-138">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="7546b-139">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="7546b-140">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="7546b-141">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="7546b-142">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="7546b-143">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="7546b-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="7546b-144">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="7546b-145">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="7546b-146">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="7546b-147">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="7546b-148">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="7546b-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="7546b-149">속성</span><span class="sxs-lookup"><span data-stu-id="7546b-149">Property</span></span> | <span data-ttu-id="7546b-150">설명</span><span class="sxs-lookup"><span data-stu-id="7546b-150">Description</span></span> | <span data-ttu-id="7546b-151">기본값</span><span class="sxs-lookup"><span data-stu-id="7546b-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="7546b-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="7546b-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="7546b-153">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="7546b-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="7546b-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="7546b-155">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="7546b-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="7546b-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="7546b-157">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="7546b-158">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="7546b-159">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="7546b-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="7546b-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="7546b-161">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="7546b-162">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="7546b-163">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="7546b-164">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="7546b-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="7546b-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="7546b-166">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="7546b-167">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-167">Use `-1` for infinite.</span></span> <span data-ttu-id="7546b-168">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="7546b-169">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="7546b-169">(machine-wide</span></span><br><span data-ttu-id="7546b-170">설정)</span><span class="sxs-lookup"><span data-stu-id="7546b-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="7546b-171"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="7546b-172">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="7546b-172">30000000 bytes</span></span><br><span data-ttu-id="7546b-173">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="7546b-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="7546b-174">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="7546b-175">1000</span><span class="sxs-lookup"><span data-stu-id="7546b-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="7546b-176">이는 서버가 요청 큐를 만들고 구성하는 것을 담당하는지, 아니면 기존 큐에 연결해야 할지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="7546b-177">기존 큐에 연결하는 경우 대부분의 기존 구성 옵션이 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="7546b-178">HTTP.sys 요청 큐의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="7546b-179">`null`(익명 큐)</span><span class="sxs-lookup"><span data-stu-id="7546b-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="7546b-180">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="7546b-181">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="7546b-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="7546b-182">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="7546b-183">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="7546b-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="7546b-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="7546b-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="7546b-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="7546b-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="7546b-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="7546b-190"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="7546b-191">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="7546b-192">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="7546b-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="7546b-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="7546b-194">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="7546b-195">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="7546b-196">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="7546b-197">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7546b-198">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="7546b-199">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7546b-200">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="7546b-201">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-202">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="7546b-203">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="7546b-205">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-205">Configure Windows Server</span></span>

1. <span data-ttu-id="7546b-206">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="7546b-207">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-208">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="7546b-209">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-210">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="7546b-211">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="7546b-212">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="7546b-213">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="7546b-214">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="7546b-215">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="7546b-216">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="7546b-217">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="7546b-218">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-218">Install the required .NET Framework.</span></span> <span data-ttu-id="7546b-219">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="7546b-220">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="7546b-221">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="7546b-222">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="7546b-223">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7546b-224">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="7546b-225">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7546b-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="7546b-226">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="7546b-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="7546b-227">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="7546b-228">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="7546b-229">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="7546b-230">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="7546b-231">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="7546b-232">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7546b-233">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="7546b-234">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7546b-235">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="7546b-236">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="7546b-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7546b-237">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="7546b-238">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="7546b-239">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="7546b-240">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="7546b-241">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="7546b-242">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="7546b-243">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="7546b-244">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-245">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="7546b-246">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="7546b-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="7546b-247">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="7546b-248">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="7546b-249">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="7546b-250">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="7546b-251">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="7546b-252">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="7546b-253">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="7546b-254">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-255">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="7546b-256">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="7546b-257">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="7546b-258">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="7546b-259">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="7546b-260">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-260">In Visual Studio:</span></span>
     * <span data-ttu-id="7546b-261">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="7546b-262">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="7546b-263">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="7546b-264">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="7546b-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="7546b-265">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-265">Open the app's project file.</span></span>
     * <span data-ttu-id="7546b-266">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="7546b-267">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7546b-267">In the following example:</span></span>

   * <span data-ttu-id="7546b-268">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="7546b-269">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="7546b-270">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="7546b-271">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="7546b-272">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-272">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="7546b-273">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="7546b-273">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="7546b-274">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="7546b-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="7546b-275">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-275">Run the app.</span></span>

   <span data-ttu-id="7546b-276">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="7546b-277">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="7546b-278">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="7546b-279">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="7546b-280">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-280">A development certificate is used in this example.</span></span> <span data-ttu-id="7546b-281">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7546b-283">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="7546b-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7546b-284">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="7546b-285">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7546b-286">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7546b-286">Additional resources</span></span>

* [<span data-ttu-id="7546b-287">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="7546b-287">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="7546b-288">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="7546b-288">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="7546b-289">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="7546b-289">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="7546b-290">호스트</span><span class="sxs-lookup"><span data-stu-id="7546b-290">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="7546b-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="7546b-292">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-292">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7546b-293">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-293">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-294">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-294">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="7546b-295">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-295">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="7546b-296">포트 공유</span><span class="sxs-lookup"><span data-stu-id="7546b-296">Port sharing</span></span>
* <span data-ttu-id="7546b-297">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="7546b-297">HTTPS with SNI</span></span>
* <span data-ttu-id="7546b-298">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-298">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="7546b-299">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="7546b-299">Direct file transmission</span></span>
* <span data-ttu-id="7546b-300">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="7546b-300">Response caching</span></span>
* <span data-ttu-id="7546b-301">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-301">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="7546b-302">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="7546b-302">Supported Windows versions:</span></span>

* <span data-ttu-id="7546b-303">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-303">Windows 7 or later</span></span>
* <span data-ttu-id="7546b-304">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-304">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7546b-305">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7546b-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="7546b-306">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-306">When to use HTTP.sys</span></span>

<span data-ttu-id="7546b-307">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-307">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="7546b-308">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-308">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="7546b-310">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-310">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="7546b-312">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-312">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="7546b-313">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-313">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7546b-314">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7546b-314">HTTP/2 support</span></span>

<span data-ttu-id="7546b-315">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-315">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7546b-316">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-316">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="7546b-317">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7546b-318">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-318">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7546b-319">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-319">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="7546b-320">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-320">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7546b-321">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-321">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="7546b-322">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-322">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="7546b-323">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-323">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="7546b-324">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-324">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="7546b-325">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-325">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="7546b-326">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-326">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="7546b-327">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-327">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="7546b-328">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="7546b-328">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="7546b-329">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-329">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="7546b-330">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-330">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="7546b-331">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-331">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="7546b-332">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-332">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="7546b-333">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="7546b-333">**HTTP.sys options**</span></span>

| <span data-ttu-id="7546b-334">속성</span><span class="sxs-lookup"><span data-stu-id="7546b-334">Property</span></span> | <span data-ttu-id="7546b-335">설명</span><span class="sxs-lookup"><span data-stu-id="7546b-335">Description</span></span> | <span data-ttu-id="7546b-336">기본값</span><span class="sxs-lookup"><span data-stu-id="7546b-336">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="7546b-337">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="7546b-337">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="7546b-338">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-338">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="7546b-339">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="7546b-339">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="7546b-340">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-340">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="7546b-341">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="7546b-341">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="7546b-342">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-342">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="7546b-343">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-343">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="7546b-344">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-344">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="7546b-345">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="7546b-345">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="7546b-346">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-346">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="7546b-347">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-347">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="7546b-348">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-348">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="7546b-349">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-349">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="7546b-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="7546b-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="7546b-351">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-351">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="7546b-352">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-352">Use `-1` for infinite.</span></span> <span data-ttu-id="7546b-353">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-353">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="7546b-354">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="7546b-354">(machine-wide</span></span><br><span data-ttu-id="7546b-355">설정)</span><span class="sxs-lookup"><span data-stu-id="7546b-355">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="7546b-356"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-356">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="7546b-357">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="7546b-357">30000000 bytes</span></span><br><span data-ttu-id="7546b-358">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="7546b-358">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="7546b-359">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-359">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="7546b-360">1000</span><span class="sxs-lookup"><span data-stu-id="7546b-360">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="7546b-361">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-361">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="7546b-362">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="7546b-362">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="7546b-363">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-363">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="7546b-364">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-364">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="7546b-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="7546b-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="7546b-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="7546b-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="7546b-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="7546b-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="7546b-371"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-371">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="7546b-372">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-372">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="7546b-373">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-373">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="7546b-374">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="7546b-374">**MaxRequestBodySize**</span></span>

<span data-ttu-id="7546b-375">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-375">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="7546b-376">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-376">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="7546b-377">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-377">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="7546b-378">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-378">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7546b-379">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-379">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="7546b-380">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-380">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7546b-381">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-381">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="7546b-382">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-382">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-383">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-383">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="7546b-384">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-384">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="7546b-386">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-386">Configure Windows Server</span></span>

1. <span data-ttu-id="7546b-387">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-387">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="7546b-388">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-388">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-389">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-389">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="7546b-390">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-390">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-391">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-391">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="7546b-392">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-392">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="7546b-393">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-393">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="7546b-394">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-394">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="7546b-395">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-395">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="7546b-396">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-396">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="7546b-397">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-397">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="7546b-398">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-398">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="7546b-399">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-399">Install the required .NET Framework.</span></span> <span data-ttu-id="7546b-400">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-400">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="7546b-401">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-401">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="7546b-402">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-402">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="7546b-403">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-403">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="7546b-404">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-404">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7546b-405">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-405">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="7546b-406">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7546b-406">`urls` command-line argument</span></span>
   * <span data-ttu-id="7546b-407">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="7546b-407">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="7546b-408">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-408">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="7546b-409">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-409">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="7546b-410">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-410">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="7546b-411">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-411">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="7546b-412">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-412">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="7546b-413">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-413">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7546b-414">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-414">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="7546b-415">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-415">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7546b-416">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-416">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="7546b-417">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="7546b-417">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7546b-418">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-418">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="7546b-419">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-419">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="7546b-420">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-420">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="7546b-421">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-421">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="7546b-422">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-422">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="7546b-423">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-423">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="7546b-424">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-424">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="7546b-425">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-425">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-426">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-426">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="7546b-427">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="7546b-427">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="7546b-428">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-428">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="7546b-429">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-429">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="7546b-430">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-430">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="7546b-431">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-431">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="7546b-432">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-432">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="7546b-433">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-433">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="7546b-434">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-434">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="7546b-435">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-435">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-436">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-436">Use a valid IP address.</span></span>
   * <span data-ttu-id="7546b-437">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-437">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="7546b-438">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-438">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="7546b-439">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-439">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="7546b-440">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-440">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="7546b-441">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-441">In Visual Studio:</span></span>
     * <span data-ttu-id="7546b-442">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-442">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="7546b-443">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-443">Select the **Package** tab.</span></span>
     * <span data-ttu-id="7546b-444">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-444">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="7546b-445">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="7546b-445">When not using Visual Studio:</span></span>
     * <span data-ttu-id="7546b-446">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-446">Open the app's project file.</span></span>
     * <span data-ttu-id="7546b-447">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-447">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="7546b-448">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7546b-448">In the following example:</span></span>

   * <span data-ttu-id="7546b-449">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-449">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="7546b-450">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-450">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="7546b-451">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-451">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="7546b-452">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-452">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="7546b-453">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-453">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="7546b-454">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="7546b-454">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="7546b-455">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="7546b-455">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="7546b-456">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-456">Run the app.</span></span>

   <span data-ttu-id="7546b-457">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-457">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="7546b-458">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-458">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="7546b-459">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-459">The app responds at the server's public IP address.</span></span> <span data-ttu-id="7546b-460">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-460">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="7546b-461">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-461">A development certificate is used in this example.</span></span> <span data-ttu-id="7546b-462">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-462">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7546b-464">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="7546b-464">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7546b-465">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-465">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="7546b-466">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-466">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7546b-467">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7546b-467">Additional resources</span></span>

* [<span data-ttu-id="7546b-468">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="7546b-468">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="7546b-469">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="7546b-469">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="7546b-470">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="7546b-470">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="7546b-471">호스트</span><span class="sxs-lookup"><span data-stu-id="7546b-471">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7546b-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="7546b-473">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-473">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7546b-474">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-474">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-475">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-475">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="7546b-476">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-476">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="7546b-477">포트 공유</span><span class="sxs-lookup"><span data-stu-id="7546b-477">Port sharing</span></span>
* <span data-ttu-id="7546b-478">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="7546b-478">HTTPS with SNI</span></span>
* <span data-ttu-id="7546b-479">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-479">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="7546b-480">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="7546b-480">Direct file transmission</span></span>
* <span data-ttu-id="7546b-481">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="7546b-481">Response caching</span></span>
* <span data-ttu-id="7546b-482">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-482">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="7546b-483">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="7546b-483">Supported Windows versions:</span></span>

* <span data-ttu-id="7546b-484">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-484">Windows 7 or later</span></span>
* <span data-ttu-id="7546b-485">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-485">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7546b-486">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7546b-486">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="7546b-487">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-487">When to use HTTP.sys</span></span>

<span data-ttu-id="7546b-488">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-488">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="7546b-489">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-489">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="7546b-491">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-491">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="7546b-493">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-493">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="7546b-494">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-494">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7546b-495">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7546b-495">HTTP/2 support</span></span>

<span data-ttu-id="7546b-496">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-496">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7546b-497">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-497">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="7546b-498">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7546b-499">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-499">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7546b-500">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-500">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="7546b-501">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-501">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7546b-502">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-502">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="7546b-503">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-503">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="7546b-504">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-504">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="7546b-505">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-505">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="7546b-506">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-506">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="7546b-507">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-507">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="7546b-508">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-508">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="7546b-509">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="7546b-509">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="7546b-510">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-510">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="7546b-511">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-511">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="7546b-512">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-512">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="7546b-513">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-513">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="7546b-514">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-514">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="7546b-515">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-515">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="7546b-516">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="7546b-516">**HTTP.sys options**</span></span>

| <span data-ttu-id="7546b-517">속성</span><span class="sxs-lookup"><span data-stu-id="7546b-517">Property</span></span> | <span data-ttu-id="7546b-518">설명</span><span class="sxs-lookup"><span data-stu-id="7546b-518">Description</span></span> | <span data-ttu-id="7546b-519">기본값</span><span class="sxs-lookup"><span data-stu-id="7546b-519">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="7546b-520">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="7546b-520">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="7546b-521">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-521">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="7546b-522">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="7546b-522">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="7546b-523">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-523">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="7546b-524">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="7546b-524">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="7546b-525">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-525">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="7546b-526">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-526">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="7546b-527">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-527">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="7546b-528">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="7546b-528">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="7546b-529">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-529">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="7546b-530">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-530">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="7546b-531">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-531">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="7546b-532">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-532">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="7546b-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="7546b-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="7546b-534">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-534">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="7546b-535">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-535">Use `-1` for infinite.</span></span> <span data-ttu-id="7546b-536">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-536">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="7546b-537">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="7546b-537">(machine-wide</span></span><br><span data-ttu-id="7546b-538">설정)</span><span class="sxs-lookup"><span data-stu-id="7546b-538">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="7546b-539"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-539">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="7546b-540">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="7546b-540">30000000 bytes</span></span><br><span data-ttu-id="7546b-541">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="7546b-541">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="7546b-542">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-542">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="7546b-543">1000</span><span class="sxs-lookup"><span data-stu-id="7546b-543">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="7546b-544">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-544">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="7546b-545">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="7546b-545">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="7546b-546">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-546">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="7546b-547">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-547">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="7546b-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="7546b-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="7546b-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="7546b-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="7546b-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="7546b-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="7546b-554"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-554">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="7546b-555">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-555">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="7546b-556">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-556">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="7546b-557">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="7546b-557">**MaxRequestBodySize**</span></span>

<span data-ttu-id="7546b-558">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-558">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="7546b-559">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-559">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="7546b-560">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-560">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="7546b-561">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-561">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7546b-562">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-562">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="7546b-563">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-563">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7546b-564">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-564">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="7546b-565">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-565">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-566">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-566">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="7546b-567">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-567">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="7546b-569">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-569">Configure Windows Server</span></span>

1. <span data-ttu-id="7546b-570">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-570">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="7546b-571">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-571">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-572">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-572">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="7546b-573">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-573">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-574">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-574">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="7546b-575">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-575">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="7546b-576">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-576">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="7546b-577">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-577">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="7546b-578">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-578">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="7546b-579">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-579">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="7546b-580">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-580">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="7546b-581">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-581">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="7546b-582">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-582">Install the required .NET Framework.</span></span> <span data-ttu-id="7546b-583">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-583">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="7546b-584">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-584">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="7546b-585">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-585">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="7546b-586">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-586">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="7546b-587">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-587">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7546b-588">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-588">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="7546b-589">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7546b-589">`urls` command-line argument</span></span>
   * <span data-ttu-id="7546b-590">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="7546b-590">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="7546b-591">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-591">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="7546b-592">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-592">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="7546b-593">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-593">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="7546b-594">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-594">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="7546b-595">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-595">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="7546b-596">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-596">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7546b-597">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-597">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="7546b-598">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-598">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7546b-599">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-599">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="7546b-600">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="7546b-600">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7546b-601">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-601">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="7546b-602">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-602">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="7546b-603">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-603">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="7546b-604">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-604">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="7546b-605">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-605">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="7546b-606">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-606">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="7546b-607">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-607">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="7546b-608">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-608">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-609">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-609">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="7546b-610">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="7546b-610">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="7546b-611">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-611">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="7546b-612">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-612">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="7546b-613">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-613">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="7546b-614">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-614">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="7546b-615">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-615">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="7546b-616">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-616">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="7546b-617">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-617">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="7546b-618">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-618">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-619">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-619">Use a valid IP address.</span></span>
   * <span data-ttu-id="7546b-620">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-620">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="7546b-621">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-621">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="7546b-622">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-622">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="7546b-623">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-623">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="7546b-624">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-624">In Visual Studio:</span></span>
     * <span data-ttu-id="7546b-625">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-625">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="7546b-626">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-626">Select the **Package** tab.</span></span>
     * <span data-ttu-id="7546b-627">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-627">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="7546b-628">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="7546b-628">When not using Visual Studio:</span></span>
     * <span data-ttu-id="7546b-629">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-629">Open the app's project file.</span></span>
     * <span data-ttu-id="7546b-630">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-630">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="7546b-631">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7546b-631">In the following example:</span></span>

   * <span data-ttu-id="7546b-632">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-632">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="7546b-633">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-633">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="7546b-634">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-634">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="7546b-635">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-635">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="7546b-636">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-636">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="7546b-637">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="7546b-637">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="7546b-638">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="7546b-638">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="7546b-639">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-639">Run the app.</span></span>

   <span data-ttu-id="7546b-640">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-640">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="7546b-641">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-641">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="7546b-642">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-642">The app responds at the server's public IP address.</span></span> <span data-ttu-id="7546b-643">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-643">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="7546b-644">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-644">A development certificate is used in this example.</span></span> <span data-ttu-id="7546b-645">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-645">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7546b-647">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="7546b-647">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7546b-648">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-648">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="7546b-649">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-649">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7546b-650">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7546b-650">Additional resources</span></span>

* [<span data-ttu-id="7546b-651">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="7546b-651">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="7546b-652">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="7546b-652">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="7546b-653">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="7546b-653">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="7546b-654">호스트</span><span class="sxs-lookup"><span data-stu-id="7546b-654">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7546b-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys)는 Windows에서만 실행되는 [ASP.NET Core에 대한 웹 서버](xref:fundamentals/servers/index)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="7546b-656">HTTP.sys는 [Kestrel](xref:fundamentals/servers/kestrel) 서버에 대한 대안이며 Kestel이 제공하지 않는 일부 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-656">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7546b-657">HTTP.sys는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)과 호환되지 않으므로 IIS 또는 IIS Express와 함께 사용될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-657">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-658">HTTP.sys는 다음과 같은 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-658">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="7546b-659">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-659">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="7546b-660">포트 공유</span><span class="sxs-lookup"><span data-stu-id="7546b-660">Port sharing</span></span>
* <span data-ttu-id="7546b-661">SNI를 사용하는 HTTPS</span><span class="sxs-lookup"><span data-stu-id="7546b-661">HTTPS with SNI</span></span>
* <span data-ttu-id="7546b-662">TLS를 통한 HTTP/2(Windows 10 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-662">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="7546b-663">직접 파일 전송</span><span class="sxs-lookup"><span data-stu-id="7546b-663">Direct file transmission</span></span>
* <span data-ttu-id="7546b-664">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="7546b-664">Response caching</span></span>
* <span data-ttu-id="7546b-665">WebSockets(Windows 8 이상)</span><span class="sxs-lookup"><span data-stu-id="7546b-665">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="7546b-666">지원되는 Windows 버전:</span><span class="sxs-lookup"><span data-stu-id="7546b-666">Supported Windows versions:</span></span>

* <span data-ttu-id="7546b-667">Windows 7 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-667">Windows 7 or later</span></span>
* <span data-ttu-id="7546b-668">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-668">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7546b-669">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7546b-669">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="7546b-670">HTTP.sys를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-670">When to use HTTP.sys</span></span>

<span data-ttu-id="7546b-671">HTTP.sys는 다음과 같은 배포에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-671">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="7546b-672">IIS를 사용하지 않고 인터넷에 서버를 직접 노출해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="7546b-672">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys는 인터넷과 직접 통신합니다.](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="7546b-674">내부 배포에는 [Windows 인증](xref:security/authentication/windowsauth)처럼 Kestrel에서 사용할 수 없는 기능이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-674">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys는 내부 네트워크와 직접 통신합니다.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="7546b-676">HTTP.sys는 많은 유형의 공격으로부터 보호하고 모든 기능을 갖춘 웹 서버의 견고성, 보안 및 확장성을 제공하는 완성도 높은 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-676">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="7546b-677">IIS 자체는 HTTP.sys 위에 HTTP 수신기로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-677">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7546b-678">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="7546b-678">HTTP/2 support</span></span>

<span data-ttu-id="7546b-679">다음 기본 요구 사항이 충족되는 경우 ASP.NET Core 앱에 대해[HTTP/2](https://httpwg.org/specs/rfc7540.html)가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-679">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="7546b-680">Windows Server 2016/Windows 10 이상</span><span class="sxs-lookup"><span data-stu-id="7546b-680">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="7546b-681">[ALPN(Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="7546b-682">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="7546b-682">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7546b-683">HTTP/2 연결이 설정된 경우 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-683">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="7546b-684">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7546b-685">HTTP/2 연결이 설정되지 않는 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-685">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="7546b-686">Windows의 이후 릴리스에서는 HTTP.sys를 사용하여 HTTP/2를 사용하지 않도록 하는 기능을 포함하여 HTTP/2 구성 플래그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-686">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="7546b-687">Kerberos를 사용하여 커널 모드 인증</span><span class="sxs-lookup"><span data-stu-id="7546b-687">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="7546b-688">HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-688">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="7546b-689">사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-689">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="7546b-690">머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-690">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="7546b-691">앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-691">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="7546b-692">HTTP.sys 사용 방법</span><span class="sxs-lookup"><span data-stu-id="7546b-692">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="7546b-693">HTTP.sys를 사용하도록 ASP.NET Core 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-693">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="7546b-694">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))를 사용할 경우 프로젝트 파일의 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-694">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="7546b-695">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-695">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="7546b-696">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 확장 메서드를 호출하여 필요한 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-696">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="7546b-697">다음 예제에서는 옵션을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-697">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="7546b-698">추가 HTTP.sys 구성은 [레지스트리 설정](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-698">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="7546b-699">**HTTP.sys 옵션**</span><span class="sxs-lookup"><span data-stu-id="7546b-699">**HTTP.sys options**</span></span>

| <span data-ttu-id="7546b-700">속성</span><span class="sxs-lookup"><span data-stu-id="7546b-700">Property</span></span> | <span data-ttu-id="7546b-701">설명</span><span class="sxs-lookup"><span data-stu-id="7546b-701">Description</span></span> | <span data-ttu-id="7546b-702">기본값</span><span class="sxs-lookup"><span data-stu-id="7546b-702">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="7546b-703">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="7546b-703">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="7546b-704">`HttpContext.Request.Body` 및 `HttpContext.Response.Body`에 대해 동기 입력/출력이 허용되는지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-704">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="7546b-705">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="7546b-705">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="7546b-706">익명 요청을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-706">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="7546b-707">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="7546b-707">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="7546b-708">허용되는 인증 체계를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-708">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="7546b-709">수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-709">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="7546b-710">값은 [AuthenticationSchemes 열거형](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, `NTLM`에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-710">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="7546b-711">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="7546b-711">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="7546b-712">적합한 헤더가 있는 응답에 대해 [커널 모드](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) 캐싱을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-712">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="7546b-713">응답에 `Set-Cookie`, `Vary` 또는 `Pragma` 헤더가 포함될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-713">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="7546b-714">`public` 및 `shared-max-age` 또는 `max-age` 값인 `Cache-Control` 헤더나 `Expires` 헤더가 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-714">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="7546b-715">최대 동시 승인 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-715">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="7546b-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="7546b-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="7546b-717">허용되는 최대 동시 연결 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-717">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="7546b-718">무한의 경우 `-1`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-718">Use `-1` for infinite.</span></span> <span data-ttu-id="7546b-719">레지스트리의 시스템 수준 설정을 사용하려면 `null`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-719">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="7546b-720">(시스템 수준</span><span class="sxs-lookup"><span data-stu-id="7546b-720">(machine-wide</span></span><br><span data-ttu-id="7546b-721">설정)</span><span class="sxs-lookup"><span data-stu-id="7546b-721">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="7546b-722"><a href="#maxrequestbodysize">MaxRequestBodySize</a> 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-722">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="7546b-723">30000000바이트</span><span class="sxs-lookup"><span data-stu-id="7546b-723">30000000 bytes</span></span><br><span data-ttu-id="7546b-724">(~28.6MB)</span><span class="sxs-lookup"><span data-stu-id="7546b-724">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="7546b-725">큐에 대기할 수 있는 최대 요청 수입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-725">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="7546b-726">1000</span><span class="sxs-lookup"><span data-stu-id="7546b-726">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="7546b-727">클라이언트 연결 해제로 인해 실패한 응답 본문 쓰기가 예외를 throw하거나 정상적으로 완료되어야 하는지 여부를 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-727">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="7546b-728">(정상적으로 완료)</span><span class="sxs-lookup"><span data-stu-id="7546b-728">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="7546b-729">레지스트리에 구성될 수도 있는 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 구성을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-729">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="7546b-730">API 링크를 따라 기본값을 포함하여 각 설정에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-730">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="7546b-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): HTTP 서버 API가 지속적 연결에서 엔터티 본문을 비우는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="7546b-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): 요청 엔터티 본문이 도착할 때까지 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="7546b-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): HTTP 서버 API가 요청 헤더를 구문 분석하는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="7546b-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): 유휴 연결에 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="7546b-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): 응답의 최소 전송 속도입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="7546b-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): 앱이 선택하기 전에 요청이 요청 큐에 남아 있는 데 허용되는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="7546b-737"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>을 지정하여 HTTP.sys를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-737">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="7546b-738">컬렉션에 접두사를 추가하는 데 사용되는 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)가 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-738">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="7546b-739">이러한 API는 수신기를 삭제하기 전에 언제든지 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-739">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="7546b-740">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="7546b-740">**MaxRequestBodySize**</span></span>

<span data-ttu-id="7546b-741">요청 본문에 대해 허용되는 최대 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-741">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="7546b-742">`null`로 설정하면 최대 요청 본문 크기는 무제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-742">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="7546b-743">항상 무제한인 업그레이드된 연결에는 이 제한이 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-743">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="7546b-744">단일 `IActionResult`에 대해 ASP.NET Core MVC 앱에서 제한을 재정의할 때는 작업 메서드에서 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 특성을 사용하는 방법이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-744">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="7546b-745">앱에서 요청을 읽기 시작한 후 요청에 대한 제한을 구성하려고 하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-745">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="7546b-746">`IsReadOnly` 속성을 사용하여 `MaxRequestBodySize` 속성이 제한을 구성하기에 너무 늦은, 읽기 전용 상태인지를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-746">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="7546b-747">앱에서 요청별로 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>를 재정의해야 하는 경우 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-747">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="7546b-748">Visual Studio를 사용하는 경우 앱이 IIS 또는 IIS Express를 실행하도록 구성되지 않았는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-748">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="7546b-749">Visual Studio에서 기본 실행 프로필은 IIS Express용입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-749">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="7546b-750">프로젝트를 콘솔 앱으로 실행하려면 다음 스크린샷에 표시된 것처럼 선택한 프로필을 수동으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-750">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![콘솔 앱 프로필 선택](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="7546b-752">Windows Server 구성</span><span class="sxs-lookup"><span data-stu-id="7546b-752">Configure Windows Server</span></span>

1. <span data-ttu-id="7546b-753">앱용으로 열 포트를 결정하고 [Windows 방화벽](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 또는 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet을 통해 방화벽 포트를 열어 HTTP.sys에 도달하는 트래픽을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-753">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="7546b-754">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-754">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-755">Azure VM에 배포할 경우 [네트워크 보안 그룹](/azure/virtual-machines/windows/nsg-quickstart-portal)에서 포트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-755">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="7546b-756">다음 명령 및 앱 구성에서는 포트 443이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-756">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="7546b-757">필요한 경우 X.509 인증서를 구하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-757">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="7546b-758">Windows에서 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate)을 사용하여 자체 서명된 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-758">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="7546b-759">지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-759">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="7546b-760">서버의 **로컬 머신** > **개인** 저장소에 자체 서명 또는 CA 서명 인증서를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-760">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="7546b-761">앱이 [프레임워크 종속 배포](/dotnet/core/deploying/#framework-dependent-deployments-fdd)인 경우 .NET Core, .NET Framework 또는 둘 다(앱이 NET Framework를 대상으로 하는 .NET Core 앱인 경우)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-761">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="7546b-762">**.NET Core**: 앱에 .NET Core가 필요한 경우 [.NET Core 다운로드](https://dotnet.microsoft.com/download)에서 **.NET Core 런타임** 설치 관리자를 가져와 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-762">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="7546b-763">서버에 전체 SDK를 설치하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-763">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="7546b-764">**.NET Framework**: 앱에 .NET Framework가 필요한 경우 [.NET Framework 설치 가이드](/dotnet/framework/install/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-764">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="7546b-765">필수 .NET Framework를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-765">Install the required .NET Framework.</span></span> <span data-ttu-id="7546b-766">최신 .NET Framework의 설치 관리자는 [.NET Core 다운로드](https://dotnet.microsoft.com/download) 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-766">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="7546b-767">앱이 [자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)인 경우 앱의 배포에 런타임이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-767">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="7546b-768">서버에 프레임워크를 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-768">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="7546b-769">앱에서 URL 및 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-769">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="7546b-770">기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-770">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="7546b-771">URL 접두사 및 포트를 구성하려면 다음 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-771">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="7546b-772">`urls` 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="7546b-772">`urls` command-line argument</span></span>
   * <span data-ttu-id="7546b-773">`ASPNETCORE_URLS`환경 변수</span><span class="sxs-lookup"><span data-stu-id="7546b-773">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="7546b-774">다음 코드 예제는 포트 443에서 서버의 로컬 IP 주소 `10.0.0.4`와 함께 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-774">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="7546b-775">`UrlPrefixes`의 장점은 형식이 잘못된 접두사에 대해 오류 메시지가 즉시 생성된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-775">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="7546b-776">`UrlPrefixes`의 설정은 `UseUrls`/`urls`/`ASPNETCORE_URLS` 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-776">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="7546b-777">따라서 `UseUrls`, `urls` 및 `ASPNETCORE_URLS` 환경 변수의 장점은 Kestrel과 HTTP.sys 간을 쉽게 전환할 수 있다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-777">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="7546b-778">HTTP.sys는 [HTTP Server API UrlPrefix 문자열 형식](/windows/win32/http/urlprefix-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-778">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="7546b-779">최상위 와일드카드 바인딩(`http://*:80/` 및 `http://+:80`)을 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-779">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7546b-780">최상위 와일드카드 바인딩으로 인해 앱 보안 취약성이 생길 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-780">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="7546b-781">강력한 와일드카드와 약한 와일드카드 모두에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-781">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7546b-782">와일드카드보다는 명시적 호스트 이름 또는 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-782">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="7546b-783">전체 부모 도메인을 제어하는 경우 하위 도메인 와일드카드 바인딩(예: `*.mysub.com`)은 보안 위험이 아닙니다(취약한 `*.com`과 반대임).</span><span class="sxs-lookup"><span data-stu-id="7546b-783">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7546b-784">자세한 내용은 [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4)(RFC 7230: 섹션 5.4: 호스트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-784">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="7546b-785">서버에 URL 접두사를 미리 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-785">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="7546b-786">HTTP.sys 구성에 대한 기본 제공 도구는 *netsh.exe*입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-786">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="7546b-787">*netsh.exe*는 URL 접두사를 예약하고 X.509 인증서를 할당하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-787">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="7546b-788">도구를 사용하려면 관리자 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-788">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="7546b-789">*netsh.exe* 도구를 사용하여 앱의 URL을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-789">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="7546b-790">`<URL>`: 정규화된 URL(Uniform Resource Locator)입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-790">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="7546b-791">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-791">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-792">유효한 호스트 이름 또는 로컬 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-792">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="7546b-793">‘URL에는 후행 슬래시가 포함되어야 합니다.’</span><span class="sxs-lookup"><span data-stu-id="7546b-793">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="7546b-794">`<USER>`: 사용자 또는 사용자 그룹 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-794">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="7546b-795">다음 예제에서 서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-795">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="7546b-796">URL이 등록되면 도구가 `URL reservation successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-796">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="7546b-797">등록된 URL을 삭제하려면 `delete urlacl` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-797">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="7546b-798">서버에 X.509 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-798">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="7546b-799">*netsh.exe* 도구를 사용하여 앱의 인증서를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-799">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="7546b-800">`<IP>`: 바인딩의 로컬 IP 주소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-800">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="7546b-801">와일드카드 바인딩을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-801">Don't use a wildcard binding.</span></span> <span data-ttu-id="7546b-802">유효한 IP 주소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-802">Use a valid IP address.</span></span>
   * <span data-ttu-id="7546b-803">`<PORT>`: 바인딩의 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-803">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="7546b-804">`<THUMBPRINT>`: X.509 인증서 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-804">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="7546b-805">`<GUID>`: 정보 제공용 앱을 나타내는 개발자가 생성한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-805">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="7546b-806">참조용으로 GUID를 패키지 태그로 앱에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-806">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="7546b-807">Visual Studio에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-807">In Visual Studio:</span></span>
     * <span data-ttu-id="7546b-808">**솔루션 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 앱의 프로젝트 속성을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-808">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="7546b-809">**패키지** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-809">Select the **Package** tab.</span></span>
     * <span data-ttu-id="7546b-810">**태그** 필드에 직접 만든 GUID를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-810">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="7546b-811">Visual Studio를 사용하지 않는 경우:</span><span class="sxs-lookup"><span data-stu-id="7546b-811">When not using Visual Studio:</span></span>
     * <span data-ttu-id="7546b-812">앱의 프로젝트 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-812">Open the app's project file.</span></span>
     * <span data-ttu-id="7546b-813">직접 만든 GUID를 사용하여 `<PackageTags>` 속성을 새로운 또는 기존 `<PropertyGroup>`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-813">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="7546b-814">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7546b-814">In the following example:</span></span>

   * <span data-ttu-id="7546b-815">서버의 로컬 IP 주소는 `10.0.0.4`입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-815">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="7546b-816">온라인 임의 GUID 생성기는 `appid` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-816">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="7546b-817">인증서가 등록되면 도구가 `SSL Certificate successfully added`로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-817">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="7546b-818">인증서 등록을 삭제하려면 `delete sslcert` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-818">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="7546b-819">*netsh.exe*에 대한 참조 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-819">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="7546b-820">HTTP(Hypertext Transfer Protocol)에 대한 Netsh 명령</span><span class="sxs-lookup"><span data-stu-id="7546b-820">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="7546b-821">UrlPrefix 문자열</span><span class="sxs-lookup"><span data-stu-id="7546b-821">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="7546b-822">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-822">Run the app.</span></span>

   <span data-ttu-id="7546b-823">1024보다 큰 포트 번호로 HTTP(HTTPS 아님)를 사용하여 localhost에 바인딩하는 경우에는 앱을 실행하는 데 관리자 권한이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-823">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="7546b-824">다른 구성(예: 로컬 IP 주소 사용 또는 포트 443에 바인딩)의 경우 관리자 권한으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-824">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="7546b-825">이 앱은 서버의 공용 IP 주소에서 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-825">The app responds at the server's public IP address.</span></span> <span data-ttu-id="7546b-826">이 예제에서는 서버가 `104.214.79.47`의 공용 IP 주소에 있는 인터넷에서 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-826">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="7546b-827">이 예제에는 개발 인증서가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-827">A development certificate is used in this example.</span></span> <span data-ttu-id="7546b-828">브라우저의 신뢰할 수 없는 인증서 경고를 무시한 후 페이지가 안전하게 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-828">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![로드된 앱의 인덱스 페이지를 보여 주는 브라우저 창](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7546b-830">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="7546b-830">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7546b-831">인터넷 또는 회사 네트워크의 요청과 상호 작용하는 HTTP.sys에서 호스팅하는 앱의 경우, 프록시 서버 및 부하 분산 장치 뒤에서 호스팅할 때 추가 구성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7546b-831">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="7546b-832">자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](xref:host-and-deploy/proxy-load-balancer)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7546b-832">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7546b-833">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7546b-833">Additional resources</span></span>

* [<span data-ttu-id="7546b-834">HTTP.sys에서 Windows 인증 사용</span><span class="sxs-lookup"><span data-stu-id="7546b-834">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* <span data-ttu-id="7546b-835">[HTTP Server API](/windows/win32/http/http-api-start-page)(HTTP 서버 API)</span><span class="sxs-lookup"><span data-stu-id="7546b-835">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* [<span data-ttu-id="7546b-836">aspnet/HttpSysServer GitHub 리포지토리(소스 코드)</span><span class="sxs-lookup"><span data-stu-id="7546b-836">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="7546b-837">호스트</span><span class="sxs-lookup"><span data-stu-id="7546b-837">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
