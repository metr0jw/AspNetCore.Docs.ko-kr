---
title: ASP.NET Core Blazor WebAssembly 디버그
author: guardrex
description: Blazor 앱을 디버그하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 7681deb70610a8fbc27ccda7317b73921646794a
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876778"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="bb74c-103">ASP.NET Core Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="bb74c-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="bb74c-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="bb74c-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="bb74c-105">Blazor WebAssembly 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="bb74c-106">또한 다음과 같은 IDE(통합 개발 환경)를 사용하여 앱을 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="bb74c-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb74c-107">Visual Studio</span></span>
* <span data-ttu-id="bb74c-108">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb74c-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="bb74c-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bb74c-109">Visual Studio Code</span></span>

<span data-ttu-id="bb74c-110">사용 가능한 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-110">Available scenarios include:</span></span>

* <span data-ttu-id="bb74c-111">중단점을 설정하고 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="bb74c-112">IDE에서 디버깅 지원으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="bb74c-113">코드를 한 단계씩 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-113">Single-step through the code.</span></span>
* <span data-ttu-id="bb74c-114">IDE에서 바로 가기 키를 사용하여 코드 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="bb74c-115">‘로컬’ 창에서 지역 변수의 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="bb74c-116">JavaScript와 .NET 간의 호출 체인을 포함한 호출 스택을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="bb74c-117">현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-117">For now, you *can't*:</span></span>

* <span data-ttu-id="bb74c-118">처리되지 않은 예외에서 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="bb74c-119">디버그 프록시가 실행되기 전에 앱을 시작하는 동안 중단점에 적중합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="bb74c-120">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bb74c-121">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="bb74c-121">Prerequisites</span></span>

<span data-ttu-id="bb74c-122">디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-122">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="bb74c-123">Google Chrome(버전 70 이상)(기본값)</span><span class="sxs-lookup"><span data-stu-id="bb74c-123">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="bb74c-124">Microsoft Edge(버전 80 이상)</span><span class="sxs-lookup"><span data-stu-id="bb74c-124">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="bb74c-125">Mac용 Visual Studio에는 버전 8.8(빌드 1532) 이상이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-125">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="bb74c-126">**Mac용 Visual Studio 다운로드** 단추([Microsoft: Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/) 페이지)를 선택하여 Mac용 Visual Studio 최신 릴리스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-126">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="bb74c-127">Visual Studio 내에서 ‘미리 보기’ 채널을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-127">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="bb74c-128">자세한 내용은 [Mac용 Visual Studio의 미리 보기 버전 설치](/visualstudio/mac/install-preview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bb74c-128">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="bb74c-129">macOS의 Apple Safari는 현재 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-129">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="bb74c-130">디버깅 사용</span><span class="sxs-lookup"><span data-stu-id="bb74c-130">Enable debugging</span></span>

<span data-ttu-id="bb74c-131">기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 `launchSettings.json` 파일을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-131">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="bb74c-132">업데이트하면 `launchSettings.json` 파일이 다음 예와 비슷해집니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-132">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="bb74c-133">`inspectUri` 속성은</span><span class="sxs-lookup"><span data-stu-id="bb74c-133">The `inspectUri` property:</span></span>

* <span data-ttu-id="bb74c-134">Blazor WebAssembly 앱이라는 것을 검색하도록 IDE를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-134">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="bb74c-135">Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-135">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="bb74c-136">시작된 브라우저(`browserInspectUri`)의 Websocket 프로토콜(`wsProtocol`), 호스트(`url.hostname`), 포트(`url.port`) 및 검사기 URI에 대한 자리 표시자 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-136">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bb74c-137">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb74c-137">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bb74c-138">Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면:</span><span class="sxs-lookup"><span data-stu-id="bb74c-138">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="bb74c-139">새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-139">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="bb74c-140"><kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-140">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="bb74c-141">**디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-141">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="bb74c-142">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-142">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="bb74c-143">‘클라이언트’ 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-143">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="bb74c-144">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-144">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="bb74c-145">Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-145">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="bb74c-146"><kbd>F5</kbd> 키를 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-146">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="bb74c-147">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-147">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="bb74c-148"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-148">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="bb74c-149">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-149">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="bb74c-150">`Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-150">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="bb74c-151"><kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-151">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="bb74c-152"><kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="bb74c-153">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="bb74c-153">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="bb74c-154">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-154">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bb74c-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bb74c-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

<a id="vscode"></a>

## <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="bb74c-156">독립 실행형 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="bb74c-156">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="bb74c-157">VS Code에서 독립 실행형 Blazor WebAssembly 앱을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-157">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="bb74c-158">디버깅을 사용하기 위해 추가 설정이 필요하다는 알림이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-158">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="bb74c-159">Blazor WebAssembly 애플리케이션을 디버그하려면 추가 설정이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-159">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="bb74c-160">이러한 알림이 표시되면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-160">If you receive the notification:</span></span>

   * <span data-ttu-id="bb74c-161">최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)이 설치되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-161">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="bb74c-162">설치된 확장을 검사하려면 메뉴 모음에서 **보기** > **확장**을 열거나 **작업** 사이드바에서 **확장** 아이콘을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-162">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="bb74c-163">JavaScript 미리 보기 디버깅이 사용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-163">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="bb74c-164">메뉴 모음에서 설정을 엽니다(**파일** > **기본 설정** > **설정**).</span><span class="sxs-lookup"><span data-stu-id="bb74c-164">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="bb74c-165">`debug preview` 키워드를 사용하여 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-165">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="bb74c-166">검색 결과에서 **디버그 > JavaScript: 미리 보기 사용** 확인란이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-166">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="bb74c-167">미리 보기 디버깅을 사용하도록 설정하는 옵션이 없는 경우 최신 버전의 VS Code로 업그레이드하거나 [JavaScript 디버거 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)(VS Code 버전 1.46 이전)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-167">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="bb74c-168">윈도우를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-168">Reload the window.</span></span>

