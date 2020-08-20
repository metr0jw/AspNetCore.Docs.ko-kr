---
title: ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지
author: rick-anderson
description: 외부 공급자에서 추가 클레임 및 토큰을 설정 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: eeddc75e7bcf368b476f62900c14575c9937e1f7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631526"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="ca1a8-103">ASP.NET Core의 외부 공급자에서 추가 클레임 및 토큰 유지</span><span class="sxs-lookup"><span data-stu-id="ca1a8-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca1a8-104">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="ca1a8-105">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="ca1a8-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca1a8-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ca1a8-107">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="ca1a8-107">Prerequisites</span></span>

<span data-ttu-id="ca1a8-108">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="ca1a8-109">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="ca1a8-110">자세한 내용은 <xref:security/authentication/social/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="ca1a8-111">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="ca1a8-112">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-112">Set the client ID and client secret</span></span>

<span data-ttu-id="ca1a8-113">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="ca1a8-114">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="ca1a8-115">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="ca1a8-116">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="ca1a8-117">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ca1a8-118">Google 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ca1a8-119">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="ca1a8-120">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ca1a8-121">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="ca1a8-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="ca1a8-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="ca1a8-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="ca1a8-123">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="ca1a8-124">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="ca1a8-124">Establish the authentication scope</span></span>

<span data-ttu-id="ca1a8-125">을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="ca1a8-126">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="ca1a8-127">공급자</span><span class="sxs-lookup"><span data-stu-id="ca1a8-127">Provider</span></span>  | <span data-ttu-id="ca1a8-128">범위</span><span class="sxs-lookup"><span data-stu-id="ca1a8-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="ca1a8-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="ca1a8-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="ca1a8-130">Google</span><span class="sxs-lookup"><span data-stu-id="ca1a8-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="ca1a8-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="ca1a8-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="ca1a8-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="ca1a8-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="ca1a8-133">샘플 앱에서 Google의 범위는 `userinfo.profile` 에서가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="ca1a8-134">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="ca1a8-135">다음 예제에서는 `https://www.googleapis.com/auth/user.birthday.read` 사용자의 생일을 검색 하기 위해 Google scope를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="ca1a8-136">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="ca1a8-136">Map user data keys and create claims</span></span>

<span data-ttu-id="ca1a8-137">공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="ca1a8-138">클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="ca1a8-139">샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="ca1a8-140">에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="ca1a8-141">로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="ca1a8-142">샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName></span><span class="sxs-lookup"><span data-stu-id="ca1a8-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="ca1a8-143">기본적으로 사용자의 클레임은 인증에 저장 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="ca1a8-144">인증이 cookie 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="ca1a8-145">브라우저가 cookie 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="ca1a8-146">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="ca1a8-147">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="ca1a8-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="ca1a8-148">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="ca1a8-149">인증 미들웨어의 사용자 지정를 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 하 여 요청에 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="ca1a8-150">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="ca1a8-151">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="ca1a8-151">Save the access token</span></span>

<span data-ttu-id="ca1a8-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="ca1a8-153">`SaveTokens` 는 `false` 최종 인증의 크기를 줄이기 위해 기본적으로로 설정 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="ca1a8-154">샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="ca1a8-155">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="ca1a8-156">샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d*s o s t o s t o s o s.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="ca1a8-157">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="ca1a8-157">How to add additional custom tokens</span></span>

<span data-ttu-id="ca1a8-158">의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="ca1a8-159">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="ca1a8-159">Creating and adding claims</span></span>

<span data-ttu-id="ca1a8-160">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="ca1a8-161">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="ca1a8-162">사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="ca1a8-163">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="ca1a8-164">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="ca1a8-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="ca1a8-165">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="ca1a8-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="ca1a8-166">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="ca1a8-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="ca1a8-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="ca1a8-168">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="ca1a8-168">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca1a8-169">ASP.NET Core 앱은 Facebook, Google, Microsoft, Twitter 등의 외부 인증 공급자에서 추가 클레임 및 토큰을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="ca1a8-170">각 공급자는 해당 플랫폼의 사용자에 대 한 다양 한 정보를 표시 하지만 사용자 데이터를 추가 클레임으로 수신 및 변환 하는 패턴은 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="ca1a8-171">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca1a8-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ca1a8-172">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="ca1a8-172">Prerequisites</span></span>

