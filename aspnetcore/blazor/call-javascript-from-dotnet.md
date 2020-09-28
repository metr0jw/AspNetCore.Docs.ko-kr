---
title: ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출
author: guardrex
description: Blazor 앱의 .NET 메서드에서 JavaScript 함수를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: a62462e3a0a2366a8662573ada5d2e7589c14c0d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722477"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="ac4f0-103">ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="ac4f0-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="ac4f0-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ac4f0-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ac4f0-105">Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="ac4f0-106">이러한 시나리오를 *JavaScript 상호 운용성*(*JS interop*)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="ac4f0-107">이 문서에서는 .NET에서 JavaScript 함수를 호출하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="ac4f0-108">JavaScript에서 .NET 메서드를 호출하는 방법에 대한 자세한 내용은 <xref:blazor/call-dotnet-from-javascript>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="ac4f0-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac4f0-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ac4f0-110">.NET에서 JavaScript를 호출하려면 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="ac4f0-111">JS interop 호출을 실행하려면 구성 요소에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="ac4f0-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>는 원하는 수의 JSON 직렬화 가능 인수와 함께 호출하려는 JavaScript 함수에 대한 식별자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="ac4f0-113">함수 식별자는 전역 범위(`window`)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="ac4f0-114">`window.someScope.someFunction`을 호출하려는 경우 식별자는 `someScope.someFunction`입니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="ac4f0-115">호출되기 전에 함수를 등록할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="ac4f0-116">반환 형식 `T` 또한 JSON 직렬화 가능해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="ac4f0-117">`T`는 반환되는 JSON 형식에 가장 잘 매핑되는 .NET 형식과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="ac4f0-118">사전 렌더링을 사용하도록 설정한 Blazor Server 앱의 경우 초기 사전 렌더링 중에 JavaScript를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="ac4f0-119">JavaScript interop 호출은 브라우저와의 연결이 설정될 때까지 지연됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="ac4f0-120">자세한 내용은 [Blazor Server 앱이 사전 렌더링 중인 경우 검색](#detect-when-a-blazor-server-app-is-prerendering) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="ac4f0-121">다음 예제는 JavaScript 기반 디코더인 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="ac4f0-122">이 예제에서는 C# 메서드에서 개발자 코드의 요구 사항을 기존 JavaScript API로 오프로드하는 JavaScript 함수를 호출하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="ac4f0-123">JavaScript 함수는 C# 메서드에서 바이트 배열을 수신하고, 배열을 디코딩하고, 표시를 위해 구성 요소에 텍스트를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="ac4f0-124">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `TextDecoder`를 사용하여 전달된 배열을 디코딩하고 디코딩된 값을 반환하는 JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="ac4f0-125">앞의 예제에 표시된 코드와 같은 JavaScript 코드는 스크립트 파일에 대한 참조를 사용하여 JavaScript 파일(`.js`)에서 로드할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="ac4f0-126">다음 구성 요소는</span><span class="sxs-lookup"><span data-stu-id="ac4f0-126">The following component:</span></span>

* <span data-ttu-id="ac4f0-127">구성 요소 단추( **`Convert Array`** )가 선택된 경우 `JSRuntime`을 사용하여 `convertArray` JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="ac4f0-128">JavaScript 함수를 호출한 후에는 전달된 배열이 문자열로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="ac4f0-129">이 문자열은 표시를 위해 구성 요소로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="ac4f0-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="ac4f0-130">IJSRuntime</span></span>

