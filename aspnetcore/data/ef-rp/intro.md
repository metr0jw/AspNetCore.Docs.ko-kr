---
title: ASP.NET Core에서 Entity Framework Core를 사용한 Razor Pages - 자습서 1/8
author: rick-anderson
description: Entity Framework Core를 사용하여 Razor Pages 앱을 만드는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: data/ef-rp/intro
ms.openlocfilehash: cd6624d107fb19da92a7e58a747cc85e876a6ba4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018639"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="d8f07-103">ASP.NET Core에서 Entity Framework Core를 사용한 Razor Pages - 자습서 1/8</span><span class="sxs-lookup"><span data-stu-id="d8f07-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="d8f07-104">작성자: [Tom Dykstra](https://github.com/tdykstra) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8f07-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8f07-105">[ASP.NET Core Razor Pages](xref:razor-pages/index) 앱에서 EF(Entity Framework) Core를 사용하는 방법을 보여 주는 자습서 시리즈 중 첫 번째 자습서입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="d8f07-106">이 자습서에서는 가상 Contoso 대학의 웹 사이트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="d8f07-107">이 사이트에는 학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="d8f07-108">이 자습서에서는 Code First 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="d8f07-109">Database First 방법을 사용하여 이 자습서를 수행하는 데 대한 정보는 [이 Github 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16897)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="d8f07-110">완성된 앱을 다운로드하거나 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="d8f07-111">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="d8f07-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8f07-112">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d8f07-112">Prerequisites</span></span>

* <span data-ttu-id="d8f07-113">Razor Pages를 처음 사용한다면 이 자습서를 시작하기 전에 Razor [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start) 자습서 시리즈를 진행하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="d8f07-116">데이터베이스 엔진</span><span class="sxs-lookup"><span data-stu-id="d8f07-116">Database engines</span></span>

<span data-ttu-id="d8f07-117">Visual Studio 지침에서는 Windows에서만 실행되는 SQL Server Express 버전인 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="d8f07-118">Visual Studio Code 지침에서는 플랫폼 간 데이터베이스 엔진인 [SQLite](https://www.sqlite.org/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="d8f07-119">SQLite를 사용하도록 선택하는 경우 [SQLite용 DB 브라우저](https://sqlitebrowser.org/)와 같이 SQLite 데이터베이스를 관리하고 볼 수 있는 타사 도구를 다운로드하여 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d8f07-120">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d8f07-120">Troubleshooting</span></span>

<span data-ttu-id="d8f07-121">해결할 수 없는 문제가 발생한 경우 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="d8f07-122">도움을 얻으려면 [ASP.NET Core 태그](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core 태그](https://stackoverflow.com/questions/tagged/entity-framework-core)를 사용하여 StackOverflow.com에 질문을 게시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="d8f07-123">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="d8f07-123">The sample app</span></span>

<span data-ttu-id="d8f07-124">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="d8f07-125">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="d8f07-126">자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-126">Here are a few of the screens created in the tutorial.</span></span>

![학생 인덱스 페이지](intro/_static/students-index30.png)

![학생 편집 페이지](intro/_static/student-edit30.png)

<span data-ttu-id="d8f07-129">이 사이트의 UI 스타일은 기본 제공 프로젝트 템플릿을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="d8f07-130">이 자습서에서는 UI를 사용자 지정하는 방법이 아닌 EF Core를 사용하는 방법을 중점적으로 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="d8f07-131">페이지 맨 위에 있는 링크를 따라 완료된 프로젝트의 소스 코드를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="d8f07-132">*cu30* 폴더에는 자습서의 ASP.NET Core 3.0 버전에 대한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="d8f07-133">자습서 1~7의 코드 상태를 반영하는 파일은 *cu30snapshots* 폴더에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8f07-135">완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="d8f07-136">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-136">Build the project.</span></span>
* <span data-ttu-id="d8f07-137">PMC(패키지 관리자 콘솔)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-137">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="d8f07-138">프로젝트를 실행하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-138">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8f07-140">완료된 프로젝트를 다운로드한 후 앱을 실행하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-140">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="d8f07-141">*ContosoUniversity.csproj*를 삭제하고 *ContosoUniversitySQLite.csproj*의 이름을 *ContosoUniversity.csproj*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-141">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="d8f07-142">*Startup.cs*를 삭제하고 *StartupSQLite.cs*의 이름을 *Startup.cs*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-142">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="d8f07-143">*appSettings.json*을 삭제하고 *appSettingsSQLite.json*의 이름을 *appSettings.json*으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-143">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="d8f07-144">*Migrations* 폴더를 삭제하고 *MigrationsSQL*의 이름을 *Migrations*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-144">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="d8f07-145">`#if SQLiteVersion`에 대한 전체 검색을 수행하고 `#if SQLiteVersion` 및 관련된 `#endif` 문을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-145">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="d8f07-146">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-146">Build the project.</span></span>
* <span data-ttu-id="d8f07-147">프로젝트 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="d8f07-148">SQLite 도구에서 다음 SQL 문을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="d8f07-149">프로젝트를 실행하여 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="d8f07-150">웹앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="d8f07-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8f07-152">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d8f07-153">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="d8f07-154">프로젝트 이름을 *ContosoUniversity*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="d8f07-155">코드를 복사하여 붙여넣을 때 네임스페이스가 일치하도록 대문자 표시를 포함하여 정확한 이름을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="d8f07-156">드롭다운에서 **.NET Core** 및 **ASP.NET Core 3.0**을 선택한 후 **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8f07-158">터미널에서 프로젝트 폴더를 만들어야 하는 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="d8f07-159">다음 명령을 실행하여 Razor Pages 프로젝트를 만들고 `cd`를 사용하여 새 프로젝트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="d8f07-160">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="d8f07-160">Set up the site style</span></span>

<span data-ttu-id="d8f07-161">*Pages/Shared/_Layout.cshtml*을 업데이트하여 사이트 헤더, 바닥글 및 메뉴를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="d8f07-162">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="d8f07-163">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-163">There are three occurrences.</span></span>

* <span data-ttu-id="d8f07-164">**홈** 및 **프라이버시** 메뉴 항목을 삭제하고 **정보**, **학생**, **과정**, **강사** 및 **부서** 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="d8f07-165">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="d8f07-166">*Pages/Index.cshtml*에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET Core에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="d8f07-167">앱을 실행하여 홈페이지가 표시되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="d8f07-168">데이터 모델</span><span class="sxs-lookup"><span data-stu-id="d8f07-168">The data model</span></span>

<span data-ttu-id="d8f07-169">다음 섹션에서는 데이터 모델을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-169">The following sections create a data model:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="d8f07-171">학생은 개수와 관계없이 원하는 과정에 등록할 수 있으며 한 과정에는 여러 명이 등록될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="d8f07-172">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="d8f07-172">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

* <span data-ttu-id="d8f07-174">프로젝트 폴더에 *Models* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="d8f07-175">다음 코드로 *Models/Student.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="d8f07-176">`ID` 속성은 이 클래스에 해당하는 데이터베이스 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="d8f07-177">기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="d8f07-178">따라서 `Student` 클래스 기본 키의 자동으로 인식되는 대체 이름은 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="d8f07-179">자세한 내용은 [EF Core - 키](/ef/core/modeling/keys?tabs=data-annotations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="d8f07-180">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="d8f07-181">탐색 속성은 이 엔터티와 관련된 다른 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="d8f07-182">이 경우 `Student` 엔터티의 `Enrollments` 속성은 해당 Student에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="d8f07-183">예를 데이터베이스의 Student 행에 두 개의 관련 Enrollment 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 Enrollment 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="d8f07-184">데이터베이스에서 StudentID 열에 학생 ID 값이 포함된 경우 Enrollment 행은 Student 행과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="d8f07-185">예를 들어 Student 행에 ID=1이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="d8f07-186">관련 Enrollment 행에는 StudentID=1이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="d8f07-187">StudentID는 Enrollment 테이블의 ‘외래 키’입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="d8f07-188">관련 Enrollment 엔터티가 여러 개 있을 수 있으므로 `Enrollments` 속성은 `ICollection<Enrollment>`로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="d8f07-189">`List<Enrollment>` 또는`HashSet<Enrollment>`와 같은 다른 컬렉션 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="d8f07-190">`ICollection<Enrollment>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<Enrollment>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="d8f07-191">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="d8f07-191">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="d8f07-193">다음 코드로 *Models/Enrollment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="d8f07-194">`EnrollmentID` 속성은 기본 키입니다. 이 엔터티는 자체적으로 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="d8f07-195">프로덕션 데이터 모델의 경우 하나의 패턴을 선택하고 일관되게 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="d8f07-196">이 자습서에서는 두 항목이 작동하는 것을 보여 주기 위해 둘 다 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="d8f07-197">`classname` 없이 `ID`를 사용하면 특정 종류의 데이터 모델 변경 내용을 더 쉽게 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="d8f07-198">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="d8f07-199">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/)이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="d8f07-200">Null인 등급은 0등급과는 다릅니다. Null은 알 수 없거나 아직 할당되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="d8f07-201">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="d8f07-202">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="d8f07-203">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="d8f07-204">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="d8f07-205">EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="d8f07-206">예를 들어 `Student` 엔터티의 기본 키는 `ID`이므로 `StudentID`는 `Student` 탐색 속성의 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="d8f07-207">외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="d8f07-208">예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="d8f07-209">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="d8f07-209">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="d8f07-211">다음 코드로 *Models/Course.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="d8f07-212">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="d8f07-213">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="d8f07-214">`DatabaseGenerated` 특성을 사용하면 데이터베이스에서 기본 키를 생성하도록 하지 않고 앱에서 기본 키를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="d8f07-215">프로젝트를 빌드하여 컴파일러 오류가 없는지 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="d8f07-216">학생 페이지 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="d8f07-216">Scaffold Student pages</span></span>

<span data-ttu-id="d8f07-217">이 섹션에서는 ASP.NET Core 스캐폴딩 도구를 사용하여 다음을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="d8f07-218">EF Core ‘컨텍스트’ 클래스.</span><span class="sxs-lookup"><span data-stu-id="d8f07-218">An EF Core *context* class.</span></span> <span data-ttu-id="d8f07-219">컨텍스트는 특정 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="d8f07-220">이 클래스는 `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="d8f07-221">`Student` 엔터티에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리하는 Razor 페이지.</span><span class="sxs-lookup"><span data-stu-id="d8f07-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8f07-223">*Pages* 폴더에 *Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="d8f07-224">**솔루션 탐색기**에서 *Pages/Students* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d8f07-225">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="d8f07-226">**Entity Framework(CRUD)를 사용한 Razor Pages 추가** 대화 상자에서 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="d8f07-227">**모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="d8f07-228">**데이터 컨텍스트 클래스** 행에서 **+** (더하기) 기호를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="d8f07-229">데이터 컨텍스트 이름을 *ContosoUniversity.Models.ContosoUniversityContext*에서 *ContosoUniversity.Data.SchoolContext*로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="d8f07-230">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-230">Select **Add**.</span></span>

<span data-ttu-id="d8f07-231">다음 패키지가 자동으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8f07-233">다음 .NET Core CLI 명령을 실행하여 필요한 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="d8f07-234">스캐폴딩에는 Microsoft.VisualStudio.Web.CodeGeneration.Design 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="d8f07-235">앱은 SQL Server를 사용하지 않지만 스캐폴딩 도구에는 SQL Server 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="d8f07-236">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="d8f07-237">다음 명령을 실행하여 [aspnet-codegenerator 스캐폴딩 도구](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="d8f07-238">다음 명령을 실행하여 학생 페이지를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="d8f07-239">**Windows**</span><span class="sxs-lookup"><span data-stu-id="d8f07-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="d8f07-240">**macOS 또는 Linux**</span><span class="sxs-lookup"><span data-stu-id="d8f07-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="d8f07-241">이전 단계에서 문제가 발생하면 프로젝트를 빌드하고 스캐폴드 단계를 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="d8f07-242">스캐폴딩 프로세스:</span><span class="sxs-lookup"><span data-stu-id="d8f07-242">The scaffolding process:</span></span>

* <span data-ttu-id="d8f07-243">*Pages/Students* 폴더에서 다음 Razor 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="d8f07-244">*Create.cshtml* 및 *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d8f07-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="d8f07-245">*Delete.cshtml* 및 *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d8f07-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="d8f07-246">*Details.cshtml* 및 *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d8f07-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="d8f07-247">*Edit.cshtml* 및 *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d8f07-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="d8f07-248">*Index.cshtml* 및 *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="d8f07-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="d8f07-249">*Data/SchoolContext.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="d8f07-250">*Startup.cs*의 종속성 주입에 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="d8f07-251">*appsettings.json*에 데이터베이스 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="d8f07-252">데이터베이스 연결 문자열</span><span class="sxs-lookup"><span data-stu-id="d8f07-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8f07-254">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="d8f07-255">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="d8f07-256">기본적으로 LocalDB는 `C:/Users/<user>` 디렉터리에 *.mdf* 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8f07-258">*CU.db*라는 SQLite 데이터베이스 파일을 가리키도록 연결 문자열을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="d8f07-259">데이터베이스 컨텍스트 클래스 업데이트</span><span class="sxs-lookup"><span data-stu-id="d8f07-259">Update the database context class</span></span>

<span data-ttu-id="d8f07-260">특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 데이터베이스 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="d8f07-261">컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="d8f07-262">컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="d8f07-263">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="d8f07-264">*SchoolContext.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="d8f07-265">강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="d8f07-266">EF Core 용어에서:</span><span class="sxs-lookup"><span data-stu-id="d8f07-266">In EF Core terminology:</span></span>

* <span data-ttu-id="d8f07-267">엔터티 집합은 일반적으로 데이터베이스 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="d8f07-268">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d8f07-269">엔터티 집합은 여러 엔터티를 포함하므로 DBSet 속성은 복수형 이름이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="d8f07-270">스캐폴딩 도구로 `Student` DBSet를 만들었으므로 이 단계에서는 이 DBSet를 복수형 `Students`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="d8f07-271">Razor Pages 코드가 새 DBSet 이름과 일치하게 만들려면 전체 프로젝트에서 `_context.Student`를 `_context.Students`로 전체 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="d8f07-272">8개 발생 항목이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-272">There are 8 occurrences.</span></span>

<span data-ttu-id="d8f07-273">프로젝트를 빌드하여 컴파일러 오류가 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="d8f07-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="d8f07-274">Startup.cs</span></span>

<span data-ttu-id="d8f07-275">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8f07-276">서비스(예: EF Core 데이터베이스 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d8f07-277">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d8f07-278">데이터베이스 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d8f07-279">스캐폴딩 도구는 종속성 주입 컨테이너에 컨텍스트 클래스를 자동으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8f07-281">`ConfigureServices`에서 강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8f07-283">`ConfigureServices`에서 스캐폴더에서 추가된 코드가 `UseSqlite`를 호출하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="d8f07-284">연결 문자열 이름은 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체의 메서드를 호출하여 컨텍스트에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d8f07-285">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="d8f07-286">데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="d8f07-286">Create the database</span></span>

<span data-ttu-id="d8f07-287">*Program.cs*를 업데이트하여 데이터베이스가 없는 경우 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="d8f07-288">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 메서드는 컨텍스트의 데이터베이스가 있는 경우 아무 작업도 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="d8f07-289">데이터베이스가 없는 경우 데이터베이스 및 스키마를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="d8f07-290">`EnsureCreated`를 사용하면 다음 워크플로가 데이터 모델 변경 내용을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="d8f07-291">데이터베이스를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-291">Delete the database.</span></span> <span data-ttu-id="d8f07-292">모든 기존 데이터가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-292">Any existing data is lost.</span></span>
* <span data-ttu-id="d8f07-293">데이터 모델을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-293">Change the data model.</span></span> <span data-ttu-id="d8f07-294">예를 들어 `EmailAddress` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="d8f07-295">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-295">Run the app.</span></span>
* <span data-ttu-id="d8f07-296">`EnsureCreated`는 새 스키마를 사용하여 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="d8f07-297">이 워크플로는 데이터를 보존할 필요가 없는 경우 스키마가 빠르게 업데이트되는 개발 초기에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="d8f07-298">데이터베이스에 입력된 데이터를 보존해야 하는 경우는 상황이 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="d8f07-299">이 경우에는 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="d8f07-300">자습서 시리즈의 뒷부분에서는 `EnsureCreated`에서 만든 데이터베이스를 삭제하고 마이그레이션을 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="d8f07-301">`EnsureCreated`에서 만든 데이터베이스는 마이그레이션을 사용하여 업데이트할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="d8f07-302">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-302">Test the app</span></span>

* <span data-ttu-id="d8f07-303">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-303">Run the app.</span></span>
* <span data-ttu-id="d8f07-304">**학생** 링크 및 **새로 만들기**를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="d8f07-305">편집, 세부 정보 및 삭제 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="d8f07-306">데이터베이스 시드</span><span class="sxs-lookup"><span data-stu-id="d8f07-306">Seed the database</span></span>

<span data-ttu-id="d8f07-307">`EnsureCreated` 메서드는 빈 데이터베이스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="d8f07-308">이 섹션에서는 테스트 데이터로 데이터베이스를 채우는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="d8f07-309">다음 코드로 *Data/DbInitializer.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-309">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="d8f07-310">이 코드는 데이터베이스에 학생이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="d8f07-311">학생이 없는 경우 테스트 데이터를 데이터베이스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="d8f07-312">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 만들어 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="d8f07-313">*Program.cs*에서 `EnsureCreated` 호출을 `DbInitializer.Initialize` 호출로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8f07-315">앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8f07-317">실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="d8f07-318">앱을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-318">Restart the app.</span></span>

* <span data-ttu-id="d8f07-319">학생 페이지를 선택하여 시드된 데이터를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="d8f07-320">데이터베이스 보기</span><span class="sxs-lookup"><span data-stu-id="d8f07-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8f07-322">Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="d8f07-323">SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="d8f07-324">데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="d8f07-325">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="d8f07-326">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기**를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="d8f07-327">**Student** 테이블을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭하여 `Student` 모델이 `Student` 테이블 스키마에 어떻게 매핑되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8f07-329">SQLite 도구를 사용하여 데이터베이스 스키마 및 시드된 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="d8f07-330">데이터베이스 파일은 *CU.db*로 이름이 지정되고 프로젝트 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="d8f07-331">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="d8f07-331">Asynchronous code</span></span>

<span data-ttu-id="d8f07-332">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="d8f07-333">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="d8f07-334">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="d8f07-335">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="d8f07-336">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="d8f07-337">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="d8f07-338">비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="d8f07-339">트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="d8f07-340">다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="d8f07-341">`async` 키워드는 컴파일러가 다음을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="d8f07-342">메서드 본문의 부분에 대한 콜백을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="d8f07-343">반환되는 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="d8f07-344">`Task<T>` 반환 형식은 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="d8f07-345">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="d8f07-346">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="d8f07-347">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="d8f07-348">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="d8f07-349">EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="d8f07-350">쿼리 또는 명령을 데이터베이스에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="d8f07-351">여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="d8f07-352">`var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="d8f07-353">EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d8f07-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="d8f07-354">비동기 코드의 성능 이점을 이용하려면 라이브러리 패키지(예: 페이징)가 쿼리를 데이터베이스에 보내는 EF Core 메서드를 호출하는 경우 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="d8f07-355">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d8f07-356">다음 단계</span><span class="sxs-lookup"><span data-stu-id="d8f07-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d8f07-357">다음 자습서</span><span class="sxs-lookup"><span data-stu-id="d8f07-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8f07-358">Contoso University 샘플 웹앱은 EF(Entity Framework) Core를 사용하여 ASP.NET Core Razor Pages 앱을 만드는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-358">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="d8f07-359">샘플 앱은 가상 Contoso University의 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-359">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="d8f07-360">학생 입학, 강좌 개설 및 강사 할당과 같은 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-360">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="d8f07-361">이 페이지는 Contoso University 샘플 앱을 빌드하는 방법을 설명하는 일련의 자습서 중 첫 번째 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-361">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="d8f07-362">완성된 앱을 다운로드하거나 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-362">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="d8f07-363">[지침을 다운로드하세요](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="d8f07-363">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8f07-364">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d8f07-364">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-365">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-366">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-366">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="d8f07-367">[Razor Pages](xref:razor-pages/index) 숙련도.</span><span class="sxs-lookup"><span data-stu-id="d8f07-367">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="d8f07-368">신규 프로그래머는 이 시리즈를 시작하기 전에 [Razor 페이지 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 먼저 완료해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-368">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d8f07-369">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d8f07-369">Troubleshooting</span></span>

<span data-ttu-id="d8f07-370">해결할 수 없는 문제가 발생한 경우 일반적으로 [완료된 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)와 코드를 비교하여 해결책을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="d8f07-371">도움을 얻으려면 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 또는 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)에 대한 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)에 질문을 게시하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-371">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="d8f07-372">Contoso University 웹앱</span><span class="sxs-lookup"><span data-stu-id="d8f07-372">The Contoso University web app</span></span>

<span data-ttu-id="d8f07-373">이러한 자습서에서 빌드한 앱은 기본 대학 웹 사이트입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-373">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="d8f07-374">사용자는 학생, 강좌 및 강사 정보를 보고 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-374">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="d8f07-375">자습서에서 만든 화면 중 몇 가지 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-375">Here are a few of the screens created in the tutorial.</span></span>

![학생 인덱스 페이지](intro/_static/students-index.png)

![학생 편집 페이지](intro/_static/student-edit.png)

<span data-ttu-id="d8f07-378">이 사이트의 UI 스타일은 기본 제공 템플릿에서 생성된 것과 가깝습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-378">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="d8f07-379">이 자습서에서는 UI가 아닌 Razor 페이지를 사용한 EF Core를 중점적으로 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-379">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="d8f07-380">ContosoUniversity Razor Pages 웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="d8f07-380">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-381">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8f07-382">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-382">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d8f07-383">새 ASP.NET Core 웹 애플리케이션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-383">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="d8f07-384">프로젝트 이름을 **ContosoUniversity**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-384">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="d8f07-385">코드를 복사/붙여넣을 때 네임스페이스와 일치할 수 있도록 프로젝트 이름을 *ContosoUniversity*로 지정하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-385">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="d8f07-386">드롭다운에서 **ASP.NET Core 2.1**을 선택한 다음, **웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-386">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="d8f07-387">이전 단계의 이미지는 [Razor 웹앱 만들기](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-387">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="d8f07-388">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-388">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="d8f07-390">사이트 스타일 설정</span><span class="sxs-lookup"><span data-stu-id="d8f07-390">Set up the site style</span></span>

<span data-ttu-id="d8f07-391">몇 가지 변경 내용으로 사이트 메뉴, 레이아웃 및 홈페이지를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-391">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="d8f07-392">*Pages/Shared/_Layout.cshtml*을 다음 변경 내용으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-392">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="d8f07-393">모든 “ContosoUniversity”를 “Contoso University”로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-393">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="d8f07-394">세 번 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-394">There are three occurrences.</span></span>

* <span data-ttu-id="d8f07-395">**학생**, **강좌**, **강사** 및 **부서**에 대한 메뉴 항목을 추가하고 **연락처** 메뉴 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-395">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="d8f07-396">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-396">The changes are highlighted.</span></span> <span data-ttu-id="d8f07-397">(모든 표시가 표시되지는 *않습니다*.)</span><span class="sxs-lookup"><span data-stu-id="d8f07-397">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="d8f07-398">*Pages/Index.cshtml*에서 다음 코드로 파일의 콘텐츠를 텍스트를 대체하여 ASP.NET 및 MVC에 대한 텍스트를 이 앱에 대한 텍스트로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-398">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="d8f07-399">데이터 모델 만들기</span><span class="sxs-lookup"><span data-stu-id="d8f07-399">Create the data model</span></span>

<span data-ttu-id="d8f07-400">Contoso University 앱에 대한 엔터티 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-400">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="d8f07-401">다음과 같은 세 가지 엔터티로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-401">Start with the following three entities:</span></span>

![과정-등록-학생 데이터 모델 다이어그램](intro/_static/data-model-diagram.png)

<span data-ttu-id="d8f07-403">`Student` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-403">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="d8f07-404">`Course` 및 `Enrollment` 엔터티 사이에 일 대 다 관계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-404">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="d8f07-405">학생은 여러 강좌에 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-405">A student can enroll in any number of courses.</span></span> <span data-ttu-id="d8f07-406">강좌는 등록된 학생이 여러 명일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-406">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="d8f07-407">다음 섹션에서 각각에 대한 이러한 엔터티에 대한 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-407">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="d8f07-408">학생 엔터티</span><span class="sxs-lookup"><span data-stu-id="d8f07-408">The Student entity</span></span>

![학생 엔터티 다이어그램](intro/_static/student-entity.png)

<span data-ttu-id="d8f07-410">*모델* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-410">Create a *Models* folder.</span></span> <span data-ttu-id="d8f07-411">*모델* 폴더에서 다음 코드로 *Student.cs*라는 이름의 클래스 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-411">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="d8f07-412">`ID` 속성은 이 클래스에 해당하는 DB(데이터베이스) 테이블의 기본 키 열이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-412">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="d8f07-413">기본적으로 EF 코어는 `ID` 또는 `classnameID`로 명명된 속성을 기본 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-413">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="d8f07-414">`classnameID`에서 `classname`은 클래스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-414">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="d8f07-415">기본 키가 자동으로 인식되는 경우 대안은 앞의 예제에서 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-415">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="d8f07-416">`Enrollments` 속성은 [탐색 속성](/ef/core/modeling/relationships)입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-416">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="d8f07-417">탐색 속성은 이 엔터티와 관련된 다른 엔터티에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-417">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="d8f07-418">이 경우 `Student entity`의 `Enrollments` 속성은 해당 `Student`에 관련된 모든 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-418">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="d8f07-419">예를 들어 DB의 학생 행에 두 개의 관련 등록 행이 있는 경우 `Enrollments` 탐색 속성은 그 두 `Enrollment` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-419">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="d8f07-420">관련된 `Enrollment` 행은 `StudentID` 열에서 해당 학생의 기본 키 값을 포함하는 열입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-420">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="d8f07-421">예를 들어 ID=1인 학생에 `Enrollment` 테이블의 두 개 행이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-421">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="d8f07-422">`Enrollment` 테이블에 `StudentID` = 1인 두 개의 행이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-422">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="d8f07-423">`StudentID`는 `Student` 테이블에서 학생을 지정하는 `Enrollment` 테이블의 외래 키입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-423">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="d8f07-424">탐색 속성이 여러 엔터티를 포함하는 경우 탐색 속성은 `ICollection<T>`와 같은 목록 유형이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-424">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="d8f07-425">`ICollection<T>`는 지정할 수 있으며, `List<T>` 또는 `HashSet<T>`와 같은 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-425">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="d8f07-426">`ICollection<T>`가 사용되는 경우 EF Core는 기본적으로 `HashSet<T>` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-426">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="d8f07-427">여러 엔터티를 포함하는 탐색 속성은 다대다 및 일대다 관계에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-427">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="d8f07-428">등록 엔터티</span><span class="sxs-lookup"><span data-stu-id="d8f07-428">The Enrollment entity</span></span>

![등록 엔터티 다이어그램](intro/_static/enrollment-entity.png)

<span data-ttu-id="d8f07-430">*모델* 폴더에서 다음 코드로 *Enrollment.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-430">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="d8f07-431">`EnrollmentID` 속성은 기본 키입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-431">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="d8f07-432">이 엔터티는 `Student` 엔터티 같은 `ID` 대신 `classnameID` 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-432">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="d8f07-433">일반적으로 개발자는 하나의 패턴을 선택하여 데이터 모델 전체에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-433">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="d8f07-434">자습서의 뒷부분에서는 클래스 이름 없이 ID를 사용하여 더 손쉽게 데이터 모델에서 상속을 구현하는 내용이 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-434">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="d8f07-435">`Grade` 속성은 `enum`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-435">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="d8f07-436">`Grade` 형식 선언 뒤에 있는 물음표는 `Grade` 속성이 nullable이라는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-436">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="d8f07-437">Null인 등급은 0 등급과는 다릅니다. Null은 알려지지 않거나 아직 등록되지 않은 등급을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-437">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="d8f07-438">`StudentID` 속성은 외래 키로, 해당 탐색 속성은 `Student`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-438">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="d8f07-439">`Enrollment` 엔터티는 하나의 `Student` 엔터티와 연결되므로 속성은 단일 `Student` 엔터티를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-439">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="d8f07-440">`Student` 엔터티는 여러 `Enrollment` 엔터티를 포함하는 `Student.Enrollments` 탐색 속성과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-440">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="d8f07-441">`CourseID` 속성은 외래 키로, 해당 탐색 속성은 `Course`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-441">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="d8f07-442">`Enrollment` 엔터티는 하나의 `Course` 엔터티와 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-442">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="d8f07-443">EF Core는 속성 이름이 `<navigation property name><primary key property name>`인 경우 외래 키로 해석합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-443">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="d8f07-444">예를 들어 `Student` 탐색 속성의 경우 `Student` 엔터티의 기본 키가 `ID`이므로 `StudentID`입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-444">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="d8f07-445">외래 키 속성의 이름은 `<primary key property name>`으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-445">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="d8f07-446">예를 들어 `Course` 엔터티의 기본 키가 `CourseID`이므로 `CourseID`라고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-446">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="d8f07-447">강좌 엔터티</span><span class="sxs-lookup"><span data-stu-id="d8f07-447">The Course entity</span></span>

![강좌 엔터티 다이어그램](intro/_static/course-entity.png)

<span data-ttu-id="d8f07-449">*모델* 폴더에서 다음 코드로 *Course.cs*를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-449">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="d8f07-450">`Enrollments` 속성은 탐색 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="d8f07-451">`Course` 엔터티는 `Enrollment` 엔터티의 개수와 관련이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="d8f07-452">앱은 `DatabaseGenerated` 특성을 통해 DB가 생성하도록 하는 대신 기본 키를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-452">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="d8f07-453">학생 모델 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="d8f07-453">Scaffold the student model</span></span>

<span data-ttu-id="d8f07-454">이 섹션에서는 학생 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-454">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="d8f07-455">즉, 스캐폴드 도구는 학생 모델에서 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 위한 페이지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-455">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="d8f07-456">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-456">Build the project.</span></span>
* <span data-ttu-id="d8f07-457">*Pages/Students* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-457">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d8f07-459">**솔루션 탐색기**에서 *Pages/Students* 폴더 > **추가** > **스캐폴드 항목 새로 만들기**를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-459">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d8f07-460">**스캐폴드 추가** 대화 상자에서 **Entity Framework를 사용한 Razor Pages(CRUD)** > **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-460">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="d8f07-461">**Entity Framework(CRUD)를 사용하여 Razor Pages 추가** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-461">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d8f07-462">**모델 클래스** 드롭다운에서 **학생(ContosoUniversity.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-462">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="d8f07-463">**데이터 컨텍스트 클래스** 행에서 **+** (더하기)를 선택 하고 생성된 이름을 서명하고 **ContosoUniversity.Models.SchoolContext**로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-463">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="d8f07-464">**데이터 컨텍스트 클래스** 드롭다운에서 **ContosoUniversity.Models.SchoolContext**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-464">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="d8f07-465">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-465">Select **Add**.</span></span>

![CRUD 대화 상자](intro/_static/s1.png)

<span data-ttu-id="d8f07-467">이전 단계에서 문제가 발생한 경우 [영화 모델 스캐폴드](xref:tutorials/razor-pages/model#scaffold-the-movie-model)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-467">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-468">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-468">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8f07-469">다음 명령을 실행하여 학생 모델을 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-469">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="d8f07-470">스캐폴드 프로세스는 다음 파일을 생성하고 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-470">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="d8f07-471">생성된 파일</span><span class="sxs-lookup"><span data-stu-id="d8f07-471">Files created</span></span>

* <span data-ttu-id="d8f07-472">*Pages/Students* 만들기, 삭제, 세부 정보, 편집, 인덱스입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-472">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="d8f07-473">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="d8f07-473">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="d8f07-474">파일 업데이트</span><span class="sxs-lookup"><span data-stu-id="d8f07-474">File updates</span></span>

* <span data-ttu-id="d8f07-475">*Startup.cs* : 이 파일의 변경 내용은 다음 섹션에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-475">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="d8f07-476">*appsettings.json* : 로컬 데이터베이스에 연결하는 데 사용된 연결 문자열이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-476">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d8f07-477">종속성 주입을 사용하여 등록된 컨텍스트 확인</span><span class="sxs-lookup"><span data-stu-id="d8f07-477">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d8f07-478">ASP.NET Core는 [종속성 주입](xref:fundamentals/dependency-injection)을 사용하여 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-478">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8f07-479">서비스(예: EF Core DB 컨텍스트)는 애플리케이션 시작 중에 종속성 주입에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-479">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d8f07-480">이러한 서비스(예: Razor Pages)가 필요한 구성 요소는 생성자 매개 변수를 통해 해당 서비스를 제공받습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-480">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d8f07-481">DB 컨텍스트 인스턴스를 가져오는 생성자 코드는 자습서 뒷부분에 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-481">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d8f07-482">스캐폴딩 도구는 자동으로 DB 컨텍스트를 생성하고 종속성 주입 컨테이너에 등록했습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-482">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d8f07-483">*Startup.cs*의 `ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-483">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="d8f07-484">강조 표시된 줄은 스캐폴더에서 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-484">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="d8f07-485">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 개체에서 메서드를 호출하여 연결 문자열의 이름을 컨텍스트에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-485">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d8f07-486">로컬 개발의 경우 [ASP.NET Core 구성 시스템](xref:fundamentals/configuration/index)은 *appsettings.json* 파일에서 연결 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-486">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="d8f07-487">기본 업데이트</span><span class="sxs-lookup"><span data-stu-id="d8f07-487">Update main</span></span>

<span data-ttu-id="d8f07-488">*Program.cs*에서 다음을 수행하는 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-488">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="d8f07-489">종속성 주입 컨테이너에서 DB 컨텍스트 인스턴스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-489">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="d8f07-490">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-490">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="d8f07-491">`EnsureCreated` 메서드가 완료되면 컨텍스트를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-491">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="d8f07-492">다음 코드는 업데이트된 *Program.cs* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-492">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="d8f07-493">`EnsureCreated`는 컨텍스트에 대한 데이터베이스가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-493">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="d8f07-494">존재하는 경우 아무런 동작이 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-494">If it exists, no action is taken.</span></span> <span data-ttu-id="d8f07-495">존재하지 않는 경우 데이터베이스 및 해당하는 모든 스키마가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-495">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="d8f07-496">`EnsureCreated`는 데이터베이스를 만드는 데 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-496">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="d8f07-497">`EnsureCreated`를 사용하여 만든 데이터베이스는 나중에 마이그레이션을 사용하여 업데이트될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-497">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="d8f07-498">앱 시작 시 다음 작업 흐름을 허용하는 `EnsureCreated`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-498">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="d8f07-499">DB를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-499">Delete the DB.</span></span>
* <span data-ttu-id="d8f07-500">DB 스키마를 변경합니다(예: `EmailAddress` 필드 추가).</span><span class="sxs-lookup"><span data-stu-id="d8f07-500">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="d8f07-501">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-501">Run the app.</span></span>
* <span data-ttu-id="d8f07-502">`EnsureCreated`가 `EmailAddress` 열이 있는 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-502">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="d8f07-503">`EnsureCreated`는 스키마가 빠르게 발전하는 경우 개발 초기에 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-503">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="d8f07-504">나중에 자습서에서 DB가 삭제되고 마이그레이션이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-504">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="d8f07-505">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="d8f07-505">Test the app</span></span>

<span data-ttu-id="d8f07-506">앱을 실행하고 cookie 정책에 동의합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-506">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="d8f07-507">이 앱은 개인 정보를 보관하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-507">This app doesn't keep personal information.</span></span> <span data-ttu-id="d8f07-508">[EU GDPR(일반 데이터 보호 규정) 지원](xref:security/gdpr)에서 cookie 정책을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-508">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="d8f07-509">**학생** 링크 및 **새로 만들기**를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-509">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="d8f07-510">편집, 세부 정보 및 삭제 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-510">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="d8f07-511">SchoolContext DB 컨텍스트 검사</span><span class="sxs-lookup"><span data-stu-id="d8f07-511">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="d8f07-512">특정 데이터 모델에 맞게 EF Core 기능을 조정하는 주 클래스는 DB 컨텍스트 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-512">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="d8f07-513">데이터 컨텍스트는 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-513">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="d8f07-514">데이터 컨텍스트는 데이터 모델에 포함되는 엔터티를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-514">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="d8f07-515">이 프로젝트에서 클래스 이름은 `SchoolContext`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-515">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="d8f07-516">*SchoolContext.cs*를 다음 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-516">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="d8f07-517">강조 표시된 코드는 각 엔터티 집합에 대해 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 속성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-517">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="d8f07-518">EF Core 용어에서:</span><span class="sxs-lookup"><span data-stu-id="d8f07-518">In EF Core terminology:</span></span>

* <span data-ttu-id="d8f07-519">엔터티 집합은 일반적으로 DB 테이블에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-519">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="d8f07-520">엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-520">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d8f07-521">`DbSet<Enrollment>` 및 `DbSet<Course>`를 생략할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-521">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="d8f07-522">`Student` 엔터티는 `Enrollment` 엔터티를 참조하고 `Enrollment` 엔터티는 `Course` 엔터티를 참조하기 때문에 EF Core는 이를 암시적으로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-522">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="d8f07-523">이 자습서의 경우 `DbSet<Enrollment>` 및 `DbSet<Course>`를 `SchoolContext`에 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-523">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="d8f07-524">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="d8f07-524">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d8f07-525">연결 문자열은 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-525">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="d8f07-526">LocalDB는 프로덕션 사용이 아닌 앱 개발용으로 고안된 SQL Server Express 데이터베이스 엔진의 경량 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="d8f07-527">LocalDB는 요청 시 시작하고 사용자 모드에서 실행되므로 복잡한 구성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-527">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d8f07-528">기본적으로 LocalDB는 *.mdf* DB 파일을 `C:/Users/<user>` 디렉터리에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-528">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="d8f07-529">코드를 추가하여 테스트 데이터로 DB 초기화</span><span class="sxs-lookup"><span data-stu-id="d8f07-529">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="d8f07-530">EF Core가 빈 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-530">EF Core creates an empty DB.</span></span> <span data-ttu-id="d8f07-531">이 섹션에서는 테스트 데이터로 채울 `Initialize` 메서드가 작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-531">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="d8f07-532">*Data* 폴더에서 *DbInitializer.cs*라는 새 클래스 파일을 만들고 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-532">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="d8f07-533">참고: 앞의 코드는 `Data`가 아닌 `Models`을 네임스페이스(`namespace ContosoUniversity.Models`)에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-533">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="d8f07-534">`Models`는 스캐폴더에서 생성된 코드와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-534">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="d8f07-535">자세한 내용은 [이 GitHub 스캐폴딩 문제](https://github.com/aspnet/Scaffolding/issues/822)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-535">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="d8f07-536">코드는 DB에 학생이 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-536">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="d8f07-537">DB에 학생이 없는 경우 DB는 테스트 데이터로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-537">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="d8f07-538">`List<T>` 컬렉션이 아닌 배열에 테스트 데이터를 로드하여 성능을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-538">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="d8f07-539">`EnsureCreated` 메서드는 자동으로 DB 컨텍스트에 대한 DB를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-539">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="d8f07-540">DB가 있는 경우 `EnsureCreated`는 DB를 수정하지 않고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-540">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="d8f07-541">*Program.cs*에서 `Initialize`를 호출하도록 `Main` 메서드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-541">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="d8f07-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f07-542">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8f07-543">앱이 실행 중이라면 중지하고 **패키지 관리자 콘솔**(PMC)에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-543">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f07-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f07-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8f07-545">실행 중인 경우 앱을 중지하고 *CU.db* 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-545">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="d8f07-546">DB 보기</span><span class="sxs-lookup"><span data-stu-id="d8f07-546">View the DB</span></span>

<span data-ttu-id="d8f07-547">데이터베이스 이름은 이전에 제공한 컨텍스트 이름과 대시 및 GUID를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-547">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="d8f07-548">따라서 데이터베이스 이름은 “SchoolContext-{GUID}”입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-548">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="d8f07-549">GUID는 사용자에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-549">The GUID will be different for each user.</span></span>
<span data-ttu-id="d8f07-550">Visual Studio의 **보기** 메뉴에서 **SSOX(SQL Server 개체 탐색기)** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-550">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="d8f07-551">SSOX에서 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-551">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="d8f07-552">**테이블** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-552">Expand the **Tables** node.</span></span>

<span data-ttu-id="d8f07-553">**학생** 테이블을 마우스 오른쪽 단추로 클릭하고, **데이터 보기**를 클릭하여 만든 열 및 테이블에 삽입된 행을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-553">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="d8f07-554">비동기 코드</span><span class="sxs-lookup"><span data-stu-id="d8f07-554">Asynchronous code</span></span>

<span data-ttu-id="d8f07-555">비동기 프로그래밍은 ASP.NET Core 및 EF Core에 대한 기본 모드입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-555">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="d8f07-556">웹 서버에는 사용할 수 있는 스레드 수가 제한적이며, 로드 양이 많은 상황에서는 사용 가능한 모든 스레드가 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-556">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="d8f07-557">이 경우 서버는 스레드가 해제될 때까지 새 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-557">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="d8f07-558">동기 코드를 사용하면 I/O 완료를 대기하느라 작업을 실제로 수행하지 않는 동안에 많은 스레드가 정체될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-558">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="d8f07-559">비동기 코드를 사용하면 프로세스가 I/O 완료를 대기할 때 다른 요청을 처리하는 데 사용하도록 해당 스레드가 서버에서 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-559">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="d8f07-560">따라서 비동기 코드를 사용하면 서버 리소스를 더 효율적으로 사용할 수 있으며 서버가 지연 없이 더 많은 트래픽을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-560">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="d8f07-561">비동기 코드는 런타임 시 약간의 오버헤드를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-561">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="d8f07-562">트래픽이 높은 상황에서는 잠재적 성능 향상이 상당한 반면, 트래픽이 낮은 상황의 경우 성능 저하는 미미합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-562">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="d8f07-563">다음 코드에서 [async](/dotnet/csharp/language-reference/keywords/async) 키워드, `Task<T>` 반환 값, `await` 키워드 및 `ToListAsync` 메서드는 비동기적으로 코드 실행을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-563">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="d8f07-564">`async` 키워드는 컴파일러가 다음을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-564">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="d8f07-565">메서드 본문의 부분에 대한 콜백을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-565">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="d8f07-566">반환되는 [작업](/dotnet/api/system.threading.tasks.task) 개체를 자동으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-566">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="d8f07-567">자세한 내용은 [작업 반환 형식](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-567">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="d8f07-568">암시적 반환 형식 `Task`는 진행 중인 작업을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-568">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="d8f07-569">`await` 키워드로 인해 컴파일러는 메서드를 두 부분으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-569">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="d8f07-570">첫 번째 부분은 비동기적으로 시작되는 작업을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-570">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="d8f07-571">두 번째 부분은 작업이 완료될 때 호출되는 콜백 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-571">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="d8f07-572">`ToListAsync`는 `ToList` 확장 메서드의 비동기 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-572">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="d8f07-573">EF Core를 사용하는 비동기 코드를 작성할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-573">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="d8f07-574">쿼리 또는 명령을 DB에 보내는 명령문만 비동기적으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-574">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="d8f07-575">여기에는 `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` 및 `SaveChangesAsync`가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-575">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="d8f07-576">`var students = context.Students.Where(s => s.LastName == "Davolio")`와 같은 `IQueryable`을 변경하는 명령문은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-576">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="d8f07-577">EF Core 컨텍스트는 스레드로부터 안전하지 않습니다. 동시에 여러 작업을 수행하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d8f07-577">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="d8f07-578">비동기 코드의 성능 이점을 활용하려면 쿼리를 DB에 보내는 EF Core 메서드를 호출하는 경우 라이브러리 패키지(예: 페이징)가 비동기를 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-578">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="d8f07-579">.NET에서의 비동기 프로그래밍에 대한 자세한 내용은 [비동기 개요](/dotnet/standard/async) 및 [Async 및 Await를 사용한 비동기 프로그래밍](/dotnet/csharp/programming-guide/concepts/async/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d8f07-579">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="d8f07-580">다음 자습서에서는 기본 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d8f07-580">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="d8f07-581">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d8f07-581">Additional resources</span></span>

* [<span data-ttu-id="d8f07-582">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="d8f07-582">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="d8f07-583">다음</span><span class="sxs-lookup"><span data-stu-id="d8f07-583">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
