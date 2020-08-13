---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013530"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="835dd-103">ASP.NET Core Blazor 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="835dd-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="835dd-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="835dd-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="835dd-105">브라우저 요구 사항</span><span class="sxs-lookup"><span data-stu-id="835dd-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="835dd-106">브라우저</span><span class="sxs-lookup"><span data-stu-id="835dd-106">Browser</span></span>                          | <span data-ttu-id="835dd-107">버전</span><span class="sxs-lookup"><span data-stu-id="835dd-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="835dd-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="835dd-108">Microsoft Edge</span></span>                   | <span data-ttu-id="835dd-109">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-109">Current</span></span>               |
| <span data-ttu-id="835dd-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="835dd-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="835dd-111">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-111">Current</span></span>               |
| <span data-ttu-id="835dd-112">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="835dd-112">Google Chrome, including Android</span></span> | <span data-ttu-id="835dd-113">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-113">Current</span></span>               |
| <span data-ttu-id="835dd-114">Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="835dd-114">Safari, including iOS</span></span>            | <span data-ttu-id="835dd-115">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-115">Current</span></span>               |
| <span data-ttu-id="835dd-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="835dd-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="835dd-117">지원되지 않음&dagger;</span><span class="sxs-lookup"><span data-stu-id="835dd-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="835dd-118">&dagger;Microsoft Internet Explorer는 [WebAssembly](https://webassembly.org)를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="835dd-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="835dd-119">브라우저</span><span class="sxs-lookup"><span data-stu-id="835dd-119">Browser</span></span>                          | <span data-ttu-id="835dd-120">버전</span><span class="sxs-lookup"><span data-stu-id="835dd-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="835dd-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="835dd-121">Microsoft Edge</span></span>                   | <span data-ttu-id="835dd-122">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-122">Current</span></span>    |
| <span data-ttu-id="835dd-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="835dd-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="835dd-124">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-124">Current</span></span>    |
| <span data-ttu-id="835dd-125">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="835dd-125">Google Chrome, including Android</span></span> | <span data-ttu-id="835dd-126">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-126">Current</span></span>    |
| <span data-ttu-id="835dd-127">Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="835dd-127">Safari, including iOS</span></span>            | <span data-ttu-id="835dd-128">현재</span><span class="sxs-lookup"><span data-stu-id="835dd-128">Current</span></span>    |
| <span data-ttu-id="835dd-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="835dd-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="835dd-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="835dd-130">11&dagger;</span></span> |

<span data-ttu-id="835dd-131">&dagger;추가 보충 기능이 필요합니다(예: [`Polyfill.io`](https://polyfill.io/v3/) 번들을 통해 프라미스를 추가할 수 있음).</span><span class="sxs-lookup"><span data-stu-id="835dd-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="835dd-132">추가 자료</span><span class="sxs-lookup"><span data-stu-id="835dd-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