<span data-ttu-id="ca1a8-173">앱에서 지원할 외부 인증 공급자를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="ca1a8-174">각 공급자에 대해 앱을 등록 하 고 클라이언트 ID 및 클라이언트 암호를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="ca1a8-175">자세한 내용은 <xref:security/authentication/social/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="ca1a8-176">샘플 앱은 [Google 인증 공급자](xref:security/authentication/google-logins)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="ca1a8-177">클라이언트 ID 및 클라이언트 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-177">Set the client ID and client secret</span></span>

<span data-ttu-id="ca1a8-178">OAuth 인증 공급자는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 앱과의 트러스트 관계를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="ca1a8-179">앱이 공급자에 등록 되 면 외부 인증 공급자가 앱에 대 한 클라이언트 ID 및 클라이언트 암호 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="ca1a8-180">앱에서 사용 하는 각 외부 공급자는 공급자의 클라이언트 ID 및 클라이언트 암호와 독립적으로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="ca1a8-181">자세한 내용은 시나리오에 적용 되는 외부 인증 공급자 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="ca1a8-182">Facebook 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ca1a8-183">Google 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ca1a8-184">Microsoft 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="ca1a8-185">Twitter 인증</span><span class="sxs-lookup"><span data-stu-id="ca1a8-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ca1a8-186">기타 인증 공급자</span><span class="sxs-lookup"><span data-stu-id="ca1a8-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="ca1a8-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="ca1a8-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="ca1a8-188">샘플 앱은 Google에서 제공 하는 클라이언트 ID 및 클라이언트 암호를 사용 하 여 Google 인증 공급자를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="ca1a8-189">인증 범위 설정</span><span class="sxs-lookup"><span data-stu-id="ca1a8-189">Establish the authentication scope</span></span>

<span data-ttu-id="ca1a8-190">을 지정 하 여 공급자에서 검색할 사용 권한 목록을 지정 <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="ca1a8-191">일반적인 외부 공급자에 대 한 인증 범위는 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="ca1a8-192">공급자</span><span class="sxs-lookup"><span data-stu-id="ca1a8-192">Provider</span></span>  | <span data-ttu-id="ca1a8-193">범위</span><span class="sxs-lookup"><span data-stu-id="ca1a8-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="ca1a8-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="ca1a8-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="ca1a8-195">Google</span><span class="sxs-lookup"><span data-stu-id="ca1a8-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="ca1a8-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="ca1a8-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="ca1a8-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="ca1a8-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="ca1a8-198">샘플 앱에서 Google의 범위는 `userinfo.profile` 에서가 호출 될 때 프레임 워크에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="ca1a8-199">앱에 추가 범위가 필요한 경우 옵션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="ca1a8-200">다음 예제에서는 `https://www.googleapis.com/auth/user.birthday.read` 사용자의 생일을 검색 하기 위해 Google scope를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="ca1a8-201">사용자 데이터 키 매핑 및 클레임 만들기</span><span class="sxs-lookup"><span data-stu-id="ca1a8-201">Map user data keys and create claims</span></span>

