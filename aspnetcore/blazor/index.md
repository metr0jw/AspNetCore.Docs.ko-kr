---
title: ASP.NET Core Blazor 소개
author: guardrex
description: ASP.NET Core 앱에서 .NET을 사용하여 대화형 클라이언트 쪽 웹 UI를 빌드하는 방법인 ASP.NET Core Blazor를 살펴봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
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
uid: blazor/index
ms.openlocfilehash: ad543087243658f09a23e4f6d957d0c6aa77b361
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014180"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="3a8a6-103">ASP.NET Core Blazor 소개</span><span class="sxs-lookup"><span data-stu-id="3a8a6-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="3a8a6-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a8a6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3a8a6-105">*Blazor에 오신 것을 환영합니다!*</span><span class="sxs-lookup"><span data-stu-id="3a8a6-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="3a8a6-106">Blazor는 .NET을 사용하여 대화형 클라이언트 쪽 웹 UI를 빌드하기 위한 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="3a8a6-107">JavaScript 대신 C#을 사용하여 풍부한 대화형 UI를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="3a8a6-108">.NET에서 작성된 서버 쪽 및 클라이언트 쪽 앱 논리를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="3a8a6-109">모바일 브라우저를 포함한 광범위한 브라우저 지원을 위해 UI를 HTML 및 CSS로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="3a8a6-110">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)와 같은 최신 호스트 플랫폼과 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="3a8a6-111">클라이언트 쪽 웹 개발에 .NET을 사용하면 다음과 같은 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="3a8a6-112">JavaScript 대신 C#으로 코드를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="3a8a6-113">.NET 라이브러리의 기존 .NET 에코시스템을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="3a8a6-114">서버 및 클라이언트에서 앱 논리를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="3a8a6-115">.NET의 성능, 안정성 및 보안을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="3a8a6-116">Windows, Linux 및 macOS에서 Visual Studio를 사용하여 생산성을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="3a8a6-117">안정적이고, 기능이 풍부하고, 사용하기 쉬운 공통 언어, 프레임워크 및 도구 세트를 기반으로 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="3a8a6-118">구성 요소</span><span class="sxs-lookup"><span data-stu-id="3a8a6-118">Components</span></span>

<span data-ttu-id="3a8a6-119">Blazor 앱은 *구성 요소*를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="3a8a6-120">Blazor의 구성 요소는 페이지, 대화 상자 또는 데이터 입력 양식과 같은 UI의 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="3a8a6-121">구성 요소는 .NET 어셈블리에 기본 제공되는 .NET 클래스이며 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="3a8a6-122">유연한 UI 렌더링 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="3a8a6-123">사용자 이벤트를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-123">Handle user events.</span></span>
* <span data-ttu-id="3a8a6-124">중첩 및 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-124">Can be nested and reused.</span></span>
* <span data-ttu-id="3a8a6-125">[Razor 클래스 라이브러리](xref:razor-pages/ui-class) 또는 [NuGet 패키지](/nuget/what-is-nuget)로 공유 및 배포될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="3a8a6-126">구성 요소 클래스는 일반적으로 `.razor` 파일 확장자를 가진 [Razor](xref:mvc/views/razor) 태그 페이지 형식으로 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="3a8a6-127">Blazor의 구성 요소는 공식적으로 *Razor 구성 요소*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="3a8a6-128">Razor는 개발자 생산성을 위해 설계된 HTML 태그와 C# 코드를 결합하는 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="3a8a6-129">Razor를 사용하여 [IntelliSense](/visualstudio/ide/using-intellisense) 지원으로 동일한 파일에서 HTML 태그와 C# 사이를 전환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="3a8a6-130">Razor Pages 및 MVC도 Razor를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="3a8a6-131">요청/응답 모델을 중심으로 빌드된 Razor Pages 및 MVC와는 달리, 구성 요소는 특별히 클라이언트 쪽 UI 논리 및 컴퍼지션에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="3a8a6-132">다음 Razor 태그는 다른 구성 요소 내에 중첩될 수 있는 구성 요소(`Dialog.razor`)를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-132">The following Razor markup demonstrates a component (`Dialog.razor`), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="3a8a6-133">대화 상자의 본문 콘텐츠(`ChildContent`) 및 제목(`Title`)은 해당 UI에서 이 구성 요소를 사용하는 구성 요소에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="3a8a6-134">`OnYes`는 단추의 `onclick` 이벤트를 통해 트리거되는 C# 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="3a8a6-135">Blazor는 UI 컴퍼지션에 자연스러운 HTML 태그를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-135">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="3a8a6-136">HTML 요소는 구성 요소를 지정하고 태그 특성은 구성 요소의 속성에 값을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="3a8a6-137">다음 예제에서는 `Index` 구성 요소가 `Dialog` 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="3a8a6-138">`ChildContent` 및 `Title`은 `<Dialog>` 요소의 특성 및 콘텐츠에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="3a8a6-139">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="3a8a6-139">`Pages/Index.razor`:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="3a8a6-140">대화 상자는 브라우저에서 부모(`Pages/Index.razor`)가 액세스될 때 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-140">The dialog is rendered when the parent (`Pages/Index.razor`) is accessed in a browser:</span></span>

