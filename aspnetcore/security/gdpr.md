---
title: ASP.NET Core의 GDPR (일반 데이터 보호 규정) 지원
author: rick-anderson
description: ASP.NET Core 웹 앱에서 GDPR 확장 점에 액세스 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: 35a12cb8d2a9617e51d886e798cff5ee60b0a8ad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634711"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="b4109-103">ASP.NET Core의 GDPR (EU 일반 데이터 보호 규정) 지원</span><span class="sxs-lookup"><span data-stu-id="b4109-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="b4109-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b4109-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b4109-105">ASP.NET Core은 [GDPR (EU 일반 데이터 보호 규정)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) 요구 사항을 충족 하는 데 도움이 되는 api 및 템플릿을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b4109-106">프로젝트 템플릿에는 개인 정보 및 정책 사용으로 대체할 수 있는 확장 지점과 스텁 태그가 포함 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="b4109-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="b4109-107">*페이지/개인 정보* 보호 페이지 또는 *보기/홈/개인 정보. cshtml* 보기는 사이트의 개인 정보 취급 방침에 대 한 세부 정보를 제공 하는 페이지를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="b4109-108">cookieASP.NET Core 3.0 템플릿에서 생성 된 앱의 ASP.NET Core 2.2 템플릿에 있는 것과 같은 기본 동의 기능을 사용 하도록 설정 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="b4109-109">`using Microsoft.AspNetCore.Http`Using 지시문 목록에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="b4109-110">[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 를에 추가 하 `Startup.ConfigureServices` 고 [ Cookie 정책을 사용](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 하 여 `Startup.Configure` 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="b4109-111">cookie *_Layout. cshtml* 파일에 동의 부분을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="b4109-112">\* \_ Cookie ConsentPartial\* 파일을 프로젝트에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="b4109-113">승인 기능에 대해 알아보려면이 문서의 ASP.NET Core 2.2 버전을 선택 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="b4109-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="b4109-114">프로젝트 템플릿에는 개인 정보 및 정책 사용으로 대체할 수 있는 확장 지점과 스텁 태그가 포함 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="b4109-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="b4109-115">cookie승인 기능을 사용 하면 개인 정보를 저장 하기 위해 사용자의 동의를 요청 하 고 추적할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="b4109-116">사용자가 데이터 수집에 동의한 하지 않고 응용 프로그램의 [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) 가로 설정 된 경우에는 `true` 필수가 아닌가 cookie 브라우저에 전송 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="b4109-117">Cookie는 필수로 표시 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="b4109-118">cookie사용자가 동의한 하지 않고 추적을 사용할 수 없는 경우에도 필수가 브라우저에 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="b4109-119">[TempData 및 Session cookie ](#tempdata) 은 추적을 사용 하지 않도록 설정 된 경우 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="b4109-120">[ Identity 관리](#pd) 페이지에서는 사용자 데이터를 다운로드 하 고 삭제할 수 있는 링크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="b4109-121">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 을 사용 하면 ASP.NET Core 2.1 템플릿에 추가 된 GDPR 확장 지점과 api를 대부분 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="b4109-122">테스트 지침은 [추가 정보](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 파일을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b4109-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="b4109-123">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4109-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="b4109-124">템플릿 생성 코드에서 GDPR 지원 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4109-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="b4109-125">Razor 프로젝트 템플릿을 사용 하 여 만든 페이지 및 MVC 프로젝트에는 다음과 같은 GDPR 지원이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="b4109-126">[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 및 [Use Cookie Policy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 는 클래스에 설정 되어 있습니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="b4109-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="b4109-127">\* \_ Cookie ConsentPartial\* [부분 뷰입니다](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b4109-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="b4109-128">**수락** 단추가이 파일에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="b4109-129">사용자가 **동의** 단추를 클릭 하면 저장소에 대 한 동의가 cookie 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="b4109-130">*페이지/개인 정보* 보호 페이지 또는 *보기/홈/개인 정보. cshtml* 보기는 사이트의 개인 정보 취급 방침에 대 한 세부 정보를 제공 하는 페이지를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="b4109-131">\* \_ Cookie ConsentPartial\* 파일은 개인 정보 페이지에 대 한 링크를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="b4109-132">개별 사용자 계정을 사용 하 여 만든 앱의 경우 관리 페이지에 [개인 사용자 데이터](#pd)를 다운로드 하 고 삭제할 수 있는 링크가 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="b4109-133">CookiePolicyOptions 및 사용 Cookie 정책</span><span class="sxs-lookup"><span data-stu-id="b4109-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="b4109-134">[ Cookie Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) 는에서 초기화 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b4109-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="b4109-135">[사용 Cookie 정책은](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) 에서 호출 됩니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b4109-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="b4109-136">\_CookieConsentPartial 부분 보기</span><span class="sxs-lookup"><span data-stu-id="b4109-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="b4109-137">\* \_ Cookie ConsentPartial\* 부분 보기:</span><span class="sxs-lookup"><span data-stu-id="b4109-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="b4109-138">이 부분:</span><span class="sxs-lookup"><span data-stu-id="b4109-138">This partial:</span></span>

* <span data-ttu-id="b4109-139">사용자에 대 한 추적 상태를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="b4109-140">앱이 동의를 요구 하도록 구성 된 경우 사용자는를 추적할 수 있도록 먼저 동의 해야 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="b4109-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="b4109-141">동의가 필요한 경우에는 cookie \* \_ Layout\* 파일에 의해 생성 된 탐색 모음 위에서 승인 패널을 고정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="b4109-142">`<p>`개인 정보를 요약 하 고 정책을 사용 하는 HTML 요소를 제공 cookie 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="b4109-143">사이트의 개인 정보 취급 방침에 대 한 세부 정보를 볼 수 있는 개인 정보 페이지 또는 보기에 대 한 링크를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="b4109-144">필수 cookie s</span><span class="sxs-lookup"><span data-stu-id="b4109-144">Essential cookies</span></span>

<span data-ttu-id="b4109-145">저장소에 대 한 동의가 cookie 제공 되지 cookie 않은 경우 필수로 표시 된만 브라우저로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="b4109-146">다음 코드는 필수를 만듭니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="b4109-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="b4109-147">TempData 공급자 및 세션 상태는 cookie 필수가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="b4109-148">[TempData 공급자](xref:fundamentals/app-state#tempdata) 는 필수적이 지 cookie 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="b4109-149">추적을 사용 하지 않도록 설정 하면 TempData 공급자가 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="b4109-150">추적이 사용 하지 않도록 설정 된 경우 TempData 공급자를 사용 하도록 설정 하려면 다음과 같이 TempData를 cookie 필수로 표시 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b4109-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="b4109-151">[세션 상태](xref:fundamentals/app-state) cookie 는 필수적이 지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="b4109-152">추적을 사용 하지 않도록 설정 하면 세션 상태가 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="b4109-153">다음 코드에서는 세션을 필수로 설정 합니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="b4109-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="b4109-154">개인 데이터</span><span class="sxs-lookup"><span data-stu-id="b4109-154">Personal data</span></span>

<span data-ttu-id="b4109-155">개별 사용자 계정을 사용 하 여 만든 ASP.NET Core 앱은 개인 데이터를 다운로드 하 고 삭제 하는 코드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="b4109-156">사용자 이름을 선택 하 고 **개인 데이터**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-156">Select the user name and then select **Personal data**:</span></span>

![개인 데이터 관리 페이지](gdpr/_static/pd.png)

<span data-ttu-id="b4109-158">참고:</span><span class="sxs-lookup"><span data-stu-id="b4109-158">Notes:</span></span>

* <span data-ttu-id="b4109-159">코드를 생성 하려면 `Account/Manage` [스 캐 폴드 Identity ](xref:security/authentication/scaffold-identity)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b4109-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="b4109-160">**삭제** 및 **다운로드** 링크는 기본 id 데이터에 대해서만 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="b4109-161">사용자 지정 사용자 데이터를 삭제/다운로드 하려면 사용자 지정 사용자 데이터를 만드는 앱을 확장 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="b4109-162">자세한 내용은 [사용자 지정 사용자 데이터 Identity 추가, 다운로드 및 삭제 ](xref:security/authentication/add-user-data)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b4109-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="b4109-163">Identity `AspNetUserTokens` [외래 키](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)로 인해 cascade delete 동작을 통해 사용자를 삭제 하면 데이터베이스 테이블에 저장 된 사용자에 대해 저장 된 토큰이 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="b4109-164">Facebook, Google 등의 [외부 공급자 인증은](xref:security/authentication/social/index)정책을 수락 하기 전에 사용할 수 없습니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="b4109-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="b4109-165">휴지 상태의 암호화</span><span class="sxs-lookup"><span data-stu-id="b4109-165">Encryption at rest</span></span>

<span data-ttu-id="b4109-166">일부 데이터베이스 및 저장소 메커니즘은 미사용 암호화를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="b4109-167">저장 데이터 암호화:</span><span class="sxs-lookup"><span data-stu-id="b4109-167">Encryption at rest:</span></span>

* <span data-ttu-id="b4109-168">저장 된 데이터를 자동으로 암호화 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="b4109-169">데이터에 액세스 하는 소프트웨어에 대해 구성, 프로그래밍 또는 기타 작업을 수행 하지 않고 암호화 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="b4109-170">는 가장 쉽고 안전한 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="b4109-171">데이터베이스에서 키와 암호화를 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="b4109-172">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="b4109-172">For example:</span></span>

* <span data-ttu-id="b4109-173">Microsoft SQL 및 Azure SQL은 tde ( [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption) )를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="b4109-174">SQL Azure는 기본적으로 데이터베이스를 암호화 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="b4109-175">[Azure blob, 파일, 테이블 및 Queue Storage는 기본적으로 암호화 됩니다](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="b4109-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="b4109-176">미사용 기본 암호화를 제공 하지 않는 데이터베이스의 경우 디스크 암호화를 사용 하 여 동일한 보호를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4109-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="b4109-177">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="b4109-177">For example:</span></span>

* [<span data-ttu-id="b4109-178">Windows Server 용 BitLocker</span><span class="sxs-lookup"><span data-stu-id="b4109-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="b4109-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="b4109-179">Linux:</span></span>
  * [<span data-ttu-id="b4109-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="b4109-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="b4109-181">[Encfs](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="b4109-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4109-182">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b4109-182">Additional resources</span></span>

* [<span data-ttu-id="b4109-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="b4109-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="b4109-184">GDPR-ASP.NET Core에서 해지 동의 단추를 추가 하는 중</span><span class="sxs-lookup"><span data-stu-id="b4109-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
