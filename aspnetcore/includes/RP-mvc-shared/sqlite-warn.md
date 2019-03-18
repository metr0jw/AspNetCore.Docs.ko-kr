---
ms.openlocfilehash: 1f8d3913c83aaf5fe6ec2cec482a30f0f066c16b
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841695"
---

> [!NOTE]
> <span data-ttu-id="8813b-101">이 자습서에서는 가능한 경우 Entity Framework Core *마이그레이션* 기능을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="8813b-102">마이그레이션은 데이터 모델의 변경 내용과 일치하도록 데이터베이스 스키마를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="8813b-103">그러나 마이그레이션은 EF Core 공급자가 지원하는 종류의 변경만 수행할 수 있으며 SQLite 공급자의 기능은 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="8813b-104">예를 들어 열 추가는 지원되지만 열 제거 또는 변경은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="8813b-105">열을 제거 또는 변경하기 위해 마이그레이션을 만들면 `ef migrations add` 명령은 성공하지만 `ef database update` 명령은 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="8813b-106">이러한 제한 때문에 이 자습서에서는 SQLite 스키마 변경에 대한 마이그레이션을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="8813b-107">대신 스키마가 변경될 때 데이터베이스를 삭제하고 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="8813b-108">SQLite 제한 사항에 대한 해결 방법은 테이블의 내용이 변경될 때 테이블 다시 빌드를 수행하기 위해 수동으로 마이그레이션 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="8813b-109">테이블 다시 빌드에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8813b-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="8813b-110">기존 테이블의 이름 바꾸기.</span><span class="sxs-lookup"><span data-stu-id="8813b-110">Renaming the existing table.</span></span>
>* <span data-ttu-id="8813b-111">새 테이블 만들기.</span><span class="sxs-lookup"><span data-stu-id="8813b-111">Creating a new table.</span></span>
>* <span data-ttu-id="8813b-112">이전 테이블에서 새 테이블로 데이터 복사.</span><span class="sxs-lookup"><span data-stu-id="8813b-112">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="8813b-113">이전 테이블 삭제.</span><span class="sxs-lookup"><span data-stu-id="8813b-113">Dropping the old table.</span></span>
>
><span data-ttu-id="8813b-114">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8813b-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="8813b-115">SQLite EF Core 데이터베이스 공급자 제한 사항</span><span class="sxs-lookup"><span data-stu-id="8813b-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="8813b-116">마이그레이션 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="8813b-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="8813b-117">데이터 시드</span><span class="sxs-lookup"><span data-stu-id="8813b-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)