---
title: 2부. ASP.NET Core에서 Razor Pages 앱에 모델 추가
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 2부입니다.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4099873142b99afb7f0659dfd9a4fde8bec3081d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633775"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="2f213-103">2부. ASP.NET Core에서 Razor Pages 앱에 모델 추가</span><span class="sxs-lookup"><span data-stu-id="2f213-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="2f213-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2f213-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2f213-105">이 섹션에서는 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="2f213-106">이 앱의 모델 클래스는 [EF Core(Entity Framework Core)](/ef/core)를 사용하여 데이터베이스 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="2f213-107">EF Core는 데이터 액세스를 간소화하는 O/RM(개체-관계형 매퍼)입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="2f213-108">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2f213-109">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2f213-110">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="2f213-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f213-112">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2f213-113">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-113">Name the folder *Models*.</span></span>

<span data-ttu-id="2f213-114">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-114">Right click the *Models* folder.</span></span> <span data-ttu-id="2f213-115">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="2f213-116">클래스의 이름을 **동영상**으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2f213-118">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2f213-119">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-120">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2f213-121">Solution Pad에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더...** 를 선택합니다. 폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="2f213-122">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="2f213-123">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="2f213-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2f213-124">왼쪽 창에서 **일반**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2f213-125">가운데 창에서 **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2f213-126">클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2f213-127">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2f213-128">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="2f213-128">Scaffold the movie model</span></span>

<span data-ttu-id="2f213-129">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2f213-130">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f213-132">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2f213-133">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2f213-134">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-134">Name the folder *Movies*</span></span>

<span data-ttu-id="2f213-135">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="2f213-137">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="2f213-139">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2f213-140">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2f213-141">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름을 RazorPagesMovie.**Models**.RazorPagesMovieContext에서 RazorPagesMovie.**Data**.RazorPagesMovieContext로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2f213-142">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2f213-143">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2f213-144">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-144">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/3/arp.png)

<span data-ttu-id="2f213-146">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2f213-148">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="2f213-149">스캐폴딩 도구를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="2f213-150">**Windows의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2f213-151">**macOS 및 Linux의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-152">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f213-153">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2f213-154">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2f213-155">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-155">Name the folder *Movies*</span></span>

<span data-ttu-id="2f213-156">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 스캐폴딩...** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

<span data-ttu-id="2f213-158">**새 스캐폴딩** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2f213-160">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2f213-161">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택하거나 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2f213-162">**데이터 컨텍스트 클래스** 행에 새 클래스의 이름 RazorPagesMovie.**Data**.RazorPagesMovieContext를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2f213-163">[이 변경은](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) 필수는 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2f213-164">올바른 네임스페이스를 사용하여 데이터베이스 컨텍스트 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2f213-165">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-165">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

<span data-ttu-id="2f213-167">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="2f213-168">EF 도구 추가</span><span class="sxs-lookup"><span data-stu-id="2f213-168">Add EF tools</span></span>

<span data-ttu-id="2f213-169">다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="2f213-170">위의 명령은 .NET Core CLI에 대한 Entity Framework Core 도구를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="2f213-171">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="2f213-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f213-173">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2f213-174">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2f213-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2f213-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2f213-176">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="2f213-176">Updated</span></span>

* <span data-ttu-id="2f213-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2f213-177">*Startup.cs*</span></span>

<span data-ttu-id="2f213-178">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-179">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f213-180">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2f213-181">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2f213-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2f213-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2f213-183">업데이트됨</span><span class="sxs-lookup"><span data-stu-id="2f213-183">Updated</span></span>

* <span data-ttu-id="2f213-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2f213-184">*Startup.cs*</span></span>

<span data-ttu-id="2f213-185">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2f213-187">스캐폴드 프로세스는 다음 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2f213-188">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2f213-189">생성된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2f213-190">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="2f213-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f213-192">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2f213-193">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-193">Add an initial migration.</span></span>
* <span data-ttu-id="2f213-194">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="2f213-195">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2f213-197">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-199">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="2f213-200">이전 명령은 다음 경고를 생성합니다. "엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2f213-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="2f213-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2f213-202">'HasColumnType()'를 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다."</span><span class="sxs-lookup"><span data-stu-id="2f213-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2f213-203">이 경고는 무시할 수 있으며 자습서의 뒷부분에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="2f213-204">migrations 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="2f213-205">이 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2f213-206">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2f213-207">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2f213-208">`update` 명령은 적용되지 않은 마이그레이션에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2f213-209">이 경우 `update`는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2f213-211">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="2f213-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2f213-212">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2f213-213">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2f213-214">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2f213-215">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2f213-216">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2f213-217">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2f213-218">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2f213-219">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2f213-220">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2f213-221">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2f213-222">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2f213-223">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2f213-224">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2f213-225">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2f213-226">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2f213-228">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-229">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f213-230">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2f213-231">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-231">Test the app</span></span>