<span data-ttu-id="ca1a8-202">공급자의 옵션에서 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> 로그인 시 읽을 앱 id에 대 한 외부 공급자 JSON 사용자 데이터의 각 키/하위 키에 대해 또는를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="ca1a8-203">클레임 유형에 대 한 자세한 내용은을 참조 하십시오 <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="ca1a8-204">샘플 앱은 `urn:google:locale` `urn:google:picture` `locale` `picture` Google 사용자 데이터의 및 키에서 로캘 () 및 그림 () 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="ca1a8-205">에서 `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ()는 `ApplicationUser` 를 사용 하 여 앱에 로그인 됩니다 <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="ca1a8-206">로그인 프로세스 중에는 <xref:Microsoft.AspNetCore.Identity.UserManager%601> `ApplicationUser` 에서 사용할 수 있는 사용자 데이터에 대 한 클레임을 저장할 수 있습니다 <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="ca1a8-207">샘플 앱에서 `OnPostConfirmationAsync` (*Account/externallogin. cshtml*)는 `urn:google:locale` 에 대 한 클레임을 포함 하 여 로그인에 대 한 로캘 () 및 그림 ( `urn:google:picture` ) 클레임을 `ApplicationUser` 설정 합니다. <xref:System.Security.Claims.ClaimTypes.GivenName></span><span class="sxs-lookup"><span data-stu-id="ca1a8-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="ca1a8-208">기본적으로 사용자의 클레임은 인증에 저장 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="ca1a8-209">인증이 cookie 너무 크면 다음 이유로 인해 앱이 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="ca1a8-210">브라우저가 cookie 헤더가 너무 긴 것을 감지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="ca1a8-211">요청의 전체 크기가 너무 깁니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="ca1a8-212">사용자 요청을 처리 하기 위해 많은 양의 사용자 데이터가 필요한 경우:</span><span class="sxs-lookup"><span data-stu-id="ca1a8-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="ca1a8-213">요청 처리에 대 한 사용자 클레임 수와 크기를 앱에 필요한 것 으로만 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="ca1a8-214">인증 미들웨어의 사용자 지정를 사용 <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> Cookie 하 여 요청에 <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> id를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="ca1a8-215">클라이언트에 작은 세션 식별자 키를 보내는 경우에만 서버에서 많은 양의 id 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="ca1a8-216">액세스 토큰 저장</span><span class="sxs-lookup"><span data-stu-id="ca1a8-216">Save the access token</span></span>

<span data-ttu-id="ca1a8-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> 인증에 성공한 후에 액세스 및 새로 고침 토큰을에 저장 해야 하는지 여부를 정의 합니다 <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="ca1a8-218">`SaveTokens` 는 `false` 최종 인증의 크기를 줄이기 위해 기본적으로로 설정 됩니다 cookie .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="ca1a8-219">샘플 앱은의 값 `SaveTokens` 을의로 설정 합니다 `true` <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="ca1a8-220">`OnPostConfirmationAsync`을 실행하는 경우 `ApplicationUser`의 `AuthenticationProperties` 외부 공급자에 액세스 토큰 ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*))을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="ca1a8-221">샘플 앱은 `OnPostConfirmationAsync` `OnGetCallbackAsync` *계정/d*s o s t o s t o s o s.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="ca1a8-222">추가 사용자 지정 토큰을 추가 하는 방법</span><span class="sxs-lookup"><span data-stu-id="ca1a8-222">How to add additional custom tokens</span></span>

<span data-ttu-id="ca1a8-223">의 일부로 저장 된 사용자 지정 토큰을 추가 하는 방법을 보여 주기 위해 `SaveTokens` 샘플 앱은 <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> AuthenticationToken.Name의 현재와 함께를 추가 합니다 <xref:System.DateTime> [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="ca1a8-224">클레임 만들기 및 추가</span><span class="sxs-lookup"><span data-stu-id="ca1a8-224">Creating and adding claims</span></span>

<span data-ttu-id="ca1a8-225">프레임 워크는 클레임을 만들고 컬렉션에 추가 하기 위한 일반적인 동작 및 확장 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="ca1a8-226">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 및 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="ca1a8-227">사용자는 추상 메서드를 파생 시키고 구현 하 여 사용자 지정 작업을 정의할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="ca1a8-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="ca1a8-228">자세한 내용은 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="ca1a8-229">클레임 작업 및 클레임 제거</span><span class="sxs-lookup"><span data-stu-id="ca1a8-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="ca1a8-230">[Claimactioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) 는 컬렉션에서 지정 된에 대 한 모든 클레임 작업을 제거 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="ca1a8-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="ca1a8-231">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) 은 id에서 지정 된의 클레임을 삭제 합니다. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType></span><span class="sxs-lookup"><span data-stu-id="ca1a8-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="ca1a8-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> 는 기본적으로 [OIDC (Openid connect Connect)](/azure/active-directory/develop/v2-protocols-oidc) 에서 프로토콜 생성 클레임을 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="ca1a8-233">샘플 앱 출력</span><span class="sxs-lookup"><span data-stu-id="ca1a8-233">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ca1a8-234">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ca1a8-234">Additional resources</span></span>

* <span data-ttu-id="ca1a8-235">[dotnet/AspNetCore 공학적 AspNetCore Alsample 앱](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): 연결 된 샘플 앱은 [Dotnet/GitHub 리포지토리의](https://github.com/dotnet/AspNetCore) `master` 엔지니어링 분기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="ca1a8-236">분기에는 `master` ASP.NET Core의 다음 릴리스에 대해 활성 개발 중인 코드가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="ca1a8-237">ASP.NET Core의 릴리스 버전에 대 한 샘플 앱 버전을 보려면 **분기** 드롭다운 목록을 사용 하 여 릴리스 분기 (예:)를 선택 `release/{X.Y}` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1a8-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
