---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 구성 요소를 Blazor 앱에 포함하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 82969bf92965bfdeb1d1474ab47ca74ecbe6dd97
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080305"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="7bd5f-103">ASP.NET Core Razor 구성 요소 클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="7bd5f-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="7bd5f-104">작성자: [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="7bd5f-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="7bd5f-105">프로젝트 간에 [RCL(Razor 클래스 라이브러리)](xref:razor-pages/ui-class)의 구성 요소를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="7bd5f-106">다음 위치에서 ‘Razor 구성 요소 클래스 라이브러리’를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="7bd5f-107">솔루션의 다른 프로젝트</span><span class="sxs-lookup"><span data-stu-id="7bd5f-107">Another project in the solution.</span></span>
* <span data-ttu-id="7bd5f-108">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="7bd5f-108">A NuGet package.</span></span>
* <span data-ttu-id="7bd5f-109">참조된 .NET 라이브러리</span><span class="sxs-lookup"><span data-stu-id="7bd5f-109">A referenced .NET library.</span></span>

<span data-ttu-id="7bd5f-110">구성 요소가 일반적인 .NET 형식인 것처럼, RCL에서 제공하는 구성 요소는 일반적인 .NET 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="7bd5f-111">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="7bd5f-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7bd5f-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7bd5f-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7bd5f-113">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-113">Create a new project.</span></span>
1. <span data-ttu-id="7bd5f-114">**Razor 클래스 라이브러리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="7bd5f-115">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-115">Select **Next**.</span></span>
1. <span data-ttu-id="7bd5f-116">**새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="7bd5f-117">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7bd5f-118">이 항목의 예제에서는 프로젝트 이름으로 `ComponentLibrary`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="7bd5f-119">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-119">Select **Create**.</span></span>
1. <span data-ttu-id="7bd5f-120">솔루션에 RCL을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="7bd5f-121">솔루션을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-121">Right-click the solution.</span></span> <span data-ttu-id="7bd5f-122">**추가** > **기존 프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="7bd5f-123">RCL의 프로젝트 파일로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="7bd5f-124">RCL의 프로젝트 파일(`.csproj`)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="7bd5f-125">앱에서 RCL 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="7bd5f-126">앱 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-126">Right-click the app project.</span></span> <span data-ttu-id="7bd5f-127">**추가** > **참조**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="7bd5f-128">RCL 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-128">Select the RCL project.</span></span> <span data-ttu-id="7bd5f-129">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="7bd5f-130">템플릿에서 RCL을 생성할 때 **페이지 및 뷰 지원** 확인란을 선택한 경우 다음 내용을 포함하는 `_Imports.razor` 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="7bd5f-131">생성된 프로젝트 루트에 파일을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7bd5f-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7bd5f-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="7bd5f-133">명령 셸에서 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 통해 **Razor 클래스 라이브러리** 템플릿(`razorclasslib`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="7bd5f-134">다음 예제에서는 이름이 `ComponentLibrary`인 RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="7bd5f-135">명령을 실행하면 `ComponentLibrary`을 포함하는 폴더가 자동으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="7bd5f-136">템플릿에서 RCL을 생성할 때 `-s|--support-pages-and-views` 스위치를 사용한 경우 다음 내용을 포함하는 `_Imports.razor` 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="7bd5f-137">생성된 프로젝트 루트에 파일을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="7bd5f-138">기존 프로젝트에 라이브러리를 추가하려면 명령 셸에서 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="7bd5f-139">다음 예제에서는 앱에 RCL을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="7bd5f-140">라이브러리 경로를 사용하여 앱의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="7bd5f-141">라이브러리 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="7bd5f-141">Consume a library component</span></span>

<span data-ttu-id="7bd5f-142">다른 프로젝트의 라이브러리에서 정의된 구성 요소를 사용하려면 다음 방법의 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="7bd5f-143">네임스페이스를 포함하는 전체 형식 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="7bd5f-144">Razor의 [`@using`](xref:mvc/views/razor#using) 지시문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="7bd5f-145">개별 구성 요소는 이름으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-145">Individual components can be added by name.</span></span>

<span data-ttu-id="7bd5f-146">다음 예제에서 `ComponentLibrary`는 `Component1` 구성 요소(`Component1.razor`)가 포함된 구성 요소 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="7bd5f-147">`Component1` 구성 요소는 라이브러리가 만들어질 때 RCL 프로젝트 템플릿에 의해 자동으로 추가되는 예제 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="7bd5f-148">네임스페이스를 사용하여 `Component1` 구성 요소를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="7bd5f-149">또는 [`@using`](xref:mvc/views/razor#using) 지시문을 사용하여 라이브러리를 범위로 전환하고 네임스페이스 없이 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="7bd5f-150">원하는 경우 최상위 `_Import.razor` 파일에 `@using ComponentLibrary` 지시문을 포함하여 전체 프로젝트에서 라이브러리 구성 요소를 사용할 수 있게 합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="7bd5f-151">임의 수준의 `_Import.razor` 파일에 지시문을 추가하여 폴더 내의 단일 구성 요소 또는 구성 요소 집합에 네임스페이스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7bd5f-152">`Component1`의 `my-component` CSS 클래스를 구성 요소에 제공하려면 `Component1.razor`에서 프레임워크의 [`Link` 구성 요소](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)를 사용하여 라이브러리의 스타일시트에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="7bd5f-153">앱에서 스타일시트를 제공하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에서 라이브러리의 스타일시트에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="7bd5f-154">자식 구성 요소에서 `Link` 구성 요소가 사용되는 경우 `Link` 구성 요소가 있는 자식이 렌더링되는 한 부모 구성 요소의 다른 모든 자식 구성 요소에서도 연결된 자산을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="7bd5f-155">자식 구성 요소에서 `Link` 구성 요소를 사용하는 것과 `wwwroot/index.html` 또는 `Pages/_Host.cshtml`에 `<link>` HTML 태그를 배치하는 것의 차이는 프레임워크 구성 요소의 렌더링된 HTML 태그입니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="7bd5f-156">애플리케이션 상태에 의해 수정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-156">Can be modified by application state.</span></span> <span data-ttu-id="7bd5f-157">하드 코드된 `<link>` HTML 태그는 애플리케이션 상태에 의해 수정될 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="7bd5f-158">부모 구성 요소가 더 이상 렌더링되지 않으면 HTML `<head>`에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7bd5f-159">`Component1`의 `my-component` CSS 클래스를 제공하려면 앱의 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에서 라이브러리의 스타일시트에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="7bd5f-160">정적 자산을 사용하여 Razor 구성 요소 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="7bd5f-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="7bd5f-161">RCL에는 정적 자산이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-161">An RCL can include static assets.</span></span> <span data-ttu-id="7bd5f-162">정적 자산은 라이브러리를 사용하는 모든 앱에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="7bd5f-163">자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="7bd5f-164">호스트된 여러 Blazor 앱에 구성 요소 및 정적 자산 제공</span><span class="sxs-lookup"><span data-stu-id="7bd5f-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="7bd5f-165">자세한 내용은 <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="7bd5f-166">빌드, 패키지 및 NuGet에 제공</span><span class="sxs-lookup"><span data-stu-id="7bd5f-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="7bd5f-167">구성 요소 라이브러리는 표준 .NET 라이브러리이기 때문에 패키지하여 NuGet에 제공하는 과정이 라이브러리를 패키지하여 NuGet에 제공하는 과정과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="7bd5f-168">명령 셸에서 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 패키지합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="7bd5f-169">명령 셸에서 [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용하여 패키지를 NuGet에 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7bd5f-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7bd5f-170">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7bd5f-170">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="7bd5f-171">라이브러리에 XML IL(중간 언어) 트리머 구성 파일 추가</span><span class="sxs-lookup"><span data-stu-id="7bd5f-171">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="7bd5f-172">라이브러리에 XML IL(중간 언어) 링커 구성 파일 추가</span><span class="sxs-lookup"><span data-stu-id="7bd5f-172">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
