---
title: ASP.NET Core에서 Razor Pages 경로 및 앱 규칙
author: rick-anderson
description: 경로 및 앱 모델 공급자 규칙을 통해 페이지 라우팅, 검색 및 처리를 제어하는 방법을 검색합니다.
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 5fbb72d2195ca9fc1494f15ba0045cbb2707f72c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019510"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="2f1b0-103">ASP.NET Core에서 Razor Pages 경로 및 앱 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2f1b0-104">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor Pages 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="2f1b0-105">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="2f1b0-106">페이지 경로를 지정하거나, 경로 세그먼트를 추가하거나, 경로에 매개 변수를 추가하려면 페이지의 `@page` 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="2f1b0-107">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="2f1b0-108">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="2f1b0-109">자세한 내용은 [라우팅: 예약된 라우팅 이름](xref:mvc/controllers/routing#reserved-routing-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="2f1b0-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f1b0-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="2f1b0-111">시나리오</span><span class="sxs-lookup"><span data-stu-id="2f1b0-111">Scenario</span></span> | <span data-ttu-id="2f1b0-112">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="2f1b0-113">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="2f1b0-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="2f1b0-114">Conventions.Add</span></span><ul><li><span data-ttu-id="2f1b0-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="2f1b0-118">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="2f1b0-119">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="2f1b0-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="2f1b0-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="2f1b0-123">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="2f1b0-124">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="2f1b0-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-127">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="2f1b0-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="2f1b0-128">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="2f1b0-129">Razor Pages 규칙은 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>를 구성하는 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 오버로드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-129">Razor Pages conventions are configured using an <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f1b0-130">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a><span data-ttu-id="2f1b0-131">경로 순서</span><span class="sxs-lookup"><span data-stu-id="2f1b0-131">Route order</span></span>

<span data-ttu-id="2f1b0-132">경로는 처리(경로 일치)를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="2f1b0-133">순서</span><span class="sxs-lookup"><span data-stu-id="2f1b0-133">Order</span></span>            | <span data-ttu-id="2f1b0-134">동작</span><span class="sxs-lookup"><span data-stu-id="2f1b0-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="2f1b0-135">-1</span><span class="sxs-lookup"><span data-stu-id="2f1b0-135">-1</span></span>               | <span data-ttu-id="2f1b0-136">경로는 다른 경로가 처리되기 전에 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="2f1b0-137">0</span><span class="sxs-lookup"><span data-stu-id="2f1b0-137">0</span></span>                | <span data-ttu-id="2f1b0-138">순서가 지정되지 않았습니다(기본값).</span><span class="sxs-lookup"><span data-stu-id="2f1b0-138">Order isn't specified (default value).</span></span> <span data-ttu-id="2f1b0-139">`Order`를 할당하지 않으면(`Order = null`) 기본적으로 경로 `Order`는 처리를 위해 0이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="2f1b0-140">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="2f1b0-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="2f1b0-141">경로 처리 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="2f1b0-142">경로 처리는 다음 규칙에 따라 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="2f1b0-143">경로는 순서대로 처리됩니다(-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="2f1b0-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="2f1b0-144">경로에 동일한 `Order`가 있는 경우 가장 구체적인 경로가 먼저 일치되고, 그 다음에 덜 구체적인 경로가 일치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="2f1b0-145">동일한 `Order`와 동일한 수의 매개 변수를 지정한 경로가 요청 URL과 일치하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가된 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="2f1b0-146">가능하면 설정된 경로 처리 순서를 따르지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="2f1b0-147">일반적으로 라우팅은 URL이 일치하는 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="2f1b0-148">경로 `Order` 속성을 경로 요청으로 올바르게 설정해야 하는 경우에는 앱의 라우팅 체계가 클라이언트에 혼동을 가져올 수 있으며 유지 관리하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="2f1b0-149">앱의 라우팅 체계를 간소화하는 방안을 모색하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="2f1b0-150">샘플 앱은 단일 앱을 사용하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="2f1b0-151">그러나 프로덕션 앱에서는 경로 `Order` 설정을 연습하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="2f1b0-152">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="2f1b0-153">MVC의 경로 순서 관련 항목은 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="2f1b0-154">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-154">Model conventions</span></span>

<span data-ttu-id="2f1b0-155"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>의 대리자를 추가하여 Razor Pages에 적용되는 [모델 규칙](xref:mvc/controllers/application-model#conventions)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-156">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="2f1b0-157"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고, 페이지 경로 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="2f1b0-158">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="2f1b0-160">이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="2f1b0-161">`TheContactPage/{text?}`에 대한 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="2f1b0-162">연락처 페이지 경로는 기본 순서가 `null`(`Order = 0`)이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="2f1b0-163">`{aboutTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="2f1b0-164">`{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="2f1b0-165">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="2f1b0-166">`{otherPagesTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="2f1b0-167">`{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="2f1b0-168">*Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-169">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-170">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-171"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 추가와 같은 Razor Pages 옵션은 Razor Pages가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가될 때 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when Razor Pages is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f1b0-172">예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="2f1b0-173">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-176">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="2f1b0-177"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고, 페이지 앱 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="2f1b0-178">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="2f1b0-179">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="2f1b0-180">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="2f1b0-181">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="2f1b0-182">샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-183">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="2f1b0-184">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-186">모든 페이지에 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="2f1b0-187"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>을 만들고, 페이지 처리기 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-188">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="2f1b0-189">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-189">Page route action conventions</span></span>

<span data-ttu-id="2f1b0-190"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생되는 기본 경로 모델 공급자는 페이지 경로를 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="2f1b0-191">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-191">Folder route model convention</span></span>

<span data-ttu-id="2f1b0-192"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>을 사용하여 지정된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="2f1b0-193">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="2f1b0-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="2f1b0-195">그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="2f1b0-196">*Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수 값(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-197">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-198">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-199">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="2f1b0-202">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-202">Page route model convention</span></span>

<span data-ttu-id="2f1b0-203"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="2f1b0-204">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="2f1b0-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="2f1b0-206">그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="2f1b0-207">`/About/RouteDataValue`에서 경로 매개 변수 값을 사용하여 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-208">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-209">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-210">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="2f1b0-213">매개 변수 변환기를 사용하여 페이지 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="2f1b0-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="2f1b0-214">ASP.NET Core에서 생성된 페이지 경로는 매개 변수 변환기를 사용하여 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="2f1b0-215">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="2f1b0-216">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="2f1b0-217">`PageRouteTransformerConvention` 페이지 경로 모델 규칙은 앱에서 자동으로 생성된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="2f1b0-218">예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml*의 Razor Pages 파일은 경로가 `/SubscriptionManagement/ViewAll`에서 `/subscription-management/view-all`로 다시 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="2f1b0-219">`PageRouteTransformerConvention`은 Razor Pages 폴더 및 파일 이름에서 가져온 자동으로 생성된 페이지 경로 세그먼트만 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="2f1b0-220">`@page` 지시문을 사용하여 추가된 경로 세그먼트는 변환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="2f1b0-221">또한 이 규칙은 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>에 의해 추가된 경로를 변환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="2f1b0-222">`PageRouteTransformerConvention`은 `Startup.ConfigureServices`에서 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="2f1b0-223">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="2f1b0-223">Configure a page route</span></span>

<span data-ttu-id="2f1b0-224"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>를 사용하여 지정된 페이지 경로에 페이지에 대한 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="2f1b0-225">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="2f1b0-226">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="2f1b0-227">샘플 앱은 *Contact.cshtml*의 `/TheContactPage`에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="2f1b0-228">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="2f1b0-229">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="2f1b0-230">방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="2f1b0-231">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="2f1b0-234">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="2f1b0-235">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="2f1b0-238">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-238">Page model action conventions</span></span>

<span data-ttu-id="2f1b0-239"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>에서 파생되는 기본 페이지 모델 공급자는 페이지 모델을 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="2f1b0-240">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="2f1b0-241">샘플 앱은 이 섹션의 예제에서 응답 헤더를 적용하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>라는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="2f1b0-242">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="2f1b0-243">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-243">**Folder app model convention**</span></span>

<span data-ttu-id="2f1b0-244"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>을 사용하여 지정된 폴더에 있는 모든 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="2f1b0-245">이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="2f1b0-246">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="2f1b0-248">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-248">**Page app model convention**</span></span>

<span data-ttu-id="2f1b0-249"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="2f1b0-250">샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="2f1b0-251">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="2f1b0-253">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-253">**Configure a filter**</span></span>

<span data-ttu-id="2f1b0-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 지정된 필터를 적용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="2f1b0-255">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="2f1b0-256">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="2f1b0-257">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="2f1b0-258">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="2f1b0-259">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="2f1b0-260">Razor Pages에서 작업 필터를 무시하므로 경로에 `OtherPages/Page2`가 포함되지 않는 경우 의도한 대로 `EmptyFilter`가 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="2f1b0-261">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="2f1b0-263">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-263">**Configure a filter factory**</span></span>

<span data-ttu-id="2f1b0-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 모든 Razor Pages에 [필터](xref:mvc/controllers/filters)를 적용하도록 지정된 센터를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="2f1b0-265">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="2f1b0-266">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="2f1b0-267">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="2f1b0-269">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="2f1b0-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="2f1b0-270">Razor Pages가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor Pages에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="2f1b0-271">사용할 수 있는 다른 유형의 MVC 필터에는 [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="2f1b0-272">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="2f1b0-273">페이지 필터(<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="2f1b0-274">자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f1b0-275">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f1b0-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2f1b0-276">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor Pages 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="2f1b0-277">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="2f1b0-278">페이지 경로를 지정하거나, 경로 세그먼트를 추가하거나, 경로에 매개 변수를 추가하려면 페이지의 `@page` 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="2f1b0-279">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="2f1b0-280">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="2f1b0-281">자세한 내용은 [라우팅: 예약된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="2f1b0-282">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f1b0-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="2f1b0-283">시나리오</span><span class="sxs-lookup"><span data-stu-id="2f1b0-283">Scenario</span></span> | <span data-ttu-id="2f1b0-284">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="2f1b0-285">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="2f1b0-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="2f1b0-286">Conventions.Add</span></span><ul><li><span data-ttu-id="2f1b0-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="2f1b0-290">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="2f1b0-291">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="2f1b0-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="2f1b0-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="2f1b0-295">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="2f1b0-296">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="2f1b0-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-299">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="2f1b0-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="2f1b0-300">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="2f1b0-301">Razor Pages 규칙은 `Startup` 클래스의 서비스 컬렉션에서 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>에 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 사용하여 추가되고 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="2f1b0-302">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-302">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="2f1b0-303">경로 순서</span><span class="sxs-lookup"><span data-stu-id="2f1b0-303">Route order</span></span>

<span data-ttu-id="2f1b0-304">경로는 처리(경로 일치)를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="2f1b0-305">순서</span><span class="sxs-lookup"><span data-stu-id="2f1b0-305">Order</span></span>            | <span data-ttu-id="2f1b0-306">동작</span><span class="sxs-lookup"><span data-stu-id="2f1b0-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="2f1b0-307">-1</span><span class="sxs-lookup"><span data-stu-id="2f1b0-307">-1</span></span>               | <span data-ttu-id="2f1b0-308">경로는 다른 경로가 처리되기 전에 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="2f1b0-309">0</span><span class="sxs-lookup"><span data-stu-id="2f1b0-309">0</span></span>                | <span data-ttu-id="2f1b0-310">순서가 지정되지 않았습니다(기본값).</span><span class="sxs-lookup"><span data-stu-id="2f1b0-310">Order isn't specified (default value).</span></span> <span data-ttu-id="2f1b0-311">`Order`를 할당하지 않으면(`Order = null`) 기본적으로 경로 `Order`는 처리를 위해 0이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="2f1b0-312">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="2f1b0-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="2f1b0-313">경로 처리 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="2f1b0-314">경로 처리는 다음 규칙에 따라 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="2f1b0-315">경로는 순서대로 처리됩니다(-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="2f1b0-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="2f1b0-316">경로에 동일한 `Order`가 있는 경우 가장 구체적인 경로가 먼저 일치되고, 그 다음에 덜 구체적인 경로가 일치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="2f1b0-317">동일한 `Order`와 동일한 수의 매개 변수를 지정한 경로가 요청 URL과 일치하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가된 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="2f1b0-318">가능하면 설정된 경로 처리 순서를 따르지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="2f1b0-319">일반적으로 라우팅은 URL이 일치하는 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="2f1b0-320">경로 `Order` 속성을 경로 요청으로 올바르게 설정해야 하는 경우에는 앱의 라우팅 체계가 클라이언트에 혼동을 가져올 수 있으며 유지 관리하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="2f1b0-321">앱의 라우팅 체계를 간소화하는 방안을 모색하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="2f1b0-322">샘플 앱은 단일 앱을 사용하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="2f1b0-323">그러나 프로덕션 앱에서는 경로 `Order` 설정을 연습하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="2f1b0-324">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="2f1b0-325">MVC의 경로 순서 관련 항목은 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="2f1b0-326">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-326">Model conventions</span></span>

<span data-ttu-id="2f1b0-327"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>의 대리자를 추가하여 Razor Pages에 적용되는 [모델 규칙](xref:mvc/controllers/application-model#conventions)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-328">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="2f1b0-329"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고, 페이지 경로 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="2f1b0-330">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="2f1b0-332">이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="2f1b0-333">`TheContactPage/{text?}`에 대한 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="2f1b0-334">연락처 페이지 경로는 기본 순서가 `null`(`Order = 0`)이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="2f1b0-335">`{aboutTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="2f1b0-336">`{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="2f1b0-337">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="2f1b0-338">`{otherPagesTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="2f1b0-339">`{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="2f1b0-340">*Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-341">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-342">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-343"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 추가와 같은 Razor Pages 옵션은 MVC가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가될 때 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f1b0-344">예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="2f1b0-345">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-348">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="2f1b0-349"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고, 페이지 앱 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="2f1b0-350">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="2f1b0-351">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="2f1b0-352">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="2f1b0-353">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="2f1b0-354">샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-355">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="2f1b0-356">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-358">모든 페이지에 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="2f1b0-359"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>을 만들고, 페이지 처리기 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-360">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="2f1b0-361">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-361">Page route action conventions</span></span>

<span data-ttu-id="2f1b0-362"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생되는 기본 경로 모델 공급자는 페이지 경로를 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="2f1b0-363">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-363">Folder route model convention</span></span>

<span data-ttu-id="2f1b0-364"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>을 사용하여 지정된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="2f1b0-365">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="2f1b0-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="2f1b0-367">그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="2f1b0-368">*Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수 값(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-369">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-370">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-371">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="2f1b0-374">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-374">Page route model convention</span></span>

<span data-ttu-id="2f1b0-375"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="2f1b0-376">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="2f1b0-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="2f1b0-378">그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="2f1b0-379">`/About/RouteDataValue`에서 경로 매개 변수 값을 사용하여 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-380">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-381">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-382">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="2f1b0-385">매개 변수 변환기를 사용하여 페이지 경로 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="2f1b0-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="2f1b0-386">ASP.NET Core에서 생성된 페이지 경로는 매개 변수 변환기를 사용하여 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="2f1b0-387">매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="2f1b0-388">예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="2f1b0-389">`PageRouteTransformerConvention` 페이지 경로 모델 규칙은 앱에서 자동으로 생성된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="2f1b0-390">예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml*의 Razor Pages 파일은 경로가 `/SubscriptionManagement/ViewAll`에서 `/subscription-management/view-all`로 다시 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="2f1b0-391">`PageRouteTransformerConvention`은 Razor Pages 폴더 및 파일 이름에서 가져온 자동으로 생성된 페이지 경로 세그먼트만 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="2f1b0-392">`@page` 지시문을 사용하여 추가된 경로 세그먼트는 변환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="2f1b0-393">또한 이 규칙은 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>에 의해 추가된 경로를 변환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="2f1b0-394">`PageRouteTransformerConvention`은 `Startup.ConfigureServices`에서 옵션으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a><span data-ttu-id="2f1b0-395">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="2f1b0-395">Configure a page route</span></span>

<span data-ttu-id="2f1b0-396"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>를 사용하여 지정된 페이지 경로에 페이지에 대한 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="2f1b0-397">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="2f1b0-398">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="2f1b0-399">샘플 앱은 *Contact.cshtml*의 `/TheContactPage`에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="2f1b0-400">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="2f1b0-401">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="2f1b0-402">방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="2f1b0-403">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="2f1b0-406">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="2f1b0-407">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="2f1b0-410">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-410">Page model action conventions</span></span>

<span data-ttu-id="2f1b0-411"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>에서 파생되는 기본 페이지 모델 공급자는 페이지 모델을 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="2f1b0-412">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="2f1b0-413">샘플 앱은 이 섹션의 예제에서 응답 헤더를 적용하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>라는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="2f1b0-414">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="2f1b0-415">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-415">**Folder app model convention**</span></span>

<span data-ttu-id="2f1b0-416"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>을 사용하여 지정된 폴더에 있는 모든 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="2f1b0-417">이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="2f1b0-418">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="2f1b0-420">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-420">**Page app model convention**</span></span>

<span data-ttu-id="2f1b0-421"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="2f1b0-422">샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="2f1b0-423">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="2f1b0-425">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-425">**Configure a filter**</span></span>

<span data-ttu-id="2f1b0-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 지정된 필터를 적용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="2f1b0-427">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="2f1b0-428">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="2f1b0-429">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="2f1b0-430">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="2f1b0-431">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="2f1b0-432">Razor Pages에서 작업 필터를 무시하므로 경로에 `OtherPages/Page2`가 포함되지 않는 경우 의도한 대로 `EmptyFilter`가 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="2f1b0-433">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="2f1b0-435">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-435">**Configure a filter factory**</span></span>

<span data-ttu-id="2f1b0-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 모든 Razor Pages에 [필터](xref:mvc/controllers/filters)를 적용하도록 지정된 센터를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="2f1b0-437">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="2f1b0-438">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="2f1b0-439">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="2f1b0-441">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="2f1b0-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="2f1b0-442">Razor Pages가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor Pages에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="2f1b0-443">사용할 수 있는 다른 유형의 MVC 필터에는 [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="2f1b0-444">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="2f1b0-445">페이지 필터(<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="2f1b0-446">자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f1b0-447">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f1b0-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2f1b0-448">페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor Pages 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="2f1b0-449">개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="2f1b0-450">페이지 경로를 지정하거나, 경로 세그먼트를 추가하거나, 경로에 매개 변수를 추가하려면 페이지의 `@page` 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="2f1b0-451">자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="2f1b0-452">경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="2f1b0-453">자세한 내용은 [라우팅: 예약된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="2f1b0-454">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f1b0-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="2f1b0-455">시나리오</span><span class="sxs-lookup"><span data-stu-id="2f1b0-455">Scenario</span></span> | <span data-ttu-id="2f1b0-456">샘플에서는 다음 사항을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="2f1b0-457">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="2f1b0-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="2f1b0-458">Conventions.Add</span></span><ul><li><span data-ttu-id="2f1b0-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="2f1b0-462">경로 템플릿 및 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="2f1b0-463">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="2f1b0-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="2f1b0-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="2f1b0-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="2f1b0-467">폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="2f1b0-468">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="2f1b0-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="2f1b0-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="2f1b0-471">ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</span><span class="sxs-lookup"><span data-stu-id="2f1b0-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="2f1b0-472">폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="2f1b0-473">Razor Pages 규칙은 `Startup` 클래스의 서비스 컬렉션에서 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>에 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 사용하여 추가되고 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="2f1b0-474">다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-474">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="2f1b0-475">경로 순서</span><span class="sxs-lookup"><span data-stu-id="2f1b0-475">Route order</span></span>

<span data-ttu-id="2f1b0-476">경로는 처리(경로 일치)를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="2f1b0-477">순서</span><span class="sxs-lookup"><span data-stu-id="2f1b0-477">Order</span></span>            | <span data-ttu-id="2f1b0-478">동작</span><span class="sxs-lookup"><span data-stu-id="2f1b0-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="2f1b0-479">-1</span><span class="sxs-lookup"><span data-stu-id="2f1b0-479">-1</span></span>               | <span data-ttu-id="2f1b0-480">경로는 다른 경로가 처리되기 전에 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="2f1b0-481">0</span><span class="sxs-lookup"><span data-stu-id="2f1b0-481">0</span></span>                | <span data-ttu-id="2f1b0-482">순서가 지정되지 않았습니다(기본값).</span><span class="sxs-lookup"><span data-stu-id="2f1b0-482">Order isn't specified (default value).</span></span> <span data-ttu-id="2f1b0-483">`Order`를 할당하지 않으면(`Order = null`) 기본적으로 경로 `Order`는 처리를 위해 0이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="2f1b0-484">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="2f1b0-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="2f1b0-485">경로 처리 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="2f1b0-486">경로 처리는 다음 규칙에 따라 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="2f1b0-487">경로는 순서대로 처리됩니다(-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="2f1b0-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="2f1b0-488">경로에 동일한 `Order`가 있는 경우 가장 구체적인 경로가 먼저 일치되고, 그 다음에 덜 구체적인 경로가 일치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="2f1b0-489">동일한 `Order`와 동일한 수의 매개 변수를 지정한 경로가 요청 URL과 일치하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가된 순서대로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="2f1b0-490">가능하면 설정된 경로 처리 순서를 따르지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="2f1b0-491">일반적으로 라우팅은 URL이 일치하는 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="2f1b0-492">경로 `Order` 속성을 경로 요청으로 올바르게 설정해야 하는 경우에는 앱의 라우팅 체계가 클라이언트에 혼동을 가져올 수 있으며 유지 관리하기 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="2f1b0-493">앱의 라우팅 체계를 간소화하는 방안을 모색하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="2f1b0-494">샘플 앱은 단일 앱을 사용하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="2f1b0-495">그러나 프로덕션 앱에서는 경로 `Order` 설정을 연습하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="2f1b0-496">Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="2f1b0-497">MVC의 경로 순서 관련 항목은 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="2f1b0-498">모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-498">Model conventions</span></span>

<span data-ttu-id="2f1b0-499"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>의 대리자를 추가하여 Razor Pages에 적용되는 [모델 규칙](xref:mvc/controllers/application-model#conventions)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-500">모든 페이지에 경로 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="2f1b0-501"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고, 페이지 경로 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="2f1b0-502">샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="2f1b0-504">이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="2f1b0-505">`TheContactPage/{text?}`에 대한 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="2f1b0-506">연락처 페이지 경로는 기본 순서가 `null`(`Order = 0`)이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="2f1b0-507">`{aboutTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="2f1b0-508">`{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="2f1b0-509">`/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="2f1b0-510">`{otherPagesTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="2f1b0-511">`{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="2f1b0-512">*Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-513">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-514">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-515"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 추가와 같은 Razor Pages 옵션은 MVC가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가될 때 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f1b0-516">예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="2f1b0-517">`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-520">모든 페이지에 앱 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="2f1b0-521"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고, 페이지 앱 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="2f1b0-522">이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="2f1b0-523">클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="2f1b0-524">이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="2f1b0-525">전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="2f1b0-526">샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-527">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="2f1b0-528">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="2f1b0-530">모든 페이지에 처리기 모델 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="2f1b0-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="2f1b0-531"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>을 만들고, 페이지 처리기 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="2f1b0-532">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="2f1b0-533">페이지 경로 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-533">Page route action conventions</span></span>

<span data-ttu-id="2f1b0-534"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생되는 기본 경로 모델 공급자는 페이지 경로를 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="2f1b0-535">폴더 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-535">Folder route model convention</span></span>

<span data-ttu-id="2f1b0-536"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>을 사용하여 지정된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="2f1b0-537">샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="2f1b0-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="2f1b0-539">그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="2f1b0-540">*Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수 값(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-541">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-542">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-543">`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="2f1b0-546">페이지 경로 모델 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-546">Page route model convention</span></span>

<span data-ttu-id="2f1b0-547"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="2f1b0-548">샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="2f1b0-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="2f1b0-550">그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="2f1b0-551">`/About/RouteDataValue`에서 경로 매개 변수 값을 사용하여 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="2f1b0-552">가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="2f1b0-553">라우팅을 사용하여 올바른 경로를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="2f1b0-554">`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="2f1b0-557">페이지 경로 구성</span><span class="sxs-lookup"><span data-stu-id="2f1b0-557">Configure a page route</span></span>

<span data-ttu-id="2f1b0-558"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>를 사용하여 지정된 페이지 경로에 페이지에 대한 경로를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="2f1b0-559">페이지에 생성된 링크는 지정된 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="2f1b0-560">`AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="2f1b0-561">샘플 앱은 *Contact.cshtml*의 `/TheContactPage`에 대한 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="2f1b0-562">연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="2f1b0-563">연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="2f1b0-564">방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="2f1b0-565">렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="2f1b0-568">일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="2f1b0-569">추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="2f1b0-572">페이지 모델 작업 규칙</span><span class="sxs-lookup"><span data-stu-id="2f1b0-572">Page model action conventions</span></span>

<span data-ttu-id="2f1b0-573"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>에서 파생되는 기본 페이지 모델 공급자는 페이지 모델을 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="2f1b0-574">이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="2f1b0-575">샘플 앱은 이 섹션의 예제에서 응답 헤더를 적용하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>라는 `AddHeaderAttribute` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="2f1b0-576">샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="2f1b0-577">**폴더 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-577">**Folder app model convention**</span></span>

<span data-ttu-id="2f1b0-578"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>을 사용하여 지정된 폴더에 있는 모든 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="2f1b0-579">이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="2f1b0-580">`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="2f1b0-582">**페이지 앱 모델 규칙**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-582">**Page app model convention**</span></span>

<span data-ttu-id="2f1b0-583"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="2f1b0-584">샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="2f1b0-585">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="2f1b0-587">**필터 구성**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-587">**Configure a filter**</span></span>

<span data-ttu-id="2f1b0-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 지정된 필터를 적용하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="2f1b0-589">필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="2f1b0-590">페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="2f1b0-591">조건이 통과하는 경우 헤더가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="2f1b0-592">그렇지 않으면 `EmptyFilter`이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="2f1b0-593">`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="2f1b0-594">Razor Pages에서 작업 필터를 무시하므로 경로에 `OtherPages/Page2`가 포함되지 않는 경우 의도한 대로 `EmptyFilter`가 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="2f1b0-595">`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="2f1b0-597">**필터 팩터리 구성**</span><span class="sxs-lookup"><span data-stu-id="2f1b0-597">**Configure a filter factory**</span></span>

<span data-ttu-id="2f1b0-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 모든 Razor Pages에 [필터](xref:mvc/controllers/filters)를 적용하도록 지정된 센터를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="2f1b0-599">샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="2f1b0-600">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f1b0-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="2f1b0-601">`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="2f1b0-603">MVC 필터 및 페이지 필터(IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="2f1b0-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="2f1b0-604">Razor Pages가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor Pages에서 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="2f1b0-605">사용할 수 있는 다른 유형의 MVC 필터에는 [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="2f1b0-606">자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="2f1b0-607">페이지 필터(<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="2f1b0-608">자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f1b0-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f1b0-609">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f1b0-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
