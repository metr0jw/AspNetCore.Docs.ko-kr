---
title: ASP.NET Core에서 필터링
author: Rick-Anderson
description: 필터 작동 방법 및 ASP.NET Core에서 사용하는 방법을 자세히 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: eeae167286e793ecd5a547cea0142cf7d8014ece
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671784"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="8ab22-103">ASP.NET Core에서 필터링</span><span class="sxs-lookup"><span data-stu-id="8ab22-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8ab22-104">작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8ab22-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8ab22-105">ASP.NET Core에서 *필터*를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8ab22-106">기본 제공 필터는 다음과 같은 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8ab22-107">권한 부여(사용자가 권한이 없는 리소스에 액세스 방지).</span><span class="sxs-lookup"><span data-stu-id="8ab22-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8ab22-108">응답 캐싱(요청 파이프라인을 단락하면 캐시된 응답을 반환합니다).</span><span class="sxs-lookup"><span data-stu-id="8ab22-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8ab22-109">사용자 지정 필터를 만들어 교차 편집 문제를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8ab22-110">횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8ab22-111">필터는 코드 중복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="8ab22-112">예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8ab22-113">이 문서 Razor 는 뷰가 있는 페이지, API 컨트롤러 및 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="8ab22-114">필터는 [ Razor 구성 요소](xref:blazor/components/index)와 직접 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="8ab22-115">필터는 다음과 같은 경우에만 간접적으로 구성 요소에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="8ab22-116">구성 요소가 페이지 또는 보기에 포함되어 있는 경우</span><span class="sxs-lookup"><span data-stu-id="8ab22-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="8ab22-117">페이지 또는 컨트롤러/보기에서 필터를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="8ab22-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="8ab22-118">[예제 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8ab22-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8ab22-119">필터 작동 방법</span><span class="sxs-lookup"><span data-stu-id="8ab22-119">How filters work</span></span>

<span data-ttu-id="8ab22-120">필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="8ab22-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="8ab22-121">필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![다른 미들웨어, 라우팅 미들웨어, 작업 선택 영역 및 작업 호출 파이프라인을 통해 요청이 처리됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8ab22-124">필터 형식</span><span class="sxs-lookup"><span data-stu-id="8ab22-124">Filter types</span></span>