* <span data-ttu-id="2f213-232">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="2f213-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2f213-233">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="2f213-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2f213-234">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2f213-235">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-235">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2f213-237">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2f213-238">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2f213-239">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f213-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2f213-240">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2f213-241">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f213-242">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f213-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2f213-243">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2f213-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2f213-244">이 섹션에서는 플랫폼 간 [SQLite 데이터베이스](https://www.sqlite.org/index.html)에서 동영상을 관리하기 위한 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="2f213-245">ASP.NET Core 템플릿으로 만든 앱이 SQLite 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="2f213-246">앱의 모델 클래스를 [EF Core(Entity Framework Core)](/ef/core)([SQLite EF Core 데이터베이스 공급자](/ef/core/providers/sqlite))와 함께 사용하여 데이터베이스를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="2f213-247">EF Core는 데이터 액세스를 간소화하는 ORM(개체-관계형 매핑) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="2f213-248">모델 클래스는 EF Core에 대한 종속성이 없으므로 POCO(Plain Old CLR Object) 클래스로 알려져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2f213-249">이 클래스는 데이터베이스에 저장되는 데이터의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2f213-250">데이터 모델 추가</span><span class="sxs-lookup"><span data-stu-id="2f213-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f213-252">**RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2f213-253">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-253">Name the folder *Models*.</span></span>

<span data-ttu-id="2f213-254">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-254">Right click the *Models* folder.</span></span> <span data-ttu-id="2f213-255">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="2f213-256">클래스의 이름을 **동영상**으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2f213-258">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2f213-259">*Models* 폴더에 *Movie.cs* 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-260">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2f213-261">솔루션 탐색기에서 **RazorPagesMovie** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="2f213-262">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="2f213-263">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="2f213-264">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="2f213-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2f213-265">왼쪽 창에서 **일반**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2f213-266">가운데 창에서 **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2f213-267">클래스 이름을 **Movie**로 지정하고 **새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2f213-268">프로젝트를 빌드하여 컴파일 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2f213-269">영화 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="2f213-269">Scaffold the movie model</span></span>

<span data-ttu-id="2f213-270">이 섹션에서는 영화 모델을 스캐폴드 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2f213-271">즉, 스캐폴드 도구로 영화 모델에 대한 CRUD(생성, 읽기, 수정 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f213-273">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2f213-274">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2f213-275">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-275">Name the folder *Movies*</span></span>

<span data-ttu-id="2f213-276">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/sca.png)

<span data-ttu-id="2f213-278">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffold.png)

