---
title: Identity 서버를 사용하여 호스트된 ASP.NET Core Blazor WebAssembly 앱 보호
author: guardrex
description: Visual Studio 내에서 인증을 통해 [IdentityServer](https://identityserver.io/) 백엔드를 사용하는 새 호스트형 Blazor 솔루션을 만들려면
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 36047844c8c2624c6fd0ee085dfad4a7af0367c5
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130290"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="ea0c5-103">Identity 서버를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="ea0c5-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="ea0c5-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ea0c5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ea0c5-105">이 문서에서는 [IdentityServer](https://identityserver.io/)를 사용하여 사용자와 API 호출을 인증하는 ‘[호스트형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)’을 만드는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="ea0c5-106">독립형 또는 호스트형 Blazor WebAssembly 앱을 구성하여 기존의 외부 Identity 서버 인스턴스를 사용하려면 <xref:blazor/security/webassembly/standalone-with-authentication-library>의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea0c5-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea0c5-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea0c5-108">인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면</span><span class="sxs-lookup"><span data-stu-id="ea0c5-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="ea0c5-109">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor WebAssembly 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="ea0c5-110">**개별 사용자 계정**을 **사용자 계정 앱 내 저장** 옵션과 함께 선택하여 ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용해 앱 내에 사용자를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="ea0c5-111">**고급**  섹션에서 **ASP.NET Core 호스트형** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="ea0c5-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ea0c5-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="ea0c5-113">인증 메커니즘을 사용하여 빈 폴더에 새 Blazor WebAssembly 프로젝트를 만들려면 `-au|--auth` 옵션으로 `Individual` 인증 메커니즘을 지정하여 ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용해 앱 내에 사용자를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="ea0c5-114">자리표시자</span><span class="sxs-lookup"><span data-stu-id="ea0c5-114">Placeholder</span></span>  | <span data-ttu-id="ea0c5-115">예제</span><span class="sxs-lookup"><span data-stu-id="ea0c5-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="ea0c5-116">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="ea0c5-117">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea0c5-118">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea0c5-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ea0c5-119">인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면</span><span class="sxs-lookup"><span data-stu-id="ea0c5-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="ea0c5-120">**새 Blazor WebAssembly 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="ea0c5-121">ASP.NET Core [Identity](xref:security/authentication/identity)를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="ea0c5-122">**ASP.NET Core 호스트형** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="ea0c5-123">서버 앱 구성</span><span class="sxs-lookup"><span data-stu-id="ea0c5-123">Server app configuration</span></span>

<span data-ttu-id="ea0c5-124">다음 섹션에서는 인증 지원이 포함된 경우 프로젝트의 추가 사항에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="ea0c5-125">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="ea0c5-125">Startup class</span></span>

<span data-ttu-id="ea0c5-126">`Startup` 클래스에는 다음과 같은 추가 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="ea0c5-127">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="ea0c5-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="ea0c5-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="ea0c5-129">IdentityServer 및 IdentityServer 위에 기본 ASP.NET Core 규칙을 설정하는 추가 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="ea0c5-130">IdentityServer에 의해 생성된 JWT 토큰의 유효성을 검사하도록 앱을 구성하는 추가 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드를 사용한 인증:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="ea0c5-131">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="ea0c5-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="ea0c5-132">IdentityServer 미들웨어가 OIDC(OpenID Connect) 엔드포인트를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="ea0c5-133">인증 미들웨어는 요청 자격 증명의 유효성을 검사하고 요청 컨텍스트에서 사용자를 설정하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="ea0c5-134">인증 미들웨어가 인증 기능을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="ea0c5-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="ea0c5-135">AddApiAuthorization</span></span>

<span data-ttu-id="ea0c5-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드는 ASP.NET Core 시나리오에 대해 [IdentityServer](https://identityserver.io/)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="ea0c5-137">IdentityServer는 앱 보안 문제를 처리하는 강력하고 확장성 있는 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="ea0c5-138">IdentityServer는 대부분의 일반적인 시나리오에서 불필요한 복잡성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="ea0c5-139">따라서 좋은 출발점이라고 생각되는 몇 가지 규칙과 구성 옵션 세트가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="ea0c5-140">인증에 변경이 필요할 경우 IdentityServer의 모든 기능을 사용하여 앱의 요구 사항에 맞게 인증을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="ea0c5-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="ea0c5-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="ea0c5-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드는 앱의 정책 체계를 기본 인증 처리기로서 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="ea0c5-143">정책은 Identity가 Identity URL 공간 `/Identity`에 있는 모든 하위 경로로 라우팅된 모든 요청을 처리할 수 있도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="ea0c5-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>는 다른 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="ea0c5-145">이 메서드는 또한</span><span class="sxs-lookup"><span data-stu-id="ea0c5-145">Additionally, this method:</span></span>

* <span data-ttu-id="ea0c5-146">기본 범위 `{APPLICATION NAME}API`를 사용하여 `{APPLICATION NAME}API` API 리소스를 IdentityServer에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="ea0c5-147">JWT 전달자 토큰 미들웨어가 앱에 대해 IdentityServer에서 발행한 토큰의 유효성을 검사하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="ea0c5-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="ea0c5-148">WeatherForecastController</span></span>

<span data-ttu-id="ea0c5-149">`WeatherForecastController`(`Controllers/WeatherForecastController.cs`)에서 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성이 클래스에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="ea0c5-150">이 특성은 사용자가 리소스에 액세스하려면 기본 정책을 기준으로 인증되어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="ea0c5-151">기본 인증 정책은 기본 인증 체계를 사용하도록 구성되었으며, 기본 인증 체계는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="ea0c5-152">도우미 메서드는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>를 앱에 대한 요청의 기본 처리기로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="ea0c5-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="ea0c5-153">ApplicationDbContext</span></span>

<span data-ttu-id="ea0c5-154">`ApplicationDbContext`(`Data/ApplicationDbContext.cs`)에서 <xref:Microsoft.EntityFrameworkCore.DbContext>는 IdentityServer에 대한 스키마를 포함하도록 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="ea0c5-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="ea0c5-156">데이터베이스 스키마에 대한 모든 권한을 얻으려면 사용 가능한 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 클래스 중 하나에서 상속하고 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 메서드에서 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`를 호출하여 Identity 스키마를 포함하도록 컨텍스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="ea0c5-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="ea0c5-157">OidcConfigurationController</span></span>

<span data-ttu-id="ea0c5-158">`OidcConfigurationController`(`Controllers/OidcConfigurationController.cs`)에서 클라이언트 엔드포인트가 OIDC 매개 변수를 제공하도록 프로비저닝됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="ea0c5-159">앱 설정</span><span class="sxs-lookup"><span data-stu-id="ea0c5-159">App settings</span></span>

<span data-ttu-id="ea0c5-160">프로젝트 루트에 있는 앱 설정 파일(`appsettings.json`)의 `IdentityServer` 섹션은 구성된 클라이언트 목록을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="ea0c5-161">다음 예제에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-161">In the following example, there's a single client.</span></span> <span data-ttu-id="ea0c5-162">클라이언트 이름은 앱 이름에 대응되며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="ea0c5-163">프로필은 구성되는 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="ea0c5-164">프로필은 서버에 대한 구성 프로세스를 간소화하는 규칙을 구현하기 위해 내부적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="ea0c5-165">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="ea0c5-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="ea0c5-166">클라이언트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="ea0c5-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ea0c5-167">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="ea0c5-167">Authentication package</span></span>

<span data-ttu-id="ea0c5-168">앱이 개별 사용자 계정을 사용하도록 만들어진 경우(`Individual`) 해당 앱은 앱의 프로젝트 파일에서 자동으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="ea0c5-169">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ea0c5-170">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a><span data-ttu-id="ea0c5-171">`HttpClient` 구성</span><span class="sxs-lookup"><span data-stu-id="ea0c5-171">`HttpClient` configuration</span></span>

<span data-ttu-id="ea0c5-172">`Program.Main`(`Program.cs`)에서 명명된 <xref:System.Net.Http.HttpClient>(`HostIS.ServerAPI`)는 서버 API에 요청할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스를 제공하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-172">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="ea0c5-173">호스트형 Blazor 솔루션의 일부가 아닌 기존 Identity 서버 인스턴스를 사용하도록 Blazor WebAssembly 앱을 구성하는 경우, <xref:System.Net.Http.HttpClient> 기준 주소 등록을 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>(`builder.HostEnvironment.BaseAddress`)에서 서버 앱의 API 권한 부여 엔드포인트 URL로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-173">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="ea0c5-174">API 권한 부여 지원</span><span class="sxs-lookup"><span data-stu-id="ea0c5-174">API authorization support</span></span>

<span data-ttu-id="ea0c5-175">사용자 인증에 대한 지원은 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 패키지 내에서 제공하는 확장 메서드를 통해 서비스 컨테이너에 연결되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-175">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="ea0c5-176">이 메서드는 앱이 기존 인증 시스템과 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-176">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="ea0c5-177">기본적으로 앱에 대한 구성은 규칙에 따라 `_configuration/{client-id}`에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-177">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="ea0c5-178">규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-178">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="ea0c5-179">이 URL은 옵션을 사용하여 오버로드를 호출함으로써 별도의 엔드포인트를 가리키도록 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-179">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="ea0c5-180">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="ea0c5-180">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="ea0c5-181">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="ea0c5-181">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="ea0c5-182">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="ea0c5-182">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="ea0c5-183">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="ea0c5-183">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="ea0c5-184">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="ea0c5-184">LoginDisplay component</span></span>

<span data-ttu-id="ea0c5-185">`LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)는 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)에서 렌더링되고 다음 동작을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-185">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="ea0c5-186">인증된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-186">For authenticated users:</span></span>
  * <span data-ttu-id="ea0c5-187">현재 사용자 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-187">Displays the current user name.</span></span>
  * <span data-ttu-id="ea0c5-188">ASP.NET Core Identity에 있는 사용자 프로필 페이지로 연결되는 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-188">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="ea0c5-189">앱에서 로그아웃하는 단추를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-189">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="ea0c5-190">익명 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-190">For anonymous users:</span></span>
  * <span data-ttu-id="ea0c5-191">등록 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-191">Offers the option to register.</span></span>
  * <span data-ttu-id="ea0c5-192">로그인 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-192">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="ea0c5-193">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="ea0c5-193">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="ea0c5-194">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="ea0c5-194">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="ea0c5-195">앱 실행</span><span class="sxs-lookup"><span data-stu-id="ea0c5-195">Run the app</span></span>

<span data-ttu-id="ea0c5-196">서버 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-196">Run the app from the Server project.</span></span> <span data-ttu-id="ea0c5-197">Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-197">When using Visual Studio, either:</span></span>

* <span data-ttu-id="ea0c5-198">도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-198">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="ea0c5-199">**솔루션 탐색기**에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-199">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="ea0c5-200">API 권한 부여를 사용한 이름 및 역할 클레임</span><span class="sxs-lookup"><span data-stu-id="ea0c5-200">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="ea0c5-201">사용자 지정 사용자 팩터리</span><span class="sxs-lookup"><span data-stu-id="ea0c5-201">Custom user factory</span></span>

<span data-ttu-id="ea0c5-202">클라이언트 앱에서 사용자 지정 사용자 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-202">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="ea0c5-203">Identity 서버가 단일 `role` 클레임에서 여러 역할을 JSON 배열로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-203">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="ea0c5-204">클레임에서 단일 역할이 문자열 값으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-204">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="ea0c5-205">팩터리가 각 사용자 역할에 대해 개별 `role` 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-205">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="ea0c5-206">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-206">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="ea0c5-207">클라이언트 앱에서 `Program.Main`(`Program.cs`)에 팩터리를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-207">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="ea0c5-208">서버 앱에서 Identity 작성기에 대해 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*>를 호출하여 역할 관련 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-208">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="ea0c5-209">Identity Server 구성</span><span class="sxs-lookup"><span data-stu-id="ea0c5-209">Configure Identity Server</span></span>

<span data-ttu-id="ea0c5-210">다음 방법 중 **하나**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-210">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="ea0c5-211">API 권한 부여 옵션</span><span class="sxs-lookup"><span data-stu-id="ea0c5-211">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="ea0c5-212">프로필 서비스</span><span class="sxs-lookup"><span data-stu-id="ea0c5-212">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="ea0c5-213">API 권한 부여 옵션</span><span class="sxs-lookup"><span data-stu-id="ea0c5-213">API authorization options</span></span>

<span data-ttu-id="ea0c5-214">서버 앱에서:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-214">In the Server app:</span></span>

* <span data-ttu-id="ea0c5-215">Identity 서버가 `name` 및 `role` 클레임을 ID 토큰 및 액세스 토큰에 배치하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-215">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="ea0c5-216">JWT 토큰 처리기에서 역할의 기본 매핑을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-216">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="ea0c5-217">프로필 서비스</span><span class="sxs-lookup"><span data-stu-id="ea0c5-217">Profile Service</span></span>

<span data-ttu-id="ea0c5-218">서버 앱에서 `ProfileService` 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-218">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="ea0c5-219">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="ea0c5-219">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="ea0c5-220">서버 앱에서 `Startup.ConfigureServices`에 프로필 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-220">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="ea0c5-221">권한 부여 메커니즘 사용</span><span class="sxs-lookup"><span data-stu-id="ea0c5-221">Use authorization mechanisms</span></span>

<span data-ttu-id="ea0c5-222">이 시점에서는 클라이언트 앱의 구성 요소 권한 부여 방식이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-222">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="ea0c5-223">구성 요소의 모든 권한 부여 메커니즘에서 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-223">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="ea0c5-224">[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)(예: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="ea0c5-224">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="ea0c5-225">[`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)(예: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="ea0c5-225">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="ea0c5-226">[절차적 논리](xref:blazor/security/index#procedural-logic)(예: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="ea0c5-226">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="ea0c5-227">여러 역할 테스트가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-227">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="ea0c5-228">클라이언트 앱에서 `User.Identity.Name`에 사용자의 사용자 이름이 채워집니다. 이는 보통 사용자의 로그인 메일 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="ea0c5-228">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ea0c5-229">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ea0c5-229">Additional resources</span></span>

* [<span data-ttu-id="ea0c5-230">Azure App Service에 배포</span><span class="sxs-lookup"><span data-stu-id="ea0c5-230">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="ea0c5-231">Key Vault에서 인증서 가져오기(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="ea0c5-231">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="ea0c5-232">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="ea0c5-232">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
