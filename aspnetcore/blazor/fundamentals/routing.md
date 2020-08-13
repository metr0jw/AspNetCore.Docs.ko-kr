---
title: ASP.NET Core Blazor 라우팅
author: guardrex
description: 앱에서 요청을 라우팅하는 방법과 NavLink 구성 요소를 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 2e0c1a479554b704b4a8cd87bc177d0f76ddc7e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014349"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="2f352-103">ASP.NET Core Blazor 라우팅</span><span class="sxs-lookup"><span data-stu-id="2f352-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="2f352-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="2f352-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2f352-105">요청을 라우팅하는 방법과 Blazor 앱에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하여 탐색 링크를 만드는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="2f352-106">ASP.NET Core 엔드포인트 라우팅 통합</span><span class="sxs-lookup"><span data-stu-id="2f352-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="2f352-107">Blazor Server는 [ASP.NET Core 엔드포인트 라우팅](xref:fundamentals/routing)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="2f352-108">`Startup.Configure`의 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>를 사용하여 대화형 구성 요소에 대해 들어오는 연결을 허용하도록 ASP.NET Core 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="2f352-109">가장 일반적인 구성은 Blazor Server 앱의 서버 쪽 호스트 역할을 하는 Razor 페이지로 모든 요청을 라우팅하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="2f352-110">규칙에 따라 ‘호스트’ 페이지의 이름은 일반적으로 `_Host.cshtml`입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="2f352-111">호스트 파일에 지정된 경로는 경로 일치 시 낮은 우선순위로 작동하기 때문에 ‘대체 경로’라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="2f352-112">일치하는 다른 경로가 없는 경우 대체(fallback) 경로가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="2f352-113">이렇게 하면 앱이 Blazor Server 앱을 방해하지 않고 다른 컨트롤러와 페이지를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="2f352-114">루트가 아닌 URL 서버 호스팅을 위한 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 구성에 대한 자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f352-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="2f352-115">경로 템플릿</span><span class="sxs-lookup"><span data-stu-id="2f352-115">Route templates</span></span>