1. <span data-ttu-id="bb74c-169"><kbd>F5</kbd> 바로 가기 키 또는 메뉴 항목을 사용하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-169">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="bb74c-170">**디버깅하지 않고 시작**(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-170">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="bb74c-171">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-171">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="bb74c-172">메시지가 표시되면 **Blazor WebAssembly 디버그** 옵션을 선택하여 디버깅을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-172">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="bb74c-173">독립 실행형 앱이 시작되고 디버깅 브라우저가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-173">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="bb74c-174">‘클라이언트’ 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-174">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="bb74c-175">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-175">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="bb74c-176">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="bb74c-176">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="bb74c-177">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-177">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="bb74c-178">호스트된 Blazor WebAssembly 디버그</span><span class="sxs-lookup"><span data-stu-id="bb74c-178">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="bb74c-179">VS Code에서 호스트된 Blazor WebAssembly 앱의 솔루션 폴더를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-179">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="bb74c-180">프로젝트에 대한 시작 구성 집합이 없는 경우 다음과 같은 알림이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-180">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="bb74c-181">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-181">Select **Yes**.</span></span>

   > <span data-ttu-id="bb74c-182">빌드 및 디버그에 필요한 자산이 ‘{APPLICATION NAME}’에서 누락되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-182">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="bb74c-183">추가할까요?</span><span class="sxs-lookup"><span data-stu-id="bb74c-183">Add them?</span></span>

1. <span data-ttu-id="bb74c-184">창의 맨 위에 있는 명령 팔레트에서 호스트된 솔루션 내의 서버 프로젝트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-184">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="bb74c-185">`launch.json` 파일은 디버거를 시작하기 위한 시작 구성으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-185">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="bb74c-186">기존 디버깅 세션에 연결</span><span class="sxs-lookup"><span data-stu-id="bb74c-186">Attach to an existing debugging session</span></span>

<span data-ttu-id="bb74c-187">실행 중인 Blazor 앱에 연결하려면 다음 구성을 사용하여 `launch.json` 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-187">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="bb74c-188">독립 실행형 앱에서만 디버깅 세션에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-188">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="bb74c-189">전체 스택 디버깅을 사용하려면 VS Code에서 앱을 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-189">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="bb74c-190">구성 옵션 시작</span><span class="sxs-lookup"><span data-stu-id="bb74c-190">Launch configuration options</span></span>

