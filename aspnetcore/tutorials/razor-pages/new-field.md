---
title: 7부. ASP.NET Core의 Razor Page에 새 필드 추가
author: rick-anderson
description: Razor Pages에 대한 자습서 시리즈의 7부입니다.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 07a28333f86bb9b3c9f07b3ddf964edf5bf8dc96
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022045"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="4d5f8-103">7부. ASP.NET Core의 Razor Page에 새 필드 추가</span><span class="sxs-lookup"><span data-stu-id="4d5f8-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="4d5f8-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d5f8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="4d5f8-105">이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4d5f8-106">모델에 새 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-106">Add a new field to the model.</span></span>
* <span data-ttu-id="4d5f8-107">데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4d5f8-108">EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는</span><span class="sxs-lookup"><span data-stu-id="4d5f8-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4d5f8-109">데이터베이스에 `__EFMigrationsHistory` 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4d5f8-110">모델 클래스가 DB와 동기화되지 않으면 EF는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="4d5f8-111">동기화된 스키마/모델의 자동 확인을 통해 일관성이 없는 데이터베이스/코드 문제를 쉽게 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4d5f8-112">영화 모델에 등급 속성 추가</span><span class="sxs-lookup"><span data-stu-id="4d5f8-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="4d5f8-113">*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="4d5f8-114">앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-114">Build the app.</span></span>

<span data-ttu-id="4d5f8-115">*Pages/Movies/Index.cshtml*를 편집하고 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="4d5f8-116">다음 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-116">Update the following pages:</span></span>

* <span data-ttu-id="4d5f8-117">삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="4d5f8-118">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="4d5f8-119">편집 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4d5f8-120">새 필드를 포함하도록 DB가 수정될 때까지 앱은 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="4d5f8-121">데이터베이스를 업데이트하지 않고 앱을 실행하면 `SqlException`이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4d5f8-122">`SqlException` 예외는 업데이트된 영화 모델 클래스가 데이터베이스의 영화 테이블의 스키마와 다르면 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4d5f8-123">(데이터베이스 테이블에 `Rating` 열이 없습니다.)</span><span class="sxs-lookup"><span data-stu-id="4d5f8-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="4d5f8-124">이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4d5f8-125">새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4d5f8-126">이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4d5f8-127">단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4d5f8-128">프로덕션 데이터베이스에서 이 방법을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="4d5f8-129">테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마에서 DB를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4d5f8-130">모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4d5f8-131">이 방법의 장점은 데이터가 유지된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="4d5f8-132">이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4d5f8-133">Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4d5f8-134">이 자습서의 경우 Code First 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4d5f8-135">새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4d5f8-136">샘플 변경은 아래에 표시되지만 각 `new Movie` 블록에 대해 이 변경을 수행하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4d5f8-137">[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4d5f8-138">솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d5f8-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d5f8-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4d5f8-140">등급 필드에 대한 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-140">Add a migration for the rating field</span></span>

