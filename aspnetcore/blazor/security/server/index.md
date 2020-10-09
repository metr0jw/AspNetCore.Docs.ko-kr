---
title: ASP.NET Core Blazor Server 앱 보호
author: guardrex
description: Blazor Server 앱을 ASP.NET Core 애플리케이션으로 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: d6d0f6f859dbaef98c6c8a9c53fe9858705cdc0a
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805507"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="02d18-103">ASP.NET Core Blazor Server 앱 보호</span><span class="sxs-lookup"><span data-stu-id="02d18-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="02d18-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="02d18-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="02d18-105">Blazor Server 앱은 ASP.NET Core 앱과 동일한 방식으로 보안이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="02d18-106">자세한 내용은 <xref:security/index>의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02d18-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="02d18-107">이 개요의 항목은 Blazor Server에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="02d18-108">Blazor Server 프로젝트 템플릿</span><span class="sxs-lookup"><span data-stu-id="02d18-108">Blazor Server project template</span></span>

<span data-ttu-id="02d18-109">프로젝트를 만들 때 인증을 위해 Blazor Server 프로젝트 템플릿을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02d18-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02d18-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02d18-111"><xref:blazor/tooling>의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="02d18-112">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor Server 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="02d18-113">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="02d18-114">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="02d18-114">**No Authentication**</span></span>
* <span data-ttu-id="02d18-115">**개별 사용자 계정**: 사용자 계정은 다음과 같이 저장될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="02d18-116">ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용하는 앱 내에 저장</span><span class="sxs-lookup"><span data-stu-id="02d18-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="02d18-117">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="02d18-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="02d18-118">**회사 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="02d18-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="02d18-119">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="02d18-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02d18-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02d18-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02d18-121"><xref:blazor/tooling>의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="02d18-122">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="02d18-123">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="02d18-123">Authentication mechanism</span></span> | <span data-ttu-id="02d18-124">설명</span><span class="sxs-lookup"><span data-stu-id="02d18-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="02d18-125">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="02d18-125">`None` (default)</span></span>         | <span data-ttu-id="02d18-126">인증 없음</span><span class="sxs-lookup"><span data-stu-id="02d18-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="02d18-127">ASP.NET Core Identity를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="02d18-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="02d18-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="02d18-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="02d18-129">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="02d18-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="02d18-130">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="02d18-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="02d18-131">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="02d18-131">Windows Authentication</span></span> |

<span data-ttu-id="02d18-132">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="02d18-133">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-133">Create a folder for the project.</span></span>
* <span data-ttu-id="02d18-134">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-134">Name the project.</span></span>

<span data-ttu-id="02d18-135">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02d18-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02d18-136">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02d18-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="02d18-137"><xref:blazor/tooling>의 Mac용 Visual Studio 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="02d18-138">**새 Blazor Server 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="02d18-139">ASP.NET Core Identity를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="02d18-140">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="02d18-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="02d18-141">명령 셸에서 다음 명령을 사용해 인증 메커니즘이 있는 새 Blazor Server 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="02d18-142">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="02d18-143">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="02d18-143">Authentication mechanism</span></span> | <span data-ttu-id="02d18-144">설명</span><span class="sxs-lookup"><span data-stu-id="02d18-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="02d18-145">`None`(기본값)</span><span class="sxs-lookup"><span data-stu-id="02d18-145">`None` (default)</span></span>         | <span data-ttu-id="02d18-146">인증 없음</span><span class="sxs-lookup"><span data-stu-id="02d18-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="02d18-147">ASP.NET Core Identity를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="02d18-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="02d18-148">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="02d18-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="02d18-149">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="02d18-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="02d18-150">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="02d18-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="02d18-151">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="02d18-151">Windows Authentication</span></span> |

<span data-ttu-id="02d18-152">`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="02d18-153">프로젝트의 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-153">Create a folder for the project.</span></span>
* <span data-ttu-id="02d18-154">프로젝트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-154">Name the project.</span></span>

<span data-ttu-id="02d18-155">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02d18-155">For more information:</span></span>

* <span data-ttu-id="02d18-156">.NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02d18-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="02d18-157">명령 셸에서 Blazor Server 템플릿(`blazorserver`)에 대한 도움말 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="02d18-158">스캐폴드 Identity</span><span class="sxs-lookup"><span data-stu-id="02d18-158">Scaffold Identity</span></span>

<span data-ttu-id="02d18-159">Blazor Server 프로젝트에 Identity를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="02d18-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="02d18-160">[기존 권한 없음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="02d18-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="02d18-161">[권한 있음](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="02d18-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02d18-162">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="02d18-162">Additional resources</span></span>

* [<span data-ttu-id="02d18-163">빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가</span><span class="sxs-lookup"><span data-stu-id="02d18-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="02d18-164">빠른 시작: Microsoft ID 플랫폼을 사용하여 ASP.NET Core 웹 API 보호</span><span class="sxs-lookup"><span data-stu-id="02d18-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
