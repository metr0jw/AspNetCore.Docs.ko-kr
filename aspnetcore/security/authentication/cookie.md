---
title: 인증을 사용 cookie 하지 않고 사용 ASP.NET Core Identity
author: rick-anderson
description: 를 사용 하지 않고 인증을 사용 하는 방법을 알아봅니다 cookie ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 04d2f0d289e2c9ec13aeb880df47240bec19d3ec
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876765"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="870f9-103">인증을 사용 cookie 하지 않고 사용 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="870f9-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="870f9-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="870f9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="870f9-105">ASP.NET Core Identity 는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="870f9-106">그러나를 cookie 사용 하지 않는 기반 인증 공급자를 ASP.NET Core Identity 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="870f9-107">자세한 내용은 <xref:security/authentication/identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="870f9-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="870f9-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="870f9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="870f9-109">샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="870f9-110">**전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="870f9-111">사용자는 `AuthenticateUser` *Pages/Account/Login. cshtml* 파일의 메서드에서 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="870f9-112">실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="870f9-113">구성</span><span class="sxs-lookup"><span data-stu-id="870f9-113">Configuration</span></span>

<span data-ttu-id="870f9-114">앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore Cookie 에 대 한 패키지 참조를 만듭니다. s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지.</span><span class="sxs-lookup"><span data-stu-id="870f9-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="870f9-115">`Startup.ConfigureServices`메서드에서 및 메서드를 사용 하 여 인증 미들웨어 서비스를 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 만듭니다 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> .</span><span class="sxs-lookup"><span data-stu-id="870f9-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="870f9-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 에 전달 된는 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="870f9-117">`AuthenticationScheme` 는 인증 인스턴스가 여러 개 있고 cookie [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="870f9-118">`AuthenticationScheme`를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) 은 Cookie 스키마에 대해 "s" 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="870f9-119">체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="870f9-120">앱의 인증 체계가 앱의 인증 체계와 다릅니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="870f9-121">cookie인증 체계가에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s")를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="870f9-122">인증 cookie 의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로로 설정 됩니다 `true` .</span><span class="sxs-lookup"><span data-stu-id="870f9-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="870f9-123">cookie사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에는 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="870f9-124">자세한 내용은 <xref:security/gdpr#essential-cookies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="870f9-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="870f9-125">에서 `Startup.Configure` 및를 `UseAuthentication` 호출 `UseAuthorization` 하 여 속성을 설정 하 `HttpContext.User` 고 요청에 대 한 권한 부여 미들웨어를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="870f9-126">`UseAuthentication`를 `UseAuthorization` 호출 하기 전에 및 메서드를 호출 합니다 `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="870f9-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="870f9-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="870f9-128">`CookieAuthenticationOptions`메서드의 인증에 대 한 서비스 구성에서 설정 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="870f9-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="870f9-129">Cookie 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="870f9-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="870f9-130">[ Cookie 정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) cookie 를 통해 정책 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="870f9-131">응용 프로그램 처리 파이프라인에 미들웨어를 추가 하는 것은 순서를 구분 하기 때문에 &mdash; 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="870f9-132"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>정책 미들웨어에 제공 된를 사용 Cookie 하 여 cookie cookie cookie 가 추가 되거나 삭제 될 때 처리 및 처리 처리기에 대 한 전역 특성을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="870f9-133">기본값은 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> `SameSiteMode.Lax` OAuth2 인증을 허용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="870f9-134">의 동일한 사이트 정책을 엄격 하 게 적용 하려면를 `SameSiteMode.Strict` 설정 `MinimumSameSitePolicy` 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="870f9-135">이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 cookie 원본 간 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 보안 수준을 강화 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="870f9-136">Cookie의 정책 미들웨어 설정은 `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` 아래 행렬에 따라 설정의 설정에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="870f9-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="870f9-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="870f9-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="870f9-138">Cookie.SameSite</span></span> | <span data-ttu-id="870f9-139">결과 Cookie . SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="870f9-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="870f9-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-140">SameSiteMode.None</span></span>     | <span data-ttu-id="870f9-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-141">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="870f9-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-144">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="870f9-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="870f9-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-148">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="870f9-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="870f9-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="870f9-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-155">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="870f9-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="870f9-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="870f9-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="870f9-161">인증 만들기 cookie</span><span class="sxs-lookup"><span data-stu-id="870f9-161">Create an authentication cookie</span></span>

<span data-ttu-id="870f9-162">cookie보유 사용자 정보를 만들려면를 구성 <xref:System.Security.Claims.ClaimsPrincipal> 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="870f9-163">사용자 정보는 serialize 되 고에 저장 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="870f9-164">필요한를 사용 하 여를 만들고를 <xref:System.Security.Claims.ClaimsIdentity> <xref:System.Security.Claims.Claim> 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="870f9-165">`SignInAsync` 암호화 된를 만들어 cookie 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="870f9-166">`AuthenticationScheme`을 지정 하지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="870f9-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> 는 기본적으로 몇 가지 특정 경로 (예: 로그인 경로 및 로그 아웃 경로) 에서만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="870f9-168">자세한 내용은 [ Cookie authenticationhandler 원본](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="870f9-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="870f9-169">ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="870f9-170">여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="870f9-171">로그아웃</span><span class="sxs-lookup"><span data-stu-id="870f9-171">Sign out</span></span>

<span data-ttu-id="870f9-172">현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> .</span><span class="sxs-lookup"><span data-stu-id="870f9-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="870f9-173">`CookieAuthenticationDefaults.AuthenticationScheme`(또는 " Cookie s")가 구성표 (예: "Contoso")로 사용 되지 않는 경우 Cookie 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="870f9-174">그렇지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="870f9-175">서버에는 클라이언트 브라우저가 제어 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-175">The server has no control of the clients browser.</span></span> <span data-ttu-id="870f9-176">사용자가 브라우저 또는 탭을 닫으면 서버에서 사용자를 로그 아웃할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-176">If the user closes the browser or tab, the server cannot sign out the user.</span></span> <span data-ttu-id="870f9-177">브라우저를 닫을 때 사용자 로그 아웃을 구현 하려면 JavaScript를 사용 하 여 해당 사용자를 검색 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-177">To implement signing out the user when the browser is closed, you must detect that with JavaScript.</span></span> <span data-ttu-id="870f9-178">"브라우저 창 탭 닫기 이벤트를 검색 하는 방법"을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-178">Search for "How to Detect Browser Window Tab Close Event?".</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="870f9-179">백 엔드 변경 내용에 대응</span><span class="sxs-lookup"><span data-stu-id="870f9-179">React to back-end changes</span></span>

<span data-ttu-id="870f9-180">가 cookie 만들어지면는 cookie id의 단일 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-180">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="870f9-181">백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="870f9-181">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="870f9-182">앱의 cookie 인증 시스템은 인증에 따라 요청을 계속 처리 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-182">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="870f9-183">인증이 유효한 경우 사용자는 앱에 로그인 상태를 유지 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-183">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="870f9-184"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>이벤트를 사용 하 여 id의 유효성 검사를 가로채 고 재정의할 수 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-184">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="870f9-185">모든 요청에서의 유효성을 검사 하면 cookie 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-185">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="870f9-186">유효성 검사에 대 한 한 가지 방법은 cookie 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-186">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="870f9-187">사용자가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에 cookie 도 해당 사용자를 다시 인증할 필요가 없습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-187">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="870f9-188">샘플 앱에서 데이터베이스는에서 구현 되 `IUserRepository` 고 값을 저장 합니다 `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="870f9-188">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="870f9-189">데이터베이스에서 사용자를 업데이트 하는 경우 값은 `LastChanged` 현재 시간으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-189">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="870f9-190">cookie값에 따라 데이터베이스가 변경 될 때를 무효화 하려면 데이터베이스의 `LastChanged` 현재 값이 포함 된 클레임을 사용 하 여를 만듭니다 cookie `LastChanged` `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="870f9-190">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="870f9-191">이벤트에 대 한 재정의를 구현 하려면 `ValidatePrincipal` 에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> .</span><span class="sxs-lookup"><span data-stu-id="870f9-191">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="870f9-192">다음은의 구현 예제입니다 `CookieAuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="870f9-192">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="870f9-193">메서드에서 서비스를 등록 하는 동안 events 인스턴스를 등록 cookie `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-193">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="870f9-194">클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다 `CustomCookieAuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="870f9-194">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="870f9-195">사용자의 이름이 &mdash; 보안에 영향을 주지 않는 결정을 업데이트 하는 상황을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-195">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="870f9-196">사용자 보안 주체를 destructively 업데이트 하려면를 호출 하 `context.ReplacePrincipal` 고 `context.ShouldRenew` 속성을로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-196">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="870f9-197">여기에 설명 된 방법은 모든 요청에서 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-197">The approach described here is triggered on every request.</span></span> <span data-ttu-id="870f9-198">모든 cookie 요청에서 모든 사용자에 대 한 인증의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-198">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="870f9-199">영구 cookie s</span><span class="sxs-lookup"><span data-stu-id="870f9-199">Persistent cookies</span></span>

<span data-ttu-id="870f9-200">가 cookie 브라우저 세션에서 지속 되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-200">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="870f9-201">이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-201">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="870f9-202">다음 코드 조각에서는 id를 만들고 cookie 브라우저 클로저를 통해 해당 하는 해당 하는를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-202">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="870f9-203">이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-203">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="870f9-204">브라우저를 cookie 닫을 때가 만료 되 면 브라우저가 cookie 다시 시작 되 면를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-204">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="870f9-205"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>에서로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="870f9-205">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="870f9-206">절대 cookie 만료</span><span class="sxs-lookup"><span data-stu-id="870f9-206">Absolute cookie expiration</span></span>

<span data-ttu-id="870f9-207">절대 만료 시간은를 사용 하 여 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="870f9-207">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="870f9-208">영구을 만들려면 cookie `IsPersistent` 도 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-208">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="870f9-209">그렇지 않은 경우는 cookie 세션 기반 수명으로 생성 되며 보유 한 인증 티켓 전이나 후에 만료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-209">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="870f9-210">`ExpiresUtc`가 설정 되 면 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> 설정 된 경우의 옵션 값을 재정의 합니다 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="870f9-210">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="870f9-211">다음 코드 조각에서는 id를 만들고 해당 id를 cookie 20 분 동안 지속 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-211">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="870f9-212">이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-212">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="870f9-213">ASP.NET Core Identity 는 로그인을 만들고 유지 관리 하기 위한 완전 한 기능을 갖춘 완전 한 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-213">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="870f9-214">그러나를 cookie 사용 하지 않는 기반 인증 공급자를 ASP.NET Core Identity 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-214">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="870f9-215">자세한 내용은 <xref:security/authentication/identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="870f9-215">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="870f9-216">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="870f9-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="870f9-217">샘플 앱의 데모용으로, 가상 사용자 (민 Rodriguez)의 사용자 계정이 앱에 하드 코딩 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-217">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="870f9-218">**전자 메일** 주소 `maria.rodriguez@contoso.com` 및 암호를 사용 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-218">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="870f9-219">사용자는 `AuthenticateUser` *Pages/Account/Login. cshtml* 파일의 메서드에서 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-219">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="870f9-220">실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-220">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="870f9-221">구성</span><span class="sxs-lookup"><span data-stu-id="870f9-221">Configuration</span></span>

<span data-ttu-id="870f9-222">앱이 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 사용 하지 않는 경우 프로젝트 파일에서 [AspNetCore Cookie 에 대 한 패키지 참조를 만듭니다. s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) 패키지.</span><span class="sxs-lookup"><span data-stu-id="870f9-222">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="870f9-223">`Startup.ConfigureServices`메서드에서 및 메서드를 사용 하 여 인증 미들웨어 서비스를 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> 만듭니다 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> .</span><span class="sxs-lookup"><span data-stu-id="870f9-223">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="870f9-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 에 전달 된는 `AddAuthentication` 앱에 대 한 기본 인증 체계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="870f9-225">`AuthenticationScheme` 는 인증 인스턴스가 여러 개 있고 cookie [특정 스키마를 사용 하 여 권한을 부여](xref:security/authorization/limitingidentitybyscheme)하려는 경우에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-225">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="870f9-226">`AuthenticationScheme`를 [ Cookie authenticationdefaults로 설정 합니다. authenticationdefaults](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) 은 Cookie 스키마에 대해 "s" 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-226">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="870f9-227">체계를 구별 하는 모든 문자열 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-227">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="870f9-228">앱의 인증 체계가 앱의 인증 체계와 다릅니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-228">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="870f9-229">cookie인증 체계가에 제공 되지 않는 경우 <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s")를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-229">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="870f9-230">인증 cookie 의 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 속성은 기본적으로로 설정 됩니다 `true` .</span><span class="sxs-lookup"><span data-stu-id="870f9-230">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="870f9-231">cookie사이트 방문자가 데이터 수집에 동의한 하지 않은 경우에는 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-231">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="870f9-232">자세한 내용은 <xref:security/gdpr#essential-cookies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="870f9-232">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="870f9-233">`Startup.Configure`메서드에서 메서드를 호출 하 여 `UseAuthentication` 속성을 설정 하는 인증 미들웨어를 호출 합니다 `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="870f9-233">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="870f9-234">또는를 `UseAuthentication` 호출 하기 전에 메서드를 호출 합니다 `UseMvcWithDefaultRoute` `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="870f9-234">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="870f9-235"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>클래스는 인증 공급자 옵션을 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-235">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="870f9-236">`CookieAuthenticationOptions`메서드의 인증에 대 한 서비스 구성에서 설정 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="870f9-236">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="870f9-237">Cookie 정책 미들웨어</span><span class="sxs-lookup"><span data-stu-id="870f9-237">Cookie Policy Middleware</span></span>

<span data-ttu-id="870f9-238">[ Cookie 정책 미들웨어](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) cookie 를 통해 정책 기능을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-238">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="870f9-239">응용 프로그램 처리 파이프라인에 미들웨어를 추가 하는 것은 순서를 구분 하기 때문에 &mdash; 파이프라인에 등록 된 다운스트림 구성 요소에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-239">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="870f9-240"><xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>정책 미들웨어에 제공 된를 사용 Cookie 하 여 cookie cookie cookie 가 추가 되거나 삭제 될 때 처리 및 처리 처리기에 대 한 전역 특성을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-240">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="870f9-241">기본값은 <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> `SameSiteMode.Lax` OAuth2 인증을 허용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-241">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="870f9-242">의 동일한 사이트 정책을 엄격 하 게 적용 하려면를 `SameSiteMode.Strict` 설정 `MinimumSameSitePolicy` 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-242">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="870f9-243">이 설정은 OAuth2 및 다른 크로스-원본 인증 스키마를 중단 하지만 cookie 원본 간 요청 처리에 의존 하지 않는 다른 유형의 앱에 대 한 보안 수준을 강화 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-243">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="870f9-244">Cookie의 정책 미들웨어 설정은 `MinimumSameSitePolicy` `Cookie.SameSite` `CookieAuthenticationOptions` 아래 행렬에 따라 설정의 설정에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-244">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="870f9-245">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="870f9-245">MinimumSameSitePolicy</span></span> | <span data-ttu-id="870f9-246">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="870f9-246">Cookie.SameSite</span></span> | <span data-ttu-id="870f9-247">결과 Cookie . SameSite 설정</span><span class="sxs-lookup"><span data-stu-id="870f9-247">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="870f9-248">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-248">SameSiteMode.None</span></span>     | <span data-ttu-id="870f9-249">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-249">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-250">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-251">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-251">SameSiteMode.Strict</span></span> | <span data-ttu-id="870f9-252">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-252">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-253">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-254">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-254">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="870f9-255">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-255">SameSiteMode.Lax</span></span>      | <span data-ttu-id="870f9-256">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-256">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-257">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-258">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-258">SameSiteMode.Strict</span></span> | <span data-ttu-id="870f9-259">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-259">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-260">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-261">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-261">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="870f9-262">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-262">SameSiteMode.Strict</span></span>   | <span data-ttu-id="870f9-263">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="870f9-263">SameSiteMode.None</span></span><br><span data-ttu-id="870f9-264">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="870f9-264">SameSiteMode.Lax</span></span><br><span data-ttu-id="870f9-265">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-265">SameSiteMode.Strict</span></span> | <span data-ttu-id="870f9-266">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-266">SameSiteMode.Strict</span></span><br><span data-ttu-id="870f9-267">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-267">SameSiteMode.Strict</span></span><br><span data-ttu-id="870f9-268">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="870f9-268">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="870f9-269">인증 만들기 cookie</span><span class="sxs-lookup"><span data-stu-id="870f9-269">Create an authentication cookie</span></span>

<span data-ttu-id="870f9-270">cookie보유 사용자 정보를 만들려면를 구성 <xref:System.Security.Claims.ClaimsPrincipal> 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-270">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="870f9-271">사용자 정보는 serialize 되 고에 저장 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-271">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="870f9-272">필요한를 사용 하 여를 만들고를 <xref:System.Security.Claims.ClaimsIdentity> <xref:System.Security.Claims.Claim> 호출 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> 하 여 사용자를 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-272">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="870f9-273">`SignInAsync` 암호화 된를 만들어 cookie 현재 응답에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-273">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="870f9-274">`AuthenticationScheme`을 지정 하지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-274">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="870f9-275">ASP.NET Core의 [데이터 보호](xref:security/data-protection/using-data-protection) 시스템이 암호화에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-275">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="870f9-276">여러 컴퓨터에서 호스트 되는 앱의 경우, 앱 간에 부하를 분산 하거나 웹 팜을 사용 하 여 [데이터 보호를 구성](xref:security/data-protection/configuration/overview) 하 여 동일한 키 링 및 앱 식별자를 사용 하도록 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-276">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="870f9-277">로그아웃</span><span class="sxs-lookup"><span data-stu-id="870f9-277">Sign out</span></span>

<span data-ttu-id="870f9-278">현재 사용자를 로그 아웃 하 고 삭제 하려면 cookie 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> .</span><span class="sxs-lookup"><span data-stu-id="870f9-278">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="870f9-279">`CookieAuthenticationDefaults.AuthenticationScheme`(또는 " Cookie s")가 구성표 (예: "Contoso")로 사용 되지 않는 경우 Cookie 인증 공급자를 구성할 때 사용 되는 체계를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-279">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="870f9-280">그렇지 않으면 기본 체계가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-280">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="870f9-281">백 엔드 변경 내용에 대응</span><span class="sxs-lookup"><span data-stu-id="870f9-281">React to back-end changes</span></span>

<span data-ttu-id="870f9-282">가 cookie 만들어지면는 cookie id의 단일 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-282">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="870f9-283">백 엔드 시스템에서 사용자 계정을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="870f9-283">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="870f9-284">앱의 cookie 인증 시스템은 인증에 따라 요청을 계속 처리 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-284">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="870f9-285">인증이 유효한 경우 사용자는 앱에 로그인 상태를 유지 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-285">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="870f9-286"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>이벤트를 사용 하 여 id의 유효성 검사를 가로채 고 재정의할 수 있습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-286">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="870f9-287">모든 요청에서의 유효성을 검사 하면 cookie 해지 된 사용자가 앱에 액세스 하는 위험을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-287">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="870f9-288">유효성 검사에 대 한 한 가지 방법은 cookie 사용자 데이터베이스가 변경 되는 시기를 추적 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-288">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="870f9-289">사용자가 발급 된 이후에 데이터베이스가 변경 되지 않은 경우에 cookie 도 해당 사용자를 다시 인증할 필요가 없습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="870f9-289">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="870f9-290">샘플 앱에서 데이터베이스는에서 구현 되 `IUserRepository` 고 값을 저장 합니다 `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="870f9-290">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="870f9-291">데이터베이스에서 사용자를 업데이트 하는 경우 값은 `LastChanged` 현재 시간으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-291">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="870f9-292">cookie값에 따라 데이터베이스가 변경 될 때를 무효화 하려면 데이터베이스의 `LastChanged` 현재 값이 포함 된 클레임을 사용 하 여를 만듭니다 cookie `LastChanged` `LastChanged` .</span><span class="sxs-lookup"><span data-stu-id="870f9-292">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="870f9-293">이벤트에 대 한 재정의를 구현 하려면 `ValidatePrincipal` 에서 파생 되는 클래스에 다음 시그니처를 사용 하 여 메서드를 작성 합니다 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> .</span><span class="sxs-lookup"><span data-stu-id="870f9-293">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="870f9-294">다음은의 구현 예제입니다 `CookieAuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="870f9-294">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="870f9-295">메서드에서 서비스를 등록 하는 동안 events 인스턴스를 등록 cookie `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-295">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="870f9-296">클래스에 대해 범위가 지정 된 [서비스 등록](xref:fundamentals/dependency-injection#service-lifetimes) 을 제공 합니다 `CustomCookieAuthenticationEvents` .</span><span class="sxs-lookup"><span data-stu-id="870f9-296">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="870f9-297">사용자의 이름이 &mdash; 보안에 영향을 주지 않는 결정을 업데이트 하는 상황을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-297">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="870f9-298">사용자 보안 주체를 destructively 업데이트 하려면를 호출 하 `context.ReplacePrincipal` 고 `context.ShouldRenew` 속성을로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-298">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="870f9-299">여기에 설명 된 방법은 모든 요청에서 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-299">The approach described here is triggered on every request.</span></span> <span data-ttu-id="870f9-300">모든 cookie 요청에서 모든 사용자에 대 한 인증의 유효성을 검사 하면 앱에 대 한 성능 저하가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-300">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="870f9-301">영구 cookie s</span><span class="sxs-lookup"><span data-stu-id="870f9-301">Persistent cookies</span></span>

<span data-ttu-id="870f9-302">가 cookie 브라우저 세션에서 지속 되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-302">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="870f9-303">이 지 속성은 로그인 또는 유사한 메커니즘에서 "사용자 이름" 확인란을 사용 하 여 명시적인 사용자 동의로만 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-303">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="870f9-304">다음 코드 조각에서는 id를 만들고 cookie 브라우저 클로저를 통해 해당 하는 해당 하는를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-304">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="870f9-305">이전에 구성 된 슬라이딩 만료 설정은 모두 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-305">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="870f9-306">브라우저를 cookie 닫을 때가 만료 되 면 브라우저가 cookie 다시 시작 되 면를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-306">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="870f9-307"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>에서로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="870f9-307">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="870f9-308">절대 cookie 만료</span><span class="sxs-lookup"><span data-stu-id="870f9-308">Absolute cookie expiration</span></span>

<span data-ttu-id="870f9-309">절대 만료 시간은를 사용 하 여 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="870f9-309">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="870f9-310">영구을 만들려면 cookie `IsPersistent` 도 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-310">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="870f9-311">그렇지 않은 경우는 cookie 세션 기반 수명으로 생성 되며 보유 한 인증 티켓 전이나 후에 만료 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-311">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="870f9-312">`ExpiresUtc`가 설정 되 면 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> 설정 된 경우의 옵션 값을 재정의 합니다 <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> .</span><span class="sxs-lookup"><span data-stu-id="870f9-312">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="870f9-313">다음 코드 조각에서는 id를 만들고 해당 id를 cookie 20 분 동안 지속 합니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-313">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="870f9-314">이렇게 하면 이전에 구성 된 슬라이딩 만료 설정이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="870f9-314">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="870f9-315">추가 자료</span><span class="sxs-lookup"><span data-stu-id="870f9-315">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="870f9-316">정책 기반 역할 검사</span><span class="sxs-lookup"><span data-stu-id="870f9-316">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
