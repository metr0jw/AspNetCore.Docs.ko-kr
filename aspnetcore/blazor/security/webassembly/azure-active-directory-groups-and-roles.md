---
title: Azure Active Directory 그룹 및 역할을 사용한 ASP.NET Core Blazor WebAssembly
author: guardrex
description: Blazor WebAssembly에서 Azure Active Directory 그룹 및 역할을 사용하도록 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 81114768a3600544dda46efbc886e2f56932aba7
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592931"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure AD 그룹, 관리 역할 및 사용자 정의 역할

작성자: [Luke Latham](https://github.com/guardrex) 및 [Javier Calvarro Nelson](https://github.com/javiercn)

AAD(Azure Active Directory)는 ASP.NET Core Identity와 결합할 수 있는 몇 가지 권한 부여 방법을 제공합니다.

* 사용자 정의 그룹
  * 보안
  * Microsoft 365
  * 분포
* 역할
  * 기본 제공 관리 역할
  * 사용자 정의 역할

이 문서의 지침은 다음 항목에서 설명하는 Blazor WebAssembly AAD 배포 시나리오에 적용됩니다.

* [Microsoft 계정을 사용하는 독립 실행형](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD를 사용하는 독립 실행형](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD를 사용하는 호스트형](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Microsoft Graph API 권한

5개 이상의 기본 제공 AAD 관리자 역할 및 보안 그룹 구성원 자격이 있는 앱 사용자에게는 [Microsoft Graph API](/graph/use-the-api) 호출이 필요합니다.

Graph API 호출을 허용하려면 Azure Portal에서 호스트된 Blazor 솔루션의 독립 실행형 또는 클라이언트 앱에 다음 [Graph API 권한](/graph/permissions-reference)을 제공합니다.

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All`은 최소 권한이며 이 문서에서 설명하는 예제에 사용되는 권한입니다.

## <a name="user-defined-groups-and-built-in-administrative-roles"></a>사용자 정의 그룹 및 기본 제공 관리 역할

Azure Portal에서 `groups` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 다음 Azure 문서를 참조하세요. 사용자를 사용자 정의 AAD 그룹 및 기본 제공 관리 역할에 할당합니다.

* [Azure AD 보안 그룹을 사용하는 역할](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` 특성](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

다음 예제에서는 사용자가 AAD 기본 제공 ‘대금 청구 관리자’ 역할에 할당되었다고 가정합니다.

AAD에서 보낸 단일 `groups` 클레임은 사용자의 그룹 및 역할을 JSON 배열의 개체 ID(GUID)로 표시합니다. 앱은 그룹 및 역할의 JSON 배열을 앱이 기준으로 삼아 [정책](xref:security/authorization/policies)을 빌드할 수 있는 개별 `group` 클레임으로 변환해야 합니다.

할당된 기본 제공 Azure 관리 역할 및 사용자 정의 그룹 수가 5개를 초과하면 AAD는 `groups` 클레임을 전송하는 대신 `true` 값으로 `hasgroups` 클레임을 전송합니다. 사용자에게 할당된 역할과 그룹이 5개 이상일 수 있는 모든 앱은 사용자의 역할 및 그룹을 가져오기 위해 별도의 Graph API 호출을 수행해야 합니다. 이 문서에서 제공하는 예제 구현은 이 시나리오를 다룹니다. `groups` 및 `hasgroups` 클레임 정보([Microsoft ID 플랫폼 액세스 토큰: 페이로드 클레임](/azure/active-directory/develop/access-tokens#payload-claims) 문서)를 참조하세요.

그룹 및 역할의 배열 속성을 포함하도록 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>를 확장합니다. 나중에 `foreach` 루프에서 속성을 사용할 때 `null` 확인이 필요하지 않도록 각 속성에 빈 배열을 할당합니다.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱에서 사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 만듭니다. 역할 및 그룹 정보를 가져오는 Graph API 호출에 올바른 범위(권한)를 사용합니다.

`GraphAPIAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

`Program.Main`(`Program.cs`)에서 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 구현 서비스를 추가하고 Graph API 요청을 위한 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다. 다음 예제에서는 클라이언트의 이름을 `GraphAPI`로 지정합니다.

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Graph API 호출에서 OData(개방형 데이터 프로토콜) 역할 및 그룹을 수신하는 AAD 디렉터리 개체 클래스를 만듭니다. OData는 JSON 형식으로 도착하고 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 호출은 `DirectoryObjects` 클래스의 인스턴스를 채웁니다.

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

역할 및 그룹 클레임을 처리하는 사용자 지정 사용자 팩터리를 만듭니다. 다음 예제 구현에서는 [사용자 정의 역할](#user-defined-roles) 섹션에서 설명하는 `roles` 클레임 배열도 처리합니다. `hasgroups` 클레임이 있는 경우 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 사용자의 역할 및 그룹을 가져올 수 있도록 Graph API에 권한 있는 요청을 합니다. 이 구현에서는 Microsoft Identity Platform v1.0 엔드포인트`https://graph.microsoft.com/v1.0/me/memberOf`([API 설명서](/graph/api/user-list-memberof))를 사용합니다. 이 토픽의 지침은 MSAL 패키지가 v2.0으로 업그레이드될 때 Identity v2.0에 맞게 업데이트됩니다.

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

원래 `groups` 클레임이 있다면 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.

> [!NOTE]
> 이 예제의 방법은 다음과 같습니다.
>
> * 사용자 지정 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 클래스를 추가하여 나가는 요청에 액세스 토큰을 연결합니다.
> * 안전한 외부 웹 API 엔드포인트에 API 요청을 하기 위해 명명된 <xref:System.Net.Http.HttpClient>를 추가합니다.
> * 명명된 <xref:System.Net.Http.HttpClient>를 사용하여 권한 있는 요청을 합니다.
>
> 이 방법에 대한 일반적인 설명은 <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> 문서에 있습니다.

호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱의 `Program.Main`(`Program.cs`)에 팩터리를 등록합니다. 앱의 추가 범위로 `Directory.Read.All` 권한 범위에 동의합니다.

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

`Program.Main`의 그룹 또는 역할에 대해 [정책](xref:security/authorization/policies)을 만듭니다. 다음 예제에서는 AAD 기본 제공 ‘대금 청구 관리자’ 역할에 대한 정책을 만듭니다.

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

AAD 역할 개체 ID의 전체 목록은 [AAD 관리 역할 그룹 ID](#aad-adminstrative-role-group-ids) 섹션을 참조하세요.

다음 예제에서 앱은 앞에 나온 정책을 사용하여 사용자에게 권한을 부여합니다.

[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)는 이 정책을 준수합니다.

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

전체 구성 요소에 대한 액세스는 [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)을 사용하는 정책을 기반으로 할 수 있습니다.

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

사용자가 로그인되어 있지 않은 경우 AAD 로그인 페이지로 리디렉션되고 로그인한 후에 구성 요소로 다시 리디렉션됩니다.

[절차적 논리](xref:blazor/security/index#procedural-logic)를 사용하여 코드에서 정책 검사를 수행할 수도 있습니다.

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="user-defined-roles"></a>사용자 정의 역할

AAD에 등록된 앱은 사용자 정의 역할을 사용하도록 구성될 수도 있습니다.

Azure Portal에서 `roles` 멤버 자격 클레임을 제공하도록 앱을 구성하려면 Azure 설명서에서 [방법: 애플리케이션에서 앱 역할을 추가하고 토큰에서 수신하기](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)를 참조하세요.

다음 예제에서는 앱이 두 개의 역할로 구성되어 있다고 가정합니다.

* `admin`
* `developer`

> [!NOTE]
> Azure AD Premium 계정을 사용하지 않고서는 보안 그룹에 역할을 할당할 수 없지만, 표준 Azure 계정을 사용하여 사용자를 역할에 할당하고 사용자에 대한 `roles` 클레임을 받을 수 있습니다. 이 섹션의 지침에는 Azure AD Premium 계정이 필요하지 않습니다.
>
> Azure Portal에서 각 추가 역할 할당에 대해 **‘사용자를 다시 추가’** 함으로써 여러 역할이 할당됩니다.

AAD에서 보낸 단일 `roles` 클레임은 사용자 정의 역할을 JSON 배열의 `appRoles`의 `value`로 표시합니다. 앱은 역할의 JSON 배열을 개별 `role` 클레임으로 변환해야 합니다.

[사용자 정의 그룹 및 AAD 기본 제공 관리 역할](#user-defined-groups-and-built-in-administrative-roles) 섹션에 표시된 `CustomUserFactory`는 JSON 배열 값을 사용하여 `roles` 클레임에 대해 작동하도록 설정되었습니다. [사용자 정의 그룹 및 AAD 기본 제공 관리 역할](#user-defined-groups-and-built-in-administrative-roles) 섹션에 나와 있는 것처럼 호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱에 `CustomUserFactory`를 추가하고 등록합니다. 원래 `roles` 클레임은 프레임워크에 의해 자동으로 제거되므로 이를 제거하는 코드를 제공할 필요는 없습니다.

호스트된 Blazor 솔루션의 독립 실행형 앱 또는 클라이언트 앱의 `Program.Main`에서 "`role`"이라는 클레임을 역할 클레임으로 지정합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

이 시점에서는 구성 요소 권한 부여 방식이 작동합니다. 구성 요소의 모든 권한 부여 메커니즘에서 `admin` 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.

* [`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)(예: `<AuthorizeView Roles="admin">`)
* [`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)(예: `@attribute [Authorize(Roles = "admin")]`)
* [절차적 논리](xref:blazor/security/index#procedural-logic)(예: `if (user.IsInRole("admin")) { ... }`)

  여러 역할 테스트가 지원됩니다.

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>AAD 관리 역할 그룹 ID

다음 표에 나와 있는 개체 ID는 `group` 클레임에 대한 [정책](xref:security/authorization/policies)을 만드는 데 사용됩니다. 정책은 앱이 앱의 여러 활동에 대해 사용자에게 권한을 부여하도록 허용합니다. 자세한 내용은 [사용자 정의 그룹 및 AAD 기본 제공 관리 역할](#user-defined-groups-and-built-in-administrative-roles) 섹션을 참조하세요.

AAD 관리 역할 | 개체 ID입니다.
--- | ---
애플리케이션 관리자 | fa11557b-4f15-4ddd-85d5-313c7cd74047
애플리케이션 개발자 | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
인증 관리자 | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps 관리자 | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection 관리자 | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C IEF 키 세트 관리자 | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C IEF 정책 관리자 | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C 사용자 흐름 관리자 | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C 사용자 흐름 특성 관리자 | dd0baca0-a535-48c1-b871-8431abe16452
대금 청구 관리자 | 69ff516a-b57d-4697-a429-9de4af7b5609
클라우드 애플리케이션 관리자 | 250b5fe3-b553-458d-9a53-b782c13c34bf
클라우드 디바이스 관리자 | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
규정 준수 관리자 | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
규정 준수 데이터 관리자 | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
조건부 액세스 관리자 | 8f71a611-137d-49af-87ad-e97f1fd5da76
고객 LockBox 액세스 승인자 | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Desktop Analytics 관리자 | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
디렉터리 읽기 권한자 | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365 관리자 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange 관리자 | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
외부 Identity 공급자 관리자 | febfaeb4-e478-407a-b4b3-f4d9716618a2
전역 관리자 | a45ba61b-44db-462c-924b-3b2719152588
글로벌 읽기 권한자 | f6903b21-6aba-4124-b44c-76671796b9d5
그룹 관리자 | 158b3e5a-d89d-460b-92b5-3b34985f0197
게스트 초대자 | 4c730a1d-cc22-44af-8f9f-4eec635c7502
기술 지원팀 관리자 | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune 관리자 | 79950741-23fa-4189-b2cb-46640601c497
Kaizala 관리자 | d6322af2-48e7-42e0-8c68-0bbe31af3412
라이선스 관리자 | 3355458a-e423-44bf-8b98-4ac5e572cea5
메시지 센터 개인 정보 읽기 권한자 | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
메시지 센터 읽기 권한자 | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office 앱 관리자 | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
암호 관리자 | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI 관리자 | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform 관리자 | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
권한 있는 인증 관리자 | 0829f731-b46d-419f-9742-aeb122367d11
권한 있는 역할 관리자 | f20a725a-d1c8-4107-83ea-1171c97d00c7
보고서 읽기 권한자 | 54635450-e8ed-4f2d-9632-07db2517b4de
Search 관리자 | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Search 편집자 | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
보안 관리자 | 20fa50e3-6531-44d8-bd39-b251420568ad
보안 운영자 | 43aae017-8e51-4188-91ab-e6debd572800
보안 판독기 | 45035cd3-fd97-4250-8197-3a53d3562d9b
서비스 지원 관리자 | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint 관리자 | e1c32229-875e-461d-ae24-3cb99116e86c
비즈니스용 Skype 관리자 | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Teams 통신 관리자 | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Teams 통신 지원 엔지니어 | 802dd94e-d717-46f6-af98-b9167071e9fc
Teams 커뮤니케이션 전문가 | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams 서비스 관리자 | 8846a0be-197b-443a-b13c-11192691fa24
사용자 관리자 | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
