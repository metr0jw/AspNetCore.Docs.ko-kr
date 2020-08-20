---
title: ASP.NET Core Identity 구성
author: AdrienTorris
description: 기본값을 이해 하 ASP.NET Core Identity 고 Identity 사용자 지정 값을 사용 하도록 속성을 구성 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
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
uid: security/authentication/identity-configuration
ms.openlocfilehash: ae4a2eb9d95339651c3810a9f8489d703d73a3fe
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632683"
---
# <a name="configure-no-locaspnet-core-identity"></a><span data-ttu-id="1f527-103">ASP.NET Core Identity 구성</span><span class="sxs-lookup"><span data-stu-id="1f527-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="1f527-104">ASP.NET Core Identity 암호 정책, 잠금, 구성 등의 설정에 대 한 기본값을 사용 cookie 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="1f527-105">이러한 설정은 클래스에서 재정의할 수 있습니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="1f527-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="no-locidentity-options"></a><span data-ttu-id="1f527-106">Identity 옵션</span><span class="sxs-lookup"><span data-stu-id="1f527-106">Identity options</span></span>

<span data-ttu-id="1f527-107">[ Identity Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 클래스는 시스템을 구성 하는 데 사용할 수 있는 옵션을 나타냅니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="1f527-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="1f527-108">`IdentityOptions` 또는를 호출한 **후** 를 설정 해야 합니다 `AddIdentity` `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="1f527-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-no-locidentity"></a><span data-ttu-id="1f527-109">요청이 Identity</span><span class="sxs-lookup"><span data-stu-id="1f527-109">Claims Identity</span></span>

<span data-ttu-id="1f527-110">[ Identity Options. 클레임 Identity ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) 은 다음 표에 표시 된 속성을 사용 하 여 [클레임 Identity 옵션](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) 을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="1f527-111">속성</span><span class="sxs-lookup"><span data-stu-id="1f527-111">Property</span></span> | <span data-ttu-id="1f527-112">Description</span><span class="sxs-lookup"><span data-stu-id="1f527-112">Description</span></span> | <span data-ttu-id="1f527-113">기본값</span><span class="sxs-lookup"><span data-stu-id="1f527-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="1f527-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="1f527-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="1f527-115">역할 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="1f527-116">ClaimTypes. 역할</span><span class="sxs-lookup"><span data-stu-id="1f527-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="1f527-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="1f527-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="1f527-118">보안 스탬프 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="1f527-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="1f527-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="1f527-120">사용자 식별자 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="1f527-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="1f527-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="1f527-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="1f527-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="1f527-123">사용자 이름 클레임에 사용 되는 클레임 형식을 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="1f527-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="1f527-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="1f527-125">잠금</span><span class="sxs-lookup"><span data-stu-id="1f527-125">Lockout</span></span>

<span data-ttu-id="1f527-126">잠금은 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) 메서드에서 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="1f527-127">위의 코드는 템플릿을 기반으로 `Login` Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="1f527-128">잠금 옵션은 `StartUp.ConfigureServices` 다음과 같이 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="1f527-129">위의 코드는 [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) [ Identity 옵션](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 을 기본값으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="1f527-130">성공적인 인증은 실패 한 액세스 시도 횟수를 다시 설정 하 고 시계를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="1f527-131">[ Identity 옵션. 잠금](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) 표에 표시 된 속성을 사용 하 여 [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) 를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="1f527-132">속성</span><span class="sxs-lookup"><span data-stu-id="1f527-132">Property</span></span> | <span data-ttu-id="1f527-133">Description</span><span class="sxs-lookup"><span data-stu-id="1f527-133">Description</span></span> | <span data-ttu-id="1f527-134">기본값</span><span class="sxs-lookup"><span data-stu-id="1f527-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="1f527-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="1f527-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="1f527-136">새 사용자를 잠글 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="1f527-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="1f527-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="1f527-138">잠금이 발생 했을 때 사용자가 잠기는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="1f527-139">5분</span><span class="sxs-lookup"><span data-stu-id="1f527-139">5 minutes</span></span> |
| [<span data-ttu-id="1f527-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="1f527-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="1f527-141">잠금이 설정 된 경우 사용자가 잠길 때까지 실패 한 액세스 시도 횟수입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="1f527-142">5</span><span class="sxs-lookup"><span data-stu-id="1f527-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="1f527-143">암호</span><span class="sxs-lookup"><span data-stu-id="1f527-143">Password</span></span>

<span data-ttu-id="1f527-144">기본적으로에서는 Identity 암호에 대문자, 소문자, 숫자 및 영숫자가 아닌 문자를 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="1f527-145">암호의 길이는 6 자 이상 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-145">Passwords must be at least six characters long.</span></span>

<span data-ttu-id="1f527-146">암호는 다음을 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-146">Passwords are configured with:</span></span>

* <span data-ttu-id="1f527-147">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Identity.PasswordOptions> 사용</span><span class="sxs-lookup"><span data-stu-id="1f527-147"><xref:Microsoft.AspNetCore.Identity.PasswordOptions> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="1f527-148">[ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Identity 가 [앱에 스 캐 폴드](xref:security/authentication/scaffold-identity)속성의 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-148">[`[StringLength]` attributes](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) of `Password` properties if Identity is [scaffolded into the app](xref:security/authentication/scaffold-identity).</span></span> <span data-ttu-id="1f527-149">`InputModel``Password`속성은 다음 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-149">`InputModel` `Password` properties are found in the following files:</span></span>
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

<span data-ttu-id="1f527-150">[ Identity Options. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) 는 표에 표시 된 속성을 사용 하 여 [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) 를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-150">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="1f527-151">속성</span><span class="sxs-lookup"><span data-stu-id="1f527-151">Property</span></span> | <span data-ttu-id="1f527-152">Description</span><span class="sxs-lookup"><span data-stu-id="1f527-152">Description</span></span> | <span data-ttu-id="1f527-153">기본값</span><span class="sxs-lookup"><span data-stu-id="1f527-153">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="1f527-154">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="1f527-154">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="1f527-155">암호에 0-9 사이의 숫자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-155">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="1f527-156">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="1f527-156">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="1f527-157">암호의 최소 길이입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-157">The minimum length of the password.</span></span> | <span data-ttu-id="1f527-158">6</span><span class="sxs-lookup"><span data-stu-id="1f527-158">6</span></span> |
| [<span data-ttu-id="1f527-159">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="1f527-159">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="1f527-160">암호에 소문자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-160">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="1f527-161">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="1f527-161">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="1f527-162">암호에 영숫자가 아닌 문자가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-162">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="1f527-163">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="1f527-163">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="1f527-164">ASP.NET Core 2.0 이상에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-164">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="1f527-165">암호에 고유한 문자 수가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-165">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="1f527-166">1</span><span class="sxs-lookup"><span data-stu-id="1f527-166">1</span></span> |
| [<span data-ttu-id="1f527-167">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="1f527-167">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="1f527-168">암호에 대문자를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-168">Requires an uppercase character in the password.</span></span> | `true` |

### <a name="sign-in"></a><span data-ttu-id="1f527-169">로그인</span><span class="sxs-lookup"><span data-stu-id="1f527-169">Sign-in</span></span>

<span data-ttu-id="1f527-170">다음 코드는 `SignIn` 설정을 기본값으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-170">The following code sets `SignIn` settings (to default values):</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

<span data-ttu-id="1f527-171">[ Identity 옵션. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) 은 테이블에 표시 된 속성을 사용 하 여 [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) 를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-171">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="1f527-172">속성</span><span class="sxs-lookup"><span data-stu-id="1f527-172">Property</span></span> | <span data-ttu-id="1f527-173">Description</span><span class="sxs-lookup"><span data-stu-id="1f527-173">Description</span></span> | <span data-ttu-id="1f527-174">기본값</span><span class="sxs-lookup"><span data-stu-id="1f527-174">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="1f527-175">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="1f527-175">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="1f527-176">로그인 하려면 확인 된 전자 메일이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-176">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="1f527-177">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="1f527-177">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="1f527-178">로그인 하려면 확인 된 전화 번호가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-178">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="1f527-179">토큰</span><span class="sxs-lookup"><span data-stu-id="1f527-179">Tokens</span></span>

<span data-ttu-id="1f527-180">[ Identity Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) 는 테이블에 표시 된 속성을 사용 하 여 [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) 를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-180">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="1f527-181">속성</span><span class="sxs-lookup"><span data-stu-id="1f527-181">Property</span></span> | <span data-ttu-id="1f527-182">Description</span><span class="sxs-lookup"><span data-stu-id="1f527-182">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="1f527-183">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="1f527-183">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | <span data-ttu-id="1f527-184">`AuthenticatorTokenProvider`인증자를 사용 하 여 2 단계 로그인의 유효성을 검사 하는 데 사용 되는를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-184">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span> |
| [<span data-ttu-id="1f527-185">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="1f527-185">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | <span data-ttu-id="1f527-186">`ChangeEmailTokenProvider`전자 메일 변경 확인 전자 메일에 사용 되는 토큰을 생성 하는 데 사용 되는를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-186">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span> |
| [<span data-ttu-id="1f527-187">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="1f527-187">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | <span data-ttu-id="1f527-188">`ChangePhoneNumberTokenProvider`전화 번호를 변경할 때 사용 되는 토큰을 생성 하는 데 사용 되는를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-188">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span> |
| [<span data-ttu-id="1f527-189">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="1f527-189">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | <span data-ttu-id="1f527-190">계정 확인 전자 메일에 사용 되는 토큰을 생성 하는 데 사용 되는 토큰 공급자를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-190">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span> |
| [<span data-ttu-id="1f527-191">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="1f527-191">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | <span data-ttu-id="1f527-192">암호 재설정 전자 메일에 사용 되는 토큰을 생성 하는 데 사용 되는 [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) 를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-192">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
| [<span data-ttu-id="1f527-193">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="1f527-193">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | <span data-ttu-id="1f527-194">공급자 이름으로 사용 되는 키를 사용 하 여 [사용자 토큰 공급자](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) 를 구성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-194">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span> |

### <a name="user"></a><span data-ttu-id="1f527-195">사용자</span><span class="sxs-lookup"><span data-stu-id="1f527-195">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="1f527-196">[ Identity 옵션. 사용자](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) 는 테이블에 표시 된 속성을 사용 하 여 [useroptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) 를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-196">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="1f527-197">속성</span><span class="sxs-lookup"><span data-stu-id="1f527-197">Property</span></span> | <span data-ttu-id="1f527-198">Description</span><span class="sxs-lookup"><span data-stu-id="1f527-198">Description</span></span> | <span data-ttu-id="1f527-199">기본값</span><span class="sxs-lookup"><span data-stu-id="1f527-199">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="1f527-200">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="1f527-200">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="1f527-201">사용자 이름에 허용 되는 문자입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-201">Allowed characters in the username.</span></span> | <span data-ttu-id="1f527-202">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="1f527-202">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="1f527-203">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="1f527-203">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="1f527-204">0123456789</span><span class="sxs-lookup"><span data-stu-id="1f527-204">0123456789</span></span><br><span data-ttu-id="1f527-205">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="1f527-205">-.\_@+</span></span> |
| [<span data-ttu-id="1f527-206">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="1f527-206">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="1f527-207">각 사용자는 고유한 전자 메일을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-207">Requires each user to have a unique email.</span></span> | `false` |

### <a name="no-loccookie-settings"></a><span data-ttu-id="1f527-208">Cookie 설정</span><span class="sxs-lookup"><span data-stu-id="1f527-208">Cookie settings</span></span>

<span data-ttu-id="1f527-209">에서 앱을 구성 cookie `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-209">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1f527-210">[ConfigureApplication Cookie ](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) 또는를 호출한 **후** 를 호출 해야 합니다 `AddIdentity` `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="1f527-210">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

<span data-ttu-id="1f527-211">자세한 내용은 [ Cookie authenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1f527-211">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="1f527-212">암호 Hasher 옵션</span><span class="sxs-lookup"><span data-stu-id="1f527-212">Password Hasher options</span></span>

<span data-ttu-id="1f527-213"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> 암호 해시에 대 한 옵션을 가져오고 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-213"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="1f527-214">옵션</span><span class="sxs-lookup"><span data-stu-id="1f527-214">Option</span></span> | <span data-ttu-id="1f527-215">Description</span><span class="sxs-lookup"><span data-stu-id="1f527-215">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="1f527-216">새 암호를 해시할 때 사용 되는 호환성 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-216">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="1f527-217">기본값은 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-217">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="1f527-218">해시 된 암호의 첫 번째 바이트 ( *형식 마커*)는 암호를 해시 하는 데 사용 되는 해시 알고리즘의 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-218">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="1f527-219">해시에 대해 암호를 확인 하는 경우 <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> 메서드는 첫 번째 바이트를 기준으로 올바른 알고리즘을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-219">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="1f527-220">클라이언트는 암호를 해시 하는 데 사용 된 알고리즘의 버전에 관계 없이 인증할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-220">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="1f527-221">호환성 모드를 설정 하면 *새 암호*의 해시에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-221">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="1f527-222">PBKDF2를 사용 하 여 암호를 해시할 때 사용 되는 반복 횟수입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-222">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="1f527-223">이 값은가로 설정 된 경우에만 사용 됩니다 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="1f527-223">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="1f527-224">값은 양의 정수 여야 하며 기본값은 `10000` 입니다.</span><span class="sxs-lookup"><span data-stu-id="1f527-224">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="1f527-225">다음 예제에서은 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> 에서로 설정 됩니다 `12000` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1f527-225">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="1f527-226">모든 사용자를 전역적으로 인증 해야 함</span><span class="sxs-lookup"><span data-stu-id="1f527-226">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]