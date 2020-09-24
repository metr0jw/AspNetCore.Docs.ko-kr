---
title: ASP.NET Core 웹 SDK
author: Rick-Anderson
description: Microsoft.NET.Sdk.Web의 개요
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: fdc2991060050c813efb6dc895cf73ff876bae29
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722503"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="be4fa-103">ASP.NET Core 웹 SDK</span><span class="sxs-lookup"><span data-stu-id="be4fa-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="be4fa-104">개요</span><span class="sxs-lookup"><span data-stu-id="be4fa-104">Overview</span></span>

<span data-ttu-id="be4fa-105">`Microsoft.NET.Sdk.Web`은 ASP.NET Core 앱을 빌드하기 위한 [MSBuild 프로젝트 SDK](/visualstudio/msbuild/how-to-use-project-sdk)입니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="be4fa-106">이 SDK를 사용하지 않고 ASP.NET Core 앱을 빌드할 수는 있지만 웹 SDK에는 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="be4fa-107">최고 수준의 환경을 제공하도록 조정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="be4fa-108">대부분의 사용자에게 권장되는 대상입니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-108">The recommended target for most users.</span></span>

<span data-ttu-id="be4fa-109">프로젝트에서 웹 SDK를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="be4fa-110">웹 SDK를 사용하여 다음과 같은 기능이 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="be4fa-111">.NET Core 3.0 이상을 대상으로 하는 프로젝트는 암시적으로 다음을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="be4fa-112">[ASP.NET Core 공유 프레임워크](xref:fundamentals/metapackage-app)</span><span class="sxs-lookup"><span data-stu-id="be4fa-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="be4fa-113">ASP.NET Core 앱을 빌드하기 위해 디자인된 [분석기](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)</span><span class="sxs-lookup"><span data-stu-id="be4fa-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="be4fa-114">웹 SDK는 게시 프로필을 사용하도록 설정하고 WebDeploy를 사용하여 게시하도록 하는 MSBuild 대상을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="be4fa-115">속성</span><span class="sxs-lookup"><span data-stu-id="be4fa-115">Properties</span></span>

| <span data-ttu-id="be4fa-116">속성</span><span class="sxs-lookup"><span data-stu-id="be4fa-116">Property</span></span> | <span data-ttu-id="be4fa-117">설명</span><span class="sxs-lookup"><span data-stu-id="be4fa-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="be4fa-118">`Microsoft.AspNetCore.App` 공유 프레임워크에 대한 암시적 참조를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="be4fa-119">ASP.NET Core 분석기에 대한 암시적 참조를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="be4fa-120">Blazor(서버) 애플리케이션을 빌드할 때 Razor 구성 요소 분석기에 대한 암시적 참조를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="be4fa-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |