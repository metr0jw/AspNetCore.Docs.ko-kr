---
title: ASP.NET Core의 모델 바인딩
author: rick-anderson
description: ASP.NET Core에서 모델 바인딩의 작동 방법 및 해당 동작을 사용자 지정하는 방법을 알아봅니다.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: ec36ff6d646e0554550a4372389aed89aa267b1f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633983"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="6ad4f-103">ASP.NET Core의 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="6ad4f-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ad4f-104">이 문서는 모델 바인딩이 무엇인지, 작동 방법 및 해당 동작을 사용자 지정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="6ad4f-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="6ad4f-106">모델 바인딩이란</span><span class="sxs-lookup"><span data-stu-id="6ad4f-106">What is Model binding</span></span>

<span data-ttu-id="6ad4f-107">컨트롤러와 Razor 페이지는 HTTP 요청에서 가져온 데이터로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="6ad4f-108">예를 들어 경로 데이터는 레코드 키를 제공할 수 있으며, 게시된 양식 필드는 모델의 속성에 대한 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="6ad4f-109">이러한 각 값을 검색하고 문자열에서 .NET 형식으로 변환하도록 코드를 작성하는 것은 번거롭고 오류가 발생하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="6ad4f-110">모델 바인딩은 이 프로세스를 자동화합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-110">Model binding automates this process.</span></span> <span data-ttu-id="6ad4f-111">모델 바인딩 시스템:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-111">The model binding system:</span></span>

* <span data-ttu-id="6ad4f-112">경로 데이터, 양식 필드 및 쿼리 문자열과 같은 다양한 원본의 데이터를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="6ad4f-113">Razor메서드 매개 변수 및 공용 속성의 컨트롤러 및 페이지에 데이터를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="6ad4f-114">문자열 데이터를 .NET 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="6ad4f-115">복합 형식의 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="6ad4f-116">예제</span><span class="sxs-lookup"><span data-stu-id="6ad4f-116">Example</span></span>

<span data-ttu-id="6ad4f-117">다음 작업 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="6ad4f-118">앱은 이 URL로 요청을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="6ad4f-119">모델 바인딩은 라우팅 시스템이 작업 메서드를 선택한 후 다음 단계를 통해 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="6ad4f-120">`GetByID`의 첫 번째 매개 변수, `id`라는 정수를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="6ad4f-121">HTTP 요청에서 사용 가능한 원본을 찾고 경로 데이터에서 `id` = "2"를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="6ad4f-122">문자열 "2"를 정수 2로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="6ad4f-123">`GetByID`의 다음 매개 변수, `dogsOnly`라는 부울을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="6ad4f-124">원본을 찾고 쿼리 문자열에서 "DogsOnly=true"를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="6ad4f-125">이름 일치는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="6ad4f-126">문자열 "true"를 부울 `true`로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="6ad4f-127">그런 다음, 프레임워크는 `id` 매개 변수에 대해 2를 `dogsOnly` 매개 변수에 대해 `true`를 전달하는 `GetById` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="6ad4f-128">앞의 예제에서 모델 바인딩 대상은 단순 형식인 메서드 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="6ad4f-129">대상은 복합 형식의 속성일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="6ad4f-130">각 속성이 성공적으로 바인딩된 후 [모델 유효성 검사](xref:mvc/models/validation)가 해당 속성에 대해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="6ad4f-131">모델에 바인딩되는 데이터의 레코드 및 모든 바인딩 또는 유효성 검사 오류는 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 또는 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="6ad4f-132">이 프로세스가 성공되었는지 확인하기 위해 앱은 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 플래그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="6ad4f-133">대상</span><span class="sxs-lookup"><span data-stu-id="6ad4f-133">Targets</span></span>

<span data-ttu-id="6ad4f-134">모델 바인딩은 다음 종류의 대상에 대한 값을 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="6ad4f-135">요청이 라우팅되는 컨트롤러 작업 메서드의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="6ad4f-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="6ad4f-136">Razor요청이 라우팅되는 페이지 처리기 메서드의 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="6ad4f-137">특성으로 지정되는 경우 컨트롤러 또는 `PageModel` 클래스의 공용 속성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="6ad4f-138">[BindProperty] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-138">[BindProperty] attribute</span></span>

<span data-ttu-id="6ad4f-139">모델 바인딩이 해당 속성을 대상으로 하도록 컨트롤러의 공용 속성 또는 `PageModel` 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="6ad4f-140">[BindProperties] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-140">[BindProperties] attribute</span></span>

<span data-ttu-id="6ad4f-141">ASP.NET Core 2.1 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="6ad4f-142">모델 바인딩이 클래스의 모든 공용 속성을 대상으로 하도록 컨트롤러 또는 `PageModel` 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="6ad4f-143">HTTP GET 요청에 대한 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="6ad4f-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="6ad4f-144">기본적으로 속성은 HTTP GET 요청에 대해 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="6ad4f-145">일반적으로 GET 요청에 대해 필요한 것은 레코드 ID 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="6ad4f-146">레코드 ID는 데이터베이스에 있는 항목을 찾는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="6ad4f-147">따라서 모델의 인스턴스를 포함하는 속성을 바인딩할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="6ad4f-148">속성을 GET 요청의 데이터에 바인딩하려는 시나리오에서 `SupportsGet` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="6ad4f-149">원본</span><span class="sxs-lookup"><span data-stu-id="6ad4f-149">Sources</span></span>

