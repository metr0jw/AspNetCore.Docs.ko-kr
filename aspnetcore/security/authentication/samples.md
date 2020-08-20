---
title: ASP.NET Core에 대 한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대 한 링크를 제공 합니다.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630746"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="6ccf8-103">ASP.NET Core에 대 한 인증 샘플</span><span class="sxs-lookup"><span data-stu-id="6ccf8-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="6ccf8-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6ccf8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ccf8-105">[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="6ccf8-106">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="6ccf8-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="6ccf8-107">[Cookie 인증은](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="6ccf8-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="6ccf8-108">사용자 지정 정책 공급자-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="6ccf8-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="6ccf8-109">동적 인증 스키마 및 옵션</span><span class="sxs-lookup"><span data-stu-id="6ccf8-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="6ccf8-110">[외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="6ccf8-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="6ccf8-111">cookie요청에 따라 및 다른 인증 체계 중에서 선택</span><span class="sxs-lookup"><span data-stu-id="6ccf8-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="6ccf8-112">정적 파일에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="6ccf8-113">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="6ccf8-113">Run the samples</span></span>

* <span data-ttu-id="6ccf8-114">[분기](https://github.com/dotnet/AspNetCore)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="6ccf8-115">예를 들어 `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="6ccf8-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="6ccf8-116">[ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="6ccf8-117">ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="6ccf8-118">*AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 샘플을 실행 `dotnet run` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ccf8-119">[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="6ccf8-120">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="6ccf8-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="6ccf8-121">[Cookie 인증은](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="6ccf8-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="6ccf8-122">사용자 지정 정책 공급자-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="6ccf8-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="6ccf8-123">동적 인증 스키마 및 옵션</span><span class="sxs-lookup"><span data-stu-id="6ccf8-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="6ccf8-124">[외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="6ccf8-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="6ccf8-125">cookie요청에 따라 및 다른 인증 체계 중에서 선택</span><span class="sxs-lookup"><span data-stu-id="6ccf8-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="6ccf8-126">정적 파일에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="6ccf8-127">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="6ccf8-127">Run the samples</span></span>

* <span data-ttu-id="6ccf8-128">[분기](https://github.com/dotnet/AspNetCore)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="6ccf8-129">예를 들어 `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="6ccf8-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="6ccf8-130">[ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="6ccf8-131">ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="6ccf8-132">*AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 샘플을 실행 `dotnet run` 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ccf8-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