<span data-ttu-id="2f213-280">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="2f213-281">**모델 클래스** 드롭다운에서 **동영상(RazorPagesMovie.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2f213-282">**데이터 컨텍스트 클래스** 행에서 **+** 기호를 선택하고 생성된 이름인 **RazorPagesMovie.Models.RazorPagesMovieContext**를 수용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2f213-283">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-283">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arp.png)

<span data-ttu-id="2f213-285">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2f213-287">프로젝트 디렉터리(*Program.cs*, *Startup.cs* 및 *.csproj* 파일이 포함된 디렉터리)에서 명령 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="2f213-288">**Windows의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2f213-289">**macOS 및 Linux의 경우**: 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-290">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f213-291">*Pages/Movies* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2f213-292">*Pages* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2f213-293">폴더 이름을 *Movies*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-293">Name the folder *Movies*</span></span>

<span data-ttu-id="2f213-294">*Pages/Movies* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드된 새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![이전 지침의 이미지입니다.](model/_static/scaMac.png)

<span data-ttu-id="2f213-296">**새 스캐폴딩 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2f213-298">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2f213-299">**모델 클래스** 드롭다운에서 **동영상**을 선택하거나 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="2f213-300">**데이터 컨텍스트 클래스** 행에 **RazorPagesMovieContext**를 입력하거나 선택합니다. 그러면 올바른 네임스페이스의 새 db 컨텍스트 클래스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="2f213-301">이 경우 클래스는 **RazorPagesMovie.Models.RazorPagesMovieContext**입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2f213-302">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-302">Select **Add**.</span></span>

![이전 지침의 이미지입니다.](model/_static/arpMac.png)

<span data-ttu-id="2f213-304">*appsettings.json* 파일을 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="2f213-305">스캐폴드 프로세스는 다음 파일을 생성하고 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2f213-306">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="2f213-306">Files created</span></span>

* <span data-ttu-id="2f213-307">*Pages/Movies*: 만들기, 삭제, 세부 정보, 편집 및 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2f213-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2f213-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2f213-309">파일 업데이트됨</span><span class="sxs-lookup"><span data-stu-id="2f213-309">File updated</span></span>

* <span data-ttu-id="2f213-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2f213-310">*Startup.cs*</span></span>

<span data-ttu-id="2f213-311">생성되고 업데이트된 파일은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2f213-312">초기 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="2f213-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f213-314">이 섹션에서는 PMC(패키지 관리자 콘솔)를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2f213-315">초기 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-315">Add an initial migration.</span></span>
* <span data-ttu-id="2f213-316">초기 마이그레이션을 사용하여 데이터베이스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="2f213-317">**도구** 메뉴에서 **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 메뉴](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2f213-319">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2f213-320">`Add-Migration` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2f213-321">스키마는 `DbContext`에 지정된 모델을 기반으로 합니다( *RazorPagesMovieContext.cs* 파일).</span><span class="sxs-lookup"><span data-stu-id="2f213-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="2f213-322">`InitialCreate` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="2f213-323">모든 이름을 사용할 수 있지만 규칙에 따라 마이그레이션을 설명하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="2f213-324">자세한 내용은 <xref:data/ef-mvc/migrations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f213-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="2f213-325">`Update-Database` 명령은 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2f213-326">`Up` 메서드는 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-328">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="2f213-329">이전 명령은 다음 경고를 생성합니다. "*엔터티 형식 'Movie'에서 10진수 열 'Price'의 형식이 지정되지 않았습니다. This will cause values to be silently truncated if they do not fit in the default precision and scale. 'HasColumnType()'을 사용하여 모든 값을 수용할 수 있는 SQL Server 열 형식을 명시적으로 지정합니다.* " 이 경고는 무시할 수 있으며 이후 자습서에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2f213-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2f213-331">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="2f213-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2f213-332">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2f213-333">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2f213-334">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2f213-335">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2f213-336">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2f213-337">`Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2f213-338">강조 표시된 줄은 스캐폴더에서 추가된 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="2f213-339">`RazorPagesMovieContext`는 `Movie` 모델에 대한 EF Core 기능(생성, 읽기, 수정, 삭제 등)을 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2f213-340">데이터 컨텍스트(`RazorPagesMovieContext`)는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2f213-341">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2f213-342">위의 코드에서는 엔터티 집합에 대해 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2f213-343">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2f213-344">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2f213-345">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2f213-346">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2f213-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f213-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2f213-348">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2f213-349">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f213-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f213-350">`Up` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2f213-351">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-351">Test the app</span></span>

* <span data-ttu-id="2f213-352">앱을 실행하고 브라우저에서 URL에 `/Movies`를 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="2f213-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2f213-353">오류가 표시될 경우:</span><span class="sxs-lookup"><span data-stu-id="2f213-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2f213-354">[마이그레이션 단계](#pmc)를 누락했을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2f213-355">**Create** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-355">Test the **Create** link.</span></span>

  ![페이지 만들기](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2f213-357">`Price` 필드에 소수점을 입력하지 못할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2f213-358">소수점으로 쉼표(",")를 사용하는 비 영어 로캘 및 비 US-English 날짜 형식에 대해[jQuery 유효성 검사](https://jqueryvalidation.org/)를 지원하려면 앱을 세계화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2f213-359">세계화 지침은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f213-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2f213-360">**Edit**, **Details** 및 **Delete** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2f213-361">다음 자습서에서는 스캐폴딩을 통해 만들어진 파일을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f213-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f213-362">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f213-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2f213-363">[이전: 시작하기](xref:tutorials/razor-pages/razor-pages-start)
> [다음: 스캐폴드된 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2f213-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