<span data-ttu-id="8ab22-125">각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8ab22-126">[권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8ab22-127">권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="8ab22-128">[리소스 필터](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8ab22-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8ab22-129">권한 부여 후 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-129">Run after authorization.</span></span>  
  * <span data-ttu-id="8ab22-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8ab22-131">예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="8ab22-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8ab22-133">[작업 필터](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="8ab22-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="8ab22-134">작업 메서드가 호출되기 전후에 즉시 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="8ab22-135">작업에 전달된 인수를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="8ab22-136">작업에서 반환된 결과를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="8ab22-137">는 페이지에서 지원 **되지 않습니다** Razor .</span><span class="sxs-lookup"><span data-stu-id="8ab22-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8ab22-138">[예외 필터](#exception-filters)는 응답 본문이 쓰여지기 전에 발생한 처리되지 않은 예외에 대한 전역 정책을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="8ab22-139">[결과 필터](#result-filters)는 작업 결과의 실행 전후에 즉시 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="8ab22-140">작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8ab22-141">보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8ab22-142">다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8ab22-145">구현</span><span class="sxs-lookup"><span data-stu-id="8ab22-145">Implementation</span></span>

<span data-ttu-id="8ab22-146">필터는 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8ab22-147">동기 필터는 해당 파이프라인 단계 전후에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="8ab22-148">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>는 작업 메서드가 호출되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="8ab22-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>는 작업 메서드가 반환된 후 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8ab22-150">위의 코드에서 [Mydebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) 는 [샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs)의 유틸리티 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="8ab22-151">비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="8ab22-152">예: <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*></span><span class="sxs-lookup"><span data-stu-id="8ab22-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8ab22-153">이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8ab22-154">여러 필터 단계</span><span class="sxs-lookup"><span data-stu-id="8ab22-154">Multiple filter stages</span></span>

<span data-ttu-id="8ab22-155">단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8ab22-156">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="8ab22-157">동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="8ab22-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="8ab22-158">비동기: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8ab22-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="8ab22-159">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8ab22-160">런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8ab22-161">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8ab22-162">비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8ab22-163">와 같은 추상 클래스를 사용 하는 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 각 필터 형식에 대해 동기 메서드 또는 비동기 메서드만 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8ab22-164">기본 제공 필터 특성</span><span class="sxs-lookup"><span data-stu-id="8ab22-164">Built-in filter attributes</span></span>

<span data-ttu-id="8ab22-165">ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8ab22-166">예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8ab22-167">특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8ab22-168">컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="8ab22-169">[브라우저 개발자 도구](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) 와 같은 도구를 사용 하 여 헤더를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="8ab22-170">**응답 헤더**에 `author: Rick Anderson`이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="8ab22-171">다음 코드는 다음과 같은 작업을 수행하는 `ActionFilterAttribute`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="8ab22-172">구성 시스템에서 제목과 이름을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="8ab22-173">앞의 샘플과 달리 다음 코드는 필터 매개 변수를 코드에 추가하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="8ab22-174">응답 헤더에 제목과 이름을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8ab22-175">구성 옵션은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 [구성 시스템](xref:fundamentals/configuration/index)에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8ab22-176">예를 들어 *appsettings.json* 파일에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="8ab22-177">`StartUp.ConfigureServices`에서</span><span class="sxs-lookup"><span data-stu-id="8ab22-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="8ab22-178">`PositionOptions` 클래스는 `"Position"` 구성 영역을 사용하여 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="8ab22-179">`MyActionFilterAttribute`는 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="8ab22-180">다음 코드에서는 `PositionOptions` 클래스를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="8ab22-181">다음 코드는 `MyActionFilterAttribute`를 `Index2` 메서드에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="8ab22-182">**응답 헤더**아래에는 `author: Rick Anderson` `Editor: Joe Smith` 끝점이 호출 될 때 및가 표시 됩니다 `Sample/Index2` .</span><span class="sxs-lookup"><span data-stu-id="8ab22-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="8ab22-183">다음 코드에서는 `MyActionFilterAttribute` 페이지에 및을 적용 합니다 `AddHeaderAttribute` Razor .</span><span class="sxs-lookup"><span data-stu-id="8ab22-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8ab22-184">페이지 처리기 메서드에는 필터를 적용할 수 없습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8ab22-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="8ab22-185">이러한 Razor 모델은 페이지 모델 또는 전역적으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="8ab22-186">여러 필터 인터페이스에는 사용자 지정 구현에 대한 기본 클래스로 사용할 수 있는 해당 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8ab22-187">필터 특성:</span><span class="sxs-lookup"><span data-stu-id="8ab22-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8ab22-188">필터 범위 및 실행 순서</span><span class="sxs-lookup"><span data-stu-id="8ab22-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="8ab22-189">다음 세 가지 *범위*중 하나에서 파이프라인에 필터를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8ab22-190">컨트롤러 작업에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="8ab22-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="8ab22-191">페이지 처리기 메서드에는 필터 특성을 적용할 수 없습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8ab22-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="8ab22-192">컨트롤러 또는 페이지에서 특성을 사용 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="8ab22-193">모든 컨트롤러, 작업 및 페이지에 대해 전역적 Razor 으로 다음 코드와 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="8ab22-194">기본 실행 순서</span><span class="sxs-lookup"><span data-stu-id="8ab22-194">Default order of execution</span></span>

<span data-ttu-id="8ab22-195">파이프라인의 특정 단계에 여러 개의 필터가 있는 경우 범위가 기본 필터 실행 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8ab22-196">전역 필터는 메서드 필터를 둘러싼 클래스 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="8ab22-197">필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8ab22-198">필터의 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-198">The filter sequence:</span></span>

* <span data-ttu-id="8ab22-199">전역 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8ab22-200">컨트롤러 및 페이지 필터의 *이전* 코드 Razor 입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="8ab22-201">작업 메서드 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8ab22-202">작업 메서드 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8ab22-203">컨트롤러 및 페이지 필터의 *이후* 코드 Razor 입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="8ab22-204">전역 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8ab22-205">다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8ab22-206">순서</span><span class="sxs-lookup"><span data-stu-id="8ab22-206">Sequence</span></span> | <span data-ttu-id="8ab22-207">필터 범위</span><span class="sxs-lookup"><span data-stu-id="8ab22-207">Filter scope</span></span> | <span data-ttu-id="8ab22-208">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8ab22-209">1</span><span class="sxs-lookup"><span data-stu-id="8ab22-209">1</span></span> | <span data-ttu-id="8ab22-210">전역</span><span class="sxs-lookup"><span data-stu-id="8ab22-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8ab22-211">2</span><span class="sxs-lookup"><span data-stu-id="8ab22-211">2</span></span> | <span data-ttu-id="8ab22-212">컨트롤러 또는 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="8ab22-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="8ab22-213">3</span><span class="sxs-lookup"><span data-stu-id="8ab22-213">3</span></span> | <span data-ttu-id="8ab22-214">메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8ab22-215">4</span><span class="sxs-lookup"><span data-stu-id="8ab22-215">4</span></span> | <span data-ttu-id="8ab22-216">메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8ab22-217">5</span><span class="sxs-lookup"><span data-stu-id="8ab22-217">5</span></span> | <span data-ttu-id="8ab22-218">컨트롤러 또는 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="8ab22-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8ab22-219">6</span><span class="sxs-lookup"><span data-stu-id="8ab22-219">6</span></span> | <span data-ttu-id="8ab22-220">전역</span><span class="sxs-lookup"><span data-stu-id="8ab22-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="8ab22-221">컨트롤러 수준 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-221">Controller level filters</span></span>

<span data-ttu-id="8ab22-222"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8ab22-223">다음 메서드는</span><span class="sxs-lookup"><span data-stu-id="8ab22-223">These methods:</span></span>

* <span data-ttu-id="8ab22-224">지정된 작업을 위해 실행되는 필터를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8ab22-225">`OnActionExecuting`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8ab22-226">`OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8ab22-227">`OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8ab22-228">작업 메서드 이후 `next`가 실행된 후의 필터 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8ab22-229">예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8ab22-230">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="8ab22-230">The `TestController`:</span></span>

* <span data-ttu-id="8ab22-231">`SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8ab22-232">`OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="8ab22-233">`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8ab22-234">컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="8ab22-235">컨트롤러 수준 필터는 메서드에 적용된 후에 실행되도록 설정할 수 **없습니다**.</span><span class="sxs-lookup"><span data-stu-id="8ab22-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="8ab22-236">순서는 다음 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-236">Order is explained in the next section.</span></span>

<span data-ttu-id="8ab22-237">Razor페이지는 [ Razor 필터 메서드를 재정의 하 여 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8ab22-238">기본 순서 재정의</span><span class="sxs-lookup"><span data-stu-id="8ab22-238">Overriding the default order</span></span>

<span data-ttu-id="8ab22-239"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8ab22-240">`IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8ab22-241">낮은 `Order` 값을 가진 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8ab22-242">더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8ab22-243">더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8ab22-244">`Order` 속성은 생성자 매개 변수를 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="8ab22-245">다음 컨트롤러의 두 작업 필터를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="8ab22-246">전역 필터는 `StartUp.ConfigureServices`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="8ab22-247">3개의 필터는 다음 순서로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="8ab22-248">`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8ab22-249">필터가 순서에 따라 먼저 정렬된 다음, 범위는 연결을 끊는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8ab22-250">모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8ab22-251">앞서 언급했듯이 컨트롤러 수준 필터는 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 속성을 `int.MinValue`로 설정합니다. 기본 제공 필터의 경우 `Order`가 0이 아닌 값으로 설정되지 않은 한 범위가 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="8ab22-252">이전 코드에서 `MySampleActionFilter`는 전역 범위를 가지므로 컨트롤러 범위를 포함하는 `MyAction2FilterAttribute` 전에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="8ab22-253">`MyAction2FilterAttribute`를 먼저 실행하려면 순서를 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="8ab22-254">전역 필터 `MySampleActionFilter`를 먼저 실행하려면 `Order`를 `int.MinValue`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8ab22-255">취소 및 단락</span><span class="sxs-lookup"><span data-stu-id="8ab22-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8ab22-256">필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8ab22-257">예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8ab22-258">다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8ab22-259">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8ab22-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8ab22-260">리소스 필터이고 `AddHeader`이 작업 필터이기 때문에 먼저 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8ab22-261">나머지 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8ab22-262">따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8ab22-263">이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8ab22-264">해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="8ab22-265">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="8ab22-265">Dependency injection</span></span>

<span data-ttu-id="8ab22-266">형식 또는 인스턴스별로 필터를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8ab22-267">인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8ab22-268">형식이 추가되면 해당 필터는 형식으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8ab22-269">형식으로 활성화된 필터는 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-269">A type-activated filter means:</span></span>

* <span data-ttu-id="8ab22-270">각 요청에 대해 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-270">An instance is created for each request.</span></span>
* <span data-ttu-id="8ab22-271">모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8ab22-272">특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가되는 필터에는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성이 없을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8ab22-273">DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8ab22-274">특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8ab22-275">특성이 작동하는 방법의 제한 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8ab22-276">다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8ab22-277">특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8ab22-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8ab22-278">위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8ab22-279">로거는 DI를 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-279">Loggers are available from DI.</span></span> <span data-ttu-id="8ab22-280">그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8ab22-281">[기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8ab22-282">필터에 추가된 로깅은:</span><span class="sxs-lookup"><span data-stu-id="8ab22-282">Logging added to filters:</span></span>

* <span data-ttu-id="8ab22-283">비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8ab22-284">작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="8ab22-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8ab22-285">기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8ab22-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8ab22-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="8ab22-287">서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8ab22-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8ab22-289">다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8ab22-290">다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="8ab22-291">다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8ab22-292">`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8ab22-293">필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8ab22-294">ASP.NET Core 런타임은 다음을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8ab22-295">필터의 단일 인스턴스 생성.</span><span class="sxs-lookup"><span data-stu-id="8ab22-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8ab22-296">나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.</span><span class="sxs-lookup"><span data-stu-id="8ab22-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8ab22-297">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8ab22-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8ab22-299">`IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8ab22-300">`CreateInstance`는 DI에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8ab22-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8ab22-301">TypeFilterAttribute</span></span>

<span data-ttu-id="8ab22-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8ab22-303"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8ab22-304">`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8ab22-305">`TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8ab22-306">DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8ab22-307">`TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8ab22-308">`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8ab22-309">필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8ab22-310">ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8ab22-311">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8ab22-312">다음 예제에서는 `TypeFilterAttribute`를 사용하여 인수를 형식에 전달하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8ab22-313">권한 부여 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-313">Authorization filters</span></span>

<span data-ttu-id="8ab22-314">권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-314">Authorization filters:</span></span>

* <span data-ttu-id="8ab22-315">필터 파이프라인에서 첫 번째로 실행되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8ab22-316">동작 메서드에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-316">Control access to action methods.</span></span>
* <span data-ttu-id="8ab22-317">before 메서드는 있지만 after 메서드는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="8ab22-318">사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8ab22-319">사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8ab22-320">기본 제공 권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="8ab22-321">권한 부여 시스템을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-321">Calls the authorization system.</span></span>
* <span data-ttu-id="8ab22-322">요청을 승인하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-322">Does not authorize requests.</span></span>

<span data-ttu-id="8ab22-323">권한 부여 필터 내에서 예외를 던지지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="8ab22-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8ab22-324">해당 예외는 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-324">The exception will not be handled.</span></span>
* <span data-ttu-id="8ab22-325">예외 필터가 해당 예외를 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8ab22-326">권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8ab22-327">[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8ab22-328">리소스 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-328">Resource filters</span></span>

<span data-ttu-id="8ab22-329">리소스 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-329">Resource filters:</span></span>

* <span data-ttu-id="8ab22-330"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8ab22-331">실행이 필터 파이프라인 대부분을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8ab22-332">[권한 부여 필터](#authorization-filters) 는 리소스 필터 이전에만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8ab22-333">리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8ab22-334">예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8ab22-335">리소스 필터 예제:</span><span class="sxs-lookup"><span data-stu-id="8ab22-335">Resource filter examples:</span></span>

* <span data-ttu-id="8ab22-336">이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="8ab22-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8ab22-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8ab22-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8ab22-338">모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8ab22-339">대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8ab22-340">작업 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-340">Action filters</span></span>

<span data-ttu-id="8ab22-341">작업 필터는 페이지에 적용 **되지** 않습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8ab22-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="8ab22-342">Razor 페이지는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및를 지원 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8ab22-343">자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8ab22-344">작업 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-344">Action filters:</span></span>

* <span data-ttu-id="8ab22-345"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8ab22-346">해당 실행은 작업 메서드의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8ab22-347">다음 코드는 예제 작업 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8ab22-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8ab22-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="8ab22-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8ab22-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8ab22-352">작업 메서드에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8ab22-353">후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8ab22-354">`Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8ab22-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 다음과 같은 속성과 함께 `Controller` 및 `Result`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8ab22-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8ab22-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8ab22-358">이 속성을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-358">Setting this property to null:</span></span>

  * <span data-ttu-id="8ab22-359">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8ab22-360">`Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8ab22-361">`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출:</span><span class="sxs-lookup"><span data-stu-id="8ab22-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8ab22-362">후속 작업 필터 및 작업 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8ab22-363">`ActionExecutedContext`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8ab22-364">단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8ab22-365">프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8ab22-366">`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8ab22-367">모델 상태의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-367">Validate model state.</span></span>
* <span data-ttu-id="8ab22-368">상태가 유효하지 않은 경우 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="8ab22-369">특성으로 주석이 달린 컨트롤러 `[ApiController]` 는 자동으로 모델 상태의 유효성을 검사 하 고 400 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="8ab22-370">자세한 정보는 [자동 HTTP 400 응답](xref:web-api/index#automatic-http-400-responses)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="8ab22-371">`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8ab22-372">그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8ab22-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8ab22-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8ab22-375">`Exception`을 Null로 설정:</span><span class="sxs-lookup"><span data-stu-id="8ab22-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8ab22-376">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8ab22-377">`ActionExecutedContext.Result`은 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8ab22-378">예외 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-378">Exception filters</span></span>

<span data-ttu-id="8ab22-379">예외 필터:</span><span class="sxs-lookup"><span data-stu-id="8ab22-379">Exception filters:</span></span>

* <span data-ttu-id="8ab22-380"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="8ab22-381">공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8ab22-382">다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8ab22-383">다음 코드에서는 예외 필터를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="8ab22-384">예외 필터:</span><span class="sxs-lookup"><span data-stu-id="8ab22-384">Exception filters:</span></span>

* <span data-ttu-id="8ab22-385">before 및 after 이벤트가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-385">Don't have before and after events.</span></span>
* <span data-ttu-id="8ab22-386"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8ab22-387">Razor페이지 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생 하는 처리 되지 않은 예외를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8ab22-388">리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생 하는 예외를 catch **하지** 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8ab22-389">예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8ab22-390">그러면 예외가 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-390">This stops propagation of the exception.</span></span> <span data-ttu-id="8ab22-391">예외 필터는 예외를 “성공”으로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8ab22-392">이는 작업 필터에서만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-392">Only an action filter can do that.</span></span>

<span data-ttu-id="8ab22-393">예외 필터:</span><span class="sxs-lookup"><span data-stu-id="8ab22-393">Exception filters:</span></span>

* <span data-ttu-id="8ab22-394">작업 내에서 발생하는 예외를 잡는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8ab22-395">오류 처리 미들웨어만큼 유연하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8ab22-396">예외 처리의 경우 미들웨어를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8ab22-397">어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다.\*\*</span><span class="sxs-lookup"><span data-stu-id="8ab22-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8ab22-398">예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8ab22-399">API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8ab22-400">결과 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-400">Result filters</span></span>

<span data-ttu-id="8ab22-401">결과 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-401">Result filters:</span></span>

* <span data-ttu-id="8ab22-402">인터페이스 구현:</span><span class="sxs-lookup"><span data-stu-id="8ab22-402">Implement an interface:</span></span>
  * <span data-ttu-id="8ab22-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8ab22-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8ab22-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8ab22-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8ab22-405">해당 실행은 작업 결과의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8ab22-406">IResultFilter 및 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8ab22-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8ab22-407">다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8ab22-408">실행되는 결과의 종류는 작업에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8ab22-409">보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8ab22-410">API 메서드는 실행 결과의 일부로 일부 serialization을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8ab22-411">[작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8ab22-412">결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8ab22-413">다음 경우에는 결과 필터가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="8ab22-414">권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.</span><span class="sxs-lookup"><span data-stu-id="8ab22-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8ab22-415">예외 필터는 작업 결과를 생성하여 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8ab22-416"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>를 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락(short-circuit) 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8ab22-417">단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8ab22-418">`IResultFilter.OnResultExecuting`에서 예외 throw:</span><span class="sxs-lookup"><span data-stu-id="8ab22-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="8ab22-419">작업 결과 및 후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8ab22-420">성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8ab22-421"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="8ab22-422">이미 클라이언트에 전송된 응답은 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="8ab22-423">`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락(short-circuit) 처리된 경우 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8ab22-424">`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8ab22-425">`Exception`을 효과적으로 null로 설정하면 예외를 '처리'하고 예외가 파이프라인의 뒷부분에서 다시 throw되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="8ab22-426">결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8ab22-427">작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8ab22-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>의 `await next` 호출은 후속 결과 필터 및 작업 결과를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8ab22-429">단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8ab22-430">프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8ab22-431">이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8ab22-432">IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8ab22-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8ab22-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8ab22-434">여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-434">This includes action results produced by:</span></span>

* <span data-ttu-id="8ab22-435">단락하는 권한 부여 필터 및 리소스 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8ab22-436">예외 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-436">Exception filters.</span></span>

<span data-ttu-id="8ab22-437">예를 들어 다음 필터는 항상 작업 결과(콘텐츠 협상이 실패할 경우 *422 Unprocessable Entity* 상태 코드가 포함된 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 실행 및 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8ab22-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8ab22-438">IFilterFactory</span></span>

<span data-ttu-id="8ab22-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8ab22-440">따라서 `IFilterFactory` 인스턴스를 필터 파이프라인에서 `IFilterMetadata` 인스턴스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8ab22-441">런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8ab22-442">해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8ab22-443">앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8ab22-444">필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8ab22-445">필터는 다음 코드에서 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="8ab22-446">[샘플 다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)를 실행하여 이전 코드를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="8ab22-447">F12 개발자 도구를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8ab22-448">`https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8ab22-449">F12 개발자 도구는 샘플 코드에 의해 추가된 다음 응답 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8ab22-450">**작성자:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="8ab22-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="8ab22-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8ab22-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8ab22-452">**내부:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8ab22-452">**internal:** `My header`</span></span>

<span data-ttu-id="8ab22-453">이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8ab22-454">특성에서 구현된 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8ab22-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8ab22-455">`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8ab22-456">매개 변수 전달을 필요로 하지 않는 필터.</span><span class="sxs-lookup"><span data-stu-id="8ab22-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="8ab22-457">DI에 의해 채워져야 할 생성자 종속성이 있는 필터.</span><span class="sxs-lookup"><span data-stu-id="8ab22-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8ab22-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8ab22-459">`IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8ab22-460">`CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8ab22-461">다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8ab22-462">이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8ab22-463">필터 파이프라인에서 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="8ab22-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8ab22-464">리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8ab22-465">하지만 필터는 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, 컨텍스트 및 구문에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="8ab22-466">미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8ab22-467">다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8ab22-468"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8ab22-469">미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8ab22-470">다음 작업</span><span class="sxs-lookup"><span data-stu-id="8ab22-470">Next actions</span></span>

* <span data-ttu-id="8ab22-471">[ Razor 페이지에 대 한 필터 메서드를](xref:razor-pages/filter)참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8ab22-472">필터를 실험하려면 [GitHub 샘플을 다운로드하고, 테스트하고, 수정](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8ab22-473">작성자: [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8ab22-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8ab22-474">ASP.NET Core에서 *필터*를 사용하면 요청 처리 파이프라인의 특정 단계 전후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8ab22-475">기본 제공 필터는 다음과 같은 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8ab22-476">권한 부여(사용자가 권한이 없는 리소스에 액세스 방지).</span><span class="sxs-lookup"><span data-stu-id="8ab22-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8ab22-477">응답 캐싱(요청 파이프라인을 단락하면 캐시된 응답을 반환합니다).</span><span class="sxs-lookup"><span data-stu-id="8ab22-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8ab22-478">사용자 지정 필터를 만들어 교차 편집 문제를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8ab22-479">횡단 관심사의 사례로는 오류 처리, 캐싱, 구성, 권한 부여 및 로깅을 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8ab22-480">필터는 코드 중복을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="8ab22-481">예를 들어 오류 처리 예외 필터는 오류 처리를 통합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8ab22-482">이 문서 Razor 는 뷰가 있는 페이지, API 컨트롤러 및 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="8ab22-483">[예제 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8ab22-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8ab22-484">필터 작동 방법</span><span class="sxs-lookup"><span data-stu-id="8ab22-484">How filters work</span></span>

<span data-ttu-id="8ab22-485">필터는 ‘필터 파이프라인’이라고도 하는 ‘ASP.NET Core 동작 호출 파이프라인’내에서 실행됩니다.\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="8ab22-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="8ab22-486">필터 파이프라인은 ASP.NET Core가 실행할 작업을 선택한 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![요청은 기타 미들웨어, 라우팅 미들웨어, 작업 선택 및 ASP.NET Core 작업 호출 파이프라인을 통해서 처리됩니다.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8ab22-489">필터 형식</span><span class="sxs-lookup"><span data-stu-id="8ab22-489">Filter types</span></span>

<span data-ttu-id="8ab22-490">각 필터 형식은 필터 파이프라인의 다른 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8ab22-491">[권한 부여 필터](#authorization-filters)는 가장 먼저 실행되고 사용자가 요청에 대한 권한이 있는지 여부를 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8ab22-492">권한 부여 필터는 요청이 인증되지 않는 경우 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="8ab22-493">[리소스 필터](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8ab22-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8ab22-494">권한 부여 후 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-494">Run after authorization.</span></span>  
  * <span data-ttu-id="8ab22-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>는 나머지 필터 파이프라인보다 먼저 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8ab22-496">예를 들어 `OnResourceExecuting`는 모델 바인딩 전에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="8ab22-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>는 파이프라인의 나머지 부분이 완료된 후에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8ab22-498">[작업 필터](#action-filters)는 개별 작업 메서드가 호출된 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="8ab22-499">작업에 전달된 인수 및 작업에서 반환된 결과를 조작하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="8ab22-500">작업 필터는 페이지에서 지원 **되지 않습니다** Razor .</span><span class="sxs-lookup"><span data-stu-id="8ab22-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8ab22-501">[예외 필터](#exception-filters)는 응답 본문에 무언가 쓰여지기 전에 발생한 처리되지 않은 예외에 전역 정책을 적용하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="8ab22-502">[결과 필터](#result-filters)는 개별 작업이 실행된 전후에 즉시 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="8ab22-503">작업 메서드가 성공적으로 실행되는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8ab22-504">보기 또는 포맷터 실행을 둘러싸야 하는 논리에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8ab22-505">다음 다이어그램은 필터 형식이 필터 파이프라인에서 상호 작용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![요청은 권한 부여 필터, 리소스 필터, 모델 바인딩, 작업 필터, 작업 실행 및 작업 결과 변환, 예외 필터, 결과 필터 및 결과 실행을 통해 처리됩니다.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8ab22-508">구현</span><span class="sxs-lookup"><span data-stu-id="8ab22-508">Implementation</span></span>

<span data-ttu-id="8ab22-509">필터는 다른 인터페이스 정의를 통해 동기 및 비동기 구현을 모두 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8ab22-510">동기 필터는 해당 파이프라인 단계 전(`On-Stage-Executing`)과 후(`On-Stage-Executed`)에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="8ab22-511">예를 들어 `OnActionExecuting`는 작업 메서드가 호출되기 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="8ab22-512">`OnActionExecuted`는 작업 메서드가 반환된 후 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8ab22-513">비동기 필터는 `On-Stage-ExecutionAsync` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8ab22-514">이전 코드에서 `SampleAsyncActionFilter`에는 작업 메서드를 실행하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>(`next`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="8ab22-515">각 `On-Stage-ExecutionAsync` 메서드는 필터의 파이프라인 단계를 실행하는 `FilterType-ExecutionDelegate`를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8ab22-516">여러 필터 단계</span><span class="sxs-lookup"><span data-stu-id="8ab22-516">Multiple filter stages</span></span>

<span data-ttu-id="8ab22-517">단일 클래스에서 여러 필터 단계에 대한 인터페이스를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8ab22-518">예를 들어 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 클래스는 `IActionFilter`, `IResultFilter` 및 해당 비동기 값을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="8ab22-519">필터 인터페이스의 동기 또는 비동기 버전을 모두 구현하지 **말고** 그 중 **한 가지**만 구현하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8ab22-520">런타임은 먼저 필터가 비동기 인터페이스를 구현하는지를 확인하고 그렇다면 이를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8ab22-521">그렇지 않으면 동기 인터페이스의 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8ab22-522">비동기 및 동기 인터페이스가 모두 하나의 클래스에 구현된 경우에는 비동기 메서드만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8ab22-523"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 같은 추상 클래스를 사용하는 경우 각 필터 형식에 대한 동기 메서드 또는 비동기 메서드만 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8ab22-524">기본 제공 필터 특성</span><span class="sxs-lookup"><span data-stu-id="8ab22-524">Built-in filter attributes</span></span>

<span data-ttu-id="8ab22-525">ASP.NET Core에는 서브클래싱 및 사용자 지정할 수 있는 기본 제공 특성 기반 필터가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8ab22-526">예를 들어 다음 결과 필터는 응답에 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8ab22-527">특성을 사용하면 이전 예제와 같이 필터에서 인수를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8ab22-528">컨트롤러나 작업 메서드에 `AddHeaderAttribute`를 적용하고 HTTP 헤더의 이름 및 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="8ab22-529">여러 필터 인터페이스에는 사용자 지정 구현에 대한 기본 클래스로 사용할 수 있는 해당 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8ab22-530">필터 특성:</span><span class="sxs-lookup"><span data-stu-id="8ab22-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8ab22-531">필터 범위 및 실행 순서</span><span class="sxs-lookup"><span data-stu-id="8ab22-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="8ab22-532">다음 세 가지 *범위*중 하나에서 파이프라인에 필터를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8ab22-533">작업에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="8ab22-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="8ab22-534">컨트롤러에서 특성 사용.</span><span class="sxs-lookup"><span data-stu-id="8ab22-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="8ab22-535">다음 코드와 같이 모든 컨트롤러와 작업에 전역으로 사용:</span><span class="sxs-lookup"><span data-stu-id="8ab22-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8ab22-536">이전 코드는 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 컬렉션을 사용하여 3개의 필터를 전역적으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="8ab22-537">기본 실행 순서</span><span class="sxs-lookup"><span data-stu-id="8ab22-537">Default order of execution</span></span>

<span data-ttu-id="8ab22-538">*동일한 유형*의 필터가 여러 개 있는 경우 범위가 필터 실행의 기본 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8ab22-539">전역 필터는 클래스 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-539">Global filters surround class filters.</span></span> <span data-ttu-id="8ab22-540">클래스 필터는 메서드 필터를 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-540">Class filters surround method filters.</span></span>

<span data-ttu-id="8ab22-541">필터 중첩의 결과로 필터의 *after* 코드는 *before* 코드의 역순으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8ab22-542">필터의 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-542">The filter sequence:</span></span>

* <span data-ttu-id="8ab22-543">전역 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8ab22-544">컨트롤러 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="8ab22-545">작업 메서드 필터의 *before* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8ab22-546">작업 메서드 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8ab22-547">컨트롤러 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="8ab22-548">전역 필터의 *after* 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8ab22-549">다음 예제는 필터 메서드가 동기 작업 필터에 대해 호출되는 순서를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8ab22-550">순서</span><span class="sxs-lookup"><span data-stu-id="8ab22-550">Sequence</span></span> | <span data-ttu-id="8ab22-551">필터 범위</span><span class="sxs-lookup"><span data-stu-id="8ab22-551">Filter scope</span></span> | <span data-ttu-id="8ab22-552">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8ab22-553">1</span><span class="sxs-lookup"><span data-stu-id="8ab22-553">1</span></span> | <span data-ttu-id="8ab22-554">전역</span><span class="sxs-lookup"><span data-stu-id="8ab22-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8ab22-555">2</span><span class="sxs-lookup"><span data-stu-id="8ab22-555">2</span></span> | <span data-ttu-id="8ab22-556">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="8ab22-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8ab22-557">3</span><span class="sxs-lookup"><span data-stu-id="8ab22-557">3</span></span> | <span data-ttu-id="8ab22-558">메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8ab22-559">4</span><span class="sxs-lookup"><span data-stu-id="8ab22-559">4</span></span> | <span data-ttu-id="8ab22-560">메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8ab22-561">5</span><span class="sxs-lookup"><span data-stu-id="8ab22-561">5</span></span> | <span data-ttu-id="8ab22-562">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="8ab22-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8ab22-563">6</span><span class="sxs-lookup"><span data-stu-id="8ab22-563">6</span></span> | <span data-ttu-id="8ab22-564">전역</span><span class="sxs-lookup"><span data-stu-id="8ab22-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="8ab22-565">이 시퀀스는 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-565">This sequence shows:</span></span>

* <span data-ttu-id="8ab22-566">메서드 필터는 컨트롤러 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="8ab22-567">컨트롤러 필터는 전역 필터 내에서 중첩됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="8ab22-568">컨트롤러 및 Razor 페이지 수준 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="8ab22-569"><xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스에서 상속되는 모든 컨트롤러에는 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 및 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 메서드가 포함됩니다. 이러한 메서드는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8ab22-570">다음 메서드는</span><span class="sxs-lookup"><span data-stu-id="8ab22-570">These methods:</span></span>

* <span data-ttu-id="8ab22-571">지정된 작업을 위해 실행되는 필터를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8ab22-572">`OnActionExecuting`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8ab22-573">`OnActionExecuted`는 모든 작업 필터 이후에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8ab22-574">`OnActionExecutionAsync`는 작업 필터 이전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8ab22-575">작업 메서드 이후 `next`가 실행된 후의 필터 코드.</span><span class="sxs-lookup"><span data-stu-id="8ab22-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8ab22-576">예를 들어 다운로드 예제에서 `MySampleActionFilter`은 시작할 때 전역적으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8ab22-577">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="8ab22-577">The `TestController`:</span></span>

* <span data-ttu-id="8ab22-578">`SampleActionFilterAttribute`(`[SampleActionFilter]`)를 `FilterTest2` 작업에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8ab22-579">`OnActionExecuting` 및 `OnActionExecuted`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="8ab22-580">`https://localhost:5001/Test/FilterTest2`로 이동하면 다음 코드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8ab22-581">Razor페이지는 [ Razor 필터 메서드를 재정의 하 여 페이지 필터 구현](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8ab22-582">기본 순서 재정의</span><span class="sxs-lookup"><span data-stu-id="8ab22-582">Overriding the default order</span></span>

<span data-ttu-id="8ab22-583"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>를 구현하여 실행의 기본 순서를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8ab22-584">`IOrderedFilter`은 실행 순서를 결정하는 데 범위보다 우선 순위가 높은 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8ab22-585">낮은 `Order` 값을 가진 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8ab22-586">더 높은 `Order` 값을 가진 필터 이전에 *before* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8ab22-587">더 높은 `Order` 값을 가진 필터 이후에 *after* 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8ab22-588">생성자 매개 변수로 `Order` 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="8ab22-589">이전 예제와 동일한 3개의 작업 필터를 가정해보세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="8ab22-590">컨트롤러와 전역 필터의 `Order` 속성을 각각 1과 2로 설정하면 실행 순서가 반대가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="8ab22-591">순서</span><span class="sxs-lookup"><span data-stu-id="8ab22-591">Sequence</span></span> | <span data-ttu-id="8ab22-592">필터 범위</span><span class="sxs-lookup"><span data-stu-id="8ab22-592">Filter scope</span></span> | <span data-ttu-id="8ab22-593">`Order` 속성</span><span class="sxs-lookup"><span data-stu-id="8ab22-593">`Order` property</span></span> | <span data-ttu-id="8ab22-594">필터 메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="8ab22-595">1</span><span class="sxs-lookup"><span data-stu-id="8ab22-595">1</span></span> | <span data-ttu-id="8ab22-596">메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-596">Method</span></span> | <span data-ttu-id="8ab22-597">0</span><span class="sxs-lookup"><span data-stu-id="8ab22-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8ab22-598">2</span><span class="sxs-lookup"><span data-stu-id="8ab22-598">2</span></span> | <span data-ttu-id="8ab22-599">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="8ab22-599">Controller</span></span> | <span data-ttu-id="8ab22-600">1</span><span class="sxs-lookup"><span data-stu-id="8ab22-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8ab22-601">3</span><span class="sxs-lookup"><span data-stu-id="8ab22-601">3</span></span> | <span data-ttu-id="8ab22-602">전역</span><span class="sxs-lookup"><span data-stu-id="8ab22-602">Global</span></span> | <span data-ttu-id="8ab22-603">2</span><span class="sxs-lookup"><span data-stu-id="8ab22-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8ab22-604">4</span><span class="sxs-lookup"><span data-stu-id="8ab22-604">4</span></span> | <span data-ttu-id="8ab22-605">전역</span><span class="sxs-lookup"><span data-stu-id="8ab22-605">Global</span></span> | <span data-ttu-id="8ab22-606">2</span><span class="sxs-lookup"><span data-stu-id="8ab22-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8ab22-607">5</span><span class="sxs-lookup"><span data-stu-id="8ab22-607">5</span></span> | <span data-ttu-id="8ab22-608">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="8ab22-608">Controller</span></span> | <span data-ttu-id="8ab22-609">1</span><span class="sxs-lookup"><span data-stu-id="8ab22-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8ab22-610">6</span><span class="sxs-lookup"><span data-stu-id="8ab22-610">6</span></span> | <span data-ttu-id="8ab22-611">메서드</span><span class="sxs-lookup"><span data-stu-id="8ab22-611">Method</span></span> | <span data-ttu-id="8ab22-612">0</span><span class="sxs-lookup"><span data-stu-id="8ab22-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="8ab22-613">`Order` 속성은 필터가 실행되는 순서를 결정할 때 범위를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8ab22-614">필터가 순서에 따라 먼저 정렬된 다음, 범위는 연결을 끊는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8ab22-615">모든 기본 제공 필터는 `IOrderedFilter`을 구현하고 기본 `Order` 값을 0으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8ab22-616">기본 제공 필터의 경우 `Order`를 0이 아닌 값으로 설정하지 않는 한 범위가 순서를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8ab22-617">취소 및 단락</span><span class="sxs-lookup"><span data-stu-id="8ab22-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8ab22-618">필터 메서드에 제공되는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 매개 변수의 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 속성을 설정하여 필터 파이프라인을 단락시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8ab22-619">예를 들어 다음 리소스 필터는 파이프라인의 나머지 부분이 실행되지 않도록 막습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8ab22-620">다음 코드에서 `ShortCircuitingResourceFilter` 및 `AddHeader` 필터는 `SomeResource` 작업 메서드를 대상으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8ab22-621">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8ab22-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8ab22-622">리소스 필터이고 `AddHeader`이 작업 필터이기 때문에 먼저 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8ab22-623">나머지 파이프라인을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8ab22-624">따라서 `AddHeader` 필터는 `SomeResource` 작업에 대해 절대 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8ab22-625">이 동작은 `ShortCircuitingResourceFilter`가 먼저 실행되기 때문에 작업 메서드 수준에서 두 필터를 적용하더라도 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8ab22-626">해당 필터 형식 또는 `Order` 속성의 명시적 사용으로 인해 `ShortCircuitingResourceFilter`이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="8ab22-627">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="8ab22-627">Dependency injection</span></span>

<span data-ttu-id="8ab22-628">형식 또는 인스턴스별로 필터를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8ab22-629">인스턴스가 추가되면 모든 요청에 해당 인스턴스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8ab22-630">형식이 추가되면 해당 필터는 형식으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8ab22-631">형식으로 활성화된 필터는 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-631">A type-activated filter means:</span></span>

* <span data-ttu-id="8ab22-632">각 요청에 대해 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-632">An instance is created for each request.</span></span>
* <span data-ttu-id="8ab22-633">모든 생성자 종속성이 DI([종속성 주입](xref:fundamentals/dependency-injection))를 통해서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8ab22-634">특성으로 구현되고 컨트롤러 클래스 또는 작업 메서드에 직접 추가되는 필터에는 DI([종속성 주입](xref:fundamentals/dependency-injection))에서 제공하는 생성자 종속성이 없을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8ab22-635">DI는 다음과 같은 이유로 생성자 종속성을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8ab22-636">특성이 적용될 때 해당 생성자 매개 변수가 제공되어야 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8ab22-637">특성이 작동하는 방법의 제한 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8ab22-638">다음 필터는 DI에서 제공하는 생성자 종속성을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8ab22-639">특성에서 구현된 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8ab22-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8ab22-640">위의 필터는 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8ab22-641">로거는 DI를 통해서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-641">Loggers are available from DI.</span></span> <span data-ttu-id="8ab22-642">그러나 로깅 용도로만 필터를 만들거나 사용하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8ab22-643">[기본 제공 프레임워크 로깅](xref:fundamentals/logging/index)은 일반적으로 로깅에 필요한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8ab22-644">필터에 추가된 로깅은:</span><span class="sxs-lookup"><span data-stu-id="8ab22-644">Logging added to filters:</span></span>

* <span data-ttu-id="8ab22-645">비즈니스 도메인 문제 또는 필터 고유의 동작에 중점을 두어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8ab22-646">작업 또는 다른 프레임워크 이벤트를 로깅해서는 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="8ab22-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8ab22-647">기본 제공 필터는 작업 및 프레임워크 이벤트를 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8ab22-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8ab22-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="8ab22-649">서비스 필터 구현 형식은 `ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8ab22-650"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 DI에서 필터의 인스턴스를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8ab22-651">다음 코드는 `AddHeaderResultServiceFilter`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8ab22-652">다음 코드에서 `AddHeaderResultServiceFilter`는 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="8ab22-653">다음 코드에서 `ServiceFilter` 특성은 DI에서 `AddHeaderResultServiceFilter` 필터의 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8ab22-654">`ServiceFilterAttribute`를 사용할 때 [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8ab22-655">필터 인스턴스가 원래 생성된 요청 범위 외부에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8ab22-656">ASP.NET Core 런타임은 다음을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8ab22-657">필터의 단일 인스턴스 생성.</span><span class="sxs-lookup"><span data-stu-id="8ab22-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8ab22-658">나중에 DI 컨테이너에서 필터가 다시 요청되지 않음.</span><span class="sxs-lookup"><span data-stu-id="8ab22-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8ab22-659">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8ab22-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8ab22-661">`IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8ab22-662">`CreateInstance`는 DI에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8ab22-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8ab22-663">TypeFilterAttribute</span></span>

<span data-ttu-id="8ab22-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>와 비슷하지만 해당 형식은 DI 컨테이너에서 직접 확인되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8ab22-665"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>를 사용하여 형식을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8ab22-666">`TypeFilterAttribute` 형식은 DI 컨테이너에서 직접 해결되지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8ab22-667">`TypeFilterAttribute`을 사용하여 참조되는 형식은 DI 컨테이너를 사용하여 등록할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8ab22-668">DI 컨테이너에서 충족하는 종속성을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8ab22-669">`TypeFilterAttribute`는 형식에 대한 생성자 인수를 필요에 따라 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8ab22-670">`TypeFilterAttribute`를 사용할 때 [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable)을 설정하면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8ab22-671">필터 인스턴스가 원래 생성된 요청 범위 밖에서 재사용될 가능성이 *있음*을 암시하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8ab22-672">ASP.NET Core 런타임은 단일 필터 인스턴스가 생성되도록 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8ab22-673">싱글톤 이외의 수명이 지정된 서비스에 의존하는 필터와 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8ab22-674">다음 예제에서는 `TypeFilterAttribute`를 사용하여 인수를 형식에 전달하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8ab22-675">권한 부여 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-675">Authorization filters</span></span>

<span data-ttu-id="8ab22-676">권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-676">Authorization filters:</span></span>

* <span data-ttu-id="8ab22-677">필터 파이프라인에서 첫 번째로 실행되는 필터입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8ab22-678">동작 메서드에 대한 액세스를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-678">Control access to action methods.</span></span>
* <span data-ttu-id="8ab22-679">before 메서드는 있지만 after 메서드는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="8ab22-680">사용자 지정 권한 부여 필터는 사용자 지정 권한 부여 프레임워크를 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8ab22-681">사용자 지정 필터를 작성하는 대신 권한 부여 정책을 구성하거나 사용자 지정 권한 부여 정책을 작성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8ab22-682">기본 제공 권한 부여 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="8ab22-683">권한 부여 시스템을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-683">Calls the authorization system.</span></span>
* <span data-ttu-id="8ab22-684">요청을 승인하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-684">Does not authorize requests.</span></span>

<span data-ttu-id="8ab22-685">권한 부여 필터 내에서 예외를 던지지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="8ab22-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8ab22-686">해당 예외는 처리되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-686">The exception will not be handled.</span></span>
* <span data-ttu-id="8ab22-687">예외 필터가 해당 예외를 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8ab22-688">권한 부여 필터에서 예외가 발생할 경우 챌린지 발행을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8ab22-689">[권한 부여](xref:security/authorization/introduction)에 대해 자세히 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8ab22-690">리소스 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-690">Resource filters</span></span>

<span data-ttu-id="8ab22-691">리소스 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-691">Resource filters:</span></span>

* <span data-ttu-id="8ab22-692"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8ab22-693">실행이 필터 파이프라인 대부분을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8ab22-694">[권한 부여 필터](#authorization-filters) 는 리소스 필터 이전에만 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8ab22-695">리소스 필터는 파이프라인의 대부분을 단락시켜야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8ab22-696">예를 들어 캐싱 필터는 캐시 적중 시 파이프라인의 나머지 부분을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8ab22-697">리소스 필터 예제:</span><span class="sxs-lookup"><span data-stu-id="8ab22-697">Resource filter examples:</span></span>

* <span data-ttu-id="8ab22-698">이전에 살펴본 [단락 리소스 필터](#short-circuiting-resource-filter).</span><span class="sxs-lookup"><span data-stu-id="8ab22-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8ab22-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8ab22-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8ab22-700">모델 바인딩이 양식 데이터에 액세스하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8ab22-701">대용량 파일 업로드에서 양식 데이터를 메모리로 읽어들이는 것을 방지하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8ab22-702">작업 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8ab22-703">작업 필터는 페이지에 적용 **되지** 않습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="8ab22-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="8ab22-704">Razor 페이지는 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 및를 지원 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8ab22-705">자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8ab22-706">작업 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-706">Action filters:</span></span>

* <span data-ttu-id="8ab22-707"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 인터페이스 중 하나를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8ab22-708">해당 실행은 작업 메서드의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8ab22-709">다음 코드는 예제 작업 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8ab22-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>는 다음과 같은 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8ab22-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 작업 메서드에 대한 입력을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="8ab22-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - 컨트롤러 인스턴스를 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8ab22-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` 설정은 작업 메서드 및 후속 작업 필터의 실행을 단락시킵니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8ab22-714">작업 메서드에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8ab22-715">후속 필터의 실행을 막습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8ab22-716">`Result` 설정과 달리 성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8ab22-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>는 다음과 같은 속성과 함께 `Controller` 및 `Result`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8ab22-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 작업 실행이 다른 필터에 의해 단락된 경우 true입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8ab22-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 작업 또는 이전에 실행된 작업 필터에서 예외가 던져진 경우 null이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8ab22-720">이 속성을 null로 설정하면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-720">Setting this property to null:</span></span>

  * <span data-ttu-id="8ab22-721">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8ab22-722">`Result`는 작업 메서드에서 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8ab22-723">`IAsyncActionFilter`의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>에 대한 호출:</span><span class="sxs-lookup"><span data-stu-id="8ab22-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8ab22-724">후속 작업 필터 및 작업 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8ab22-725">`ActionExecutedContext`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8ab22-726">단락시키려면 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName>을 결과 인스턴스에 할당하고 `next`(`ActionExecutionDelegate`)를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8ab22-727">프레임워크는 서브클래싱 할 수 있는 추상 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8ab22-728">`OnActionExecuting` 작업 필터는 다음에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8ab22-729">모델 상태의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-729">Validate model state.</span></span>
* <span data-ttu-id="8ab22-730">상태가 유효하지 않은 경우 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="8ab22-731">`OnActionExecuted` 메서드는 작업 메서드 이후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8ab22-732">그리고 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 속성을 통해 작업 결과를 확인하고 조작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8ab22-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>는 작업 실행이 다른 필터에 의해 단락된 경우 true로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8ab22-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>은 작업 또는 후속 작업 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8ab22-735">`Exception`을 Null로 설정:</span><span class="sxs-lookup"><span data-stu-id="8ab22-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8ab22-736">예외를 효과적으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8ab22-737">`ActionExecutedContext.Result`은 작업 메서드에서 정상적으로 반환되는 것처럼 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8ab22-738">예외 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-738">Exception filters</span></span>

<span data-ttu-id="8ab22-739">예외 필터:</span><span class="sxs-lookup"><span data-stu-id="8ab22-739">Exception filters:</span></span>

* <span data-ttu-id="8ab22-740"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="8ab22-741">공통 오류 처리 정책을 구현하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8ab22-742">다음 예제 예외 필터는 사용자 지정 오류 보기를 사용하여 앱을 개발 중인 경우에 발생하는 예외에 대한 세부 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8ab22-743">예외 필터:</span><span class="sxs-lookup"><span data-stu-id="8ab22-743">Exception filters:</span></span>

* <span data-ttu-id="8ab22-744">before 및 after 이벤트가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-744">Don't have before and after events.</span></span>
* <span data-ttu-id="8ab22-745"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8ab22-746">Razor페이지 또는 컨트롤러 생성, [모델 바인딩](xref:mvc/models/model-binding), 작업 필터 또는 작업 메서드에서 발생 하는 처리 되지 않은 예외를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8ab22-747">리소스 필터, 결과 필터 또는 MVC 결과 실행에서 발생 하는 예외를 catch **하지** 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8ab22-748">예외를 처리하려면 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 속성을 `true`로 설정하거나 응답을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8ab22-749">그러면 예외가 전파되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-749">This stops propagation of the exception.</span></span> <span data-ttu-id="8ab22-750">예외 필터는 예외를 “성공”으로 변환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8ab22-751">이는 작업 필터에서만 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-751">Only an action filter can do that.</span></span>

<span data-ttu-id="8ab22-752">예외 필터:</span><span class="sxs-lookup"><span data-stu-id="8ab22-752">Exception filters:</span></span>

* <span data-ttu-id="8ab22-753">작업 내에서 발생하는 예외를 잡는 데 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8ab22-754">오류 처리 미들웨어만큼 유연하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8ab22-755">예외 처리의 경우 미들웨어를 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8ab22-756">어떤 작업 메서드가 호출되는지에 따라 오류 처리 방식이 ‘다른’ 경우에만 예외 필터를 사용합니다.\*\*</span><span class="sxs-lookup"><span data-stu-id="8ab22-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8ab22-757">예를 들어 앱에는 API 엔드포인트 및 보기/HTML 모두에 대한 작업 메서드가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8ab22-758">API 엔드포인트는 JSON으로 오류 정보를 반환할 수 있습니다. 반면 보기 기반 작업은 HTML로 오류 페이지를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8ab22-759">결과 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-759">Result filters</span></span>

<span data-ttu-id="8ab22-760">결과 필터는:</span><span class="sxs-lookup"><span data-stu-id="8ab22-760">Result filters:</span></span>

* <span data-ttu-id="8ab22-761">인터페이스 구현:</span><span class="sxs-lookup"><span data-stu-id="8ab22-761">Implement an interface:</span></span>
  * <span data-ttu-id="8ab22-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8ab22-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8ab22-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 또는 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8ab22-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8ab22-764">해당 실행은 작업 결과의 실행을 둘러쌉니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8ab22-765">IResultFilter 및 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8ab22-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8ab22-766">다음 코드는 HTTP 헤더를 추가하는 결과 필터를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8ab22-767">실행되는 결과의 종류는 작업에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8ab22-768">보기를 반환하는 작업에는 실행 중인 <xref:Microsoft.AspNetCore.Mvc.ViewResult>의 일부로 모든 Razor 프로세스가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8ab22-769">API 메서드는 실행 결과의 일부로 일부 serialization을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8ab22-770">[작업 결과](xref:mvc/controllers/actions)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8ab22-771">결과 필터는 작업 또는 작업 필터가 작업 결과를 생성하는 경우에만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8ab22-772">다음 경우에는 결과 필터가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="8ab22-773">권한 부여 필터 또는 리소스 필터가 파이프라인을 단락시킬 경우.</span><span class="sxs-lookup"><span data-stu-id="8ab22-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8ab22-774">예외 필터는 작업 결과를 생성하여 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8ab22-775"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 메서드는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName>를 `true`로 설정하여 작업 결과 및 후속 결과 필터를 단락(short-circuit) 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8ab22-776">단락시킬 경우 빈 응답을 생성하지 않도록 응답 개체에 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8ab22-777">`IResultFilter.OnResultExecuting`에서 예외를 던지면:</span><span class="sxs-lookup"><span data-stu-id="8ab22-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="8ab22-778">작업 결과 및 후속 필터의 실행을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8ab22-779">성공적인 결과 대신 실패로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8ab22-780"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 메서드가 실행될 때에는 응답이 이미 클라이언트에 전송되었을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="8ab22-781">이미 클라이언트에 전송된 응답은 더이상 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="8ab22-782">`ResultExecutedContext.Canceled`는 작업 결과 실행이 다른 필터에 의해 단락(short-circuit) 처리된 경우 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8ab22-783">`ResultExecutedContext.Exception`은 작업 결과 또는 후속 결과 필터에서 예외가 던져진 경우 null이 아닌 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8ab22-784">`Exception`을 null로 설정하면 효과적으로 예외를 처리하고 이후의 파이프라인에서 ASP.NET Core에 의해 예외가 다시 던져지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="8ab22-785">결과 필터에서 예외를 처리하는 경우 데이터를 응답에 쓸 수 있는 신뢰할 수 있는 방법이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8ab22-786">작업 결과가 예외를 던질 때 헤더가 클라이언트에 플러시된 경우 오류 코드를 전송하기 위한 신뢰할 수 있는 메커니즘이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8ab22-787"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>의 경우 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate>의 `await next` 호출은 후속 결과 필터 및 작업 결과를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8ab22-788">단락시키려면 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel)을 `true`로 설정하고 `ResultExecutionDelegate`를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8ab22-789">프레임워크는 서브클래싱 할 수 있는 추상 `ResultFilterAttribute`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8ab22-790">이전에 표시된 [AddHeaderAttribute](#add-header-attribute) 클래스는 결과 필터 특성의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8ab22-791">IAlwaysRunResultFilter 및 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8ab22-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8ab22-792"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 인터페이스는 모든 작업 결과에 대해 실행되는 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 구현을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8ab22-793">여기에는 다음에 의해 생성되는 작업 결과가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-793">This includes action results produced by:</span></span>

* <span data-ttu-id="8ab22-794">단락하는 권한 부여 필터 및 리소스 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8ab22-795">예외 필터</span><span class="sxs-lookup"><span data-stu-id="8ab22-795">Exception filters.</span></span>

<span data-ttu-id="8ab22-796">예를 들어 다음 필터는 항상 작업 결과(콘텐츠 협상이 실패할 경우 *422 Unprocessable Entity* 상태 코드가 포함된 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>)를 실행 및 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8ab22-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8ab22-797">IFilterFactory</span></span>

<span data-ttu-id="8ab22-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8ab22-799">따라서 `IFilterFactory` 인스턴스를 필터 파이프라인에서 `IFilterMetadata` 인스턴스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8ab22-800">런타임이 필터를 호출하려고 준비할 때 `IFilterFactory`로 캐스팅을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8ab22-801">해당 캐스팅에 성공하면 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 호출하여 호출되는 `IFilterMetadata` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8ab22-802">앱이 시작될 때 정확한 필터 파이프라인을 명시적으로 설정할 필요가 없으므로 유연한 디자인을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8ab22-803">필터를 만드는 다른 방법으로 사용자 지정 특성 구현을 사용하여 `IFilterFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8ab22-804">[다운로드 예제](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)를 실행하여 이전 코드를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="8ab22-805">F12 개발자 도구를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8ab22-806">`https://localhost:5001/Sample/HeaderWithFactory`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8ab22-807">F12 개발자 도구는 샘플 코드에 의해 추가된 다음 응답 헤더를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8ab22-808">**작성자:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="8ab22-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="8ab22-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8ab22-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8ab22-810">**내부:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8ab22-810">**internal:** `My header`</span></span>

<span data-ttu-id="8ab22-811">이전 코드에서는 **internal:** `My header` 응답 헤더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8ab22-812">특성에서 구현된 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8ab22-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8ab22-813">`IFilterFactory`를 구현하는 필터는 다음과 같은 필터에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8ab22-814">매개 변수 전달을 필요로 하지 않는 필터.</span><span class="sxs-lookup"><span data-stu-id="8ab22-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="8ab22-815">DI에 의해 채워져야 할 생성자 종속성이 있는 필터.</span><span class="sxs-lookup"><span data-stu-id="8ab22-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8ab22-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>는 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8ab22-817">`IFilterFactory`은 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 인스턴스를 만들기 위해 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8ab22-818">`CreateInstance`는 서비스 컨테이너(DI)에서 지정된 형식을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8ab22-819">다음 코드는 `[SampleActionFilter]`을 적용하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8ab22-820">이전 코드에서 `[SampleActionFilter]`로 메서드를 장식하는 것이 `SampleActionFilter`을 적용하는 데 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8ab22-821">필터 파이프라인에서 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="8ab22-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8ab22-822">리소스 필터는 파이프라인의 뒷부분에 제공되는 모든 실행을 둘러싼다는 점에서 [미들웨어](xref:fundamentals/middleware/index)처럼 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8ab22-823">하지만 필터는 ASP.NET Core 런타임의 일부라는 점에서 미들웨어와 다릅니다. 즉, ASP.NET Core 컨텍스트 및 구문에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="8ab22-824">미들웨어를 필터로 사용하려면 필터 파이프라인에 삽입할 미들웨어를 지정하는 `Configure` 메서드를 포함한 형식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8ab22-825">다음 예제는 지역화 미들웨어를 사용하여 요청에 대한 현재 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8ab22-826"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute>를 사용하여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8ab22-827">미들웨어 필터는 모델 바인딩 이전 및 나머지 파이프라인 이후에 리소스 필터와 동일한 필터 파이프라인 단계에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8ab22-828">다음 작업</span><span class="sxs-lookup"><span data-stu-id="8ab22-828">Next actions</span></span>

* <span data-ttu-id="8ab22-829">[ Razor 페이지에 대 한 필터 메서드를](xref:razor-pages/filter)참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8ab22-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8ab22-830">필터를 실험하려면 [GitHub 샘플을 다운로드하고, 테스트하고, 수정](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="8ab22-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