<span data-ttu-id="2f352-116"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 지정된 경로를 사용하여 각 구성 요소로 라우팅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="2f352-117"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소는 `App.razor` 파일에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="2f352-118">`@page` 지시문을 포함하는 `.razor` 파일을 컴파일하면 생성된 클래스에 경로 템플릿을 지정하는 <xref:Microsoft.AspNetCore.Components.RouteAttribute>가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="2f352-119">런타임에 <xref:Microsoft.AspNetCore.Components.RouteView> 구성 요소는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="2f352-120">원하는 매개 변수와 함께 <xref:Microsoft.AspNetCore.Components.Routing.Router>에서 <xref:Microsoft.AspNetCore.Components.RouteData>를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="2f352-121">지정된 매개 변수를 사용하여 지정된 구성 요소를 해당 레이아웃(또는 선택적 기본 레이아웃)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="2f352-122">필요한 경우, 레이아웃을 지정하지 않는 구성 요소에 사용할 <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> 매개 변수를 레이아웃 클래스로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="2f352-123">기본 Blazor 템플릿은 `MainLayout` 구성 요소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="2f352-124">`MainLayout.razor`는 템플릿 프로젝트의 `Shared` 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="2f352-125">레이아웃에 대한 자세한 내용은 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f352-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="2f352-126">한 구성 요소에 여러 개의 경로 템플릿을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="2f352-127">다음 구성 요소는 `/BlazorRoute` 및 `/DifferentBlazorRoute`에 대한 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="2f352-128">URL을 올바르게 확인하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 `<base>` 태그가 있어야 하고, 앱 기본 경로가 `href` 특성에 지정되어 있어야 합니다(`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="2f352-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="2f352-129">자세한 내용은 <xref:blazor/host-and-deploy/index#app-base-path>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f352-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="2f352-130">콘텐츠를 찾을 수 없는 경우 사용자 지정 콘텐츠 제공</span><span class="sxs-lookup"><span data-stu-id="2f352-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="2f352-131"><xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소를 사용하면 요청된 경로의 콘텐츠를 찾을 수 없는 경우 앱에서 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="2f352-132">`App.razor` 파일에서 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소의 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 템플릿 매개 변수에 사용자 지정 콘텐츠를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="2f352-133">`<NotFound>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="2f352-134"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> 콘텐츠에 기본 레이아웃을 적용하려면 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f352-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="2f352-135">여러 어셈블리에서 구성 요소로 라우팅</span><span class="sxs-lookup"><span data-stu-id="2f352-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="2f352-136"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 매개 변수를 사용하여 라우팅 가능한 구성 요소를 검색할 때 고려할 추가 어셈블리를 <xref:Microsoft.AspNetCore.Components.Routing.Router> 구성 요소에 대해 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="2f352-137">지정한 어셈블리는 `AppAssembly`에서 지정한 어셈블리에 추가로 고려됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="2f352-138">다음 예제에서 `Component1`은 참조된 클래스 라이브러리에서 정의된 라우팅 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="2f352-139">다음 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 예제에서는 `Component1`에 대해 라우팅 지원이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="2f352-140">경로 매개 변수</span><span class="sxs-lookup"><span data-stu-id="2f352-140">Route parameters</span></span>

<span data-ttu-id="2f352-141">라우터는 경로 매개 변수를 사용하여 해당 구성 요소 매개 변수를 동일한 이름(대/소문자 구분 안 함)으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="2f352-142">선택적 매개 변수는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="2f352-143">위의 예제에서는 두 개의 `@page` 지시문이 적용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="2f352-144">첫 번째 지시문은 매개 변수 없이 구성 요소 탐색을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="2f352-145">두 번째 `@page` 지시문은 `{text}` 경로 매개 변수를 사용하고 `Text` 속성에 값을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="2f352-146">경로 제약 조건</span><span class="sxs-lookup"><span data-stu-id="2f352-146">Route constraints</span></span>

<span data-ttu-id="2f352-147">경로 제약 조건은 경로 세그먼트의 형식 일치를 구성 요소에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="2f352-148">다음 예제에서 `Users` 구성 요소의 경로는 다음과 같은 경우에만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="2f352-149">요청 URL에 `Id` 경로 세그먼트가 있는 경우</span><span class="sxs-lookup"><span data-stu-id="2f352-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="2f352-150">`Id` 세그먼트가 정수(`int`)인 경우</span><span class="sxs-lookup"><span data-stu-id="2f352-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="2f352-151">다음 표에 나와 있는 경로 제약 조건을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="2f352-152">고정 문화권과 일치하는 경로 제약 조건에 대한 자세한 내용은 표 아래에 있는 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f352-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="2f352-153">제약 조건</span><span class="sxs-lookup"><span data-stu-id="2f352-153">Constraint</span></span> | <span data-ttu-id="2f352-154">예제</span><span class="sxs-lookup"><span data-stu-id="2f352-154">Example</span></span>           | <span data-ttu-id="2f352-155">일치하는 예제</span><span class="sxs-lookup"><span data-stu-id="2f352-155">Example Matches</span></span>                                                                  | <span data-ttu-id="2f352-156">고정</span><span class="sxs-lookup"><span data-stu-id="2f352-156">Invariant</span></span><br><span data-ttu-id="2f352-157">culture</span><span class="sxs-lookup"><span data-stu-id="2f352-157">culture</span></span><br><span data-ttu-id="2f352-158">일치</span><span class="sxs-lookup"><span data-stu-id="2f352-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="2f352-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="2f352-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="2f352-160">아니요</span><span class="sxs-lookup"><span data-stu-id="2f352-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="2f352-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2f352-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="2f352-162">예</span><span class="sxs-lookup"><span data-stu-id="2f352-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="2f352-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2f352-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="2f352-164">예</span><span class="sxs-lookup"><span data-stu-id="2f352-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="2f352-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2f352-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2f352-166">예</span><span class="sxs-lookup"><span data-stu-id="2f352-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="2f352-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2f352-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2f352-168">예</span><span class="sxs-lookup"><span data-stu-id="2f352-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="2f352-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="2f352-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="2f352-170">아니요</span><span class="sxs-lookup"><span data-stu-id="2f352-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="2f352-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2f352-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2f352-172">예</span><span class="sxs-lookup"><span data-stu-id="2f352-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="2f352-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2f352-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2f352-174">예</span><span class="sxs-lookup"><span data-stu-id="2f352-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="2f352-175">CLR 형식(예: `int` 또는 <xref:System.DateTime>)으로 변환되는 URL을 확인하는 경로 제약 조건은 항상 고정 문화권을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="2f352-176">이러한 제약 조건은 URL은 지역화될 수 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="2f352-177">점이 포함된 URL을 사용한 라우팅</span><span class="sxs-lookup"><span data-stu-id="2f352-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="2f352-178">Blazor Server 앱에서 `_Host.cshtml`의 기본 경로는 `/`(`@page "/"`)입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-178">In Blazor Server apps, the default route in `_Host.cshtml` is `/` (`@page "/"`).</span></span> <span data-ttu-id="2f352-179">점(`.`)이 포함된 요청 URL은 URL이 파일을 요청하는 것 같으므로 기본 경로와 일치되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-179">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="2f352-180">Blazor 앱은 존재하지 않는 정적 파일에 대해 *404 - 찾을 수 없음* 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-180">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="2f352-181">점이 포함된 경로를 사용하려면 다음 경로 템플릿을 통해 `_Host.cshtml`을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-181">To use routes that contain a dot, configure `_Host.cshtml` with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="2f352-182">`"/{**path}"` 템플릿에는 다음이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-182">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="2f352-183">슬래시(`/`)를 인코딩하지 않고 여러 폴더 경계에 걸쳐 있는 경로를 캡처하기 위한 이중 별표 *catch-all* 구문(`**`)</span><span class="sxs-lookup"><span data-stu-id="2f352-183">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="2f352-184">`path` 경로 매개 변수 이름</span><span class="sxs-lookup"><span data-stu-id="2f352-184">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="2f352-185">*Catch-all* 매개 변수 구문(`*`/`**`)은 Razor 구성 요소(`.razor`)에서 지원되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="2f352-185">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (`.razor`).</span></span>

<span data-ttu-id="2f352-186">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f352-186">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="2f352-187">NavLink 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2f352-187">NavLink component</span></span>

<span data-ttu-id="2f352-188">탐색 링크를 만드는 경우 HTML 하이퍼링크 요소(`<a>`) 대신 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-188">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="2f352-189"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `href`가 현재 URL과 일치하는지 아닌지에 따라 `active` CSS 클래스를 전환한다는 점을 제외하고 `<a>` 요소처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-189">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="2f352-190">`active` 클래스는 사용자가 표시되는 탐색 링크 중에서 활성 페이지를 파악하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-190">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="2f352-191">현재 경로가 `href`와 일치하는 경우, 필요에 따라 <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>에 CSS 클래스 이름을 할당하여 렌더링된 링크에 사용자 지정 CSS 클래스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-191">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="2f352-192">다음 `NavMenu` 구성 요소는 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소를 사용하는 방법을 보여 주는 [`Bootstrap`](https://getbootstrap.com/docs/) 탐색 모음을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-192">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="2f352-193">`<NavLink>` 요소의 `Match` 특성에 할당할 수 있는 다음 두 가지 <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-193">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="2f352-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: 현재 URL 전체와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-194"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="2f352-195"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType>(기본값): 현재 URL의 접두사와 일치하는 경우 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>가 활성 상태입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-195"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="2f352-196">위의 예제에서 홈 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""`는 홈 URL과 일치하고, 앱의 기본 경로 URL(예: `https://localhost:5001/`)에 있는 `active` CSS 클래스만 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-196">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="2f352-197">두 번째 <xref:Microsoft.AspNetCore.Components.Routing.NavLink>는 사용자가 `MyComponent` 접두사를 포함하는 URL(예: `https://localhost:5001/MyComponent` 및 `https://localhost:5001/MyComponent/AnotherSegment`)을 방문할 때 `active` 클래스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-197">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="2f352-198">추가 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소 특성이 렌더링된 앵커 태그로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-198">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="2f352-199">다음 예제에서 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 구성 요소는 `target` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-199">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="2f352-200">다음 HTML 태그가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-200">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="2f352-201">Blazor가 자식 콘텐츠를 렌더링하는 방식 때문에 `for` 루프 내에서 `NavLink` 구성 요소를 렌더링하려면 `NavLink`(자식) 구성 요소 콘텐츠에서 증분 루프 변수를 사용할 경우 로컬 인덱스 변수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-201">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="2f352-202">이 시나리오에서 인덱스 변수를 사용하는 것은 `NavLink` 구성 요소뿐 아니라 [자식 콘텐츠](xref:blazor/components/index#child-content)에서 루프 변수를 사용하는 **모든** 자식 구성 요소의 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-202">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="2f352-203">또는 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>을 활용하여 `foreach` 루프를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-203">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="2f352-204">URI 및 탐색 상태 도우미</span><span class="sxs-lookup"><span data-stu-id="2f352-204">URI and navigation state helpers</span></span>

<span data-ttu-id="2f352-205"><xref:Microsoft.AspNetCore.Components.NavigationManager>를 사용하여 C# 코드로 URI 및 탐색 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-205">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="2f352-206"><xref:Microsoft.AspNetCore.Components.NavigationManager>는 다음 표에 나와 있는 이벤트와 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-206"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="2f352-207">멤버</span><span class="sxs-lookup"><span data-stu-id="2f352-207">Member</span></span> | <span data-ttu-id="2f352-208">설명</span><span class="sxs-lookup"><span data-stu-id="2f352-208">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="2f352-209">현재 절대 URI를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-209">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="2f352-210">절대 URI를 생성하기 위해 상대 URI 경로 앞에 추가할 수 있는 기본 URI(후행 슬래시 포함)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-210">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="2f352-211">일반적으로 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>는 `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)에 있는 문서 `<base>` 요소의 `href` 특성에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-211">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="2f352-212">지정한 URI로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-212">Navigates to the specified URI.</span></span> <span data-ttu-id="2f352-213">`forceLoad`가 `true`인 경우</span><span class="sxs-lookup"><span data-stu-id="2f352-213">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="2f352-214">클라이언트 쪽 라우팅이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-214">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="2f352-215">클라이언트 쪽 라우터에서 URI를 정상적으로 처리했는지와 상관없이 브라우저에서 서버의 새 페이지를 강제로 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-215">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="2f352-216">탐색 위치가 변경된 경우에 발생하는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-216">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="2f352-217">상대 URI를 절대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-217">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="2f352-218">기본 URI(예: 이전에 <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>에서 반환된 URI)가 제공된 경우, 절대 URI를 기본 URI 접두사의 상대 URI로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-218">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="2f352-219">단추를 선택하면 다음 구성 요소가 앱의 `Counter` 구성 요소로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-219">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="2f352-220">다음 구성 요소는 <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>을 구독하여 위치 변경 이벤트를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-220">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2f352-221">`HandleLocationChanged` 메서드는 프레임워크에서 `Dispose`를 호출할 때 언후크됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-221">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="2f352-222">메서드를 언후크하면 구성 요소의 가비지 수집이 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-222">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="2f352-223"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>는 이벤트에 대해 다음과 같은 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-223"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="2f352-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: 새 위치의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="2f352-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: `true`인 경우 Blazor는 브라우저에서 탐색을 가로챘습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="2f352-226">`false`인 경우 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>에서 탐색이 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-226">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="2f352-227">구성 요소 삭제에 대한 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f352-227">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="2f352-228">쿼리 문자열 및 구문 분석 매개 변수</span><span class="sxs-lookup"><span data-stu-id="2f352-228">Query string and parse parameters</span></span>

<span data-ttu-id="2f352-229">요청의 쿼리 문자열은 <xref:Microsoft.AspNetCore.Components.NavigationManager>의 <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> 속성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-229">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="2f352-230">쿼리 문자열의 매개 변수를 구문 분석하려면</span><span class="sxs-lookup"><span data-stu-id="2f352-230">To parse a query string's parameters:</span></span>

* <span data-ttu-id="2f352-231">[Microsoft.AspNetCore.Hosting.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-231">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="2f352-232"><xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>을 사용하여 쿼리 문자열을 구문 분석한 후 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-232">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="2f352-233">앞의 예에서 자리 표시자 `{KEY}`는 쿼리 문자열 매개 변수 키입니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-233">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="2f352-234">예를 들어 URL 키-값 쌍 `?ship=Tardis`는 `ship` 키를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f352-234">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