<span data-ttu-id="bb74c-191">`blazorwasm` 디버그 형식(`.vscode/launch.json`)에 대해 지원되는 시작 구성 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-191">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="bb74c-192">옵션</span><span class="sxs-lookup"><span data-stu-id="bb74c-192">Option</span></span>    | <span data-ttu-id="bb74c-193">설명</span><span class="sxs-lookup"><span data-stu-id="bb74c-193">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="bb74c-194">`launch`를 사용해 시작하고 Blazor WebAssembly 앱에 디버깅 세션을 연결하거나 `attach`를 사용해 이미 실행 중인 앱에 디버깅 세션을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-194">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="bb74c-195">디버깅할 때 브라우저에서 열 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-195">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="bb74c-196">기본값은 `https://localhost:5001`입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-196">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="bb74c-197">디버깅 세션을 시작하는 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-197">The browser to launch for the debugging session.</span></span> <span data-ttu-id="bb74c-198">`edge` 또는 `chrome`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-198">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="bb74c-199">기본값은 `chrome`입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-199">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="bb74c-200">JS 디버거에서 로그를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-200">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="bb74c-201">로그를 생성하려면 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-201">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="bb74c-202">호스트된 Blazor WebAssembly 앱을 시작하고 디버그하는 경우 `true`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-202">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="bb74c-203">웹 서버의 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-203">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="bb74c-204">하위 경로에서 앱이 제공되는 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-204">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="bb74c-205">디버깅 세션이 연결되기를 기다릴 밀리초 수입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-205">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="bb74c-206">기본값은 30,000 밀리초(30초)입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-206">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="bb74c-207">호스트된 앱의 서버를 실행하는 실행 파일에 대한 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-207">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="bb74c-208">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-208">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="bb74c-209">앱을 시작할 작업 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-209">The working directory to launch the app under.</span></span> <span data-ttu-id="bb74c-210">`hosted`가 `true`일 경우 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-210">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="bb74c-211">시작된 프로세스에 제공할 환경 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-211">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="bb74c-212">`hosted`가 `true`로 설정된 경우에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-212">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="bb74c-213">시작 구성 예제</span><span class="sxs-lookup"><span data-stu-id="bb74c-213">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="bb74c-214">독립 실행형 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="bb74c-214">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="bb74c-215">지정된 URL에서 실행 중인 앱에 연결</span><span class="sxs-lookup"><span data-stu-id="bb74c-215">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="bb74c-216">Microsoft Edge를 사용하여 호스트된 Blazor WebAssembly 앱 시작 및 디버그</span><span class="sxs-lookup"><span data-stu-id="bb74c-216">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="bb74c-217">브라우저 구성의 기본값은 Google Chrome입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-217">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="bb74c-218">디버깅에 Microsoft Edge를 사용하는 경우 `browser`를 `edge`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-218">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="bb74c-219">Google Chrome을 사용하려면 `browser` 옵션을 설정하거나 옵션의 값을 `chrome`로 설정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="bb74c-219">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="bb74c-220">앞의 예에서 `MyHostedApp.Server.dll`은  서버 앱의 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-220">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="bb74c-221">`.vscode` 폴더는 `Client`, `Server` 및 `Shared` 폴더 옆의 솔루션 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-221">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bb74c-222">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb74c-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bb74c-223">Mac용 Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-223">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="bb74c-224">새 ASP.NET Core 호스트된 Blazor WebAssembly 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-224">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="bb74c-225"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 디버거에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-225">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="bb74c-226">**디버깅하지 않고 시작**(<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>)은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-226">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="bb74c-227">디버그 구성에서 앱을 실행하는 경우 디버깅 오버헤드로 인해 항상 약간의 성능 저하가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-227">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="bb74c-228">디버깅 세션용으로 Google Chrome 또는 Microsoft Edge 브라우저를 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-228">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="bb74c-229">‘클라이언트’ 앱에서 `Pages/Counter.razor`의 `currentCount++;` 줄에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-229">In the *Client* app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="bb74c-230">브라우저에서 `Counter` 페이지로 이동하고 **Click Me**(여기 클릭) 단추를 선택하여 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-230">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="bb74c-231">Visual Studio에서 **지역** 창에 있는 `currentCount` 필드의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-231">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="bb74c-232"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-232">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="bb74c-233">Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-233">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="bb74c-234"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 `Pages/FetchData.razor` 페이지에서 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-234">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="bb74c-235">`Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-235">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="bb74c-236">`Fetch Data` 페이지로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-236">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="bb74c-237"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 눌러 실행을 계속한 다음 `WeatherForecastController`에서 서버의 중단점을 적중시킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-237">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="bb74c-238"><kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>을 다시 눌러 실행이 계속되도록 하고 브라우저에서 날씨 예측 테이블이 렌더링되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-238">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="bb74c-239">앱 시작 도중 디버그 프록시가 실행되기 전에 중단점이 적중되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="bb74c-239">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="bb74c-240">여기에는 `Program.Main`(`Program.cs`)의 중단점과 애플리케이션에서 요청하는 첫 페이지에서 로드되는 구성 요소의 [`OnInitialized{Async}` 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-240">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="bb74c-241">자세한 내용은 [Mac용 Visual Studio를 사용하여 디버깅](/visualstudio/mac/debugging?view=vsmac-2019)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bb74c-241">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging?view=vsmac-2019).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="bb74c-242">브라우저에서 디버그</span><span class="sxs-lookup"><span data-stu-id="bb74c-242">Debug in the browser</span></span>

<span data-ttu-id="bb74c-243">‘이 섹션의 지침은 Windows에서 실행되는 Google Chrome 및 Microsoft Edge에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="bb74c-243">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="bb74c-244">개발 환경에서 앱의 디버그 빌드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-244">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="bb74c-245">브라우저를 시작하고 앱의 URL(예: `https://localhost:5001`)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-245">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="bb74c-246">브라우저에서 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 눌러 원격 디버깅을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-246">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="bb74c-247">브라우저는 원격 디버깅을 사용하도록 설정하고 실행해야 합니다(기본값 아님).</span><span class="sxs-lookup"><span data-stu-id="bb74c-247">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="bb74c-248">원격 디버깅을 사용하지 않도록 설정하면 디버깅 포트가 열려 있는 브라우저를 시작하라는 지침과 함께 **디버깅 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-248">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="bb74c-249">브라우저에 대한 지침을 다릅니다. 그러면 새 브라우저 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-249">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="bb74c-250">이전 브라우저 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-250">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="bb74c-251">원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 이전 단계에 나온 디버깅 바로 가기 키를 사용하면 새 디버거 탭이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-251">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="bb74c-252">잠시 후 **소스** 탭에는 `file://` 노드 내에 있는 앱의 .NET 어셈블리 목록이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-252">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="bb74c-253">구성 요소 코드(`.razor` 파일) 및 C# 코드 파일(`.cs`)에서 사용자가 설정하는 중단점은 코드가 실행될 때 적중됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-253">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="bb74c-254">중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-254">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="bb74c-255">Blazor에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-255">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="bb74c-256">디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-256">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="bb74c-257">프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-257">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="bb74c-258">브라우저 소스 맵</span><span class="sxs-lookup"><span data-stu-id="bb74c-258">Browser source maps</span></span>