<span data-ttu-id="6ad4f-150">기본적으로 모델 바인딩은 HTTP 요청의 다음 원본에서 키-값 쌍의 양식으로 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="6ad4f-151">양식 필드</span><span class="sxs-lookup"><span data-stu-id="6ad4f-151">Form fields</span></span>
1. <span data-ttu-id="6ad4f-152">요청 본문([[ApiController] 특성이 있는 컨트롤러](xref:web-api/index#binding-source-parameter-inference)의 경우)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="6ad4f-153">경로 데이터</span><span class="sxs-lookup"><span data-stu-id="6ad4f-153">Route data</span></span>
1. <span data-ttu-id="6ad4f-154">쿼리 문자열 매개 변수</span><span class="sxs-lookup"><span data-stu-id="6ad4f-154">Query string parameters</span></span>
1. <span data-ttu-id="6ad4f-155">업로드된 파일</span><span class="sxs-lookup"><span data-stu-id="6ad4f-155">Uploaded files</span></span>

<span data-ttu-id="6ad4f-156">각 대상 매개 변수 또는 속성의 경우, 소스가 이 목록에 표시된 순서대로 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="6ad4f-157">몇 가지 예외도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-157">There are a few exceptions:</span></span>

* <span data-ttu-id="6ad4f-158">경로 데이터 및 쿼리 문자열 값은 단순 형식에 대해서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="6ad4f-159">업로드된 파일은 `IFormFile` 또는 `IEnumerable<IFormFile>`을 구현하는 대상 유형에만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="6ad4f-160">기본 소스가 올바르지 않으면 다음 특성 중 하나를 사용하여 소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="6ad4f-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -쿼리 문자열에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="6ad4f-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -경로 데이터에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="6ad4f-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -게시 된 양식 필드에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="6ad4f-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -요청 본문에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="6ad4f-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP 헤더에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="6ad4f-166">이러한 특성:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-166">These attributes:</span></span>

* <span data-ttu-id="6ad4f-167">다음 예제와 같이 모델 속성(모델 클래스가 아닌)에 개별적으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="6ad4f-168">필요에 따라 생성자에서 모델 이름 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="6ad4f-169">이 옵션은 속성 이름이 요청의 값과 일치하지 않는 경우에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="6ad4f-170">예를 들어 요청의 값은 다음 예제와 같이 해당 이름에 하이픈이 있는 헤더일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="6ad4f-171">[FromBody] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-171">[FromBody] attribute</span></span>

<span data-ttu-id="6ad4f-172">매개 변수에 `[FromBody]` 특성을 적용하여 HTTP 요청의 본문에서 해당 속성을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="6ad4f-173">ASP.NET Core 런타임은 본문을 읽을 책임을 입력 포맷터에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="6ad4f-174">입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="6ad4f-175">`[FromBody]`이(가) 복합 형식 매개 변수에 적용되는 경우 해당 속성에 적용된 모든 바인딩 소스 특성은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="6ad4f-176">예를 들어, 다음 `Create` 작업은 해당 `pet` 매개 변수가 본문에서 채워지도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="6ad4f-177">`Pet` 클래스는 `Breed` 속성이 쿼리 문자열 매개 변수에서 채워지도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="6ad4f-178">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-178">In the preceding example:</span></span>

* <span data-ttu-id="6ad4f-179">`[FromQuery]` 특성은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="6ad4f-180">`Breed` 속성은 쿼리 문자열 매개 변수에서 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="6ad4f-181">입력 포맷터는 본문만 읽고 바인딩 소스 특성은 인식하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="6ad4f-182">본문에 적절한 값이 있는 경우 해당 값은 `Breed` 속성을 채우는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="6ad4f-183">작업 메서드당 둘 이상의 매개 변수에 `[FromBody]`를 적용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="6ad4f-184">입력 포맷터에서 요청 스트림을 읽으면 더 이상 다른 `[FromBody]` 매개 변수를 바인딩하기 위해 다시 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="6ad4f-185">추가 원본</span><span class="sxs-lookup"><span data-stu-id="6ad4f-185">Additional sources</span></span>

<span data-ttu-id="6ad4f-186">원본 데이터는 *값 공급 기업*에 의해 모델 바인딩 시스템에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="6ad4f-187">다른 원본에서 모델 바인딩에 대한 데이터를 가져오는 사용자 지정 값 공급 기업을 작성 및 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="6ad4f-188">예를 들어 또는 세션 상태의 데이터를 사용할 수 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6ad4f-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="6ad4f-189">새 원본에서 데이터를 가져오려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-189">To get data from a new source:</span></span>

* <span data-ttu-id="6ad4f-190">`IValueProvider`를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="6ad4f-191">`IValueProviderFactory`를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="6ad4f-192">`Startup.ConfigureServices`에서 팩터리 클래스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="6ad4f-193">샘플 앱에는의 값을 가져오는 [값 공급자](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) 및 [팩터리](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) 예제가 포함 되어 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6ad4f-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="6ad4f-194">다음은 `Startup.ConfigureServices`의 등록 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="6ad4f-195">표시된 코드는 사용자 지정 값 공급 기업을 모든 기본 제공 값 공급 기업 다음에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="6ad4f-196">목록에서 첫 번째로 지정하려면 `Add` 대신에 `Insert(0, new CookieValueProviderFactory())`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="6ad4f-197">모델 속성에 대한 원본 없음</span><span class="sxs-lookup"><span data-stu-id="6ad4f-197">No source for a model property</span></span>

<span data-ttu-id="6ad4f-198">기본적으로 모델 속성에 대한 값이 없으면 모델 상태 오류가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="6ad4f-199">속성은 Null 또는 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="6ad4f-200">Nullable 단순 형식은 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="6ad4f-201">Null을 허용하지 않는 값 형식은 `default(T)`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="6ad4f-202">예를 들어 매개 변수 `int id`는 0으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="6ad4f-203">복합 형식의 경우 모델 바인딩은 속성을 설정하지 않고 기본 생성자를 사용하여 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="6ad4f-204">배열은 `byte[]` 배열이 `null`로 설정되는 점을 제외하고 `Array.Empty<T>()`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="6ad4f-205">모델 속성의 폼 필드에 아무 것도 없는 경우 모델 상태가 무효화 되어야 하면 특성을 사용 [`[BindRequired]`](#bindrequired-attribute) 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="6ad4f-206">이 `[BindRequired]` 동작은 요청 본문의 JSON 또는 XML 데이터가 아니라 게시된 양식 데이터의 모델 바인딩에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="6ad4f-207">요청 본문 데이터는 [입력 포맷터](#input-formatters)에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="6ad4f-208">형식 변환 오류</span><span class="sxs-lookup"><span data-stu-id="6ad4f-208">Type conversion errors</span></span>

<span data-ttu-id="6ad4f-209">원본이 있지만 대상 형식으로 변환될 수 없는 경우 모델 상태는 잘못된 것으로 플래그가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="6ad4f-210">이전 섹션에서 설명한 것처럼 대상 매개 변수 또는 속성은 Null 또는 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="6ad4f-211">`[ApiController]` 특성이 있는 API 컨트롤러에서 잘못된 모델 상태로 자동 HTTP 400 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="6ad4f-212">페이지에서 오류 메시지를 표시 하 고 페이지를 다시 표시 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="6ad4f-213">클라이언트 쪽 유효성 검사는 페이지 형태로 전송 될 수 있는 대부분의 잘못 된 데이터를 catch Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="6ad4f-214">이 유효성 검사는 위의 강조 표시된 코드를 트리거하기 어렵게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="6ad4f-215">샘플 앱은 **Hire Date** 필드에 잘못된 데이터를 배치하고 양식을 제출하는 **잘못된 데이터로 제출** 단추를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="6ad4f-216">이 단추는 데이터 변환 오류가 발생하는 경우 페이지를 다시 표시하기 위해 코드가 작동하는 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="6ad4f-217">위의 코드로 페이지가 다시 표시될 때 잘못된 입력은 양식 필드에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="6ad4f-218">이는 모델 속성이 Null 또는 기본값으로 설정되었기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="6ad4f-219">잘못된 입력은 오류 메시지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="6ad4f-220">그러나 양식 필드에 잘못된 데이터를 다시 표시하려는 경우 모델 속성을 문자열로 만들고 데이터 변환을 수동으로 수행하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="6ad4f-221">형식 변환 오류를 모델 상태 오류로 만들려 하지 않는 경우 동일한 전략을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="6ad4f-222">이 경우 모델 속성을 문자열로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="6ad4f-223">단순 형식</span><span class="sxs-lookup"><span data-stu-id="6ad4f-223">Simple types</span></span>

<span data-ttu-id="6ad4f-224">모델 바인더에서 원본 문자열을 변환할 수 있는 단순 형식은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="6ad4f-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="6ad4f-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="6ad4f-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="6ad4f-227">Char</span><span class="sxs-lookup"><span data-stu-id="6ad4f-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="6ad4f-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="6ad4f-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="6ad4f-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="6ad4f-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="6ad4f-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="6ad4f-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="6ad4f-231">double</span><span class="sxs-lookup"><span data-stu-id="6ad4f-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="6ad4f-232">열거형</span><span class="sxs-lookup"><span data-stu-id="6ad4f-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="6ad4f-233">Eid</span><span class="sxs-lookup"><span data-stu-id="6ad4f-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="6ad4f-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="6ad4f-235">Single</span><span class="sxs-lookup"><span data-stu-id="6ad4f-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="6ad4f-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="6ad4f-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="6ad4f-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="6ad4f-238">Uri</span><span class="sxs-lookup"><span data-stu-id="6ad4f-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="6ad4f-239">버전</span><span class="sxs-lookup"><span data-stu-id="6ad4f-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="6ad4f-240">복합 형식</span><span class="sxs-lookup"><span data-stu-id="6ad4f-240">Complex types</span></span>

<span data-ttu-id="6ad4f-241">복합 형식에 공용 기본 생성자와 바인딩할 공용 쓰기 가능 속성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="6ad4f-242">모델 바인딩이 발생하면 클래스는 공용 기본 생성자를 사용하여 인스턴스화됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="6ad4f-243">복합 형식의 각 속성의 경우 모델 바인딩은 이름 패턴*prefix.property_name*에 대한 원본을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="6ad4f-244">아무것도 없는 경우 접두사 없이 *property_name*만을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="6ad4f-245">매개 변수에 대한 바인딩의 경우 접두사는 매개 변수 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="6ad4f-246">`PageModel` 공용 속성에 대한 바인딩의 경우 접두사는 공용 속성 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="6ad4f-247">일부 특성에는 매개 변수의 기본 사용 또는 속성 이름을 재정의할 수 있도록 하는 `Prefix` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="6ad4f-248">예를 들어 복합 형식이 다음 `Instructor` 클래스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="6ad4f-249">접두사 = 매개 변수 이름</span><span class="sxs-lookup"><span data-stu-id="6ad4f-249">Prefix = parameter name</span></span>

<span data-ttu-id="6ad4f-250">바인딩될 모델이 `instructorToUpdate`라는 매개 변수인 경우:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="6ad4f-251">모델 바인딩은 키 `instructorToUpdate.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="6ad4f-252">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="6ad4f-253">접두사 = 속성 이름</span><span class="sxs-lookup"><span data-stu-id="6ad4f-253">Prefix = property name</span></span>

<span data-ttu-id="6ad4f-254">바인딩될 모델이 컨트롤러 또는 `PageModel` 클래스의 `Instructor`라는 속성인 경우:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="6ad4f-255">모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6ad4f-256">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="6ad4f-257">사용자 지정 접두사</span><span class="sxs-lookup"><span data-stu-id="6ad4f-257">Custom prefix</span></span>

<span data-ttu-id="6ad4f-258">바인딩될 모델이 `instructorToUpdate`라는 매개 변수이고 `Bind` 특성이 접두사로 `Instructor`를 지정하는 경우:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="6ad4f-259">모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6ad4f-260">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="6ad4f-261">복합 형식 대상에 대한 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-261">Attributes for complex type targets</span></span>

<span data-ttu-id="6ad4f-262">여러 기본 제공 특성은 복합 형식의 모델 바인딩을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="6ad4f-263">이러한 특성은 게시된 양식 데이터가 값의 원본일 때 모델 바인딩에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="6ad4f-264">게시 된 JSON 및 XML 요청 본문을 처리 하는 입력 포맷터에 ***는 영향을 주지 않습니다.***</span><span class="sxs-lookup"><span data-stu-id="6ad4f-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="6ad4f-265">입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="6ad4f-266">[Bind] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-266">[Bind] attribute</span></span>

<span data-ttu-id="6ad4f-267">클래스 또는 메서드 매개 변수에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="6ad4f-268">모델 바인딩에 포함되어야 하는 모델의 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="6ad4f-269">`[Bind]`입력 포맷터 ***에는 영향을 주지 않습니다.***</span><span class="sxs-lookup"><span data-stu-id="6ad4f-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="6ad4f-270">다음 예제에서 모든 처리기 또는 작업 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="6ad4f-271">다음 예제에서 `OnPost` 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="6ad4f-272">`[Bind]` 특성은 *만들기* 시나리오에서 초과 게시를 방지하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="6ad4f-273">제외된 속성은 변경되지 않은 채로 남겨지는 대신 Null 또는 기본값으로 설정되기 때문에 편집 시나리오에서 잘 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="6ad4f-274">초과 게시에 대한 방어의 경우 뷰 모델이 `[Bind]` 특성보다 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="6ad4f-275">자세한 내용은 [초과 게시에 대한 보안 정보](xref:data/ef-mvc/crud#security-note-about-overposting)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="6ad4f-276">[BindRequired] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-276">[BindRequired] attribute</span></span>

<span data-ttu-id="6ad4f-277">메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6ad4f-278">모델의 속성에 대한 바인딩이 발생할 수 없는 경우 모델 바인딩이 모델 상태 오류를 추가하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="6ad4f-279">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="6ad4f-280">[모델 유효성 검사](xref:mvc/models/validation#required-attribute)에서 `[Required]` 특성의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="6ad4f-281">[BindNever] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-281">[BindNever] attribute</span></span>

<span data-ttu-id="6ad4f-282">메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6ad4f-283">모델 바인딩이 모델의 속성을 설정하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="6ad4f-284">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="6ad4f-285">컬렉션</span><span class="sxs-lookup"><span data-stu-id="6ad4f-285">Collections</span></span>

<span data-ttu-id="6ad4f-286">단순 형식의 컬렉션인 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6ad4f-287">일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6ad4f-288">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="6ad4f-288">For example:</span></span>

* <span data-ttu-id="6ad4f-289">바인딩되는 매개 변수가 `selectedCourses`라는 배열이라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="6ad4f-290">양식 또는 쿼리 문자열 데이터는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-290">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="6ad4f-291">다음 형식은 양식 데이터에서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="6ad4f-292">앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 배열을 `selectedCourses` 매개 변수에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6ad4f-293">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="6ad4f-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="6ad4f-294">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="6ad4f-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="6ad4f-295">아래 첨자 숫자(... [0] ... [1] ...)를 사용하는 데이터 형식은 0부터 시작하여 순차적으로 번호가 매겨지는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="6ad4f-296">아래 첨자 번호에 간격이 있는 경우 간격 뒤에 있는 모든 항목은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="6ad4f-297">예를 들어 아래 첨자가 0과 1 대신 0과 2인 경우 두 번째 항목은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="6ad4f-298">사전</span><span class="sxs-lookup"><span data-stu-id="6ad4f-298">Dictionaries</span></span>

<span data-ttu-id="6ad4f-299">`Dictionary` 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6ad4f-300">일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6ad4f-301">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="6ad4f-301">For example:</span></span>

* <span data-ttu-id="6ad4f-302">대상 매개 변수가 `selectedCourses`라는 `Dictionary<int, string>`라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="6ad4f-303">게시된 양식 또는 쿼리 문자열 데이터는 다음 예제 중 하나와 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-303">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="6ad4f-304">앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 사전을 `selectedCourses` 매개 변수에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6ad4f-305">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="6ad4f-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="6ad4f-306">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="6ad4f-306">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="6ad4f-307">모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작</span><span class="sxs-lookup"><span data-stu-id="6ad4f-307">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="6ad4f-308">ASP.NET Core 경로 값 공급자와 쿼리 문자열 값 공급자:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-308">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="6ad4f-309">값을 고정 문화권으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-309">Treat values as invariant culture.</span></span>
* <span data-ttu-id="6ad4f-310">URL을 고정 문화권으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-310">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="6ad4f-311">이와 대조적으로, 양식 데이터에서 가져온 값은 문화권을 구분하여 변환을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-311">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="6ad4f-312">이는 기본적으로 URL을 로캘 간에 공유할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-312">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="6ad4f-313">ASP.NET Core 경로 값 공급자와 쿼리 문자열 값 공급자가 문화권 구분 변환을 수행하도록 하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-313">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="6ad4f-314"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>에서 상속됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-314">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="6ad4f-315">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) 또는 [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)에서 코드를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-315">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="6ad4f-316">값 공급자 생성자로 전달된 [문화권 값](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)을 [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-316">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="6ad4f-317">MVC 옵션의 기본 값 공급자 팩터리를 새 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-317">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="6ad4f-318">특수 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="6ad4f-318">Special data types</span></span>

<span data-ttu-id="6ad4f-319">모델 바인딩이 처리할 수 있는 일부 특수 데이터 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-319">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="6ad4f-320">IFormFile 및 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="6ad4f-320">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="6ad4f-321">HTTP 요청에 포함되는 업로드된 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-321">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="6ad4f-322">또한 여러 파일에 대해 `IEnumerable<IFormFile>`이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-322">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="6ad4f-323">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="6ad4f-323">CancellationToken</span></span>

<span data-ttu-id="6ad4f-324">비동기 컨트롤러에서 작업을 취소하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-324">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="6ad4f-325">FormCollection</span><span class="sxs-lookup"><span data-stu-id="6ad4f-325">FormCollection</span></span>

<span data-ttu-id="6ad4f-326">게시된 양식 데이터에서 모든 값을 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-326">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="6ad4f-327">입력 포맷터</span><span class="sxs-lookup"><span data-stu-id="6ad4f-327">Input formatters</span></span>

<span data-ttu-id="6ad4f-328">요청 본문의 데이터는 JSON, XML 또는 일부 다른 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-328">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="6ad4f-329">이 데이터를 구문 분석하기 위해 모델 바인딩은 특정 콘텐츠 유형을 처리하도록 구성된 *입력 포맷터*를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-329">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="6ad4f-330">기본적으로 ASP.NET Core는 JSON 데이터를 처리하기 위한 JSON 기반 입력 포맷터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-330">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="6ad4f-331">다른 콘텐츠 형식에 대해 다른 포맷터를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-331">You can add other formatters for other content types.</span></span>

<span data-ttu-id="6ad4f-332">ASP.NET Core는 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 특성을 기반으로 입력 포맷터를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-332">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="6ad4f-333">특성이 없는 경우 [Content-Type 헤더](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-333">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="6ad4f-334">기본 제공 XML 입력 포맷터를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-334">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="6ad4f-335">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-335">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="6ad4f-336">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 또는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-336">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="6ad4f-337">요청 본문에서 XML을 필요로 하는 컨트롤러 클래스 또는 작업 메서드에 `Consumes` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-337">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="6ad4f-338">자세한 내용은 [XML Serialization 소개](/dotnet/standard/serialization/introducing-xml-serialization)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-338">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="6ad4f-339">입력 포맷터를 사용하여 모델 바인딩 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="6ad4f-339">Customize model binding with input formatters</span></span>

<span data-ttu-id="6ad4f-340">입력 포맷터는 요청 본문에서 데이터를 읽기 위한 모든 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-340">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="6ad4f-341">이 프로세스를 사용자 지정하려면 입력 포맷터에서 사용하는 API를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-341">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="6ad4f-342">이 섹션에서는 `ObjectId`라는 사용자 지정 형식을 이해하기 위해 `System.Text.Json` 기반 입력 포맷터를 사용자 지정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-342">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="6ad4f-343">`Id`라는 사용자 지정 `ObjectId` 속성을 포함하는 다음 모델을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-343">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="6ad4f-344">`System.Text.Json`를 사용할 때 모델 바인딩 프로세스를 사용자 지정하려면 <xref:System.Text.Json.Serialization.JsonConverter%601>에서 파생된 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-344">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="6ad4f-345">사용자 지정 변환기를 사용하려면 형식에 <xref:System.Text.Json.Serialization.JsonConverterAttribute> 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-345">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="6ad4f-346">다음 예제에서 `ObjectId` 형식은 `ObjectIdConverter`를 사용자 지정 변환기로 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-346">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="6ad4f-347">자세한 내용은 [사용자 지정 변환기를 작성하는 방법](/dotnet/standard/serialization/system-text-json-converters-how-to)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-347">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="6ad4f-348">모델 바인딩에서 지정된 형식 제외</span><span class="sxs-lookup"><span data-stu-id="6ad4f-348">Exclude specified types from model binding</span></span>

<span data-ttu-id="6ad4f-349">모델 바인딩 및 시스템 동작의 유효성 검사는 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-349">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="6ad4f-350">[MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)에 세부 정보 공급 기업을 추가하여 `ModelMetadata`를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-350">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="6ad4f-351">기본 제공 세부 정보 공급 기업을 지정된 형식에 대한 모델 바인딩 또는 유효성 검사를 비활성화하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-351">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="6ad4f-352">지정된 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-352">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ad4f-353">예를 들어 `System.Version` 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-353">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="6ad4f-354">지정된 형식의 속성에 대한 유효성 검사를 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-354">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ad4f-355">예를 들어 `System.Guid` 형식의 속성에 대한 유효성 검사를 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-355">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="6ad4f-356">사용자 지정 모델 바인더</span><span class="sxs-lookup"><span data-stu-id="6ad4f-356">Custom model binders</span></span>

<span data-ttu-id="6ad4f-357">사용자 지정 모델 바인더를 작성하고 지정된 대상에 대해 선택하도록 `[ModelBinder]` 특성을 사용하여 모델 바인딩을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-357">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="6ad4f-358">[사용자 모델 바인딩](xref:mvc/advanced/custom-model-binding)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-358">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="6ad4f-359">수동 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="6ad4f-359">Manual model binding</span></span> 

<span data-ttu-id="6ad4f-360"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 메서드를 사용하여 모델 바인딩을 수동으로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-360">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="6ad4f-361">메서드는 `ControllerBase` 및 `PageModel` 클래스에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-361">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="6ad4f-362">메서드 오버로드를 통해 사용할 접두사 및 값 공급 기업을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-362">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="6ad4f-363">모델 바인딩이 실패하는 경우 메서드는 `false`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-363">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="6ad4f-364">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-364">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="6ad4f-365"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>는 값 공급자를 사용하여 양식 본문, 쿼리 문자열 및 경로 데이터에서 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-365"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="6ad4f-366">`TryUpdateModelAsync`는 일반적으로</span><span class="sxs-lookup"><span data-stu-id="6ad4f-366">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="6ad4f-367">과도 한 Razor 게시를 방지 하기 위해 컨트롤러 및 뷰를 사용 하는 페이지 및 MVC 앱과 함께 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-367">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="6ad4f-368">양식 데이터, 쿼리 문자열 및 경로 데이터에서 사용하지 않는 한 웹 API와 함께 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-368">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="6ad4f-369">JSON을 사용하는 웹 API 엔드포인트는 [입력 포맷터](#input-formatters)를 사용하여 요청 본문을 개체로 역직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-369">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="6ad4f-370">자세한 내용은 [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-370">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="6ad4f-371">[FromServices] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-371">[FromServices] attribute</span></span>

<span data-ttu-id="6ad4f-372">이 특성의 이름은 데이터 원본을 지정하는 모델 바인딩 특성의 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-372">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="6ad4f-373">그러나 값 공급 기업의 바인딩 데이터에 대한 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-373">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="6ad4f-374">[종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에서 형식의 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-374">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6ad4f-375">특정 메서드가 호출되는 경우에만 서비스가 필요할 때 생성자 주입에 대안을 제공하는 것이 목적입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-375">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ad4f-376">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6ad4f-376">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ad4f-377">이 문서는 모델 바인딩이 무엇인지, 작동 방법 및 해당 동작을 사용자 지정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-377">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="6ad4f-378">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-378">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="6ad4f-379">모델 바인딩이란</span><span class="sxs-lookup"><span data-stu-id="6ad4f-379">What is Model binding</span></span>

<span data-ttu-id="6ad4f-380">컨트롤러와 Razor 페이지는 HTTP 요청에서 가져온 데이터로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-380">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="6ad4f-381">예를 들어 경로 데이터는 레코드 키를 제공할 수 있으며, 게시된 양식 필드는 모델의 속성에 대한 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-381">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="6ad4f-382">이러한 각 값을 검색하고 문자열에서 .NET 형식으로 변환하도록 코드를 작성하는 것은 번거롭고 오류가 발생하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-382">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="6ad4f-383">모델 바인딩은 이 프로세스를 자동화합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-383">Model binding automates this process.</span></span> <span data-ttu-id="6ad4f-384">모델 바인딩 시스템:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-384">The model binding system:</span></span>

* <span data-ttu-id="6ad4f-385">경로 데이터, 양식 필드 및 쿼리 문자열과 같은 다양한 원본의 데이터를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-385">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="6ad4f-386">Razor메서드 매개 변수 및 공용 속성의 컨트롤러 및 페이지에 데이터를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-386">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="6ad4f-387">문자열 데이터를 .NET 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-387">Converts string data to .NET types.</span></span>
* <span data-ttu-id="6ad4f-388">복합 형식의 속성을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-388">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="6ad4f-389">예제</span><span class="sxs-lookup"><span data-stu-id="6ad4f-389">Example</span></span>

<span data-ttu-id="6ad4f-390">다음 작업 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-390">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="6ad4f-391">앱은 이 URL로 요청을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-391">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="6ad4f-392">모델 바인딩은 라우팅 시스템이 작업 메서드를 선택한 후 다음 단계를 통해 진행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-392">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="6ad4f-393">`GetByID`의 첫 번째 매개 변수, `id`라는 정수를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-393">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="6ad4f-394">HTTP 요청에서 사용 가능한 원본을 찾고 경로 데이터에서 `id` = "2"를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-394">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="6ad4f-395">문자열 "2"를 정수 2로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-395">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="6ad4f-396">`GetByID`의 다음 매개 변수, `dogsOnly`라는 부울을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-396">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="6ad4f-397">원본을 찾고 쿼리 문자열에서 "DogsOnly=true"를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-397">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="6ad4f-398">이름 일치는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-398">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="6ad4f-399">문자열 "true"를 부울 `true`로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-399">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="6ad4f-400">그런 다음, 프레임워크는 `id` 매개 변수에 대해 2를 `dogsOnly` 매개 변수에 대해 `true`를 전달하는 `GetById` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-400">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="6ad4f-401">앞의 예제에서 모델 바인딩 대상은 단순 형식인 메서드 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-401">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="6ad4f-402">대상은 복합 형식의 속성일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-402">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="6ad4f-403">각 속성이 성공적으로 바인딩된 후 [모델 유효성 검사](xref:mvc/models/validation)가 해당 속성에 대해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-403">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="6ad4f-404">모델에 바인딩되는 데이터의 레코드 및 모든 바인딩 또는 유효성 검사 오류는 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 또는 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-404">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="6ad4f-405">이 프로세스가 성공되었는지 확인하기 위해 앱은 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 플래그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-405">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="6ad4f-406">대상</span><span class="sxs-lookup"><span data-stu-id="6ad4f-406">Targets</span></span>

<span data-ttu-id="6ad4f-407">모델 바인딩은 다음 종류의 대상에 대한 값을 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-407">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="6ad4f-408">요청이 라우팅되는 컨트롤러 작업 메서드의 매개 변수</span><span class="sxs-lookup"><span data-stu-id="6ad4f-408">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="6ad4f-409">Razor요청이 라우팅되는 페이지 처리기 메서드의 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-409">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="6ad4f-410">특성으로 지정되는 경우 컨트롤러 또는 `PageModel` 클래스의 공용 속성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-410">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="6ad4f-411">[BindProperty] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-411">[BindProperty] attribute</span></span>

<span data-ttu-id="6ad4f-412">모델 바인딩이 해당 속성을 대상으로 하도록 컨트롤러의 공용 속성 또는 `PageModel` 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-412">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="6ad4f-413">[BindProperties] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-413">[BindProperties] attribute</span></span>

<span data-ttu-id="6ad4f-414">ASP.NET Core 2.1 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-414">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="6ad4f-415">모델 바인딩이 클래스의 모든 공용 속성을 대상으로 하도록 컨트롤러 또는 `PageModel` 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-415">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="6ad4f-416">HTTP GET 요청에 대한 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="6ad4f-416">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="6ad4f-417">기본적으로 속성은 HTTP GET 요청에 대해 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-417">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="6ad4f-418">일반적으로 GET 요청에 대해 필요한 것은 레코드 ID 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-418">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="6ad4f-419">레코드 ID는 데이터베이스에 있는 항목을 찾는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-419">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="6ad4f-420">따라서 모델의 인스턴스를 포함하는 속성을 바인딩할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-420">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="6ad4f-421">속성을 GET 요청의 데이터에 바인딩하려는 시나리오에서 `SupportsGet` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-421">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="6ad4f-422">원본</span><span class="sxs-lookup"><span data-stu-id="6ad4f-422">Sources</span></span>

<span data-ttu-id="6ad4f-423">기본적으로 모델 바인딩은 HTTP 요청의 다음 원본에서 키-값 쌍의 양식으로 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-423">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="6ad4f-424">양식 필드</span><span class="sxs-lookup"><span data-stu-id="6ad4f-424">Form fields</span></span>
1. <span data-ttu-id="6ad4f-425">요청 본문([[ApiController] 특성이 있는 컨트롤러](xref:web-api/index#binding-source-parameter-inference)의 경우)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-425">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="6ad4f-426">경로 데이터</span><span class="sxs-lookup"><span data-stu-id="6ad4f-426">Route data</span></span>
1. <span data-ttu-id="6ad4f-427">쿼리 문자열 매개 변수</span><span class="sxs-lookup"><span data-stu-id="6ad4f-427">Query string parameters</span></span>
1. <span data-ttu-id="6ad4f-428">업로드된 파일</span><span class="sxs-lookup"><span data-stu-id="6ad4f-428">Uploaded files</span></span>

<span data-ttu-id="6ad4f-429">각 대상 매개 변수 또는 속성의 경우, 소스가 이 목록에 표시된 순서대로 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-429">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="6ad4f-430">몇 가지 예외도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-430">There are a few exceptions:</span></span>

* <span data-ttu-id="6ad4f-431">경로 데이터 및 쿼리 문자열 값은 단순 형식에 대해서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-431">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="6ad4f-432">업로드된 파일은 `IFormFile` 또는 `IEnumerable<IFormFile>`을 구현하는 대상 유형에만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-432">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="6ad4f-433">기본 소스가 올바르지 않으면 다음 특성 중 하나를 사용하여 소스를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-433">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="6ad4f-434">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -쿼리 문자열에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-434">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="6ad4f-435">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -경로 데이터에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-435">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="6ad4f-436">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -게시 된 양식 필드에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-436">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="6ad4f-437">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -요청 본문에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-437">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="6ad4f-438">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP 헤더에서 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-438">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="6ad4f-439">이러한 특성:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-439">These attributes:</span></span>

* <span data-ttu-id="6ad4f-440">다음 예제와 같이 모델 속성(모델 클래스가 아닌)에 개별적으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-440">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="6ad4f-441">필요에 따라 생성자에서 모델 이름 값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-441">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="6ad4f-442">이 옵션은 속성 이름이 요청의 값과 일치하지 않는 경우에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-442">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="6ad4f-443">예를 들어 요청의 값은 다음 예제와 같이 해당 이름에 하이픈이 있는 헤더일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-443">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="6ad4f-444">[FromBody] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-444">[FromBody] attribute</span></span>

<span data-ttu-id="6ad4f-445">매개 변수에 `[FromBody]` 특성을 적용하여 HTTP 요청의 본문에서 해당 속성을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-445">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="6ad4f-446">ASP.NET Core 런타임은 본문을 읽을 책임을 입력 포맷터에 위임합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-446">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="6ad4f-447">입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-447">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="6ad4f-448">`[FromBody]`이(가) 복합 형식 매개 변수에 적용되는 경우 해당 속성에 적용된 모든 바인딩 소스 특성은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-448">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="6ad4f-449">예를 들어, 다음 `Create` 작업은 해당 `pet` 매개 변수가 본문에서 채워지도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-449">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="6ad4f-450">`Pet` 클래스는 `Breed` 속성이 쿼리 문자열 매개 변수에서 채워지도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-450">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="6ad4f-451">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-451">In the preceding example:</span></span>

* <span data-ttu-id="6ad4f-452">`[FromQuery]` 특성은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-452">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="6ad4f-453">`Breed` 속성은 쿼리 문자열 매개 변수에서 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-453">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="6ad4f-454">입력 포맷터는 본문만 읽고 바인딩 소스 특성은 인식하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-454">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="6ad4f-455">본문에 적절한 값이 있는 경우 해당 값은 `Breed` 속성을 채우는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-455">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="6ad4f-456">작업 메서드당 둘 이상의 매개 변수에 `[FromBody]`를 적용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-456">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="6ad4f-457">입력 포맷터에서 요청 스트림을 읽으면 더 이상 다른 `[FromBody]` 매개 변수를 바인딩하기 위해 다시 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-457">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="6ad4f-458">추가 원본</span><span class="sxs-lookup"><span data-stu-id="6ad4f-458">Additional sources</span></span>

<span data-ttu-id="6ad4f-459">원본 데이터는 *값 공급 기업*에 의해 모델 바인딩 시스템에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-459">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="6ad4f-460">다른 원본에서 모델 바인딩에 대한 데이터를 가져오는 사용자 지정 값 공급 기업을 작성 및 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-460">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="6ad4f-461">예를 들어 또는 세션 상태의 데이터를 사용할 수 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6ad4f-461">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="6ad4f-462">새 원본에서 데이터를 가져오려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-462">To get data from a new source:</span></span>

* <span data-ttu-id="6ad4f-463">`IValueProvider`를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-463">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="6ad4f-464">`IValueProviderFactory`를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-464">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="6ad4f-465">`Startup.ConfigureServices`에서 팩터리 클래스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-465">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="6ad4f-466">샘플 앱에는의 값을 가져오는 [값 공급자](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) 및 [팩터리](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) 예제가 포함 되어 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="6ad4f-466">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="6ad4f-467">다음은 `Startup.ConfigureServices`의 등록 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-467">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="6ad4f-468">표시된 코드는 사용자 지정 값 공급 기업을 모든 기본 제공 값 공급 기업 다음에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-468">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="6ad4f-469">목록에서 첫 번째로 지정하려면 `Add` 대신에 `Insert(0, new CookieValueProviderFactory())`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-469">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="6ad4f-470">모델 속성에 대한 원본 없음</span><span class="sxs-lookup"><span data-stu-id="6ad4f-470">No source for a model property</span></span>

<span data-ttu-id="6ad4f-471">기본적으로 모델 속성에 대한 값이 없으면 모델 상태 오류가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-471">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="6ad4f-472">속성은 Null 또는 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-472">The property is set to null or a default value:</span></span>

* <span data-ttu-id="6ad4f-473">Nullable 단순 형식은 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-473">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="6ad4f-474">Null을 허용하지 않는 값 형식은 `default(T)`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-474">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="6ad4f-475">예를 들어 매개 변수 `int id`는 0으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-475">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="6ad4f-476">복합 형식의 경우 모델 바인딩은 속성을 설정하지 않고 기본 생성자를 사용하여 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-476">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="6ad4f-477">배열은 `byte[]` 배열이 `null`로 설정되는 점을 제외하고 `Array.Empty<T>()`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-477">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="6ad4f-478">모델 속성의 폼 필드에 아무 것도 없는 경우 모델 상태가 무효화 되어야 하면 특성을 사용 [`[BindRequired]`](#bindrequired-attribute) 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-478">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="6ad4f-479">이 `[BindRequired]` 동작은 요청 본문의 JSON 또는 XML 데이터가 아니라 게시된 양식 데이터의 모델 바인딩에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-479">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="6ad4f-480">요청 본문 데이터는 [입력 포맷터](#input-formatters)에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-480">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="6ad4f-481">형식 변환 오류</span><span class="sxs-lookup"><span data-stu-id="6ad4f-481">Type conversion errors</span></span>

<span data-ttu-id="6ad4f-482">원본이 있지만 대상 형식으로 변환될 수 없는 경우 모델 상태는 잘못된 것으로 플래그가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-482">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="6ad4f-483">이전 섹션에서 설명한 것처럼 대상 매개 변수 또는 속성은 Null 또는 기본값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-483">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="6ad4f-484">`[ApiController]` 특성이 있는 API 컨트롤러에서 잘못된 모델 상태로 자동 HTTP 400 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-484">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="6ad4f-485">페이지에서 오류 메시지를 표시 하 고 페이지를 다시 표시 Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-485">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="6ad4f-486">클라이언트 쪽 유효성 검사는 페이지 형태로 전송 될 수 있는 대부분의 잘못 된 데이터를 catch Razor 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-486">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="6ad4f-487">이 유효성 검사는 위의 강조 표시된 코드를 트리거하기 어렵게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-487">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="6ad4f-488">샘플 앱은 **Hire Date** 필드에 잘못된 데이터를 배치하고 양식을 제출하는 **잘못된 데이터로 제출** 단추를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-488">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="6ad4f-489">이 단추는 데이터 변환 오류가 발생하는 경우 페이지를 다시 표시하기 위해 코드가 작동하는 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-489">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="6ad4f-490">위의 코드로 페이지가 다시 표시될 때 잘못된 입력은 양식 필드에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-490">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="6ad4f-491">이는 모델 속성이 Null 또는 기본값으로 설정되었기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-491">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="6ad4f-492">잘못된 입력은 오류 메시지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-492">The invalid input does appear in an error message.</span></span> <span data-ttu-id="6ad4f-493">그러나 양식 필드에 잘못된 데이터를 다시 표시하려는 경우 모델 속성을 문자열로 만들고 데이터 변환을 수동으로 수행하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-493">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="6ad4f-494">형식 변환 오류를 모델 상태 오류로 만들려 하지 않는 경우 동일한 전략을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-494">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="6ad4f-495">이 경우 모델 속성을 문자열로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-495">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="6ad4f-496">단순 형식</span><span class="sxs-lookup"><span data-stu-id="6ad4f-496">Simple types</span></span>

<span data-ttu-id="6ad4f-497">모델 바인더에서 원본 문자열을 변환할 수 있는 단순 형식은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-497">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="6ad4f-498">Boolean</span><span class="sxs-lookup"><span data-stu-id="6ad4f-498">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="6ad4f-499">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-499">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="6ad4f-500">Char</span><span class="sxs-lookup"><span data-stu-id="6ad4f-500">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="6ad4f-501">DateTime</span><span class="sxs-lookup"><span data-stu-id="6ad4f-501">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="6ad4f-502">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="6ad4f-502">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="6ad4f-503">Decimal</span><span class="sxs-lookup"><span data-stu-id="6ad4f-503">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="6ad4f-504">double</span><span class="sxs-lookup"><span data-stu-id="6ad4f-504">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="6ad4f-505">열거형</span><span class="sxs-lookup"><span data-stu-id="6ad4f-505">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="6ad4f-506">Eid</span><span class="sxs-lookup"><span data-stu-id="6ad4f-506">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="6ad4f-507">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-507">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="6ad4f-508">Single</span><span class="sxs-lookup"><span data-stu-id="6ad4f-508">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="6ad4f-509">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="6ad4f-509">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="6ad4f-510">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="6ad4f-510">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="6ad4f-511">Uri</span><span class="sxs-lookup"><span data-stu-id="6ad4f-511">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="6ad4f-512">버전</span><span class="sxs-lookup"><span data-stu-id="6ad4f-512">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="6ad4f-513">복합 형식</span><span class="sxs-lookup"><span data-stu-id="6ad4f-513">Complex types</span></span>

<span data-ttu-id="6ad4f-514">복합 형식에 공용 기본 생성자와 바인딩할 공용 쓰기 가능 속성이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-514">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="6ad4f-515">모델 바인딩이 발생하면 클래스는 공용 기본 생성자를 사용하여 인스턴스화됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-515">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="6ad4f-516">복합 형식의 각 속성의 경우 모델 바인딩은 이름 패턴*prefix.property_name*에 대한 원본을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-516">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="6ad4f-517">아무것도 없는 경우 접두사 없이 *property_name*만을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-517">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="6ad4f-518">매개 변수에 대한 바인딩의 경우 접두사는 매개 변수 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-518">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="6ad4f-519">`PageModel` 공용 속성에 대한 바인딩의 경우 접두사는 공용 속성 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-519">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="6ad4f-520">일부 특성에는 매개 변수의 기본 사용 또는 속성 이름을 재정의할 수 있도록 하는 `Prefix` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-520">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="6ad4f-521">예를 들어 복합 형식이 다음 `Instructor` 클래스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-521">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="6ad4f-522">접두사 = 매개 변수 이름</span><span class="sxs-lookup"><span data-stu-id="6ad4f-522">Prefix = parameter name</span></span>

<span data-ttu-id="6ad4f-523">바인딩될 모델이 `instructorToUpdate`라는 매개 변수인 경우:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-523">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="6ad4f-524">모델 바인딩은 키 `instructorToUpdate.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-524">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="6ad4f-525">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-525">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="6ad4f-526">접두사 = 속성 이름</span><span class="sxs-lookup"><span data-stu-id="6ad4f-526">Prefix = property name</span></span>

<span data-ttu-id="6ad4f-527">바인딩될 모델이 컨트롤러 또는 `PageModel` 클래스의 `Instructor`라는 속성인 경우:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-527">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="6ad4f-528">모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-528">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6ad4f-529">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-529">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="6ad4f-530">사용자 지정 접두사</span><span class="sxs-lookup"><span data-stu-id="6ad4f-530">Custom prefix</span></span>

<span data-ttu-id="6ad4f-531">바인딩될 모델이 `instructorToUpdate`라는 매개 변수이고 `Bind` 특성이 접두사로 `Instructor`를 지정하는 경우:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-531">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="6ad4f-532">모델 바인딩은 키 `Instructor.ID`에 대한 원본을 찾아 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-532">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="6ad4f-533">없는 경우 접두사 없이 `ID`를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-533">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="6ad4f-534">복합 형식 대상에 대한 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-534">Attributes for complex type targets</span></span>

<span data-ttu-id="6ad4f-535">여러 기본 제공 특성은 복합 형식의 모델 바인딩을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-535">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="6ad4f-536">이러한 특성은 게시된 양식 데이터가 값의 원본일 때 모델 바인딩에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-536">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="6ad4f-537">게시된 JSON 및 XML 요청 본문을 처리하는 입력 포맷터에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-537">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="6ad4f-538">입력 포맷터는 [이 문서의 뒷부분](#input-formatters)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-538">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="6ad4f-539">[모델 유효성 검사](xref:mvc/models/validation#required-attribute)에서 `[Required]` 특성의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-539">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="6ad4f-540">[BindRequired] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-540">[BindRequired] attribute</span></span>

<span data-ttu-id="6ad4f-541">메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-541">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6ad4f-542">모델의 속성에 대한 바인딩이 발생할 수 없는 경우 모델 바인딩이 모델 상태 오류를 추가하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-542">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="6ad4f-543">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-543">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="6ad4f-544">[BindNever] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-544">[BindNever] attribute</span></span>

<span data-ttu-id="6ad4f-545">메서드 매개 변수가 아닌 모델 속성에만 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-545">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="6ad4f-546">모델 바인딩이 모델의 속성을 설정하는 것을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-546">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="6ad4f-547">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-547">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="6ad4f-548">[Bind] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-548">[Bind] attribute</span></span>

<span data-ttu-id="6ad4f-549">클래스 또는 메서드 매개 변수에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-549">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="6ad4f-550">모델 바인딩에 포함되어야 하는 모델의 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-550">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="6ad4f-551">다음 예제에서 모든 처리기 또는 작업 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-551">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="6ad4f-552">다음 예제에서 `OnPost` 메서드가 호출될 때 지정된 속성의 `Instructor` 모델만 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-552">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="6ad4f-553">`[Bind]` 특성은 *만들기* 시나리오에서 초과 게시를 방지하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-553">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="6ad4f-554">제외된 속성은 변경되지 않은 채로 남겨지는 대신 Null 또는 기본값으로 설정되기 때문에 편집 시나리오에서 잘 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-554">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="6ad4f-555">초과 게시에 대한 방어의 경우 뷰 모델이 `[Bind]` 특성보다 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-555">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="6ad4f-556">자세한 내용은 [초과 게시에 대한 보안 정보](xref:data/ef-mvc/crud#security-note-about-overposting)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-556">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="6ad4f-557">컬렉션</span><span class="sxs-lookup"><span data-stu-id="6ad4f-557">Collections</span></span>

<span data-ttu-id="6ad4f-558">단순 형식의 컬렉션인 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-558">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6ad4f-559">일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-559">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6ad4f-560">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="6ad4f-560">For example:</span></span>

* <span data-ttu-id="6ad4f-561">바인딩되는 매개 변수가 `selectedCourses`라는 배열이라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-561">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="6ad4f-562">양식 또는 쿼리 문자열 데이터는 다음 형식 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-562">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="6ad4f-563">다음 형식은 양식 데이터에서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-563">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="6ad4f-564">앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 배열을 `selectedCourses` 매개 변수에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-564">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6ad4f-565">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="6ad4f-565">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="6ad4f-566">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="6ad4f-566">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="6ad4f-567">아래 첨자 숫자(... [0] ... [1] ...)를 사용하는 데이터 형식은 0부터 시작하여 순차적으로 번호가 매겨지는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-567">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="6ad4f-568">아래 첨자 번호에 간격이 있는 경우 간격 뒤에 있는 모든 항목은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-568">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="6ad4f-569">예를 들어 아래 첨자가 0과 1 대신 0과 2인 경우 두 번째 항목은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-569">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="6ad4f-570">사전</span><span class="sxs-lookup"><span data-stu-id="6ad4f-570">Dictionaries</span></span>

<span data-ttu-id="6ad4f-571">`Dictionary` 대상의 경우 모델 바인딩은 *parameter_name* 또는 *property_name*에 대한 일치 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-571">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="6ad4f-572">일치하는 항목이 없는 경우 접두사 없이 지원되는 양식 중 하나를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-572">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="6ad4f-573">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="6ad4f-573">For example:</span></span>

* <span data-ttu-id="6ad4f-574">대상 매개 변수가 `selectedCourses`라는 `Dictionary<int, string>`라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-574">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="6ad4f-575">게시된 양식 또는 쿼리 문자열 데이터는 다음 예제 중 하나와 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-575">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="6ad4f-576">앞의 모든 예제 형식의 경우 모델 바인딩은 두 항목의 사전을 `selectedCourses` 매개 변수에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-576">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="6ad4f-577">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="6ad4f-577">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="6ad4f-578">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="6ad4f-578">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="6ad4f-579">모델 바인딩 경로 데이터 및 쿼리 문자열의 세계화 동작</span><span class="sxs-lookup"><span data-stu-id="6ad4f-579">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="6ad4f-580">ASP.NET Core 경로 값 공급자와 쿼리 문자열 값 공급자:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-580">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="6ad4f-581">값을 고정 문화권으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-581">Treat values as invariant culture.</span></span>
* <span data-ttu-id="6ad4f-582">URL을 고정 문화권으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-582">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="6ad4f-583">이와 대조적으로, 양식 데이터에서 가져온 값은 문화권을 구분하여 변환을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-583">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="6ad4f-584">이는 기본적으로 URL을 로캘 간에 공유할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-584">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="6ad4f-585">ASP.NET Core 경로 값 공급자와 쿼리 문자열 값 공급자가 문화권 구분 변환을 수행하도록 하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-585">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="6ad4f-586"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>에서 상속됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-586">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="6ad4f-587">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) 또는 [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)에서 코드를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-587">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="6ad4f-588">값 공급자 생성자로 전달된 [문화권 값](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)을 [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-588">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="6ad4f-589">MVC 옵션의 기본 값 공급자 팩터리를 새 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-589">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="6ad4f-590">특수 데이터 형식</span><span class="sxs-lookup"><span data-stu-id="6ad4f-590">Special data types</span></span>

<span data-ttu-id="6ad4f-591">모델 바인딩이 처리할 수 있는 일부 특수 데이터 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-591">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="6ad4f-592">IFormFile 및 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="6ad4f-592">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="6ad4f-593">HTTP 요청에 포함되는 업로드된 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-593">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="6ad4f-594">또한 여러 파일에 대해 `IEnumerable<IFormFile>`이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-594">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="6ad4f-595">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="6ad4f-595">CancellationToken</span></span>

<span data-ttu-id="6ad4f-596">비동기 컨트롤러에서 작업을 취소하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-596">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="6ad4f-597">FormCollection</span><span class="sxs-lookup"><span data-stu-id="6ad4f-597">FormCollection</span></span>

<span data-ttu-id="6ad4f-598">게시된 양식 데이터에서 모든 값을 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-598">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="6ad4f-599">입력 포맷터</span><span class="sxs-lookup"><span data-stu-id="6ad4f-599">Input formatters</span></span>

<span data-ttu-id="6ad4f-600">요청 본문의 데이터는 JSON, XML 또는 일부 다른 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-600">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="6ad4f-601">이 데이터를 구문 분석하기 위해 모델 바인딩은 특정 콘텐츠 유형을 처리하도록 구성된 *입력 포맷터*를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-601">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="6ad4f-602">기본적으로 ASP.NET Core는 JSON 데이터를 처리하기 위한 JSON 기반 입력 포맷터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-602">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="6ad4f-603">다른 콘텐츠 형식에 대해 다른 포맷터를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-603">You can add other formatters for other content types.</span></span>

<span data-ttu-id="6ad4f-604">ASP.NET Core는 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 특성을 기반으로 입력 포맷터를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-604">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="6ad4f-605">특성이 없는 경우 [Content-Type 헤더](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-605">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="6ad4f-606">기본 제공 XML 입력 포맷터를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-606">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="6ad4f-607">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-607">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="6ad4f-608">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 또는 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-608">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="6ad4f-609">요청 본문에서 XML을 필요로 하는 컨트롤러 클래스 또는 작업 메서드에 `Consumes` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-609">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="6ad4f-610">자세한 내용은 [XML Serialization 소개](/dotnet/standard/serialization/introducing-xml-serialization)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-610">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="6ad4f-611">모델 바인딩에서 지정된 형식 제외</span><span class="sxs-lookup"><span data-stu-id="6ad4f-611">Exclude specified types from model binding</span></span>

<span data-ttu-id="6ad4f-612">모델 바인딩 및 시스템 동작의 유효성 검사는 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-612">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="6ad4f-613">[MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)에 세부 정보 공급 기업을 추가하여 `ModelMetadata`를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-613">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="6ad4f-614">기본 제공 세부 정보 공급 기업을 지정된 형식에 대한 모델 바인딩 또는 유효성 검사를 비활성화하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-614">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="6ad4f-615">지정된 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-615">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ad4f-616">예를 들어 `System.Version` 형식의 모든 모델에 대한 모델 바인딩을 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-616">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="6ad4f-617">지정된 형식의 속성에 대한 유효성 검사를 비활성화하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-617">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ad4f-618">예를 들어 `System.Guid` 형식의 속성에 대한 유효성 검사를 비활성화하려면:</span><span class="sxs-lookup"><span data-stu-id="6ad4f-618">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="6ad4f-619">사용자 지정 모델 바인더</span><span class="sxs-lookup"><span data-stu-id="6ad4f-619">Custom model binders</span></span>

<span data-ttu-id="6ad4f-620">사용자 지정 모델 바인더를 작성하고 지정된 대상에 대해 선택하도록 `[ModelBinder]` 특성을 사용하여 모델 바인딩을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-620">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="6ad4f-621">[사용자 모델 바인딩](xref:mvc/advanced/custom-model-binding)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-621">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="6ad4f-622">수동 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="6ad4f-622">Manual model binding</span></span>

<span data-ttu-id="6ad4f-623"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 메서드를 사용하여 모델 바인딩을 수동으로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-623">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="6ad4f-624">메서드는 `ControllerBase` 및 `PageModel` 클래스에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-624">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="6ad4f-625">메서드 오버로드를 통해 사용할 접두사 및 값 공급 기업을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-625">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="6ad4f-626">모델 바인딩이 실패하는 경우 메서드는 `false`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-626">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="6ad4f-627">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-627">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="6ad4f-628">[FromServices] 특성</span><span class="sxs-lookup"><span data-stu-id="6ad4f-628">[FromServices] attribute</span></span>

<span data-ttu-id="6ad4f-629">이 특성의 이름은 데이터 원본을 지정하는 모델 바인딩 특성의 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-629">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="6ad4f-630">그러나 값 공급 기업의 바인딩 데이터에 대한 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-630">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="6ad4f-631">[종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에서 형식의 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-631">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6ad4f-632">특정 메서드가 호출되는 경우에만 서비스가 필요할 때 생성자 주입에 대안을 제공하는 것이 목적입니다.</span><span class="sxs-lookup"><span data-stu-id="6ad4f-632">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ad4f-633">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6ad4f-633">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
