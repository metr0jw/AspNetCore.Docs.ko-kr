---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
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
uid: tutorials/first-web-api
ms.openlocfilehash: ce0dfdf1ce88b55790d33918a2d20bc19a09b288
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626898"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4b0b3-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4b0b3-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4b0b3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4b0b3-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b0b3-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4b0b3-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-107">Create a web API project.</span></span>
> * <span data-ttu-id="4b0b3-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4b0b3-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4b0b3-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4b0b3-111">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4b0b3-112">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4b0b3-113">개요</span><span class="sxs-lookup"><span data-stu-id="4b0b3-113">Overview</span></span>

<span data-ttu-id="4b0b3-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4b0b3-115">API</span><span class="sxs-lookup"><span data-stu-id="4b0b3-115">API</span></span> | <span data-ttu-id="4b0b3-116">설명</span><span class="sxs-lookup"><span data-stu-id="4b0b3-116">Description</span></span> | <span data-ttu-id="4b0b3-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="4b0b3-117">Request body</span></span> | <span data-ttu-id="4b0b3-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="4b0b3-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="4b0b3-119">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-119">Get all to-do items</span></span> | <span data-ttu-id="4b0b3-120">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-120">None</span></span> | <span data-ttu-id="4b0b3-121">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="4b0b3-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="4b0b3-122">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-122">Get an item by ID</span></span> | <span data-ttu-id="4b0b3-123">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-123">None</span></span> | <span data-ttu-id="4b0b3-124">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="4b0b3-125">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-125">Add a new item</span></span> | <span data-ttu-id="4b0b3-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-126">To-do item</span></span> | <span data-ttu-id="4b0b3-127">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="4b0b3-128">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b0b3-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4b0b3-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-129">To-do item</span></span> | <span data-ttu-id="4b0b3-130">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-130">None</span></span> |
|<span data-ttu-id="4b0b3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b0b3-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="4b0b3-132">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b0b3-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4b0b3-133">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-133">None</span></span> | <span data-ttu-id="4b0b3-134">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-134">None</span></span>|

<span data-ttu-id="4b0b3-135">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-135">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4b0b3-141">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="4b0b3-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-144">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4b0b3-145">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-147">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4b0b3-148">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4b0b3-149">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4b0b3-150">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="4b0b3-151">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-151">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b0b3-154">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4b0b3-155">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4b0b3-156">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="4b0b3-157">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4b0b3-158">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="4b0b3-158">The preceding commands:</span></span>

  * <span data-ttu-id="4b0b3-159">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4b0b3-160">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-161">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b0b3-162">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-162">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4b0b3-164">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="4b0b3-165">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 템플릿 선택](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="4b0b3-167">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 3.x **대상 프레임워크**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="4b0b3-168">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-168">Select **Next**.</span></span>

* <span data-ttu-id="4b0b3-169">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="4b0b3-171">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="4b0b3-172">API 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-172">Test the API</span></span>

<span data-ttu-id="4b0b3-173">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4b0b3-174">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b0b3-176">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4b0b3-177">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4b0b3-178">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4b0b3-179">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4b0b3-181">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4b0b3-182">브라우저에서 `https://localhost:5001/WeatherForecast` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-183">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b0b3-184">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4b0b3-185">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4b0b3-186">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4b0b3-187">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="4b0b3-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4b0b3-188">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-188">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4b0b3-189">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-189">Add a model class</span></span>

<span data-ttu-id="4b0b3-190">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4b0b3-191">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-193">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4b0b3-194">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b0b3-195">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="4b0b3-196">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4b0b3-197">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4b0b3-198">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b0b3-200">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4b0b3-201">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-202">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b0b3-203">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-203">Right-click the project.</span></span> <span data-ttu-id="4b0b3-204">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b0b3-205">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-205">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4b0b3-207">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4b0b3-208">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4b0b3-209">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="4b0b3-210">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4b0b3-211">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4b0b3-212">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-212">Add a database context</span></span>

<span data-ttu-id="4b0b3-213">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4b0b3-214">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="4b0b3-216">NuGet 패키지 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-216">Add NuGet packages</span></span>

* <span data-ttu-id="4b0b3-217">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4b0b3-218">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="4b0b3-219">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="4b0b3-220">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="4b0b3-221">앞의 지침을 사용하여 **Microsoft.EntityFrameworkCore.InMemory** NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4b0b3-223">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="4b0b3-224">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4b0b3-225">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-226">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4b0b3-227">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4b0b3-228">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4b0b3-229">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="4b0b3-229">Register the database context</span></span>

