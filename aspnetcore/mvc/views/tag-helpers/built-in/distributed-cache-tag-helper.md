---
title: ASP.NET Core의 분산 캐시 태그 도우미
author: pkellner
description: 분산 캐시 태그 도우미를 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
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
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 67e5b7ef09525063da6e6b7dfce6fd084d279869
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633905"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="d8d62-103">ASP.NET Core의 분산 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="d8d62-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="d8d62-104">작성자: [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="d8d62-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="d8d62-105">분산 캐시 태그 도우미는 ASP.NET Core 앱의 콘텐츠를 분산 캐시 원본에 캐싱하여 성능을 획기적으로 개선하는 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="d8d62-106">태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8d62-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="d8d62-107">분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 기본 클래스를 상속받습니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="d8d62-108">모든 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) 특성을 분산 태그 도우미에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="d8d62-109">분산 캐시 태그 도우미는 [생성자 주입](xref:fundamentals/dependency-injection#constructor-injection-behavior)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="d8d62-110">분산 캐시 태그 도우미의 생성자에는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스가 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="d8d62-111">`Startup.ConfigureServices`(*Startup.cs*)에서 `IDistributedCache`의 구체적 구현이 생성되지 않으면, 분산 캐시 태그 도우미는 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)와 동일한 메모리 내 공급자를 사용하여 캐시된 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="d8d62-112">분산 캐시 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="d8d62-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="d8d62-113">캐시 태그 도우미와 공유되는 특성</span><span class="sxs-lookup"><span data-stu-id="d8d62-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="d8d62-114">분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 클래스를 상속받습니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="d8d62-115">이러한 특성에 대한 설명은 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8d62-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="d8d62-116">name</span><span class="sxs-lookup"><span data-stu-id="d8d62-116">name</span></span>

| <span data-ttu-id="d8d62-117">특성 유형</span><span class="sxs-lookup"><span data-stu-id="d8d62-117">Attribute Type</span></span> | <span data-ttu-id="d8d62-118">예제</span><span class="sxs-lookup"><span data-stu-id="d8d62-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="d8d62-119">String</span><span class="sxs-lookup"><span data-stu-id="d8d62-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="d8d62-120">`name`은 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-120">`name` is required.</span></span> <span data-ttu-id="d8d62-121">`name` 특성은 저장된 각 캐시 인스턴스의 키로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="d8d62-122">페이지의 페이지 이름과 위치를 기준으로 각 인스턴스에 캐시 키를 할당 하는 캐시 태그 도우미와 달리 Razor Razor 분산 캐시 태그 도우미는 특성에 대 한 키만을 기준으로 `name` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="d8d62-123">예:</span><span class="sxs-lookup"><span data-stu-id="d8d62-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="d8d62-124">분산 캐시 태그 도우미 IDistributedCache 구현</span><span class="sxs-lookup"><span data-stu-id="d8d62-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="d8d62-125">ASP.NET Core에는 두 가지 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현이 기본으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="d8d62-126">하나는 SQL Server 기반이고 다른 하나는 Redis 기반입니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="d8d62-127">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="d8d62-128">이러한 구현의 세부 정보는 <xref:performance/caching/distributed>에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="d8d62-129">두 가지 구현 모두 `Startup`에서 `IDistributedCache` 인스턴스를 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="d8d62-130">특정 `IDistributedCache` 구현의 사용과 특별히 관련된 태그 특성은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d8d62-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8d62-131">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d8d62-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
