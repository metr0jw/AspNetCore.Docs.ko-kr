---
title: ASP.NET Core에서 오류 처리
author: rick-anderson
description: ASP.NET Core 앱에서 오류를 처리하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: da7f50b27e447b86bd8a06851b767488d51b7050
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592893"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="f5e52-103">ASP.NET Core에서 오류 처리</span><span class="sxs-lookup"><span data-stu-id="f5e52-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f5e52-104">작성자: [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f5e52-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f5e52-105">이 항목에서는 ASP.NET Core 웹앱에서 오류를 처리하는 일반적인 접근법을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="f5e52-106">웹 API는 <xref:web-api/handle-errors>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="f5e52-107">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="f5e52-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f5e52-108">([다운로드하는 방법](xref:index#how-to-download-a-sample)) F12 브라우저 개발자 도구의 네트워크 탭은 샘플 앱을 테스트할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f5e52-109">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="f5e52-109">Developer Exception Page</span></span>

<span data-ttu-id="f5e52-110">개발자 예외 페이지에는 요청 예외에 대한 자세한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f5e52-111">ASP.NET Core 템플릿에서 다음 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="f5e52-112">위의 강조 표시된 코드는 앱이 [개발 환경](xref:fundamentals/environments)에서 실행 중인 경우 개발자 예외 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f5e52-113">템플릿은 미들웨어 파이프라인의 앞부분에 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>를 배치하여 다음에 오는 미들웨어에서 throw된 예외를 catch할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="f5e52-114">위의 코드는 앱이 개발 환경에서 실행되는 경우에***만*** 개발자 예외 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-114">The preceding code enables the Developer Exception Page ***only*** when the app runs in the Development environment.</span></span> <span data-ttu-id="f5e52-115">앱이 프로덕션 환경에서 실행되는 경우에는 자세한 예외 정보를 공개적으로 표시해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="f5e52-116">환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f5e52-117">개발자 예외 페이지에는 예외 및 요청에 대한 다음 정보가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="f5e52-118">스택 추적</span><span class="sxs-lookup"><span data-stu-id="f5e52-118">Stack trace</span></span>
* <span data-ttu-id="f5e52-119">쿼리 문자열 매개 변수(있는 경우)</span><span class="sxs-lookup"><span data-stu-id="f5e52-119">Query string parameters if any</span></span>
* <span data-ttu-id="f5e52-120">Cookie(있는 경우)</span><span class="sxs-lookup"><span data-stu-id="f5e52-120">Cookies if any</span></span>
* <span data-ttu-id="f5e52-121">헤더</span><span class="sxs-lookup"><span data-stu-id="f5e52-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f5e52-122">예외 처리기 페이지</span><span class="sxs-lookup"><span data-stu-id="f5e52-122">Exception handler page</span></span>

<span data-ttu-id="f5e52-123">[프로덕션 환경](xref:fundamentals/environments)의 사용자 지정 오류 처리 페이지를 구성하려면 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f5e52-124">이 예외 처리 미들웨어에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-124">This exception handling middleware:</span></span>

* <span data-ttu-id="f5e52-125">예외를 잡고 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f5e52-126">대체 파이프라인에서 표시된 경로를 사용하여 요청을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="f5e52-127">응답이 시작된 경우에는 요청이 다시 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="f5e52-128">템플릿 생성 코드는 `/Error` 경로를 사용하여 요청을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="f5e52-129">다음 예제에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 개발 환경이 아닌 환경에서 예외 처리 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f5e52-130">Razor Pages 앱 템플릿은 *Pages* 폴더에 오류 페이지( *.cshtml*) 및 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 클래스(`ErrorModel`)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f5e52-131">MVC 앱의 프로젝트 템플릿에는 홈 컨트롤러에 대한 `Error` 작업 메서드와 오류 보기가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="f5e52-132">오류 처리기 작업 메서드를 `HttpGet`와 같은 HTTP 메서드 특성을 사용하여 표시하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f5e52-133">명시적 동사는 일부 요청이 작업 메서드에 도달하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="f5e52-134">인증되지 않은 사용자에게 오류 보기를 표시해야 하는 경우 메서드에 대한 익명 액세스를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f5e52-135">예외에 액세스</span><span class="sxs-lookup"><span data-stu-id="f5e52-135">Access the exception</span></span>

<span data-ttu-id="f5e52-136"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>를 사용하여 오류 처리기에서 예외 및 원래 요청 경로에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="f5e52-137">다음 코드에서는 ASP.NET Core 템플릿에서 생성된 기본 *Pages/Error.cshtml.cs*에 `ExceptionMessage`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="f5e52-138">클라이언트에 중요한 오류 정보를 **제공하지 마세요**.</span><span class="sxs-lookup"><span data-stu-id="f5e52-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f5e52-139">오류 제공은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="f5e52-140">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 예외를 테스트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f5e52-141">환경을 프로덕션으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-141">Set the environment to production.</span></span>
* <span data-ttu-id="f5e52-142">*Program.cs*의 `webBuilder.UseStartup<Startup>();`에서 주석을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f5e52-143">홈페이지에서 **예외 트리거**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f5e52-144">예외 처리기 람다</span><span class="sxs-lookup"><span data-stu-id="f5e52-144">Exception handler lambda</span></span>

<span data-ttu-id="f5e52-145">[사용자 지정 예외 처리기 페이지](#exception-handler-page)의 대안은 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>에 람다를 제공하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f5e52-146">람다를 사용하면 응답을 반환하기 전에 오류에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f5e52-147">다음 코드에서는 예외 처리에 람다를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="f5e52-148"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 또는 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>에서 클라이언트에 중요한 오류 정보를 **제공하지 마세요**.</span><span class="sxs-lookup"><span data-stu-id="f5e52-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f5e52-149">오류 제공은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="f5e52-150">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 예외 처리 람다를 테스트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f5e52-151">환경을 프로덕션으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-151">Set the environment to production.</span></span>
* <span data-ttu-id="f5e52-152">*Program.cs*의 `webBuilder.UseStartup<StartupLambda>();`에서 주석을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f5e52-153">홈페이지에서 **예외 트리거**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f5e52-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f5e52-154">UseStatusCodePages</span></span>

<span data-ttu-id="f5e52-155">기본적으로 ASP.NET Core 앱은 ‘404 - 찾을 수 없음’과 같은 HTTP 오류 상태 코드에 대한 상태 코드 페이지를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f5e52-156">앱에서 본문이 없는 HTTP 400-499 오류 상태가 발생하면 상태 코드와 빈 응답 본문을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="f5e52-157">상태 코드 페이지를 제공하려면 상태 코드 페이지 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="f5e52-158">일반적인 오류 상태 코드에 대해 기본 텍스트 전용 처리기를 사용하려면 `Startup.Configure` 메서드에서 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="f5e52-159">요청 처리 미들웨어보다 먼저 `UseStatusCodePages`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="f5e52-160">예를 들어 정적 파일 미들웨어 및 엔드포인트 미들웨어보다 먼저 `UseStatusCodePages`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="f5e52-161">`UseStatusCodePages`를 사용하지 않는 경우 엔드포인트가 없는 URL로 이동하면 엔드포인트를 찾을 수 없다는 브라우저 종속 오류 메시지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="f5e52-162">예를 들어 `Home/Privacy2`로 이동하는 경우</span><span class="sxs-lookup"><span data-stu-id="f5e52-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="f5e52-163">`UseStatusCodePages`를 호출하면 브라우저에서 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="f5e52-164">`UseStatusCodePages`는 사용자에게 유용하지 않은 메시지를 반환하기 때문에 일반적으로 프로덕션에서 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f5e52-165">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="f5e52-166">환경을 프로덕션으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-166">Set the environment to production.</span></span>
* <span data-ttu-id="f5e52-167">*Program.cs*의 `webBuilder.UseStartup<StartupUseStatusCodePages>();`에서 주석을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="f5e52-168">홈페이지에서 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f5e52-169">형식 문자열을 사용하는 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f5e52-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f5e52-170">응답 콘텐츠 형식 및 텍스트를 사용자 지정하려면 콘텐츠 형식 및 형식 문자열을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="f5e52-171">위의 코드에서 `{0}`은 오류 코드에 대한 자리 표시자입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="f5e52-172">형식 문자열이 있는 `UseStatusCodePages`는 사용자에게 유용하지 않은 메시지를 반환하기 때문에 일반적으로 프로덕션에서 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f5e52-173">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs*의 `webBuilder.UseStartup<StartupFormat>();`에서 주석을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f5e52-174">람다를 사용하는 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f5e52-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f5e52-175">사용자 지정 오류 처리 및 응답 쓰기 코드를 지정하려면 람다 식을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="f5e52-176">람다를 사용하는 `UseStatusCodePages`는 사용자에게 유용하지 않은 메시지를 반환하기 때문에 일반적으로 프로덕션에서 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="f5e52-177">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs*의 `webBuilder.UseStartup<StartupStatusLambda>();`에서 주석을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="f5e52-178">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="f5e52-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="f5e52-179"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 확장 메서드는:</span><span class="sxs-lookup"><span data-stu-id="f5e52-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="f5e52-180">‘302 - 찾음’ 상태 코드를 클라이언트에 보냅니다. </span><span class="sxs-lookup"><span data-stu-id="f5e52-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="f5e52-181">URL 템플릿에 제공된 오류 처리 엔드포인트로 클라이언트를 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="f5e52-182">오류 처리 엔드포인트는 일반적으로 오류 정보를 표시하고 HTTP 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="f5e52-183">위의 코드에 표시된 것처럼 URL 템플릿에는 상태 코드에 대한 `{0}` 자리 표시자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="f5e52-184">URL 템플릿이 `~`(물결표)로 시작하는 경우 `~`는 앱의 `PathBase`로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f5e52-185">앱에서 엔드포인트를 지정할 때 해당 엔드포인트에 대한 MVC 보기 또는 Razor 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-185">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f5e52-186">Razor Pages 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)의 [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-186">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f5e52-187">이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f5e52-188">일반적으로 다른 앱이 오류를 처리하는 상황에서 앱이 클라이언트를 다른 엔드포인트로 리디렉션해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f5e52-189">웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 리디렉션된 엔드포인트가 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f5e52-190">초기 리디렉션 응답과 함께 원래 상태 코드를 유지하고 반환하면 안 되는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="f5e52-191">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs*의 `webBuilder.UseStartup<StartupSCredirect>();`에서 주석을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f5e52-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f5e52-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f5e52-193"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 확장 메서드는:</span><span class="sxs-lookup"><span data-stu-id="f5e52-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="f5e52-194">원래 상태 코드를 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f5e52-195">대체 경로에서 요청 파이프라인을 다시 실행하여 응답 본문을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="f5e52-196">앱 내에 엔드포인트가 지정된 경우 해당 엔드포인트에 대한 MVC 보기 또는 Razor 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-196">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f5e52-197">요청이 상태 페이지로 다시 라우팅될 수 있도록 `UseRouting` 전에 `UseStatusCodePagesWithReExecute`를 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="f5e52-198">Razor Pages 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)의 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-198">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f5e52-199">이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f5e52-200">다른 엔드포인트로 리디렉션하지 않고 요청을 처리해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f5e52-201">웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 원래 요청된 엔드포인트가 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f5e52-202">원래 상태 코드를 유지하고 응답과 함께 반환해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f5e52-203">URL 및 쿼리 문자열 템플릿에는 상태 코드에 대한 자리 표시자(`{0}`)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="f5e52-204">URL 템플릿은 `/`로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="f5e52-205">오류를 처리하는 엔드포인트는 다음 예제와 같이 오류를 생성한 원래 URL을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e52-206">Razor Pages 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)의 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-206">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="f5e52-207">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)에서 `UseStatusCodePages`를 테스트하려면 *Program.cs*의 `webBuilder.UseStartup<StartupSCreX>();`에서 주석을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="f5e52-208">상태 코드 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f5e52-208">Disable status code pages</span></span>

<span data-ttu-id="f5e52-209">MVC 컨트롤러 또는 작업 메서드에 대한 상태 코드 페이지를 비활성화하려면 [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="f5e52-210">Razor Pages 처리기 메서드 또는 MVC 컨트롤러에서 특정 요청에 대한 상태 코드 페이지를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-210">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="f5e52-211">예외 처리 코드</span><span class="sxs-lookup"><span data-stu-id="f5e52-211">Exception-handling code</span></span>

<span data-ttu-id="f5e52-212">예외 처리 페이지의 코드도 예외를 throw할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="f5e52-213">프로덕션 오류 페이지는 철저히 테스트하고 자체적으로 예외를 throw하지 않도록 특히 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="f5e52-214">응답 헤더</span><span class="sxs-lookup"><span data-stu-id="f5e52-214">Response headers</span></span>

<span data-ttu-id="f5e52-215">또한 응답의 헤더가 전송되고 나면 다음 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f5e52-216">앱에서 응답의 상태 코드를 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f5e52-217">예외 페이지 또는 처리기를 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f5e52-218">응답을 완료하거나 연결이 중단되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f5e52-219">서버 예외 처리</span><span class="sxs-lookup"><span data-stu-id="f5e52-219">Server exception handling</span></span>

<span data-ttu-id="f5e52-220">앱의 예외 처리 논리 외에도 [HTTP 서버 구현](xref:fundamentals/servers/index)에서 몇 가지 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f5e52-221">응답 헤더가 전송되기 전에 예외를 catch한 서버는 응답 본문 없이 `500 - Internal Server Error` 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="f5e52-222">응답 헤더가 전송된 후에 예외를 catch한 서버는 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f5e52-223">앱에서 처리되지 않는 요청은 서버에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="f5e52-224">서버에서 요청을 처리할 때 발생하는 모든 예외는 서버의 예외 처리에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f5e52-225">앱의 사용자 지정 오류 페이지, 예외 처리 미들웨어 및 필터는 이 동작에 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f5e52-226">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="f5e52-226">Startup exception handling</span></span>

<span data-ttu-id="f5e52-227">호스팅 계층만 앱 시작 시 발생하는 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f5e52-228">[시작 오류를 캡처](xref:fundamentals/host/web-host#capture-startup-errors)하고 [자세한 오류를 캡처](xref:fundamentals/host/web-host#detailed-errors)하도록 호스트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f5e52-229">호스팅 계층은 호스트 주소/포트 바인딩 후에 오류가 발생하는 경우에만 캡처된 시작 오류에 대한 오류 페이지만 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f5e52-230">바인딩이 실패하면 결과는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-230">If binding fails:</span></span>

* <span data-ttu-id="f5e52-231">호스팅 계층에서 심각한 예외를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f5e52-232">dotnet 프로세스의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="f5e52-233">HTTP 서버가 [Kestrel](xref:fundamentals/servers/kestrel)인 경우 오류 페이지가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f5e52-234">[IIS](/iis)(또는 Azure App Service) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)에서 실행 중일 때, 프로세스를 시작할 수 없는 경우 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 ‘502.5 - 프로세스 실패’를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f5e52-235">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f5e52-236">데이터베이스 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="f5e52-236">Database error page</span></span>

<span data-ttu-id="f5e52-237">데이터베이스 개발자 페이지 예외 필터 `AddDatabaseDeveloperPageExceptionFilter`는 Entity Framework Core 마이그레이션을 사용하여 해결할 수 있는 데이터베이스 관련 예외를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="f5e52-238">이 예외가 발생하면 문제 해결을 위해 가능한 작업의 세부 정보가 포함된 HTML 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="f5e52-239">이 페이지는 개발 환경에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="f5e52-240">다음 코드는 개별 사용자 계정을 지정할 때 ASP.NET Core Razor 페이지 템플릿에서 생성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-240">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="f5e52-241">예외 필터</span><span class="sxs-lookup"><span data-stu-id="f5e52-241">Exception filters</span></span>

<span data-ttu-id="f5e52-242">MVC 앱에서는 예외 필터를 전역으로 구성하거나 컨트롤러별 또는 작업별로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f5e52-243">Razor Pages 앱에서는 전역으로 구성하거나 페이지 모델별로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-243">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f5e52-244">이러한 필터는 컨트롤러 작업 또는 다른 필터를 실행하는 동안 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f5e52-245">자세한 내용은 <xref:mvc/controllers/filters#exception-filters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="f5e52-246">예외 필터는 MVC 작업 내에서 발생하는 예외를 트래핑하는 데 유용하지만 기본 제공 [예외 처리 미들웨어](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler`만큼 유연하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="f5e52-247">선택한 MVC 작업에 따라 오류 처리를 다르게 수행해야 하는 경우에만 `UseExceptionHandler`를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="f5e52-248">모델 상태 오류</span><span class="sxs-lookup"><span data-stu-id="f5e52-248">Model state errors</span></span>

<span data-ttu-id="f5e52-249">모델 상태 오류를 처리하는 방법에 대한 자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 및 [모델 유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5e52-250">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f5e52-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f5e52-251">작성자: [Tom Dykstra](https://github.com/tdykstra/) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f5e52-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f5e52-252">이 항목에서는 ASP.NET Core 웹앱에서 오류를 처리하는 일반적인 접근법을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="f5e52-253">웹 API는 <xref:web-api/handle-errors>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="f5e52-254">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="f5e52-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f5e52-255">([다운로드하는 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5e52-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f5e52-256">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="f5e52-256">Developer Exception Page</span></span>

<span data-ttu-id="f5e52-257">개발자 예외 페이지에는 요청 예외에 대한 자세한 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f5e52-258">ASP.NET Core 템플릿에서 다음 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="f5e52-259">위의 코드는 앱이 [개발 환경](xref:fundamentals/environments)에서 실행 중인 경우 개발자 예외 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f5e52-260">템플릿은 미들웨어 앞에 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>를 배치하여 다음에 오는 미들웨어에서 예외를 catch할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="f5e52-261">위의 코드에서는 **앱이 개발 환경에서 실행 중인 경우에만** 개발자 예외 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="f5e52-262">앱이 프로덕션에서 실행되는 경우에는 자세한 예외 정보를 공개적으로 표시해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="f5e52-263">환경 구성 방법에 대한 자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f5e52-264">개발자 예외 페이지에는 예외 및 요청에 대한 다음 정보가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="f5e52-265">스택 추적</span><span class="sxs-lookup"><span data-stu-id="f5e52-265">Stack trace</span></span>
* <span data-ttu-id="f5e52-266">쿼리 문자열 매개 변수(있는 경우)</span><span class="sxs-lookup"><span data-stu-id="f5e52-266">Query string parameters if any</span></span>
* <span data-ttu-id="f5e52-267">Cookie(있는 경우)</span><span class="sxs-lookup"><span data-stu-id="f5e52-267">Cookies if any</span></span>
* <span data-ttu-id="f5e52-268">헤더</span><span class="sxs-lookup"><span data-stu-id="f5e52-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f5e52-269">예외 처리기 페이지</span><span class="sxs-lookup"><span data-stu-id="f5e52-269">Exception handler page</span></span>

<span data-ttu-id="f5e52-270">프로덕션 환경의 사용자 지정 오류 처리 페이지를 구성하려면 예외 처리 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="f5e52-271">이 미들웨어는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-271">The middleware:</span></span>

* <span data-ttu-id="f5e52-272">예외를 잡고 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f5e52-273">표시된 페이지 또는 컨트롤러에 대한 대체 파이프라인에서 요청을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="f5e52-274">응답이 시작된 경우에는 요청이 다시 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="f5e52-275">템플릿 생성 코드는 `/Error`에 대한 요청을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="f5e52-276">다음 예제에서 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 비 개발 환경에서 예외 처리 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f5e52-277">Razor Pages 앱 템플릿은 *Pages* 폴더에 오류 페이지( *.cshtml*) 및 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 클래스(`ErrorModel`)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-277">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f5e52-278">MVC 앱의 프로젝트 템플릿에는 홈 컨트롤러에 대한 오류 작업 메서드와 오류 보기가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="f5e52-279">오류 처리기 작업 메서드를 `HttpGet`와 같은 HTTP 메서드 특성을 사용하여 표시하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f5e52-280">명시적 동사는 일부 요청이 메서드에 도달하지 못하게 방해합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="f5e52-281">인증되지 않은 사용자에게 오류 보기를 표시해야 하는 경우 메서드에 대한 익명 액세스를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f5e52-282">예외에 액세스</span><span class="sxs-lookup"><span data-stu-id="f5e52-282">Access the exception</span></span>

<span data-ttu-id="f5e52-283"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>를 사용하여 오류 처리기 컨트롤러 또는 페이지에서 예외 및 원래 요청 경로에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="f5e52-284">클라이언트에 중요한 오류 정보를 **제공하지 마세요**.</span><span class="sxs-lookup"><span data-stu-id="f5e52-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f5e52-285">오류 제공은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="f5e52-286">위의 예외 처리 페이지를 트리거하려면 환경을 프로덕션으로 설정하고 예외를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f5e52-287">예외 처리기 람다</span><span class="sxs-lookup"><span data-stu-id="f5e52-287">Exception handler lambda</span></span>

<span data-ttu-id="f5e52-288">[사용자 지정 예외 처리기 페이지](#exception-handler-page)의 대안은 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>에 람다를 제공하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="f5e52-289">람다를 사용하면 응답을 반환하기 전에 오류에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f5e52-290">다음은 예외 처리를 위해 람다를 사용하는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="f5e52-291">위의 코드에서 Internet Explorer 브라우저에 IE 오류 메시지가 아닌 오류 메시지가 표시되도록 `await context.Response.WriteAsync(new string(' ', 512));`가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="f5e52-292">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/16144)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="f5e52-293"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 또는 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>에서 클라이언트에 중요한 오류 정보를 **제공하지 마세요**.</span><span class="sxs-lookup"><span data-stu-id="f5e52-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f5e52-294">오류 제공은 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="f5e52-295">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)에서 예외 처리 람다의 결과를 확인하려면 `ProdEnvironment` 및 `ErrorHandlerLambda` 전처리기 지시문을 사용하고 홈페이지에서 **Trigger an exception**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f5e52-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f5e52-296">UseStatusCodePages</span></span>

<span data-ttu-id="f5e52-297">기본적으로 ASP.NET Core 앱은 ‘404 - 찾을 수 없음’과 같은 HTTP 상태 코드에 대한 상태 코드 페이지를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f5e52-298">앱은 상태 코드와 빈 응답 본문을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="f5e52-299">상태 코드 페이지를 제공하려면 상태 코드 페이지 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="f5e52-300">미들웨어는 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 패키지를 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="f5e52-301">일반적인 오류 상태 코드에 대해 기본 텍스트 전용 처리기를 사용하려면 `Startup.Configure` 메서드에서 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="f5e52-302">요청 처리 미들웨어(예: 정적 파일 미들웨어 및 MVC 미들웨어) 이전에 `UseStatusCodePages`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="f5e52-303">`UseStatusCodePages`를 사용하지 않는 경우 엔드포인트가 없는 URL로 이동하면 엔드포인트를 찾을 수 없다는 브라우저 종속 오류 메시지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="f5e52-304">예를 들어 `Home/Privacy2`로 이동하는 경우</span><span class="sxs-lookup"><span data-stu-id="f5e52-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="f5e52-305">`UseStatusCodePages`를 호출하면 브라우저에서 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f5e52-306">형식 문자열을 사용하는 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f5e52-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f5e52-307">응답 콘텐츠 형식 및 텍스트를 사용자 지정하려면 콘텐츠 형식 및 형식 문자열을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f5e52-308">람다를 사용하는 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f5e52-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f5e52-309">사용자 지정 오류 처리 및 응답 쓰기 코드를 지정하려면 람다 식을 사용하는 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>의 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="f5e52-310">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="f5e52-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="f5e52-311"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 확장 메서드는:</span><span class="sxs-lookup"><span data-stu-id="f5e52-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="f5e52-312">‘302 - 찾음’ 상태 코드를 클라이언트에 보냅니다. </span><span class="sxs-lookup"><span data-stu-id="f5e52-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="f5e52-313">클라이언트를 URL 템플릿에 제공된 위치로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="f5e52-314">예제에서 볼 수 있는 것과 같이 URL 템플릿에는 상태 코드에 대한 `{0}` 자리 표시자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="f5e52-315">URL 템플릿이 `~`(물결표)로 시작하는 경우 `~`는 앱의 `PathBase`로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f5e52-316">앱 내의 엔드포인트를 가리키는 경우 해당 엔드포인트에 대한 MVC 뷰 또는 Razor 페이지를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-316">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f5e52-317">Razor Pages 예제를 보려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)의 *Pages/StatusCode.cshtml*을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-317">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f5e52-318">이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f5e52-319">일반적으로 다른 앱이 오류를 처리하는 상황에서 앱이 클라이언트를 다른 엔드포인트로 리디렉션해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f5e52-320">웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 리디렉션된 엔드포인트가 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f5e52-321">초기 리디렉션 응답과 함께 원래 상태 코드를 유지하고 반환하면 안 되는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f5e52-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f5e52-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f5e52-323"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 확장 메서드는:</span><span class="sxs-lookup"><span data-stu-id="f5e52-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="f5e52-324">원래 상태 코드를 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f5e52-325">대체 경로에서 요청 파이프라인을 다시 실행하여 응답 본문을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="f5e52-326">앱 내의 엔드포인트를 가리키는 경우 해당 엔드포인트에 대한 MVC 뷰 또는 Razor 페이지를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-326">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f5e52-327">요청이 상태 페이지로 다시 라우팅될 수 있도록 `UseRouting` 전에 `UseStatusCodePagesWithReExecute`를 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="f5e52-328">Razor Pages 예제를 보려면 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)의 *Pages/StatusCode.cshtml*을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-328">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f5e52-329">이 메서드는 일반적으로 앱이 다음과 같은 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f5e52-330">다른 엔드포인트로 리디렉션하지 않고 요청을 처리해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f5e52-331">웹앱의 경우 클라이언트의 브라우저 주소 표시줄에 원래 요청된 엔드포인트가 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f5e52-332">원래 상태 코드를 유지하고 응답과 함께 반환해야 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="f5e52-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f5e52-333">URL 및 쿼리 문자열 템플릿에는 상태 코드에 대한 자리 표시자(`{0}`)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="f5e52-334">URL 템플릿은 슬래시(`/`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="f5e52-335">경로에서 자리 표시자를 사용하는 경우, 엔드포인트(페이지 또는 컨트롤러)가 경로 세그먼트를 처리할 수 있는지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="f5e52-336">예를 들어 오류를 위한 Razor 페이지는 `@page` 지시문을 사용하여 선택적 경로 세그먼트 값을 수락해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-336">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="f5e52-337">오류를 처리하는 엔드포인트는 다음 예제와 같이 오류를 생성한 원래 URL을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="f5e52-338">상태 코드 페이지 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="f5e52-338">Disable status code pages</span></span>

<span data-ttu-id="f5e52-339">MVC 컨트롤러 또는 작업 메서드에 대한 상태 코드 페이지를 비활성화하려면 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="f5e52-340">Razor Pages 처리기 메서드 또는 MVC 컨트롤러에서 특정 요청에 대한 상태 코드 페이지를 사용하지 않으려면 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-340">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="f5e52-341">예외 처리 코드</span><span class="sxs-lookup"><span data-stu-id="f5e52-341">Exception-handling code</span></span>

<span data-ttu-id="f5e52-342">예외 처리 페이지의 코드도 예외를 던질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="f5e52-343">프로덕션 오류 페이지를 완전한 정적 콘텐츠로 구성하는 것은 종종 좋은 생각입니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="f5e52-344">응답 헤더</span><span class="sxs-lookup"><span data-stu-id="f5e52-344">Response headers</span></span>

<span data-ttu-id="f5e52-345">또한 응답의 헤더가 전송되고 나면 다음 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f5e52-346">앱에서 응답의 상태 코드를 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f5e52-347">예외 페이지 또는 처리기를 실행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f5e52-348">응답을 완료하거나 연결이 중단되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f5e52-349">서버 예외 처리</span><span class="sxs-lookup"><span data-stu-id="f5e52-349">Server exception handling</span></span>

<span data-ttu-id="f5e52-350">앱의 예외 처리 논리 외에도 [HTTP 서버 구현](xref:fundamentals/servers/index)에서 몇 가지 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f5e52-351">응답 헤더가 전송되기 전에 예외를 catch한 서버는 응답 본문 없이 ‘500 - 내부 서버 오류’ 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="f5e52-352">응답 헤더가 전송된 후에 예외를 catch한 서버는 연결을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f5e52-353">앱으로 처리되지 않는 요청은 서버에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="f5e52-354">서버에서 요청을 처리할 때 발생하는 모든 예외는 서버의 예외 처리에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f5e52-355">앱의 사용자 지정 오류 페이지, 예외 처리 미들웨어 및 필터는 이 동작에 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f5e52-356">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="f5e52-356">Startup exception handling</span></span>

<span data-ttu-id="f5e52-357">호스팅 계층만 앱 시작 시 발생하는 예외를 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f5e52-358">[시작 오류를 캡처](xref:fundamentals/host/web-host#capture-startup-errors)하고 [자세한 오류를 캡처](xref:fundamentals/host/web-host#detailed-errors)하도록 호스트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f5e52-359">호스팅 계층은 호스트 주소/포트 바인딩 후에 오류가 발생하는 경우에만 캡처된 시작 오류에 대한 오류 페이지만 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f5e52-360">바인딩이 실패하면 결과는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-360">If binding fails:</span></span>

* <span data-ttu-id="f5e52-361">호스팅 계층에서 심각한 예외를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f5e52-362">dotnet 프로세스의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="f5e52-363">HTTP 서버가 [Kestrel](xref:fundamentals/servers/kestrel)인 경우 오류 페이지가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f5e52-364">[IIS](/iis)(또는 Azure App Service) 또는 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)에서 실행 중일 때, 프로세스를 시작할 수 없는 경우 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)이 ‘502.5 - 프로세스 실패’를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f5e52-365">자세한 내용은 <xref:test/troubleshoot-azure-iis>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f5e52-366">데이터베이스 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="f5e52-366">Database error page</span></span>

<span data-ttu-id="f5e52-367">데이터베이스 오류 페이지 미들웨어는 Entity Framework 마이그레이션을 사용하여 해결할 수 있는 데이터베이스 관련 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="f5e52-368">이 예외가 발생하면 문제 해결을 위한 가능한 작업의 세부 정보가 포함된 HTML 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="f5e52-369">이 페이지는 개발 환경에서만 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="f5e52-370">`Startup.Configure`에 코드를 추가하여 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="f5e52-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>에는 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="f5e52-372">예외 필터</span><span class="sxs-lookup"><span data-stu-id="f5e52-372">Exception filters</span></span>

<span data-ttu-id="f5e52-373">MVC 앱에서는 예외 필터를 전역으로 구성하거나 컨트롤러별 또는 작업별로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f5e52-374">Razor Pages 앱에서는 전역으로 구성하거나 페이지 모델별로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-374">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f5e52-375">이러한 필터는 컨트롤러 작업 또는 다른 필터를 실행하는 동안 발생하는 처리되지 않은 예외를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f5e52-376">자세한 내용은 <xref:mvc/controllers/filters#exception-filters>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="f5e52-377">예외 필터는 MVC 작업 내에서 발생하는 예외를 트래핑하는 데 유용하지만 예외 처리 미들웨어만큼 유연하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="f5e52-378">미들웨어를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f5e52-378">We recommend using the middleware.</span></span> <span data-ttu-id="f5e52-379">선택한 MVC 작업에 따라 오류 처리를 다르게 수행해야 하는 경우에만 필터를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="f5e52-380">모델 상태 오류</span><span class="sxs-lookup"><span data-stu-id="f5e52-380">Model state errors</span></span>

<span data-ttu-id="f5e52-381">모델 상태 오류를 처리하는 방법에 대한 자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 및 [모델 유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5e52-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5e52-382">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f5e52-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
