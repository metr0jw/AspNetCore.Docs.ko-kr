---
title: Swagger/OpenAPI를 사용한 ASP.NET Core Web API 도움말 페이지
author: RicoSuter
description: 이 자습서에서는 Swagger를 추가하여 Web API 앱에 대한 설명서 및 도움말 페이지를 생성하는 연습을 제공합니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: a3b8efcdb1f196fc878e0ad29580d305bb24f50a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021980"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="f7bfd-103">Swagger/OpenAPI를 사용한 ASP.NET Core 웹 API 도움말 페이지</span><span class="sxs-lookup"><span data-stu-id="f7bfd-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="f7bfd-104">작성자: [Christoph Nienaber](https://twitter.com/zuckerthoben) 및 [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="f7bfd-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="f7bfd-105">웹 API를 사용할 때 여러 메서드를 이해하는 것이 개발자에게 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="f7bfd-106">[Swagger](https://swagger.io/)([OpenAPI](https://www.openapis.org/)라고도 함)는 웹 API에 대한 유용한 설명서 및 도움말 페이지를 생성하는 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="f7bfd-107">대화형 설명서, 클라이언트 SDK 생성 및 API 검색 기능과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="f7bfd-108">이 아티클에서는 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 및 [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="f7bfd-109">**Swashbuckle.AspNetCore**는 ASP.NET Core Web API에 대한 Swagger 문서를 생성하기 위한 오픈 소스 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="f7bfd-110">**NSwag**는 Swagger 문서를 생성하고 [Swagger UI](https://swagger.io/swagger-ui/) 또는 [ReDoc](https://github.com/Rebilly/ReDoc)를 ASP.NET Core Web API에 통합하기 위한 또 다른 오픈 소스 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="f7bfd-111">또한 NSwag는 API용 C# 및 TypeScript 클라이언트 코드를 생성하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="f7bfd-112">Swagger/OpenAPI란?</span><span class="sxs-lookup"><span data-stu-id="f7bfd-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="f7bfd-113">Swagger는 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API를 설명하는 언어 중립적 사양입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="f7bfd-114">Swagger 프로젝트는 [OpenAPI 이니셔티브](https://www.openapis.org/)에 기부되었으며, 현재는 OpenAPI라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="f7bfd-115">두 이름은 동일한 의미로 사용되지만 OpenAPI를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="f7bfd-116">컴퓨터와 사람 모두 구현(소스 코드, 네트워크 액세스, 설명서)에 직접 액세스하지 않고도 서비스의 기능을 이해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="f7bfd-117">단일 목표는 분리된 서비스를 연결하는 데 필요한 작업량을 최소화하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="f7bfd-118">또 다른 목표는 서비스를 정확하게 문서화하는 데 필요한 시간을 줄이는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="f7bfd-119">OpenAPI 사양(openapi.json)</span><span class="sxs-lookup"><span data-stu-id="f7bfd-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="f7bfd-120">OpenAPI 흐름의 핵심은 사양이며 기본적으로 *openapi.json*이라는 문서입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json*.</span></span> <span data-ttu-id="f7bfd-121">서비스에 따라 OpenAPI 도구 체인(또는 타사 구현 도구)에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="f7bfd-122">API의 기능과 HTTP를 사용하여 액세스하는 방법에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="f7bfd-123">Swagger UI를 구동하고, 도구 체인에서 검색 및 클라이언트 코드 생성을 활성화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="f7bfd-124">다음은 간략하게 축약된 OpenAPI 사양의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="f7bfd-125">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="f7bfd-125">Swagger UI</span></span>

<span data-ttu-id="f7bfd-126">[Swagger UI](https://swagger.io/swagger-ui/)는 생성된 OpenAPI 사양을 사용하여 서비스에 대한 정보를 제공하는 웹 기반 UI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="f7bfd-127">Swashbuckle과 NSwag에는 모두 임베디드 버전의 Swagger UI가 포함되어 있어, 미들웨어 등록 호출을 사용하여 ASP.NET Core 앱에서 호스팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="f7bfd-128">웹 UI는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-128">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="f7bfd-130">컨트롤러의 각 공용 작업 메서드는 UI에서 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="f7bfd-131">메서드 이름을 클릭하여 섹션을 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-131">Click a method name to expand the section.</span></span> <span data-ttu-id="f7bfd-132">필요한 매개 변수를 모두 추가하고 **사용해 보기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![예제 Swagger GET 테스트](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="f7bfd-134">스크린샷에 사용된 Swagger UI 버전은 버전 2입니다.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="f7bfd-135">버전 3 예제는 [Petstore 예제](https://petstore.swagger.io/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f7bfd-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f7bfd-136">다음 단계</span><span class="sxs-lookup"><span data-stu-id="f7bfd-136">Next steps</span></span>

* [<span data-ttu-id="f7bfd-137">Swashbuckle 시작</span><span class="sxs-lookup"><span data-stu-id="f7bfd-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="f7bfd-138">NSwag 시작</span><span class="sxs-lookup"><span data-stu-id="f7bfd-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
