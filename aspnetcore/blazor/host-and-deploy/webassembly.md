---
title: ASP.NET Core 호스트 및 배포 Blazor WebAssembly
author: guardrex
description: ASP.NET Core, CDN(콘텐츠 배달 네트워크), 파일 서버 및 GitHub 페이지를 사용하여 Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 3436620123618ab32daa44c4a37057aaadb89563
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393693"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="f7948-103">ASP.NET Core 호스트 및 배포 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f7948-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="f7948-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) 및 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="f7948-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="f7948-105">[Blazor WebAssembly 호스팅 모델](xref:blazor/hosting-models#blazor-webassembly)을 사용하면 다음과 같이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="f7948-106">Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 병렬로 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="f7948-107">해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="f7948-108">다음 배포 전략이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="f7948-109">Blazor 앱은 ASP.NET Core 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="f7948-110">이 전략은 [ASP.NET Core를 사용하여 호스트된 배포](#hosted-deployment-with-aspnet-core) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="f7948-111">Blazor 앱은 정적 호스팅 웹 서버 또는 서비스에 배치되며, 이 경우 Blazor 앱을 처리하기 위해 .NET을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="f7948-112">이 전략은 Blazor WebAssembly 앱을 IIS 하위 앱으로 호스트하는 방법에 대한 정보를 포함하는 [독립 실행형 배포](#standalone-deployment) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="f7948-113">압축</span><span class="sxs-lookup"><span data-stu-id="f7948-113">Compression</span></span>

