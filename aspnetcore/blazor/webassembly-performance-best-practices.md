---
title: ASP.NET Core Blazor WebAssembly 성능 모범 사례
author: pranavkm
description: ASP.NET Core Blazor WebAssembly 앱의 성능을 높이고 일반적인 성능 문제를 방지하기 위한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 819947be90e7f09c7ba853df1af1f3c7066c0219
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625819"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="da7ea-103">ASP.NET Core Blazor WebAssembly 성능 모범 사례</span><span class="sxs-lookup"><span data-stu-id="da7ea-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="da7ea-104">작성자: [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="da7ea-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="da7ea-105">이 문서에서는 ASP.NET Core Blazor WebAssembly 성능 모범 사례를 위한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="da7ea-106">불필요한 구성 요소 렌더링 방지</span><span class="sxs-lookup"><span data-stu-id="da7ea-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="da7ea-107">Blazor의 diff 알고리즘은 알고리즘에서 구성 요소가 변경되지 않았음을 감지하는 경우 구성 요소의 렌더링을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="da7ea-108">구성 요소 렌더링을 세밀하게 제어하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>를 재정의하세요.</span><span class="sxs-lookup"><span data-stu-id="da7ea-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="da7ea-109">초기 렌더링 이후 변경되지 않는 UI 전용 구성 요소를 작성하는 경우에는 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 `false`를 반환하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="da7ea-110">대부분의 앱에는 세밀한 제어가 필요하지 않지만 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>는 UI 이벤트에 응답하는 구성 요소를 선택적으로 렌더링하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="da7ea-111">수많은 구성 요소가 렌더링되는 시나리오에서는 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 사용하는 것이 중요할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="da7ea-112">그리드의 한 셀에 있는 하나의 구성 요소에서 <xref:Microsoft.AspNetCore.Components.EventCallback>을 사용하면 그리드에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출되는 그리드를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="da7ea-113"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하면 모든 자식 구성 요소가 다시 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="da7ea-114">적은 수의 셀만 다시 렌더링해야 하는 경우에는 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 사용하여 불필요한 렌더링으로 인한 성능 저하를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="da7ea-115">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="da7ea-115">In the following example:</span></span>

* <span data-ttu-id="da7ea-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 재정의되어 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 필드의 값으로 설정됩니다. 이 필드는 구성 요소가 로드될 때 초기 값으로 `false`를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="da7ea-117">단추가 선택되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 `true`로 설정되어 구성 요소가 업데이트된 `currentCount`를 사용하여 다시 렌더링되도록 강제합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="da7ea-118">다시 렌더링이 이루어진 직후에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>가 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>의 값을 다시 `false`로 설정하여 다음번에 단추가 선택될 때까지 추가로 다시 렌더링이 이루어지지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="da7ea-119">자세한 내용은 <xref:blazor/components/lifecycle#after-component-render>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="da7ea-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="da7ea-120">재사용 가능한 조각 가상화</span><span class="sxs-lookup"><span data-stu-id="da7ea-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="da7ea-121">구성 요소는 코드와 태그의 재사용 가능한 조각을 생성하는 편리한 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="da7ea-122">일반적으로 앱의 요구 사항에 가장 잘 맞는 개별 구성 요소를 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="da7ea-123">한 가지 주의할 점은 각 추가 자식 구성 요소가 부모 구성 요소를 렌더링하는 데 소요되는 총 시간에 기여한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="da7ea-124">대부분의 앱에서 추가 오버헤드는 무시해도 되는 정도입니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="da7ea-125">많은 수의 구성 요소를 생성하는 앱은 렌더링된 구성 요소의 수를 제한하는 등 처리 오버헤드를 줄이기 위한 전략을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="da7ea-126">예를 들어 구성 요소를 포함하는 수백 개의 행을 렌더링하는 그리드 또는 목록은 렌더링할 때 프로세서를 많이 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="da7ea-127">어떤 시점에도 구성 요소의 하위 집합만 렌더링되도록 그리드 또는 목록 레이아웃을 가상화하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="da7ea-128">구성 요소 하위 집합 렌더링 예제는 [`Virtualization` 샘플 앱(aspnet/samples GitHub 리포지토리)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)에서 다음 구성 요소를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="da7ea-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="da7ea-129">`Virtualize` 구성 요소([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): <xref:Microsoft.AspNetCore.Components.ComponentBase>를 구현하는 C#으로 작성된 구성 요소로, 사용자 스크롤을 기준으로 날씨 데이터 행 세트를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="da7ea-130">`FetchData` 구성 요소([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): `Virtualize` 구성 요소를 사용하여 한 번에 25개의 날씨 데이터 행을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="da7ea-131">JavaScript interop의 데이터 마샬링 방지</span><span class="sxs-lookup"><span data-stu-id="da7ea-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="da7ea-132">Blazor WebAssembly에서 JS(JavaScript) interop 호출은 WebAssembly-JS 경계를 트래버스해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="da7ea-133">이 두 가지 컨텍스트에서 콘텐츠를 직렬화 및 역직렬화하는 것은 앱에서 처리 오버헤드를 발생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="da7ea-134">JS interop 호출이 자주 발생하면 성능이 저하되는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="da7ea-135">경계 주변에서 데이터 마샬링을 줄이려면 앱이 여러 개의 작은 페이로드를 하나의 큰 페이로드로 통합하여 WebAssembly와 JS 간에 이루어지는 다량의 컨텍스트 전환을 방지할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="da7ea-136">System.Text.Json 사용</span><span class="sxs-lookup"><span data-stu-id="da7ea-136">Use System.Text.Json</span></span>

<span data-ttu-id="da7ea-137">Blazor의 JS interop 구현은 메모리 할당이 작은 고성능 JSON serialization 라이브러리인 <xref:System.Text.Json>에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-137">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="da7ea-138"><xref:System.Text.Json>을 사용해도 하나 이상의 대체 JSON 라이브러리를 추가하는 것에 비해 추가 앱 페이로드 크기가 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="da7ea-139">마이그레이션 지침은 [`Newtonsoft.Json`에서 `System.Text.Json`으로 마이그레이션하는 방법](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="da7ea-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="da7ea-140">가능한 경우 동기식 및 역 마샬링된 JS interop API 사용</span><span class="sxs-lookup"><span data-stu-id="da7ea-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="da7ea-141">Blazor WebAssembly는 Blazor Server 앱에서 사용 가능한 단일 버전에 더해 두 개의 추가 <xref:Microsoft.JSInterop.IJSRuntime> 버전을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-141">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="da7ea-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime>을 사용하면 JS interop 호출을 동기식으로 호출할 수 있습니다. 이렇게 하면 비동기식 버전에 비해 오버헤드가 덜 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="da7ea-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>에서는 역 마샬링된 JS interop 호출이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="da7ea-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>을 사용하는 것이 JS interop 접근 방식 중에서 오버헤드가 가장 덜 발생하지만, 이러한 API와 상호 작용하는 데 필요한 JavaScript API는 현재 문서화되지 않은 상태이며 이후 릴리스에서 호환성이 손상되는 변경이 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="da7ea-145">앱 크기 줄이기</span><span class="sxs-lookup"><span data-stu-id="da7ea-145">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="da7ea-146">IL(중간 언어) 연결</span><span class="sxs-lookup"><span data-stu-id="da7ea-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="da7ea-147">[Blazor WebAssembly 앱을 연결](xref:blazor/host-and-deploy/configure-linker)하면 앱의 이진 파일에서 사용되지 않는 코드를 잘라내어 앱 크기를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="da7ea-148">기본적으로 링커는 `Release` 구성에서 빌드할 때만 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-148">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="da7ea-149">이 기능의 이점을 활용하려면 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령에서 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 옵션을 `Release`로 설정하여 배포를 위해 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="da7ea-150">어셈블리 지연 로드</span><span class="sxs-lookup"><span data-stu-id="da7ea-150">Lazy load assemblies</span></span>

<span data-ttu-id="da7ea-151">어셈블리가 경로에 필요한 경우 런타임에 어셈블리를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-151">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="da7ea-152">자세한 내용은 <xref:blazor/webassembly-lazy-load-assemblies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="da7ea-152">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="da7ea-153">압축</span><span class="sxs-lookup"><span data-stu-id="da7ea-153">Compression</span></span>

<span data-ttu-id="da7ea-154">Blazor WebAssembly 앱이 게시될 때 게시하는 도중에 출력을 정적으로 압축하여 앱의 크기를 줄이고 런타임 압축의 오버헤드를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-154">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="da7ea-155">Blazor는 콘텐츠 협상을 수행하고 정적으로 압축된 파일을 처리하기 위해 서버에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-155">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="da7ea-156">앱이 배포된 후에는 앱이 압축된 파일을 처리하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-156">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="da7ea-157">브라우저의 개발자 도구에서 네트워크 탭을 조사하여 파일이 `Content-Encoding: br` 또는 `Content-Encoding: gz`로 처리되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-157">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="da7ea-158">호스트가 압축된 파일을 처리하지 않는 경우 <xref:blazor/host-and-deploy/webassembly#compression>의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="da7ea-158">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="da7ea-159">사용되지 않는 기능을 사용하지 않도록 설정</span><span class="sxs-lookup"><span data-stu-id="da7ea-159">Disable unused features</span></span>

<span data-ttu-id="da7ea-160">Blazor WebAssembly의 런타임에는 앱에서 더 작은 페이로드 크기를 위해 해당 기능이 필요하지 않은 경우 사용하지 않도록 설정할 수 있는 다음과 같은 .NET 기능이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-160">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="da7ea-161">정확한 표준 시간대 정보를 위해 데이터 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-161">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="da7ea-162">앱에서 이 기능이 필요하지 않은 경우 앱의 프로젝트 파일에서 `BlazorEnableTimeZoneSupport` MSBuild 속성을 `false`로 설정하여 사용하지 않도록 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-162">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="da7ea-163"><xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>와 같은 API의 올바른 작동을 위해 데이터 정렬 정보가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-163">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="da7ea-164">앱에서 데이터 정렬 데이터가 필요하지 않은 경우 앱의 프로젝트 파일에서 `BlazorWebAssemblyPreserveCollationData` MSBuild 속성을 `false`로 설정하여 사용하지 않도록 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="da7ea-164">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