![브라우저에서 렌더링되는 Dialog 구성 요소](index/_static/dialog.png)

<span data-ttu-id="3a8a6-142">이 구성 요소를 앱에서 사용할 경우 [Visual Studio](/visualstudio/ide/using-intellisense) 및 [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense)의 IntelliSense는 구문 및 매개 변수 완성을 통해 개발 속도를 높입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="3a8a6-143">구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용되는 *렌더링 크리*라는 브라우저 DOM(문서 개체 모델)의 메모리 내 표시로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="3a8a6-144">Blazor WebAssembly는 .NET을 사용하여 대화형 클라이언트 쪽 웹앱을 빌드하기 위한 단일 페이지 앱 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-144">Blazor WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="3a8a6-145">Blazor WebAssembly는 플러그인이나 코드 소스 간 컴파일 없이 개방형 웹 표준을 사용하며, 모바일 브라우저를 포함한 모든 최신 웹 브라우저에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-145">Blazor WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="3a8a6-146">웹 브라우저 내에서 .NET 코드를 실행하는 것은 [WebAssembly](https://webassembly.org)(약식 `wasm`)를 통해 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="3a8a6-147">WebAssembly는 빠른 다운로드와 최대 실행 속도를 위해 최적화된 압축 바이트 코드 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="3a8a6-148">WebAssembly는 개방형 웹 표준이고 플러그 인 없이 웹 브라우저에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="3a8a6-149">WebAssembly 코드는 JavaScript를 통해 브라우저의 전체 기능에 액세스할 수 있고, 이를 *JavaScript 상호 운용성*(또는 *JavaScript interop*)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="3a8a6-150">브라우저에서 WebAssembly를 통해 실행된 .NET 코드는 브라우저의 JavaScript 샌드박스에서 클라이언트 컴퓨터의 악의적 활동에 대해 제공하는 보호를 사용하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor WebAssembly에서는 WebAssembly와 함께 브라우저에서 .NET 코드를 실행합니다.](index/_static/blazor-webassembly.png)

<span data-ttu-id="3a8a6-152">Blazor WebAssembly 앱이 빌드되고 브라우저에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="3a8a6-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="3a8a6-153">C# 코드 파일과 Razor 파일은 .NET 어셈블리로 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="3a8a6-154">어셈블리와 .NET 런타임이 브라우저에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="3a8a6-155">Blazor WebAssembly는 .NET 런타임을 부트스트랩하고 앱의 어셈블리를 로드하도록 런타임을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-155">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="3a8a6-156">Blazor WebAssembly 런타임은 JavaScript interop을 사용하여 DOM 조작 및 브라우저 API 호출을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="3a8a6-157">게시된 앱의 크기인 해당 페이로드 크기는 앱의 유용성에 중요한 성능 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="3a8a6-158">대규모 앱은 브라우저에 다운로드되는 데 비교적 오랜 시간이 걸리므로 사용자 환경이 감소됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="3a8a6-159">Blazor WebAssembly는 페이로드 크기를 최적화하여 다운로드 시간을 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-159">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="3a8a6-160">[IL(중간 언어) 링커](xref:blazor/host-and-deploy/configure-linker)에서 게시하면 사용되지 않는 코드가 앱에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="3a8a6-161">HTTP 응답이 압축됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="3a8a6-162">.NET 런타임 및 어셈블리가 브라우저에 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## Blazor Server

<span data-ttu-id="3a8a6-163">Blazor는 UI 업데이트 적용 방법에서 구성 요소 렌더링 논리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-163">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="3a8a6-164">Blazor Server에서는 ASP.NET Core 앱의 서버에서 Razor 구성 요소를 호스팅할 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-164">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="3a8a6-165">UI 업데이트는 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-165">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="3a8a6-166">런타임은 브라우저에서 서버로 UI 이벤트 전송을 처리하고, 구성 요소를 실행한 후 서버에서 보낸 UI 업데이트를 브라우저에 다시 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-166">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="3a8a6-167">Blazor Server에서 브라우저와 통신하기 위해 사용하는 연결은 JavaScript interop 호출을 처리하는 데도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-167">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor Server에서 서버의 .NET 코드를 실행하고 SignalR 연결을 통해 클라이언트의 문서 개체 모델과 상호 작용합니다.](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="3a8a6-169">JavaScript interop</span><span class="sxs-lookup"><span data-stu-id="3a8a6-169">JavaScript interop</span></span>

<span data-ttu-id="3a8a6-170">타사 JavaScript 라이브러리 및 브라우저 API에 대한 액세스를 필요로 하는 앱의 경우 구성 요소는 JavaScript와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-170">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="3a8a6-171">구성 요소는 JavaScript가 사용할 수 있는 모든 라이브러리 또는 API를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-171">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="3a8a6-172">C# 코드는 JavaScript 코드로 호출할 수 있으며, JavaScript 코드는 C# 코드로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-172">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="3a8a6-173">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-173">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="3a8a6-174">코드 공유 및 .NET Standard</span><span class="sxs-lookup"><span data-stu-id="3a8a6-174">Code sharing and .NET Standard</span></span>

<span data-ttu-id="3a8a6-175">Blazor는 Blazor 프로젝트에서 .NET Standard 2.1 또는 이전 사양을 준수하는 라이브러리를 참조할 수 있도록 하는 [.NET Standard 2.1](/dotnet/standard/net-standard)을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-175">Blazor implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="3a8a6-176">.NET Standard는 .NET 구현에서 공통적인 .NET API의 공식 사양입니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-176">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="3a8a6-177">.NET Standard 클래스 라이브러리는 Blazor, .NET Framework, .NET Core, Xamarin, Mono, Unity 등 다양한 .NET 플랫폼 간에 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-177">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="3a8a6-178">웹 브라우저 내에서 적용되지 않는 API(예: 파일 시스템 액세스, 소켓 열기 및 스레딩)에서 <xref:System.PlatformNotSupportedException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="3a8a6-178">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a8a6-179">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3a8a6-179">Additional resources</span></span>

* [<span data-ttu-id="3a8a6-180">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3a8a6-180">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="3a8a6-181">C# 가이드</span><span class="sxs-lookup"><span data-stu-id="3a8a6-181">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="3a8a6-182">HTML</span><span class="sxs-lookup"><span data-stu-id="3a8a6-182">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="3a8a6-183">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) 커뮤니티 링크</span><span class="sxs-lookup"><span data-stu-id="3a8a6-183">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
