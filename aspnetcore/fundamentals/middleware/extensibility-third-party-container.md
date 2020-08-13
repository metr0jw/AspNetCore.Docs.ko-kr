---
title: ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화
author: rick-anderson
description: ASP.NET Core에서 팩터리 기반 활성화 및 타사 컨테이너를 사용하여 강력한 형식의 미들웨어를 사용하는 방법을 배웁니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 5d453de26b265b795768befeaa4071e7b0e1ec08
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017036"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="d53fd-103">ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화</span><span class="sxs-lookup"><span data-stu-id="d53fd-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d53fd-104">이 문서에서는 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 및 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 타사 컨테이너를 사용한 [미들웨어](xref:fundamentals/middleware/index) 활성화를 위한 확장 지점으로 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="d53fd-105">`IMiddlewareFactory` 및 `IMiddleware`에 대한 정보는 <xref:fundamentals/middleware/extensibility>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d53fd-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="d53fd-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d53fd-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d53fd-107">샘플 앱은 `IMiddlewareFactory` 구현 `SimpleInjectorMiddlewareFactory`를 사용한 미들웨어 활성화를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="d53fd-108">이 샘플에서는 [간단한 인젝터](https://simpleinjector.org) DI(종속성 주입) 컨테이너를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="d53fd-109">이 샘플의 미들웨어 구현은 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="d53fd-110">이 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="d53fd-111">이 샘플 앱에서는 순전히 예시 목적으로 [간단한 인젝터](https://github.com/simpleinjector/SimpleInjector)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="d53fd-112">간단한 인젝터의 사용은 보증하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="d53fd-113">간단한 인젝터 설명서 및 GitHub 문제에 설명된 미들웨어 활성화 방법은 간단한 인젝터의 관리자에서 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="d53fd-114">자세한 내용은 [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html) 및 [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d53fd-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="d53fd-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="d53fd-115">IMiddlewareFactory</span></span>

<span data-ttu-id="d53fd-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="d53fd-117">샘플 앱에서는 미들웨어 팩터리를 구현하여 `SimpleInjectorActivatedMiddleware` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="d53fd-118">미들웨어 팩터리는 간단한 인젝터 컨테이너를 사용하여 미들웨어를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="d53fd-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="d53fd-119">IMiddleware</span></span>

<span data-ttu-id="d53fd-120"><xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="d53fd-121">`IMiddlewareFactory` 구현에 의해 활성화된 미들웨어(*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="d53fd-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="d53fd-122">미들웨어에 대한 확장(*Middleware/MiddlewareExtensions.cs*)이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="d53fd-123">`Startup.ConfigureServices`는 여러 작업을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="d53fd-124">간단한 인젝터 컨테이너를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="d53fd-125">팩터리와 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="d53fd-126">간단한 인젝터 컨테이너에서 앱의 데이터베이스 컨텍스트를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="d53fd-127">미들웨어는 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d53fd-128">이 문서에서는 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 및 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 타사 컨테이너를 사용한 [미들웨어](xref:fundamentals/middleware/index) 활성화를 위한 확장 지점으로 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="d53fd-129">`IMiddlewareFactory` 및 `IMiddleware`에 대한 정보는 <xref:fundamentals/middleware/extensibility>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d53fd-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="d53fd-130">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d53fd-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d53fd-131">샘플 앱은 `IMiddlewareFactory` 구현 `SimpleInjectorMiddlewareFactory`를 사용한 미들웨어 활성화를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="d53fd-132">이 샘플에서는 [간단한 인젝터](https://simpleinjector.org) DI(종속성 주입) 컨테이너를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="d53fd-133">이 샘플의 미들웨어 구현은 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="d53fd-134">이 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="d53fd-135">이 샘플 앱에서는 순전히 예시 목적으로 [간단한 인젝터](https://github.com/simpleinjector/SimpleInjector)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="d53fd-136">간단한 인젝터의 사용은 보증하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="d53fd-137">간단한 인젝터 설명서 및 GitHub 문제에 설명된 미들웨어 활성화 방법은 간단한 인젝터의 관리자에서 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="d53fd-138">자세한 내용은 [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html) 및 [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d53fd-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="d53fd-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="d53fd-139">IMiddlewareFactory</span></span>

<span data-ttu-id="d53fd-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="d53fd-141">샘플 앱에서는 미들웨어 팩터리를 구현하여 `SimpleInjectorActivatedMiddleware` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="d53fd-142">미들웨어 팩터리는 간단한 인젝터 컨테이너를 사용하여 미들웨어를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="d53fd-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="d53fd-143">IMiddleware</span></span>

<span data-ttu-id="d53fd-144"><xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="d53fd-145">`IMiddlewareFactory` 구현에 의해 활성화된 미들웨어(*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="d53fd-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="d53fd-146">미들웨어에 대한 확장(*Middleware/MiddlewareExtensions.cs*)이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="d53fd-147">`Startup.ConfigureServices`는 여러 작업을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="d53fd-148">간단한 인젝터 컨테이너를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="d53fd-149">팩터리와 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="d53fd-150">간단한 인젝터 컨테이너에서 앱의 데이터베이스 컨텍스트를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="d53fd-151">미들웨어는 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d53fd-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d53fd-152">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d53fd-152">Additional resources</span></span>

* [<span data-ttu-id="d53fd-153">미들웨어</span><span class="sxs-lookup"><span data-stu-id="d53fd-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d53fd-154">팩터리 기반 미들웨어 활성화</span><span class="sxs-lookup"><span data-stu-id="d53fd-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="d53fd-155">간단한 인젝터 GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="d53fd-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="d53fd-156">간단한 인젝터 설명서</span><span class="sxs-lookup"><span data-stu-id="d53fd-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