<span data-ttu-id="bb74c-259">브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-259">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="bb74c-260">그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-260">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="bb74c-261">대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-261">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="bb74c-262">문제 해결</span><span class="sxs-lookup"><span data-stu-id="bb74c-262">Troubleshoot</span></span>

<span data-ttu-id="bb74c-263">오류가 발생할 경우 다음 팁이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-263">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="bb74c-264">**디버거** 탭에서 브라우저의 개발자 도구를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-264">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="bb74c-265">콘솔에서 `localStorage.clear()`를 실행하여 중단점을 모두 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-265">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="bb74c-266">ASP.NET Core HTTPS 개발 인증서를 설치하고 신뢰할 수 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-266">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="bb74c-267">자세한 내용은 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bb74c-267">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="bb74c-268">Visual Studio를 사용하려면 **도구** > **옵션** > **디버깅** > **일반**에서 **ASP.NET에 대해 JavaScript 디버깅 사용(Chrome, Edge 및 IE)** 옵션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-268">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="bb74c-269">이것은 Visual Studio의 기본 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-269">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="bb74c-270">디버깅이 작동하지 않는 경우 옵션이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-270">If debugging isn't working, confirm that the option is selected.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="bb74c-271">`OnInitialized{Async}`의 중단점이 적중되지 않음</span><span class="sxs-lookup"><span data-stu-id="bb74c-271">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="bb74c-272">Blazor 프레임워크의 디버깅 프록시는 시작하는 데 약간 시간이 걸리므로 [`OnInitialized{Async}` 수명 주기 메서드](xref:blazor/components/lifecycle#component-initialization-methods)의 중단점이 적중되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-272">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="bb74c-273">메서드 본문의 시작 부분에 지연을 추가하여 중단점이 적중될 때까지 디버그 프록시가 시작될 시간 여유를 주는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-273">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="bb74c-274">[`if` 컴파일러 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)에 따라 지연을 포함하여 앱의 릴리스 빌드에 지연이 존재하지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-274">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="bb74c-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="bb74c-275"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="bb74c-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="bb74c-276"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="bb74c-277">Visual Studio(Windows) 시간 제한</span><span class="sxs-lookup"><span data-stu-id="bb74c-277">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="bb74c-278">Visual Studio에서 시간 제한에 도달했다는 메시지가 표시되며 디버그 어댑터가 실행되지 않는 예외가 throw되는 경우 레지스트리 설정을 사용하여 시간 제한을 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-278">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="bb74c-279">이전 명령의 `{TIMEOUT}` 자리 표시자는 밀리초 단위입니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-279">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="bb74c-280">예를 들어 1분은 `60000`으로 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="bb74c-280">For example, one minute is assigned as `60000`.</span></span>