<span data-ttu-id="4d5f8-141">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="4d5f8-142">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="4d5f8-143">`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4d5f8-144">`Movie` 모델을 `Movie` DB 스키마와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="4d5f8-145">DB 스키마를 새 모델로 마이그레이션하도록 코드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="4d5f8-146">"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4d5f8-147">마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4d5f8-148">`Update-Database` 명령은 프레임워크에게 스키마 변경 내용을 데이터베이스에 적용하고 기존 데이터를 보존하라고 명령합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4d5f8-149">DB의 모든 레코드를 삭제하는 경우 이니셜라이저에서 DB를 시드하고 `Rating` 필드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="4d5f8-150">브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4d5f8-151">다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4d5f8-152">SSOX에서 데이터베이스를 삭제하려면:</span><span class="sxs-lookup"><span data-stu-id="4d5f8-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="4d5f8-153">SSOX에서 데이터베이스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="4d5f8-154">데이터베이스를 마우스 오른쪽 단추로 클릭하고 *삭제*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="4d5f8-155">**기존 연결 닫기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="4d5f8-156">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-156">Select **OK**.</span></span>
* <span data-ttu-id="4d5f8-157">[PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d5f8-158">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d5f8-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4d5f8-159">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="4d5f8-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="4d5f8-160">마이그레이션 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-160">Delete the migration folder.</span></span>  <span data-ttu-id="4d5f8-161">다음 명령을 사용하여 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="4d5f8-162">앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4d5f8-163">데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d5f8-164">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4d5f8-164">Additional resources</span></span>

* [<span data-ttu-id="4d5f8-165">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="4d5f8-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="4d5f8-166">[이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4d5f8-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="4d5f8-167">이 섹션에서는 [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 마이그레이션으로 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4d5f8-168">모델에 새 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-168">Add a new field to the model.</span></span>
* <span data-ttu-id="4d5f8-169">데이터베이스로 새 필드 스키마 변경 내용을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="4d5f8-170">EF Code First를 사용하여 자동으로 데이터베이스를 만들 경우 Code First는</span><span class="sxs-lookup"><span data-stu-id="4d5f8-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4d5f8-171">데이터베이스에 테이블을 추가하여 데이터베이스의 스키마가 생성된 모델 클래스와 동기화되어 있는지 여부를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="4d5f8-172">모델 클래스가 DB와 동기화되지 않으면 EF는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="4d5f8-173">동기화된 스키마/모델의 자동 확인을 통해 일관성이 없는 데이터베이스/코드 문제를 쉽게 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4d5f8-174">영화 모델에 등급 속성 추가</span><span class="sxs-lookup"><span data-stu-id="4d5f8-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="4d5f8-175">*Models/Movie.cs* 파일을 열고 `Rating` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="4d5f8-176">앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-176">Build the app.</span></span>

<span data-ttu-id="4d5f8-177">*Pages/Movies/Index.cshtml*를 편집하고 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="4d5f8-178">다음 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-178">Update the following pages:</span></span>

* <span data-ttu-id="4d5f8-179">삭제 및 세부 정보 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="4d5f8-180">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)을 `Rating` 필드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="4d5f8-181">편집 페이지에 `Rating` 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="4d5f8-182">앱은 새 필드를 포함하도록 DB가 업데이트될 때까지 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="4d5f8-183">지금 실행하면 앱은 `SqlException`을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4d5f8-184">이 오류는 데이터베이스의 동영상 테이블의 스키마와 다른 업데이트된 동영상 모델 클래스로 인해 발생됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="4d5f8-185">(데이터베이스 테이블에 `Rating` 열이 없습니다.)</span><span class="sxs-lookup"><span data-stu-id="4d5f8-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="4d5f8-186">이 오류를 해결할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4d5f8-187">새 모델 클래스 스키마를 사용하여 Entity Framework를 자동으로 삭제하고 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="4d5f8-188">이 방법은 개발 주기의 초기 단계에서 편리하며 신속하게 모델 및 데이터베이스 스키마를 함께 개발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4d5f8-189">단점은 데이터베이스의 기존 데이터를 잃게 된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="4d5f8-190">프로덕션 데이터베이스에서 이 방법을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="4d5f8-191">테스트 데이터로 데이터베이스를 자동으로 시드하도록 스키마에서 DB를 삭제하고 이니셜라이저를 사용하는 것은 종종 앱을 개발하는 효율적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="4d5f8-192">모델 클래스와 일치하도록 기존 데이터베이스의 스키마를 명시적으로 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4d5f8-193">이 방법의 장점은 데이터가 유지된다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="4d5f8-194">이러한 변경을 수동으로 수행하거나 데이터베이스 변경 스크립트를 만들어 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4d5f8-195">Code First 마이그레이션을 사용하여 데이터베이스 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4d5f8-196">이 자습서의 경우 Code First 마이그레이션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="4d5f8-197">새 열에 대한 값을 제공하도록 `SeedData` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4d5f8-198">샘플 변경은 아래에 표시되지만 각 `new Movie` 블록에 대해 이 변경을 수행하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="4d5f8-199">[완료된 SeedData.cs 파일](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="4d5f8-200">솔루션을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d5f8-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d5f8-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="4d5f8-202">등급 필드에 대한 마이그레이션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-202">Add a migration for the rating field</span></span>

<span data-ttu-id="4d5f8-203">**도구** 메뉴에서 **NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="4d5f8-204">PMC에서 다음 명령을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="4d5f8-205">`Add-Migration` 명령은 프레임워크에 다음 작업을 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="4d5f8-206">`Movie` 모델을 `Movie` DB 스키마와 비교합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="4d5f8-207">DB 스키마를 새 모델로 마이그레이션하도록 코드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="4d5f8-208">"Rating" 이름은 임의로 지정되며 마이그레이션 파일의 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4d5f8-209">마이그레이션 파일에 의미 있는 이름을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4d5f8-210">`Update-Database` 명령은 스키마 변경 내용을 데이터베이스에 적용하기 위한 프레임워크를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="4d5f8-211">DB의 모든 레코드를 삭제하는 경우 이니셜라이저에서 DB를 시드하고 `Rating` 필드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="4d5f8-212">브라우저 또는 [SSOX](xref:tutorials/razor-pages/sql#ssox)(Sql Server 개체 탐색기)에서 삭제 링크를 사용하여 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="4d5f8-213">다른 옵션은 데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4d5f8-214">SSOX에서 데이터베이스를 삭제하려면:</span><span class="sxs-lookup"><span data-stu-id="4d5f8-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="4d5f8-215">SSOX에서 데이터베이스를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="4d5f8-216">데이터베이스를 마우스 오른쪽 단추로 클릭하고 *삭제*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="4d5f8-217">**기존 연결 닫기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="4d5f8-218">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-218">Select **OK**.</span></span>
* <span data-ttu-id="4d5f8-219">[PMC](xref:tutorials/razor-pages/new-field#pmc)에서 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d5f8-220">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d5f8-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="4d5f8-221">데이터베이스를 삭제하고 다시 만들기</span><span class="sxs-lookup"><span data-stu-id="4d5f8-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="4d5f8-222">데이터베이스를 삭제하고 마이그레이션을 사용하여 데이터베이스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4d5f8-223">데이터베이스를 삭제하려면 데이터베이스 파일(*MvcMovie.db*)을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="4d5f8-224">그런 다음, `ef database update` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="4d5f8-225">앱을 실행하고 `Rating` 필드를 사용하여 영화를 만들고/편집/표시할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="4d5f8-226">데이터베이스가 시드되지 않은 경우 `SeedData.Initialize` 메서드에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="4d5f8-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d5f8-227">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4d5f8-227">Additional resources</span></span>

* [<span data-ttu-id="4d5f8-228">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="4d5f8-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="4d5f8-229">[이전: 검색 추가](xref:tutorials/razor-pages/search)
> [다음: 유효성 검사 추가](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="4d5f8-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