<span data-ttu-id="f7948-114">Blazor WebAssembly 앱이 게시될 때 게시하는 도중에 출력을 정적으로 압축하여 앱의 크기를 줄이고 런타임 압축의 오버헤드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="f7948-115">다음 압축 알고리즘이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="f7948-116">[Brotli](https://tools.ietf.org/html/rfc7932)(최고 수준)</span><span class="sxs-lookup"><span data-stu-id="f7948-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="f7948-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="f7948-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="f7948-118">Blazor는 호스트를 사용하여 적절한 압축 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="f7948-119">ASP.NET Core 호스트 프로젝트를 사용하는 경우 호스트 프로젝트는 콘텐츠 협상을 수행하고 정적으로 압축된 파일을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="f7948-120">Blazor WebAssembly 독립 실행형 앱을 호스트하는 경우 정적으로 압축된 파일이 제공되도록 추가 작업이 필요할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="f7948-121">IIS `web.config` 압축 구성에 대해서는 [IIS: Brotli 및 Gzip 압축](#brotli-and-gzip-compression) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="f7948-122">GitHub 페이지와 같이 정적으로 압축된 파일 콘텐츠 협상을 지원하지 않는 정적 호스팅 솔루션에서 호스트하는 경우 Brotli로 압축된 파일을 가져와 디코딩하는 앱을 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="f7948-123">[google/brotli GitHub 리포지토리](https://github.com/google/brotli)에서 JavaScript Brotli 디코더를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="f7948-124">2020년 9월부터 디코더 파일의 이름은 `decode.js`이며 리포지토리의 [`js` 폴더](https://github.com/google/brotli/tree/master/js)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="f7948-125">회귀는 [google/brotli GitHub 리포지토리](https://github.com/google/brotli)에서 `decode.js` 스크립트(`decode.min.js`)의 축소 버전으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="f7948-126">[Window.BrotliDecode가 decode.min.js에서 설정되지 않음(google/brotli #844)](https://github.com/google/brotli/issues/844) 문제가 해결되기 전까지는 직접 스크립트를 축소하거나 [npm 패키지](https://www.npmjs.com/package/brotli)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="f7948-127">이 섹션의 예제 코드는 **축소되지 않은** 버전의 스크립트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="f7948-128">디코더를 사용하도록 앱을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-128">Update the app to use the decoder.</span></span> <span data-ttu-id="f7948-129">`wwwroot/index.html`에서 닫는 `<body>` 태그 내부의 태그를 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="f7948-130">압축을 해제하려면 앱의 프로젝트 파일에 `BlazorEnableCompression` MSBuild 속성을 추가하고 값을 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="f7948-131">명령 셸에서 다음 구문을 사용하여 `BlazorEnableCompression` 속성을 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="f7948-132">올바른 라우팅을 위해 URL 다시 생성</span><span class="sxs-lookup"><span data-stu-id="f7948-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="f7948-133">Blazor WebAssembly 앱의 페이지 구성 요소에 대한 라우팅 요청은 Blazor Server에서 호스트된 앱의 요청을 라우팅하는 것처럼 간단하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="f7948-134">다음 두 구성 요소를 사용하는 Blazor WebAssembly 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="f7948-135">`Main.razor`: 앱의 루트에 로드되며 `About` 구성 요소에 대한 링크(`href="About"`)를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="f7948-136">`About.razor`: `About` 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="f7948-137">브라우저의 주소 표시줄을 사용하여 앱의 기본 문서를 요청하는 경우(예: `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="f7948-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="f7948-138">브라우저가 요청을 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-138">The browser makes a request.</span></span>
1. <span data-ttu-id="f7948-139">기본 페이지(일반적으로 `index.html`)가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="f7948-140">`index.html`이 앱을 부트스트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="f7948-141">Blazor의 라우터가 로드되고 Razor `Main` 구성 요소가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="f7948-142">Blazor 라우터는 브라우저가 인터넷에서 `www.contoso.com`으로 `About`을 요청하는 것을 중단하고 렌더링된 `About` 구성 요소를 직접 제공하므로 기본 페이지에서 `About` 구성 요소에 대한 링크 선택은 클라이언트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="f7948-143">‘Blazor WebAssembly 앱 내’의 내부 엔드포인트에 대한 모든 요청도 같은 방법으로 작동합니다. 요청은 인터넷상에서 서버가 호스트하는 리소스에 대한 브라우저 기반 요청을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="f7948-144">라우터가 내부적으로 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-144">The router handles the requests internally.</span></span>

<span data-ttu-id="f7948-145">브라우저의 주소 표시줄을 사용하여 `www.contoso.com/About`을 요청하면 해당 요청이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="f7948-146">앱의 인터넷 호스트에 해당 리소스가 없으므로 *404 - 찾을 수 없음* 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="f7948-147">브라우저는 인터넷 기반 호스트에 클라이언트 쪽 페이지를 요청하므로, 웹 서버 및 호스팅 서비스는 서버에 실제로 존재하지 않는 리소스에 대한 모든 요청을 `index.html` 페이지에 다시 써야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="f7948-148">`index.html`이 반환되는 경우 앱의 Blazor 라우터가 넘겨받아 올바른 리소스로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="f7948-149">IIS 서버에 배포하는 경우 앱의 게시된 `web.config` 파일과 함께 URL 재작성 모듈을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="f7948-150">자세한 내용은 [IIS](#iis) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="f7948-151">ASP.NET Core를 사용하여 호스트된 배포</span><span class="sxs-lookup"><span data-stu-id="f7948-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="f7948-152">‘호스트된 배포’는 웹 서버에서 실행되는 [ASP.NET Core 앱](xref:index)에서 Blazor WebAssembly 앱을 브라우저에 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="f7948-153">클라이언트 Blazor WebAssembly 앱이 서버 앱의 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="f7948-154">두 앱이 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-154">The two apps are deployed together.</span></span> <span data-ttu-id="f7948-155">ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="f7948-156">호스트된 배포의 경우 Visual Studio는 **`Hosted`** (`dotnet new` 명령을 사용하는 경우 `-ho|--hosted`) 옵션이 선택된 **Blazor WebAssembly 앱** 프로젝트 템플릿([`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorwasm` 템플릿)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="f7948-157">ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="f7948-158">Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="f7948-159">여러 Blazor WebAssembly 앱을 사용하여 호스트된 배포</span><span class="sxs-lookup"><span data-stu-id="f7948-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="f7948-160">앱 구성</span><span class="sxs-lookup"><span data-stu-id="f7948-160">App configuration</span></span>

<span data-ttu-id="f7948-161">여러 Blazor WebAssembly 앱을 제공하도록 호스트된 Blazor 솔루션을 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="f7948-162">호스트된 기존 Blazor 솔루션을 사용하거나 Blazor 호스트 프로젝트 템플릿에서 새 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="f7948-163">클라이언트 앱의 프로젝트 파일에서 값이 `FirstApp`인 `<PropertyGroup>`에 `<StaticWebAssetBasePath>` 속성을 추가하여 프로젝트의 정적 자산에 대한 기본 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="f7948-164">두 번째 클라이언트 앱을 솔루션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="f7948-165">`SecondClient`라는 폴더를 솔루션의 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="f7948-166">Blazor WebAssembly 프로젝트 템플릿의 `SecondClient` 폴더에 `SecondBlazorApp.Client`라는 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="f7948-167">앱의 프로젝트 파일에서:</span><span class="sxs-lookup"><span data-stu-id="f7948-167">In the app's project file:</span></span>

    * <span data-ttu-id="f7948-168">`SecondApp` 값을 사용하여 `<PropertyGroup>`에 `<StaticWebAssetBasePath>` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="f7948-169">`Shared` 프로젝트에 프로젝트 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="f7948-170">자리 표시자 `{SOLUTION NAME}`은 솔루션의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="f7948-171">서버 앱의 프로젝트 파일에서 추가된 클라이언트 앱에 대한 프로젝트 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="f7948-172">서버 앱의 `Properties/launchSettings.json` 파일에서 포트 5001 및 5002의 클라이언트 앱에 액세스하도록 Kestrel 프로필(`{SOLUTION NAME}.Server`)의 `applicationUrl`을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="f7948-173">서버 앱의 `Startup.Configure` 메서드(`Startup.cs`)에서 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>에 대한 호출 뒤에 표시되는 다음 줄을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="f7948-174">클라이언트 앱에 요청을 매핑하는 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="f7948-175">다음 예제는 다음 경우에 실행되도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="f7948-176">요청 포트가 원래 클라이언트 앱의 경우 5001이고 추가된 클라이언트 앱의 경우 5002입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="f7948-177">요청 호스트가 원래 클라이언트 앱의 경우 `firstapp.com`이고 추가된 클라이언트 앱의 경우 `secondapp.com`입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="f7948-178">이 섹션에 표시된 예제에는 다음에 대한 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="f7948-179">예제 호스트 도메인, `firstapp.com` 및 `secondapp.com`에 있는 앱에 액세스.</span><span class="sxs-lookup"><span data-stu-id="f7948-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="f7948-180">TLS 보안(HTTPS)을 사용하도록 설정하는 클라이언트 앱 인증서.</span><span class="sxs-lookup"><span data-stu-id="f7948-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="f7948-181">필요한 구성은 이 문서의 범위를 벗어나며 솔루션이 호스트되는 방법에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="f7948-182">자세한 내용은 [호스트 및 배포 문서](xref:host-and-deploy/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="f7948-183">이전에 줄이 제거된 곳에 다음 코드를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="f7948-184">서버 앱의 일기 예보 컨트롤러(`Controllers/WeatherForecastController.cs`)에서 기존 `WeatherForecastController` 경로(`[Route("[controller]")]`)를 다음 경로로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="f7948-185">이전에 서버 앱의 `Startup.Configure` 메서드에 추가된 미들웨어는 `/WeatherForecast`로 들어오는 요청을 포트(5001/5002) 또는 도메인(`firstapp.com`/`secondapp.com`)에 따라 `/FirstApp/WeatherForecast` 또는 `/SecondApp/WeatherForecast`로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="f7948-186">서버 앱에서 클라이언트 앱으로 날씨 데이터를 반환하려면 이전 컨트롤러 경로가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="f7948-187">정적 자산 및 클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="f7948-187">Static assets and class libraries</span></span>

<span data-ttu-id="f7948-188">정적 자산에는 다음 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="f7948-189">자산이 클라이언트 앱의 `wwwroot` 폴더에 있는 경우 정상적으로 해당 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="f7948-190">자산이 [Razor 클래스 라이브러리(RCL)](xref:blazor/components/class-libraries)의 `wwwroot` 폴더에 있는 경우 [RCL 문서](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)의 지침에 따라 클라이언트 앱에서 정적 자산을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f7948-191">클래스 라이브러리에 의해 클라이언트 앱에 제공된 구성 요소는 정상적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="f7948-192">구성 요소에 스타일시트 또는 JavaScript 파일이 필요한 경우 다음 방법 중 하나를 사용하여 정적 자산을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="f7948-193">클라이언트 앱의 `wwwroot/index.html` 파일은 정적 자산에 연결(`<link>`)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="f7948-194">구성 요소는 프레임워크의 [`Link` 구성 요소](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)를 사용하여 정적 자산을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="f7948-195">다음 예제에서는 앞의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f7948-196">클래스 라이브러리에 의해 클라이언트 앱에 제공된 구성 요소는 정상적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="f7948-197">구성 요소에 스타일시트 또는 JavaScript 파일이 필요한 경우 클라이언트 앱의 `wwwroot/index.html` 파일은 올바른 정적 자산 링크를 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="f7948-198">다음 예제에서는 이러한 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="f7948-199">클라이언트 앱 중 하나에 다음 `Jeep` 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="f7948-200">`Jeep` 구성 요소는 다음을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="f7948-201">클라이언트 앱의 `wwwroot` 폴더(`jeep-cj.png`)에 있는 이미지.</span><span class="sxs-lookup"><span data-stu-id="f7948-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="f7948-202">[추가된 Razor 구성 요소 라이브러리](xref:blazor/components/class-libraries)(`JeepImage`) `wwwroot` 폴더(`jeep-yj.png`)의 이미지.</span><span class="sxs-lookup"><span data-stu-id="f7948-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="f7948-203">예제 구성 요소(`Component1`)는 `JeepImage` 라이브러리가 솔루션에 추가될 때 RCL 프로젝트 템플릿에 의해 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="f7948-204">이미지를 소유하고 있지 않다면 차량 이미지를 공개적으로 게시하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="f7948-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="f7948-205">게시할 경우 저작권 침해의 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f7948-206">라이브러리의 `jeep-yj.png` 이미지를 라이브러리의 `Component1` 구성 요소(`Component1.razor`)에 추가할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="f7948-207">클라이언트 앱의 페이지에 `my-component` CSS 클래스를 제공하려면 프레임워크의 [`Link` 구성 요소](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)를 사용하여 라이브러리의 스타일시트에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="f7948-208">[`Link` 구성 요소](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)를 사용하는 대신 클라이언트 앱의 `wwwroot/index.html` 파일에서 스타일시트를 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="f7948-209">이 방법을 사용하면 클라이언트 앱의 모든 구성 요소에서 스타일시트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f7948-210">라이브러리의 `jeep-yj.png` 이미지를 라이브러리의 `Component1` 구성 요소(`Component1.razor`)에 추가할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="f7948-211">클라이언트 앱의 `wwwroot/index.html` 파일은 추가된 다음 `<link>` 태그를 사용하여 라이브러리의 스타일시트를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="f7948-212">클라이언트 앱의 `NavMenu` 구성 요소(`Shared/NavMenu.razor`)에 `Jeep` 구성 요소에 대한 탐색을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="f7948-213">RCL에 대한 자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="f7948-214">독립 실행형 배포</span><span class="sxs-lookup"><span data-stu-id="f7948-214">Standalone deployment</span></span>

<span data-ttu-id="f7948-215">‘독립 실행형 배포’는 Blazor WebAssembly 앱을 클라이언트가 직접 요청하는 정적 파일 세트로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="f7948-216">모든 정적 파일 서버는 Blazor 앱을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="f7948-217">독립 실행형 배포 자산은 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="f7948-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f7948-218">Azure App Service</span></span>

<span data-ttu-id="f7948-219">Blazor WebAssembly 앱은 [IIS](#iis)에서 앱을 호스트하는 Windows의 Azure App Services에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="f7948-220">Linux용 Azure App Service에 독립 실행형 Blazor WebAssembly 앱 배포는 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="f7948-221">지금은 앱을 호스트하는 Linux 서버 이미지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="f7948-222">이 시나리오를 사용할 수 있도록 하는 작업이 진행 중입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="f7948-223">IIS</span><span class="sxs-lookup"><span data-stu-id="f7948-223">IIS</span></span>

<span data-ttu-id="f7948-224">IIS는 Blazor 앱에 사용할 수 있는 정적 파일 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="f7948-225">Blazor를 호스트하도록 IIS를 구성하려면 [IIS에서 정적 웹 사이트 작성](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="f7948-226">게시된 자산은 `/bin/Release/{TARGET FRAMEWORK}/publish` 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="f7948-227">웹 서버 또는 호스팅 서비스에서 `publish` 폴더의 콘텐츠를 호스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="f7948-228">web.config</span><span class="sxs-lookup"><span data-stu-id="f7948-228">web.config</span></span>

<span data-ttu-id="f7948-229">Blazor 프로젝트가 게시되면 다음 IIS 구성을 사용하여 `web.config` 파일이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="f7948-230">다음 파일 확장명에 대해 MIME 형식을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="f7948-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="f7948-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="f7948-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="f7948-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="f7948-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="f7948-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="f7948-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="f7948-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="f7948-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="f7948-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="f7948-236">다음 MIME 형식에 대해 HTTP 압축을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="f7948-237">URL 재작성 모듈 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="f7948-238">앱의 정적 자산이 상주하는 하위 디렉터리(`wwwroot/{PATH REQUESTED}`)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="f7948-239">파일이 아닌 자산 요청이 정적 자산 폴더에 있는 앱의 기본 문서(`wwwroot/index.html`)로 리디렉션되도록 SPA 대체(fallback) 라우팅을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="f7948-240">사용자 지정 web.config 사용</span><span class="sxs-lookup"><span data-stu-id="f7948-240">Use a custom web.config</span></span>

<span data-ttu-id="f7948-241">사용자 지정 `web.config` 파일을 사용하려면 사용자 지정 `web.config` 파일을 프로젝트 폴더의 루트에 배치하고 프로젝트를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="f7948-242">URL 재작성 모듈 설치</span><span class="sxs-lookup"><span data-stu-id="f7948-242">Install the URL Rewrite Module</span></span>

<span data-ttu-id="f7948-243">URL을 다시 생성하려면 [URL 다시 생성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-243">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="f7948-244">이 모듈은 기본적으로 설치되지 않으며 웹 서버(IIS) 역할 서비스 기능으로 설치하는 데 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-244">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="f7948-245">이 모듈은 IIS 웹 사이트에서 다운로드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-245">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="f7948-246">웹 플랫폼 설치 관리자를 사용하여 이 모듈을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-246">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="f7948-247">로컬에서 [URL 다시 생성 모듈 다운로드 페이지](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-247">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="f7948-248">영어 버전의 경우 **WebPI**를 선택하여 WebPI 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-248">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="f7948-249">다른 언어의 경우 서버에 맞는 아키텍처(x86 x64)를 선택하여 설치 관리자를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-249">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="f7948-250">설치 관리자를 서버에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-250">Copy the installer to the server.</span></span> <span data-ttu-id="f7948-251">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-251">Run the installer.</span></span> <span data-ttu-id="f7948-252">**설치** 버튼을 선택하고 사용 조건에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-252">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="f7948-253">설치가 완료된 후 서버를 다시 시작하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-253">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="f7948-254">웹 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="f7948-254">Configure the website</span></span>

<span data-ttu-id="f7948-255">웹 사이트의 **실제 경로**를 앱의 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-255">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="f7948-256">이 폴더는 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-256">The folder contains:</span></span>

* <span data-ttu-id="f7948-257">필요한 리디렉션 규칙 및 파일 콘텐츠 형식 등 IIS가 웹 사이트를 구성하기 위해 사용하는 `web.config` 파일</span><span class="sxs-lookup"><span data-stu-id="f7948-257">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="f7948-258">앱의 정적 자산 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-258">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="f7948-259">IIS 하위 앱으로 호스트</span><span class="sxs-lookup"><span data-stu-id="f7948-259">Host as an IIS sub-app</span></span>

<span data-ttu-id="f7948-260">독립 실행형 앱이 IIS 하위 앱으로 호스트되는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-260">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="f7948-261">상속된 ASP.NET Core 모듈 처리기를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-261">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="f7948-262">Blazor 앱의 게시된 `web.config` 파일에 `<handlers>` 섹션을 추가하여 파일에서 처리기를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-262">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="f7948-263">`inheritInChildApplications`를 `false`로 설정한 상태에서 `<location>` 요소를 사용하여 루트(상위) 앱의 `<system.webServer>` 섹션의 상속을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-263">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="f7948-264">처리기 제거 또는 상속을 사용하지 않도록 하는 설정은 [앱의 기본 경로 구성](xref:blazor/host-and-deploy/index#app-base-path)에 더하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-264">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="f7948-265">앱의 `index.html` 파일에 있는 앱 기본 경로를 IIS에서 하위 앱을 구성할 때 사용되는 IIS 별칭으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-265">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="f7948-266">Brotli 및 Gzip 압축</span><span class="sxs-lookup"><span data-stu-id="f7948-266">Brotli and Gzip compression</span></span>

<span data-ttu-id="f7948-267">`web.config`를 통해 IIS를 구성하여 Brotli 또는 Gzip 압축 Blazor 자산을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-267">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="f7948-268">예제 구성은 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true)을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-268">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="f7948-269">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f7948-269">Troubleshooting</span></span>

<span data-ttu-id="f7948-270">500 - 내부 서버 오류가 수신되고 웹 사이트의 구성에 액세스를 시도할 때 IIS 관리자가 오류를 표시하면 URL 다시 생성 모듈이 설치되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-270">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="f7948-271">모듈이 설치되지 않은 경우 IIS가 `web.config` 파일을 구문 분석할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-271">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="f7948-272">그러면 IIS 관리자가 웹 사이트의 구성을 로드할 수 없으며 웹 사이트가 Blazor의 정적 파일을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-272">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="f7948-273">IIS 배포 문제 해결에 대한 자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-273">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="f7948-274">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="f7948-274">Azure Storage</span></span>

<span data-ttu-id="f7948-275">[Azure Storage](/azure/storage/) 정적 파일 호스팅으로 서버리스 Blazor 앱 호스팅이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-275">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="f7948-276">사용자 지정 도메인 이름, Azure 콘텐츠 배달 네트워크(CDN) 및 HTTPS가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-276">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="f7948-277">스토리지 계정에서 정적 웹 사이트 호스팅을 위해 BLOB 서비스를 사용할 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="f7948-277">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="f7948-278">**인덱스 문서 이름**을 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-278">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="f7948-279">**오류 문서 경로**를 `index.html`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-279">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="f7948-280">Razor 구성 요소 및 기타 파일이 아닌 엔드포인트는 Blob 서비스에 의해 저장된 정적 콘텐츠의 실제 경로에 존재하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-280">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="f7948-281">이러한 리소스 중 하나를 Blazor 라우터가 처리해야 한다는 요청이 수신되면 BLOB 서비스에 의해 생성된 *404 - 찾을 수 없음* 오류가 요청을 **오류 문서 경로**로 라우팅합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-281">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="f7948-282">`index.html` BLOB이 반환되고 Blazor 라우터가 로드되어 경로를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-282">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="f7948-283">파일의 `Content-Type` 헤더에 부적절한 MIME 형식으로 인해 런타임에 파일이 로드되지 않을 경우 다음 작업 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-283">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="f7948-284">파일이 배포될 때 올바른 MIME 형식(`Content-Type` 헤더)을 설정하도록 도구를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-284">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="f7948-285">앱이 배포된 후 파일에 대한 MIME 형식(`Content-Type` 헤더)을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-285">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="f7948-286">각 파일의 스토리지 탐색기(Azure Portal)에서</span><span class="sxs-lookup"><span data-stu-id="f7948-286">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="f7948-287">파일을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-287">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="f7948-288">**ContentType**을 설정하고 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-288">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="f7948-289">자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스팅](/azure/storage/blobs/storage-blob-static-website)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-289">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="f7948-290">Nginx</span><span class="sxs-lookup"><span data-stu-id="f7948-290">Nginx</span></span>

<span data-ttu-id="f7948-291">다음 `nginx.conf` 파일은 Nginx가 디스크에서 대응하는 파일을 찾을 수 없을 때마다 `index.html` 파일을 보내도록 구성하는 방법을 보여 주기 위해 단순화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-291">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="f7948-292">[`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req)를 사용하여 [NGINX 버스트 속도 한도](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)를 설정하는 경우 Blazor WebAssembly 앱에서 수행하는 비교적 많은 수의 요청을 수용하기 위해 큰 `burst` 매개 변수 값이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-292">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="f7948-293">처음에는 값을 60 이상으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-293">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="f7948-294">브라우저 개발자 도구 또는 네트워크 트래픽 도구에서 요청이 ‘503 - 서비스를 사용할 수 없음’ 상태 코드를 수신하는 것으로 확인되는 경우 값을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-294">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="f7948-295">프로덕션 Nginx 웹 서버 구성에 대한 자세한 내용은 [NGINX Plus 및 NGINX 구성 파일 만들기](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-295">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="f7948-296">Docker의 Nginx</span><span class="sxs-lookup"><span data-stu-id="f7948-296">Nginx in Docker</span></span>

<span data-ttu-id="f7948-297">Docker에서 Nginx를 사용하여 Blazor를 호스트하려면 Dockerfile을 Alpine 기반 Nginx 이미지를 사용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-297">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="f7948-298">Dockerfile을 업데이트하여 `nginx.config` 파일을 컨테이너에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-298">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="f7948-299">다음 예제와 같이 Dockerfile에 한 줄을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-299">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="f7948-300">Apache</span><span class="sxs-lookup"><span data-stu-id="f7948-300">Apache</span></span>

<span data-ttu-id="f7948-301">CentOS 7 이상에 Blazor WebAssembly 앱을 배포하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-301">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="f7948-302">Apache 구성 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-302">Create the Apache configuration file.</span></span> <span data-ttu-id="f7948-303">다음 예제는 단순화된 구성 파일입니다(`blazorapp.config`).</span><span class="sxs-lookup"><span data-stu-id="f7948-303">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="f7948-304">Apache 구성 파일을 CentOS 7의 기본 Apache 구성 디렉터리인 `/etc/httpd/conf.d/` 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-304">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="f7948-305">앱의 파일을 `/var/www/blazorapp` 디렉터리(구성 파일의 `DocumentRoot`에 지정된 위치)에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-305">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="f7948-306">Apache 서비스를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-306">Restart the Apache service.</span></span>

<span data-ttu-id="f7948-307">자세한 내용은 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 및 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-307">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="f7948-308">GitHub 페이지</span><span class="sxs-lookup"><span data-stu-id="f7948-308">GitHub Pages</span></span>

<span data-ttu-id="f7948-309">URL 다시 쓰기를 처리하려면 `index.html` 페이지로 요청 리디렉션을 처리하는 스크립트를 사용하여 `wwwroot/404.html` 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-309">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="f7948-310">예제는 [SteveSandersonMS/BlazorOnGitHubPages GitHub 리포지토리](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-310">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="f7948-311">[라이브 사이트](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="f7948-311">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="f7948-312">조직 사이트 대신 프로젝트 사이트를 사용하는 경우 `wwwroot/index.html`의 `<base>` 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-312">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="f7948-313">`href` 특성 값을 후행 슬래시가 있는 GitHub 리포지토리 이름으로 설정합니다(예: `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="f7948-313">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="f7948-314">[SteveSandersonMS/BlazorOnGitHubPages GitHub 리포지토리](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)에서 기본 `href`는 [`.github/workflows/main.yml` 구성 파일](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)에 의해 게시 시 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-314">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="f7948-315">[SteveSandersonMS/BlazorOnGitHubPages GitHub 리포지토리](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)는 .NET Foundation 또는 Microsoft에서 소유, 유지 관리 또는 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-315">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="f7948-316">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="f7948-316">Host configuration values</span></span>

<span data-ttu-id="f7948-317">[Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)은 개발 환경의 런타임에 다음 호스트 구성 값을 명령줄 인수로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-317">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="f7948-318">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="f7948-318">Content root</span></span>

<span data-ttu-id="f7948-319">`--contentroot` 인수는 앱의 콘텐츠 파일을 포함하는 디렉터리([콘텐츠 루트](xref:fundamentals/index#content-root))에 대한 절대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-319">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="f7948-320">다음 예제에서 `/content-root-path`는 앱의 콘텐츠 루트 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-320">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="f7948-321">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-321">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="f7948-322">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-322">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="f7948-323">**IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-323">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="f7948-324">앱이 Visual Studio 디버거를 통해 실행되거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행되는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-324">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="f7948-325">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-325">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="f7948-326">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-326">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="f7948-327">경로 기준</span><span class="sxs-lookup"><span data-stu-id="f7948-327">Path base</span></span>

<span data-ttu-id="f7948-328">`--pathbase` 인수는 루트가 아닌 상대 URL 경로를 사용하여 로컬로 앱을 실행하기 위한 앱 기본 경로를 설정합니다. (준비 및 프로덕션의 경우 `<base>` 태그 `href`는 `/` 이외의 경로로 설정됩니다.)</span><span class="sxs-lookup"><span data-stu-id="f7948-328">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="f7948-329">다음 예제에서 `/relative-URL-path`는 앱의 경로 기준입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-329">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="f7948-330">자세한 내용은 [앱 기본 경로](xref:blazor/host-and-deploy/index#app-base-path)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-330">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f7948-331">`<base>` 태그의 `href`에 대해 제공되는 경로와 달리 `--pathbase` 인수 값을 전달할 때 뒤에 슬래시(`/`)를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-331">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="f7948-332">앱 기본 경로가 `<base>` 태그에 `<base href="/CoolApp/">`로 제공되는 경우(뒤에 슬래시 포함) 명령줄 인수 값을 `--pathbase=/CoolApp`로 전달합니다(뒤에 슬래시 없음).</span><span class="sxs-lookup"><span data-stu-id="f7948-332">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="f7948-333">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="f7948-334">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="f7948-335">**IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="f7948-336">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="f7948-337">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="f7948-338">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="f7948-339">URL</span><span class="sxs-lookup"><span data-stu-id="f7948-339">URLs</span></span>

<span data-ttu-id="f7948-340">`--urls` 인수는 요청을 수신하기 위한 포트 및 프로토콜을 포함하는 IP 주소 또는 호스트 주소를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-340">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="f7948-341">명령 프롬프트에서 앱을 로컬로 실행할 때 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-341">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="f7948-342">앱의 디렉터리에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-342">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="f7948-343">**IIS Express** 프로필에서 앱의 `launchSettings.json` 파일에 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-343">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="f7948-344">앱을 Visual Studio 디버거를 통해 실행하거나 `dotnet run`을 사용하여 명령 프롬프트에서 실행하는 경우 이 설정이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-344">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="f7948-345">Visual Studio의 **속성** > **디버그** > **애플리케이션 인수**에서 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-345">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="f7948-346">Visual Studio 속성 페이지에서 인수를 설정하면 해당 인수가 `launchSettings.json` 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-346">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="f7948-347">트리머 구성</span><span class="sxs-lookup"><span data-stu-id="f7948-347">Configure the Trimmer</span></span>

<span data-ttu-id="f7948-348">Blazor는 각 릴리스 빌드에 IL(중간 언어) 트리밍을 수행하여 출력 어셈블리에서 필요 없는 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-348">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="f7948-349">자세한 내용은 <xref:blazor/host-and-deploy/configure-trimmer>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-349">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="f7948-350">링커 구성</span><span class="sxs-lookup"><span data-stu-id="f7948-350">Configure the Linker</span></span>

<span data-ttu-id="f7948-351">Blazor는 각 릴리스 빌드에 대해 IL(중간 언어) 연결을 수행하여 출력 어셈블리에서 불필요한 IL을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-351">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="f7948-352">자세한 내용은 <xref:blazor/host-and-deploy/configure-linker>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-352">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="f7948-353">사용자 지정 부팅 리소스 로드</span><span class="sxs-lookup"><span data-stu-id="f7948-353">Custom boot resource loading</span></span>

<span data-ttu-id="f7948-354">`loadBootResource` 함수로 Blazor WebAssembly 앱을 초기화하여 기본 제공 부팅 리소스 로드 메커니즘을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-354">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="f7948-355">다음 시나리오에 `loadBootResource`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-355">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="f7948-356">사용자가 CDN에서 표준 시간대 데이터 또는 `dotnet.wasm` 같은 정적 리소스를 로드하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-356">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="f7948-357">HTTP 요청을 사용하여 압축된 어셈블리를 로드하고 서버에서 압축된 콘텐츠 페치를 지원하지 않는 호스트의 경우 클라이언트에서 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-357">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="f7948-358">각 `fetch` 요청을 새 이름으로 리디렉션하여 리소스 별칭을 다른 이름으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-358">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="f7948-359">`loadBootResource` 매개 변수는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-359">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="f7948-360">매개 변수</span><span class="sxs-lookup"><span data-stu-id="f7948-360">Parameter</span></span>    | <span data-ttu-id="f7948-361">Description</span><span class="sxs-lookup"><span data-stu-id="f7948-361">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="f7948-362">리소스 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-362">The type of the resource.</span></span> <span data-ttu-id="f7948-363">허용되는 형식: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="f7948-363">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="f7948-364">리소스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-364">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="f7948-365">리소스의 상대 또는 절대 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-365">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="f7948-366">응답에서 예상되는 콘텐츠를 나타내는 무결성 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-366">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="f7948-367">`loadBootResource`는 로드 프로세스를 재정의하기 위해 다음 항목을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-367">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="f7948-368">URI 문자열.</span><span class="sxs-lookup"><span data-stu-id="f7948-368">URI string.</span></span> <span data-ttu-id="f7948-369">다음 예제(`wwwroot/index.html`)에서 다음 파일은 `https://my-awesome-cdn.com/`의 CDN에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-369">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="f7948-370">표준 시간대 데이터</span><span class="sxs-lookup"><span data-stu-id="f7948-370">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="f7948-371">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="f7948-371">`Promise<Response>`.</span></span> <span data-ttu-id="f7948-372">헤더에서 `integrity` 매개 변수를 전달하여 기본 무결성 검사 동작을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-372">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="f7948-373">다음 예제(`wwwroot/index.html`)에서는 사용자 지정 HTTP 헤더를 아웃바운드 요청에 추가하고 `integrity` 매개 변수를 `fetch` 호출에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-373">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="f7948-374">`null`/`undefined`, 기본 로드 동작이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-374">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="f7948-375">외부 소스는 브라우저에서 원본 간 리소스 로드를 허용하는 데 필요한 CORS 헤더를 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-375">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="f7948-376">일반적으로 CDN은 필요한 헤더를 기본적으로 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-376">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="f7948-377">사용자 지정 동작의 경우에만 형식을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-377">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="f7948-378">`loadBootResource`에 지정되지 않은 형식은 기본 로드 동작에 따라 프레임워크에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-378">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="f7948-379">DLL 파일의 파일 이름 확장명 변경</span><span class="sxs-lookup"><span data-stu-id="f7948-379">Change the filename extension of DLL files</span></span>

<span data-ttu-id="f7948-380">앱 게시 `.dll` 파일의 파일 이름 확장명을 변경해야 하는 경우 이 섹션의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-380">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="f7948-381">앱을 게시한 후 셸 스크립트 또는 DevOps 빌드 파이프라인을 사용하여 다른 파일 확장명을 사용하도록 `.dll` 파일의 이름을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-381">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="f7948-382">앱 게시 출력(예: `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`)의 `wwwroot` 디렉터리에 있는 `.dll` 파일을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-382">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="f7948-383">다음 예제에서는 `.bin` 파일 확장명을 사용하도록 `.dll` 파일의 이름이 바뀝니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-383">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="f7948-384">Windows에서:</span><span class="sxs-lookup"><span data-stu-id="f7948-384">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="f7948-385">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-385">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="f7948-386">Linux 또는 macOS에서:</span><span class="sxs-lookup"><span data-stu-id="f7948-386">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="f7948-387">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-387">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="f7948-388">`.bin` 이외의 다른 파일 확장명을 사용하려면 이전 명령에서 `.bin`을 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-388">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="f7948-389">압축된 `blazor.boot.json.gz` 및 `blazor.boot.json.br` 파일을 처리하려면 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-389">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="f7948-390">압축된 `blazor.boot.json.gz` 및 `blazor.boot.json.br` 파일을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-390">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="f7948-391">압축은 이 접근 방법으로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-391">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="f7948-392">업데이트된 `blazor.boot.json` 파일을 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-392">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="f7948-393">위의 지침은 서비스 작업자 자산을 사용 중인 경우에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-393">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="f7948-394">`wwwroot/service-worker-assets.js.br` 및 `wwwroot/service-worker-assets.js.gz`를 제거하거나 다시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-394">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="f7948-395">그렇지 않으면 브라우저에서 파일 무결성 검사가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-395">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="f7948-396">다음 Windows 예제에서는 프로젝트의 루트에 배치된 PowerShell 스크립트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-396">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="f7948-397">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="f7948-397">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="f7948-398">서비스 작업자 자산도 사용 중인 경우 다음 명령을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-398">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="f7948-399">프로젝트 파일에서 스크립트는 앱을 게시한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7948-399">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="f7948-400">동일한 어셈블리의 이름을 바꾸고 로드를 지연하는 경우 <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7948-400">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>