<span data-ttu-id="4b0b3-230">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4b0b3-231">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="4b0b3-232">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4b0b3-233">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="4b0b3-233">The preceding code:</span></span>

* <span data-ttu-id="4b0b3-234">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4b0b3-235">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4b0b3-236">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4b0b3-237">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="4b0b3-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-239">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4b0b3-240">**추가** > **스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4b0b3-241">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러**를 선택하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4b0b3-242">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="4b0b3-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4b0b3-243">**모델 클래스**에서 **TodoItem (TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4b0b3-244">**데이터 컨텍스트 클래스**에서 **TodoContext (TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4b0b3-245">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-246">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4b0b3-247">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet-aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="4b0b3-248">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="4b0b3-248">The preceding commands:</span></span>

* <span data-ttu-id="4b0b3-249">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4b0b3-250">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="4b0b3-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4b0b3-251">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4b0b3-252">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="4b0b3-252">The generated code:</span></span>

* <span data-ttu-id="4b0b3-253">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="4b0b3-254">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4b0b3-255">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4b0b3-256">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4b0b3-257">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="4b0b3-258">ASP.NET Core 템플릿과 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="4b0b3-259">뷰가 있는 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="4b0b3-260">API 컨트롤러용 ASP.NET Core 템플릿의 경우, 경로 템플릿에 `[action]`이 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="4b0b3-261">`[action]` 토큰이 경로 템플릿에 없는 경우 경로에서 [작업](xref:mvc/controllers/routing#action) 이름이 제외됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="4b0b3-262">즉, 일치하는 경로에서 작업과 연결된 메서드 이름이 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4b0b3-263">PostTodoItem 만들기 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="4b0b3-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4b0b3-264">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4b0b3-265">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="4b0b3-266">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4b0b3-267">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4b0b3-268"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4b0b3-269">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4b0b3-270">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4b0b3-271">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4b0b3-272">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4b0b3-273">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4b0b3-274">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4b0b3-275">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4b0b3-276">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-276">Install Postman</span></span>

<span data-ttu-id="4b0b3-277">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4b0b3-278">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4b0b3-279">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-279">Start the web app.</span></span>
* <span data-ttu-id="4b0b3-280">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-280">Start Postman.</span></span>
* <span data-ttu-id="4b0b3-281">**SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="4b0b3-282">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4b0b3-283">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4b0b3-284">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4b0b3-285">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-285">Create a new request.</span></span>
* <span data-ttu-id="4b0b3-286">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4b0b3-287">URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4b0b3-288">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4b0b3-289">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="4b0b3-290">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4b0b3-291">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4b0b3-292">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4b0b3-293">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-293">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="4b0b3-295">Postman을 사용하여 위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="4b0b3-296">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4b0b3-297">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-297">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="4b0b3-299">HTTP 메서드를 `GET`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="4b0b3-300">URI를 `https://localhost:<port>/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="4b0b3-301">예: `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="4b0b3-302">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4b0b3-303">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="4b0b3-303">Examine the GET methods</span></span>

<span data-ttu-id="4b0b3-304">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4b0b3-305">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4b0b3-306">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="4b0b3-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="4b0b3-307">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4b0b3-308">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-308">Test Get with Postman</span></span>

* <span data-ttu-id="4b0b3-309">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-309">Create a new request.</span></span>
* <span data-ttu-id="4b0b3-310">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4b0b3-311">요청 URI를 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4b0b3-312">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4b0b3-313">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4b0b3-314">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-314">Select **Send**.</span></span>

<span data-ttu-id="4b0b3-315">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-315">This app uses an in-memory database.</span></span> <span data-ttu-id="4b0b3-316">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4b0b3-317">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4b0b3-318">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="4b0b3-318">Routing and URL paths</span></span>

<span data-ttu-id="4b0b3-319">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4b0b3-320">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4b0b3-321">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4b0b3-322">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4b0b3-323">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems**Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4b0b3-324">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4b0b3-325">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4b0b3-326">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-326">This sample doesn't use a template.</span></span> <span data-ttu-id="4b0b3-327">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4b0b3-328">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4b0b3-329">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4b0b3-330">반환 값</span><span class="sxs-lookup"><span data-stu-id="4b0b3-330">Return values</span></span>

<span data-ttu-id="4b0b3-331">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4b0b3-332">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4b0b3-333">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4b0b3-334">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4b0b3-335">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4b0b3-336">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4b0b3-337">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="4b0b3-338">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4b0b3-339">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4b0b3-340">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="4b0b3-340">The PutTodoItem method</span></span>

<span data-ttu-id="4b0b3-341">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4b0b3-342">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4b0b3-343">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4b0b3-344">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4b0b3-345">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4b0b3-346">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4b0b3-347">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="4b0b3-348">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4b0b3-349">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4b0b3-350">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4b0b3-351">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4b0b3-352">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-352">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4b0b3-354">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="4b0b3-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="4b0b3-355">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4b0b3-356">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4b0b3-357">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4b0b3-358">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4b0b3-359">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="4b0b3-360">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="4b0b3-361">과도한 게시 방지</span><span class="sxs-lookup"><span data-stu-id="4b0b3-361">Prevent over-posting</span></span>

<span data-ttu-id="4b0b3-362">현재 샘플 앱은 전체 `TodoItem` 개체를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="4b0b3-363">일반적으로 프로덕션 앱은 모델의 하위 집합을 사용하여 입력 및 반환되는 데이터를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="4b0b3-364">이 동작에는 여러 가지 이유가 있으며, 보안이 주요 이유 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="4b0b3-365">일반적으로 모델의 하위 집합을 DTO(데이터 전송 개체), 입력 모델 또는 뷰 모델이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="4b0b3-366">이 문서에서는 **DTO**를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="4b0b3-367">DTO는 다음과 같은 용도로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-367">A DTO may be used to:</span></span>

* <span data-ttu-id="4b0b3-368">과도한 게시를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-368">Prevent over-posting.</span></span>
* <span data-ttu-id="4b0b3-369">클라이언트에 표시되지 않아야 하는 속성을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="4b0b3-370">페이로드 크기를 줄이기 위해 일부 속성을 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="4b0b3-371">중첩된 개체를 포함하는 개체 그래프를 평면화합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="4b0b3-372">클라이언트에는 평면화된 개체 그래프가 더 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="4b0b3-373">DTO 방법을 설명하려면 비밀 필드를 포함하도록 `TodoItem` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="4b0b3-374">이 앱에서는 숨겨진 필드를 숨겨야 하지만, 관리 앱은 숨겨진 필드를 공개할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="4b0b3-375">비밀 필드를 게시하고 가져올 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="4b0b3-376">DTO 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="4b0b3-377">`TodoItemDTO`를 사용하도록 `TodoItemsController`를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="4b0b3-378">비밀 필드를 게시하거나 가져올 수 없음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4b0b3-379">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="4b0b3-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="4b0b3-380">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b0b3-381">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4b0b3-382">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-382">Create a web API project.</span></span>
> * <span data-ttu-id="4b0b3-383">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4b0b3-384">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-384">Add a controller.</span></span>
> * <span data-ttu-id="4b0b3-385">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="4b0b3-386">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="4b0b3-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4b0b3-387">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="4b0b3-387">Specify return values.</span></span>
> * <span data-ttu-id="4b0b3-388">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4b0b3-389">JavaScript를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="4b0b3-390">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="4b0b3-391">개요</span><span class="sxs-lookup"><span data-stu-id="4b0b3-391">Overview</span></span>

<span data-ttu-id="4b0b3-392">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4b0b3-393">API</span><span class="sxs-lookup"><span data-stu-id="4b0b3-393">API</span></span> | <span data-ttu-id="4b0b3-394">설명</span><span class="sxs-lookup"><span data-stu-id="4b0b3-394">Description</span></span> | <span data-ttu-id="4b0b3-395">요청 본문</span><span class="sxs-lookup"><span data-stu-id="4b0b3-395">Request body</span></span> | <span data-ttu-id="4b0b3-396">응답 본문</span><span class="sxs-lookup"><span data-stu-id="4b0b3-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4b0b3-397">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4b0b3-397">GET /api/TodoItems</span></span> | <span data-ttu-id="4b0b3-398">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-398">Get all to-do items</span></span> | <span data-ttu-id="4b0b3-399">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-399">None</span></span> | <span data-ttu-id="4b0b3-400">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="4b0b3-400">Array of to-do items</span></span>|
|<span data-ttu-id="4b0b3-401">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4b0b3-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4b0b3-402">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-402">Get an item by ID</span></span> | <span data-ttu-id="4b0b3-403">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-403">None</span></span> | <span data-ttu-id="4b0b3-404">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-404">To-do item</span></span>|
|<span data-ttu-id="4b0b3-405">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4b0b3-405">POST /api/TodoItems</span></span> | <span data-ttu-id="4b0b3-406">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-406">Add a new item</span></span> | <span data-ttu-id="4b0b3-407">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-407">To-do item</span></span> | <span data-ttu-id="4b0b3-408">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-408">To-do item</span></span> |
|<span data-ttu-id="4b0b3-409">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4b0b3-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4b0b3-410">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b0b3-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4b0b3-411">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="4b0b3-411">To-do item</span></span> | <span data-ttu-id="4b0b3-412">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-412">None</span></span> |
|<span data-ttu-id="4b0b3-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b0b3-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4b0b3-414">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4b0b3-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4b0b3-415">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-415">None</span></span> | <span data-ttu-id="4b0b3-416">없음</span><span class="sxs-lookup"><span data-stu-id="4b0b3-416">None</span></span>|

<span data-ttu-id="4b0b3-417">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-417">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4b0b3-423">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="4b0b3-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-426">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4b0b3-427">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-429">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4b0b3-430">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4b0b3-431">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4b0b3-432">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2**가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4b0b3-433">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4b0b3-434">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-434">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b0b3-437">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4b0b3-438">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4b0b3-439">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4b0b3-440">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4b0b3-441">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-442">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b0b3-443">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-443">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4b0b3-445">Mac용 Visual Studio 버전 8.6 미만에서 **.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="4b0b3-446">버전 8.6 이상에서 **웹 및 콘솔** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="4b0b3-447">**새 ASP.NET Core Web API 구성** 대화 상자에서 최신 .NET Core 2.x **대상 프레임워크**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="4b0b3-448">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-448">Select **Next**.</span></span>

* <span data-ttu-id="4b0b3-449">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4b0b3-451">API 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-451">Test the API</span></span>

<span data-ttu-id="4b0b3-452">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-452">The project template creates a `values` API.</span></span> <span data-ttu-id="4b0b3-453">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b0b3-455">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4b0b3-456">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4b0b3-457">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4b0b3-458">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4b0b3-460">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4b0b3-461">브라우저에서 `https://localhost:5001/api/values` URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-462">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b0b3-463">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4b0b3-464">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4b0b3-465">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4b0b3-466">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="4b0b3-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4b0b3-467">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4b0b3-468">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-468">Add a model class</span></span>

<span data-ttu-id="4b0b3-469">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4b0b3-470">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-472">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4b0b3-473">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b0b3-474">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="4b0b3-475">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4b0b3-476">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4b0b3-477">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4b0b3-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b0b3-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b0b3-479">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4b0b3-480">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-481">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b0b3-482">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-482">Right-click the project.</span></span> <span data-ttu-id="4b0b3-483">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b0b3-484">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-484">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4b0b3-486">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4b0b3-487">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4b0b3-488">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4b0b3-489">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4b0b3-490">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4b0b3-491">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-491">Add a database context</span></span>

<span data-ttu-id="4b0b3-492">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4b0b3-493">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-495">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4b0b3-496">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-497">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4b0b3-498">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4b0b3-499">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4b0b3-500">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="4b0b3-500">Register the database context</span></span>

<span data-ttu-id="4b0b3-501">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4b0b3-502">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="4b0b3-503">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4b0b3-504">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="4b0b3-504">The preceding code:</span></span>

* <span data-ttu-id="4b0b3-505">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4b0b3-506">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4b0b3-507">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4b0b3-508">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-510">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4b0b3-511">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4b0b3-512">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4b0b3-513">클래스 이름을 *TodoController*로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-515">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4b0b3-516">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4b0b3-517">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4b0b3-518">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="4b0b3-518">The preceding code:</span></span>

* <span data-ttu-id="4b0b3-519">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4b0b3-520">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="4b0b3-521">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4b0b3-522">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4b0b3-523">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4b0b3-524">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4b0b3-525">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4b0b3-526">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4b0b3-527">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4b0b3-528">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4b0b3-529">GET 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-529">Add Get methods</span></span>

<span data-ttu-id="4b0b3-530">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4b0b3-531">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4b0b3-532">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-532">Stop the app if it's still running.</span></span> <span data-ttu-id="4b0b3-533">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4b0b3-534">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4b0b3-535">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="4b0b3-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4b0b3-536">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4b0b3-537">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="4b0b3-537">Routing and URL paths</span></span>

<span data-ttu-id="4b0b3-538">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4b0b3-539">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4b0b3-540">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4b0b3-541">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4b0b3-542">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4b0b3-543">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4b0b3-544">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4b0b3-545">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-545">This sample doesn't use a template.</span></span> <span data-ttu-id="4b0b3-546">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4b0b3-547">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4b0b3-548">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4b0b3-549">반환 값</span><span class="sxs-lookup"><span data-stu-id="4b0b3-549">Return values</span></span>

<span data-ttu-id="4b0b3-550">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4b0b3-551">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4b0b3-552">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4b0b3-553">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4b0b3-554">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4b0b3-555">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4b0b3-556">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="4b0b3-557">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4b0b3-558">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4b0b3-559">GetTodoItems 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="4b0b3-560">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4b0b3-561">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="4b0b3-562">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-562">Start the web app.</span></span>
* <span data-ttu-id="4b0b3-563">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-563">Start Postman.</span></span>
* <span data-ttu-id="4b0b3-564">**SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4b0b3-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4b0b3-566">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4b0b3-567">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b0b3-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4b0b3-568">**Postman** > **기본 설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="4b0b3-569">또는 렌치를 선택하고 **설정**을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="4b0b3-570">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4b0b3-571">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-571">Create a new request.</span></span>
  * <span data-ttu-id="4b0b3-572">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4b0b3-573">요청 URI를 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4b0b3-574">예: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4b0b3-575">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4b0b3-576">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-576">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4b0b3-578">메서드 만들기 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-578">Add a Create method</span></span>

<span data-ttu-id="4b0b3-579">*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4b0b3-580">위의 코드는 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="4b0b3-581">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4b0b3-582">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4b0b3-583">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4b0b3-584">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4b0b3-585">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4b0b3-586">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4b0b3-587">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4b0b3-588">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4b0b3-589">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4b0b3-590">PostTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4b0b3-591">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-591">Build the project.</span></span>
* <span data-ttu-id="4b0b3-592">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4b0b3-593">URI를 `https://localhost:<port>/api/TodoItem`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="4b0b3-594">예: `https://localhost:5001/api/TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="4b0b3-595">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="4b0b3-596">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4b0b3-597">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4b0b3-598">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4b0b3-599">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-599">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="4b0b3-601">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4b0b3-602">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-602">Test the location header URI</span></span>

* <span data-ttu-id="4b0b3-603">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4b0b3-604">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-604">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4b0b3-606">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-606">Set the method to GET.</span></span>
<span data-ttu-id="4b0b3-607">\* URI를  `https://localhost:<port>/api/TodoItems/2`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="4b0b3-608"> 예:  `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="4b0b3-609">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4b0b3-610">PutTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="4b0b3-611">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4b0b3-612">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4b0b3-613">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4b0b3-614">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4b0b3-615">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4b0b3-616">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4b0b3-617">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="4b0b3-618">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="4b0b3-619">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4b0b3-620">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4b0b3-621">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 "feed fish"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4b0b3-622">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-622">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4b0b3-624">DeleteTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4b0b3-625">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4b0b3-626">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4b0b3-627">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4b0b3-628">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4b0b3-629">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4b0b3-630">삭제할 개체의 URI를 설정합니다(예: `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="4b0b3-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="4b0b3-631">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-631">Select **Send**.</span></span>

<span data-ttu-id="4b0b3-632">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4b0b3-633">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4b0b3-634">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="4b0b3-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="4b0b3-635">이 섹션에는 JavaScript를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="4b0b3-636">jQuery가 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-636">jQuery initiates the request.</span></span> <span data-ttu-id="4b0b3-637">JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="4b0b3-638">다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)하고 [기본 파일 매핑을 사용](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4b0b3-639">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4b0b3-640">*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4b0b3-641">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4b0b3-642">*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4b0b3-643">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4b0b3-644">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4b0b3-645">*Properties\launchSettings.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4b0b3-646">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4b0b3-647">이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="4b0b3-648">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4b0b3-649">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="4b0b3-649">Get a list of to-do items</span></span>

<span data-ttu-id="4b0b3-650">jQuery는 할 일 항목의 배열을 나타내는 JSON을 반환하는 웹 API에 HTTP GET 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4b0b3-651">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4b0b3-652">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4b0b3-653">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-653">Add a to-do item</span></span>

<span data-ttu-id="4b0b3-654">jQuery는 요청 본문에 있는 할 일 항목을 사용하여 HTTP POST 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="4b0b3-655">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4b0b3-656">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4b0b3-657">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4b0b3-658">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="4b0b3-658">Update a to-do item</span></span>

<span data-ttu-id="4b0b3-659">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4b0b3-660">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4b0b3-661">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="4b0b3-661">Delete a to-do item</span></span>

<span data-ttu-id="4b0b3-662">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="4b0b3-663">웹 API에 인증 지원 추가</span><span class="sxs-lookup"><span data-stu-id="4b0b3-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="4b0b3-664">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4b0b3-664">Additional resources</span></span>

<span data-ttu-id="4b0b3-665">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="4b0b3-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4b0b3-666">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4b0b3-667">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4b0b3-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4b0b3-668">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="4b0b3-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
