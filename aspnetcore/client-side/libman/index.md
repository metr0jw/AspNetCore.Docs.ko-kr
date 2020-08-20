---
title: LibMan을 사용하여 ASP.NET Core에서 클라이언트 쪽 라이브러리 획득
author: scottaddie
description: 라이브러리 관리자(LibMan)를 사용하여 ASP.NET Core 프로젝트에 클라이언트 쪽 라이브러리 자산을 설치하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 8458f2fe1cdc139a3f22c7d73675301d89ad368e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625637"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="716d4-103">LibMan을 사용하여 ASP.NET Core에서 클라이언트 쪽 라이브러리 획득</span><span class="sxs-lookup"><span data-stu-id="716d4-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="716d4-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="716d4-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="716d4-105">라이브러리 관리자(LibMan)는 가벼운 클라이언트 쪽 라이브러리 획득 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="716d4-106">LibMan은 파일 시스템에서 또는 [CDN(콘텐츠 전송 네트워크)](https://wikipedia.org/wiki/Content_delivery_network)에서 인기 있는 라이브러리 및 프레임워크를 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="716d4-107">지원되는 CDN은 [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/) 및 [unpkg](https://unpkg.com/#/)입니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="716d4-108">선택한 라이브러리 파일은 ASP.NET Core 프로젝트 내부의 적절한 위치에 페치 및 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="716d4-109">LibMan 사용 사례</span><span class="sxs-lookup"><span data-stu-id="716d4-109">LibMan use cases</span></span>

<span data-ttu-id="716d4-110">LibMan은 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="716d4-111">필요한 라이브러리 파일만 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="716d4-112">라이브러리의 파일 하위 집합을 획득하기 위해 [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) 및 [WebPack](https://webpack.js.org) 같은 추가 도구가 반드시 필요한 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="716d4-113">파일은 작업 또는 수동 파일 복사를 빌드하도록 재정렬하지 않고도 특정 위치에 배치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="716d4-114">LibMan의 이점에 대한 자세한 내용은 [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)(Visual Studio 2017의 최신 프런트 엔드 웹 개발: LibMan 세그먼트)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="716d4-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="716d4-115">LibMan은 패키지 관리 시스템이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="716d4-116">npm 또는 [yarn](https://yarnpkg.com) 같은 패키지 관리자를 이미 사용 중인 경우 계속 사용하시면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="716d4-117">LibMan은 이러한 도구를 대체하기 위해 개발된 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="716d4-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="716d4-118">추가 자료</span><span class="sxs-lookup"><span data-stu-id="716d4-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="716d4-119">LibMan GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="716d4-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
