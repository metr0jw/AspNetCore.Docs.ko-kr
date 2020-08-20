---
title: ASP.NET Core 1.1의 새로운 기능
author: rick-anderson
description: ASP.NET Core 1.1의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.date: 12/18/2018
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
uid: aspnetcore-1.1
ms.openlocfilehash: 1068307dc792ffb8def4f0652de497f666d4318d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634789"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="f8cd0-103">ASP.NET Core 1.1의 새로운 기능</span><span class="sxs-lookup"><span data-stu-id="f8cd0-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="f8cd0-104">ASP.NET Core 1.1에는 다음과 같은 새로운 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f8cd0-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="f8cd0-105">URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f8cd0-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="f8cd0-106">응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f8cd0-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="f8cd0-107">태그 도우미인 구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="f8cd0-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="f8cd0-108">MVC 필터인 미들웨어</span><span class="sxs-lookup"><span data-stu-id="f8cd0-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="f8cd0-109">Cookie 기반 TempData 공급자</span><span class="sxs-lookup"><span data-stu-id="f8cd0-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="f8cd0-110">Azure App Service 로깅 공급자</span><span class="sxs-lookup"><span data-stu-id="f8cd0-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="f8cd0-111">Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="f8cd0-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="f8cd0-112">Azure 및 Redis 스토리지 데이터 보호 키 리포지토리</span><span class="sxs-lookup"><span data-stu-id="f8cd0-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="f8cd0-113">Windows용 WebListener 서버</span><span class="sxs-lookup"><span data-stu-id="f8cd0-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="f8cd0-114">WebSockets 지원</span><span class="sxs-lookup"><span data-stu-id="f8cd0-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="f8cd0-115">ASP.NET Core 버전 1.0과 1.1 중에서 선택</span><span class="sxs-lookup"><span data-stu-id="f8cd0-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="f8cd0-116">ASP.NET Core 1.1에는 ASP.NET Core 1.0보다 더 많은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f8cd0-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="f8cd0-117">일반적으로 최신 버전을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f8cd0-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="f8cd0-118">추가 정보</span><span class="sxs-lookup"><span data-stu-id="f8cd0-118">Additional Information</span></span>

- [<span data-ttu-id="f8cd0-119">ASP.NET Core 1.1.0 릴리스 정보</span><span class="sxs-lookup"><span data-stu-id="f8cd0-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="f8cd0-120">ASP.NET Core 개발팀의 진행 상황 및 계획과 연결하려면 [ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)을 시청하세요.</span><span class="sxs-lookup"><span data-stu-id="f8cd0-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