<span data-ttu-id="ac4f0-131"><xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용하려면 다음 방법 중 하나를 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="ac4f0-132">Razor 구성 요소(`.razor`)에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="ac4f0-133">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="ac4f0-134">이 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출되며 값을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="ac4f0-135"><xref:Microsoft.JSInterop.IJSRuntime> 추상화를 클래스(`.cs`)에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="ac4f0-136">`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="ac4f0-137">이 함수는 `JSRuntime.InvokeAsync`를 사용하여 호출되며 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="ac4f0-138">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)를 사용하여 동적 콘텐츠를 생성하려면 `[Inject]` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="ac4f0-139">이 항목과 함께 제공되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 수신하고 환영 메시지를 표시하기 위해 DOM과 상호 작용하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="ac4f0-140">`showPrompt`: 사용자 입력(사용자 이름)을 수락하라는 메시지를 표시하고 호출자에게 이름을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="ac4f0-141">`displayWelcome`: 호출자의 환영 메시지를 `id`가 `welcome`인 DOM 개체에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="ac4f0-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="ac4f0-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="ac4f0-143">JavaScript 파일을 참조하는 `<script>` 태그를 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="ac4f0-144">`wwwroot/index.html`(Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="ac4f0-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="ac4f0-145">`Pages/_Host.cshtml`(Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="ac4f0-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="ac4f0-146">`<script>` 태그를 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="ac4f0-147">.NET 메서드는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>을 호출하여 `exampleJsInterop.js` 파일의 JavaScript 함수와 상호 운용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="ac4f0-148"><xref:Microsoft.JSInterop.IJSRuntime> 추상화는 Blazor Server 시나리오를 허용하기 위해 비동기적으로 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="ac4f0-149">앱이 Blazor WebAssembly 앱이고 JavaScript 함수를 동기적으로 호출하려는 경우에는 <xref:Microsoft.JSInterop.IJSInProcessRuntime>으로 다운캐스트하고 대신 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="ac4f0-150">대부분의 JS interop 라이브러리는 비동기 API를 사용하여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ac4f0-151">표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정하려면 [Blazor JavaScript 격리 및 개체 참조](#blazor-javascript-isolation-and-object-references) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-151">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="ac4f0-152">샘플 앱에는 JS interop를 시연하기 위한 구성 요소가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="ac4f0-153">이 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-153">The component:</span></span>

* <span data-ttu-id="ac4f0-154">JavaScript 프롬프트를 통해 사용자 입력을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="ac4f0-155">처리를 위해 구성 요소에 텍스트를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="ac4f0-156">DOM과 상호 작용하여 환영 메시지를 표시하는 두 번째 JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="ac4f0-157">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="ac4f0-157">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="ac4f0-158">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="ac4f0-158">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="ac4f0-159">구성 요소의 **`Trigger JavaScript Prompt`** 단추를 선택하여 `TriggerJsPrompt`가 실행되면 `wwwroot/exampleJsInterop.js` 파일에 제공된 JavaScript `showPrompt` 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-159">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="ac4f0-160">`showPrompt` 함수는 HTML로 인코딩되고 구성 요소로 반환되는 사용자 입력(사용자 이름)을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-160">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="ac4f0-161">이 구성 요소는 지역 변수, `name`에 사용자 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-161">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="ac4f0-162">`name`에 저장된 문자열은 환영 메시지에 통합됩니다.그런 후 이 메시지는 JavaScript 함수, `displayWelcome`에 전달되고, 이 함수는 환영 메시지를 제목 태그로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-162">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="ac4f0-163">Void JavaScript 함수 호출</span><span class="sxs-lookup"><span data-stu-id="ac4f0-163">Call a void JavaScript function</span></span>

<span data-ttu-id="ac4f0-164">[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)를 반환하는 JavaScript 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-164">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="ac4f0-165">Blazor Server 앱을 미리 렌더링 중인 경우 검색</span><span class="sxs-lookup"><span data-stu-id="ac4f0-165">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="ac4f0-166">요소에 대한 참조 캡처</span><span class="sxs-lookup"><span data-stu-id="ac4f0-166">Capture references to elements</span></span>

<span data-ttu-id="ac4f0-167">일부 JS interop 시나리오에는 HTML 요소에 대한 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-167">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="ac4f0-168">예를 들어, UI 라이브러리에는 초기화를 위해 요소 참조가 필요하거나, `focus` 또는 `play`와 같은 요소에서 명령 같은 API를 호출해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-168">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="ac4f0-169">다음 방법을 사용하여 구성 요소의 HTML 요소에 대한 참조를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-169">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="ac4f0-170">HTML 요소에 `@ref` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-170">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="ac4f0-171">해당 이름이 `@ref` 특성 값과 일치하는 <xref:Microsoft.AspNetCore.Components.ElementReference> 형식의 필드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-171">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="ac4f0-172">다음 예에서는 `username` `<input>` 요소에 대한 참조를 캡처하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-172">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="ac4f0-173">요소 참조만 사용하여 Blazor와 상호 작용하지 않는 빈 요소의 내용을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-173">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="ac4f0-174">이 시나리오는 타사 API가 요소에 콘텐츠를 제공하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-174">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="ac4f0-175">Blazor는 요소와 상호 작용하지 않으므로 요소의 Blazor 표시와 DOM 간에 충돌이 발생할 가능성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-175">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="ac4f0-176">다음 예제에서는 Blazor가 DOM과 상호 작용하여 이 요소의 목록 항목(`<li>`)을 채우기 때문에 순서가 지정되지 않은 목록(`ul`)의 콘텐츠를 변경하는 것은 *위험*합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-176">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="ac4f0-177">JS interop이 요소 `MyList`의 내용을 변경하고 Blazor가 요소에 diff를 적용하려고 하면 diff는 DOM과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-177">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="ac4f0-178">.NET 코드와 관련하여 <xref:Microsoft.AspNetCore.Components.ElementReference>는 불투명 핸들입니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-178">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="ac4f0-179"><xref:Microsoft.AspNetCore.Components.ElementReference>으로 수행할 수 있는 *유일한* 작업이 JS interop을 통해 JavaScript 코드에 전달하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-179">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="ac4f0-180">이렇게 하면 JavaScript 쪽 코드는 일반적인 DOM API에서 사용할 수 있는 `HTMLElement` 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-180">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="ac4f0-181">예를 들어, 다음 코드는 요소에 포커스를 설정할 수 있도록 하는 .NET 확장 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-181">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="ac4f0-182">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="ac4f0-182">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="ac4f0-183">값을 반환하지 않는 JavaScript 함수를 호출하려면 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-183">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ac4f0-184">다음 코드는 캡처된 <xref:Microsoft.AspNetCore.Components.ElementReference>로 이전 JavaScript 함수를 호출하여 사용자 이름 입력에 포커스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-184">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="ac4f0-185">확장 메서드를 사용하려면 <xref:Microsoft.JSInterop.IJSRuntime> 인스턴스를 수신하는 정적 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-185">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="ac4f0-186">`Focus` 메서드는 개체에서 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-186">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="ac4f0-187">다음 예제에서는 `Focus` 메서드를 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-187">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="ac4f0-188">`username` 변수는 구성 요소가 렌더링된 후에만 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-188">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="ac4f0-189">JavaScript 코드에 채워지지 않은 <xref:Microsoft.AspNetCore.Components.ElementReference>가 전달되면 JavaScript 코드는 `null` 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-189">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="ac4f0-190">구성 요소에서 렌더링을 완료한 후 요소에 초기 포커스를 설정하기 위해 요소 참조를 조작하려면 [`OnAfterRenderAsync` 또는 `OnAfterRender` 구성 요소 수명 주기 메서드](xref:blazor/components/lifecycle#after-component-render)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-190">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="ac4f0-191">제네릭 형식으로 작업하고 값을 반환하는 경우 <xref:System.Threading.Tasks.ValueTask%601>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-191">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="ac4f0-192">`GenericMethod`는 형식이 있는 개체에 대해 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-192">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="ac4f0-193">다음 예제에서는 `GenericMethod`가 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-193">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="ac4f0-194">구성 요소 간 참조 요소</span><span class="sxs-lookup"><span data-stu-id="ac4f0-194">Reference elements across components</span></span>

<span data-ttu-id="ac4f0-195"><xref:Microsoft.AspNetCore.Components.ElementReference>는 구성 요소의 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 메서드에서만 유효한 것으로 보장되며 요소 참조는 `struct`이므로 구성 요소 간에 요소 참조를 전달할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-195">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="ac4f0-196">부모 구성 요소는 다른 구성 요소에서 요소 참조를 사용할 수 있도록 하기 위해 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-196">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="ac4f0-197">자식 구성 요소가 콜백을 등록할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-197">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="ac4f0-198">전달된 요소 참조를 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 이벤트 중에 등록된 콜백을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-198">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="ac4f0-199">간접적으로 이 방법을 사용하면 자식 구성 요소가 부모의 요소 참조와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-199">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="ac4f0-200">다음 Blazor WebAssembly 예제에서 이 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-200">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="ac4f0-201">`wwwroot/index.html`의 `<head>`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-201">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="ac4f0-202">`wwwroot/index.html`의 `<body>`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-202">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="ac4f0-203">`Pages/Index.razor`(부모 구성 요소):</span><span class="sxs-lookup"><span data-stu-id="ac4f0-203">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="ac4f0-204">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="ac4f0-204">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="ac4f0-205">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="ac4f0-205">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="ac4f0-206">`Shared/SurveyPrompt.razor`(자식 구성 요소):</span><span class="sxs-lookup"><span data-stu-id="ac4f0-206">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="ac4f0-207">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="ac4f0-207">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="ac4f0-208">자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="ac4f0-208">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="ac4f0-209">JS interop 호출 강화</span><span class="sxs-lookup"><span data-stu-id="ac4f0-209">Harden JS interop calls</span></span>

<span data-ttu-id="ac4f0-210">JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-210">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="ac4f0-211">기본적으로 Blazor Server 앱은 서버에서 1분 후에 JS interop 호출 시간을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-211">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="ac4f0-212">앱에서 좀 더 적극적인 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용하여 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-212">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="ac4f0-213">`Startup.ConfigureServices`에서 전역적으로 시간 제한을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-213">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="ac4f0-214">구성 요소 코드의 호출마다 단일 호출은 다음과 같이 제한 시간을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-214">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="ac4f0-215">리소스 소모에 대한 자세한 내용은 <xref:blazor/security/server/threat-mitigation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-215">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="ac4f0-216">순환 개체 참조 방지</span><span class="sxs-lookup"><span data-stu-id="ac4f0-216">Avoid circular object references</span></span>

<span data-ttu-id="ac4f0-217">순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-217">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="ac4f0-218">.NET 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="ac4f0-218">.NET method calls.</span></span>
* <span data-ttu-id="ac4f0-219">반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="ac4f0-219">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="ac4f0-220">자세한 내용은 다음 문제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-220">For more information, see the following issues:</span></span>

* [<span data-ttu-id="ac4f0-221">순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="ac4f0-221">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="ac4f0-222">제안: 직렬화할 때 순환 참조를 처리하는 메커니즘 추가(dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="ac4f0-222">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="ac4f0-223">Blazor JavaScript 격리 및 개체 참조</span><span class="sxs-lookup"><span data-stu-id="ac4f0-223">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="ac4f0-224">Blazor에서는 표준 [JavaScript 모듈](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)에서 JavaScript 격리를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-224">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="ac4f0-225">JavaScript 격리는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-225">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="ac4f0-226">가져온 JavaScript는 전역 네임스페이스를 더 이상 오염시키지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-226">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="ac4f0-227">라이브러리 및 구성 요소의 소비자는 관련 JavaScript를 가져올 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-227">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="ac4f0-228">예를 들어 다음 JavaScript 모듈은 브라우저 프롬프트를 표시하기 위해 JavaScript 함수를 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-228">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="ac4f0-229">위의 JavaScript 모듈을 .NET 라이브러리에 정적 웹 자산(`wwwroot/exampleJsInterop.js`)으로 추가한 다음, <xref:Microsoft.JSInterop.IJSRuntime> 서비스를 사용하여 .NET 코드로 모듈을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-229">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="ac4f0-230">다음 예제에서는 서비스가 `jsRuntime`(표시되지 않음)으로 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-230">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="ac4f0-231">위 예제에서 `import` 식별자는 JavaScript 모듈을 가져오기 위해 특별히 사용되는 특수 식별자입니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-231">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="ac4f0-232">안정적인 정적 웹 자산 경로 `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`를 사용하여 모듈을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-232">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="ac4f0-233">`{LIBRARY NAME}` 자리 표시자는 라이브러리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-233">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="ac4f0-234">`{PATH UNDER WWWROOT}` 자리 표시자는 `wwwroot` 아래의 스크립트에 대한 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-234">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="ac4f0-235"><xref:Microsoft.JSInterop.IJSRuntime>은 모듈을 `JSObjectReference`로 가져와 .NET 코드에서 JavaScript 개체에 대한 참조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-235"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="ac4f0-236">`JSObjectReference`를 사용하여 모듈에서 내보낸 JavaScript 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ac4f0-236">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ac4f0-237">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ac4f0-237">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="ac4f0-238">InteropComponent.razor 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)</span><span class="sxs-lookup"><span data-stu-id="ac4f0-238">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="ac4f0-239">Blazor Server 앱에서 대량 데이터 전송 수행</span><span class="sxs-lookup"><span data-stu-id="ac4f0-239">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
