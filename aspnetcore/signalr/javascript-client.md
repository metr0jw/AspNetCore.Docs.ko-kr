---
title: SignalRJavaScript 클라이언트 ASP.NET Core
author: bradygaster
description: SignalRJavaScript 클라이언트 ASP.NET Core 개요
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606679"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="ed818-103">SignalRJavaScript 클라이언트 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ed818-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ed818-104">작성자: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="ed818-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="ed818-105">개발자는 ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하 여 서버 쪽 허브 코드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="ed818-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ed818-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="ed818-107">클라이언트 패키지를 설치 합니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="ed818-107">Install the SignalR client package</span></span>

<span data-ttu-id="ed818-108">SignalRJavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="ed818-109">다음 섹션에서는 클라이언트 라이브러리를 설치 하는 다양 한 방법에 대해 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="ed818-110">Npm를 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="ed818-110">Install with npm</span></span>

<span data-ttu-id="ed818-111">Visual Studio의 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="ed818-112">Visual Studio Code의 경우 **통합 터미널**에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="ed818-113">npm 패키지 콘텐츠를 \*node_modules \\ @microsoft\signalr\dist\browser \* 폴더에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="ed818-114">*Wwwroot \\ lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="ed818-115">*signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="ed818-116">SignalR요소에서 JavaScript 클라이언트를 참조 `<script>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="ed818-117">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="ed818-118">CDN (Content Delivery Network) 사용</span><span class="sxs-lookup"><span data-stu-id="ed818-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="ed818-119">Npm 필수 구성 요소 없이 클라이언트 라이브러리를 사용 하려면 클라이언트 라이브러리의 CDN 호스트 복사본을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="ed818-120">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="ed818-121">클라이언트 라이브러리는 다음의 CDNs에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="ed818-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="ed818-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="ed818-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="ed818-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="ed818-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="ed818-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="ed818-125">을 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="ed818-125">Install with LibMan</span></span>

<span data-ttu-id="ed818-126">라이브러리 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치 하는 데 [사용 될 수 있습니다.](xref:client-side/libman/index)</span><span class="sxs-lookup"><span data-stu-id="ed818-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="ed818-127">예를 들어 프로젝트에는 축소 된 JavaScript 파일만 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="ed818-128">이 방법에 대 한 자세한 내용은 [ SignalR 클라이언트 라이브러리 추가](xref:tutorials/signalr#add-the-signalr-client-library)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ed818-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="ed818-129">허브에 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-129">Connect to a hub</span></span>

<span data-ttu-id="ed818-130">다음 코드에서는 연결을 만들고 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="ed818-131">허브 이름은 대/소문자를 구분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="ed818-132">크로스-원본 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-132">Cross-origin connections</span></span>

<span data-ttu-id="ed818-133">일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="ed818-134">그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="ed818-135">악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="ed818-136">원본 간 요청을 허용 하려면 클래스에서 사용 하도록 설정 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="ed818-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="ed818-137">클라이언트에서 허브 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="ed818-137">Call hub methods from the client</span></span>

<span data-ttu-id="ed818-138">JavaScript 클라이언트는 [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection)의 [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) 메서드를 통해 허브에서 공용 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="ed818-139">이 `invoke` 메서드는 다음을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="ed818-140">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-140">The name of the hub method.</span></span>
* <span data-ttu-id="ed818-141">허브 메서드에 정의 된 모든 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="ed818-142">다음 예제에서 허브의 메서드 이름은 `SendMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="ed818-143">허브 메서드의 및 인수에 매핑하기 위해 전달 되는 두 번째 및 세 번째 인수는 `invoke` `user` `message` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="ed818-144">클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="ed818-145">자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ed818-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="ed818-146">`invoke`메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="ed818-147">는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="ed818-148">서버에서 메서드가 오류를 throw 하는 경우 `Promise` 오류 메시지와 함께이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ed818-149">`async`및 `await` 또는 `Promise` 의 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="ed818-150">JavaScript 클라이언트는의 [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) 메서드를 통해 허브에서 공용 메서드를 호출할 수도 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="ed818-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="ed818-151">메서드와 달리 `invoke` `send` 메서드는 서버의 응답을 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="ed818-152">`send`메서드는 JavaScript를 반환 합니다 `Promise` .</span><span class="sxs-lookup"><span data-stu-id="ed818-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="ed818-153">`Promise`메시지가 서버에 전송 되 면이 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="ed818-154">메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ed818-155">`async`및 `await` 또는 `Promise` 의 `then` 및 `catch` 메서드를 사용 하 여 이러한 경우를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="ed818-156">를 사용 하면 `send` 서버에서 메시지를 받을 때까지 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="ed818-157">따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="ed818-158">허브에서 클라이언트 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="ed818-158">Call client methods from the hub</span></span>

<span data-ttu-id="ed818-159">허브에서 메시지를 수신 하려면의 [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) 메서드를 사용 하 여 메서드를 정의 합니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="ed818-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="ed818-160">JavaScript 클라이언트 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="ed818-161">허브가 메서드에 전달 하는 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="ed818-162">다음 예제에서 메서드 이름은 `ReceiveMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="ed818-163">인수 이름은 및입니다 `user` `message` .</span><span class="sxs-lookup"><span data-stu-id="ed818-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="ed818-164">에서 위의 코드는 `connection.on` 메서드를 사용 하 여 서버 쪽 코드가 호출 하는 경우 실행 됩니다 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="ed818-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="ed818-165">SignalR 및에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 `SendAsync` 합니다 `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="ed818-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="ed818-166">모범 사례에 따라 이후에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다 `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="ed818-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="ed818-167">이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="ed818-168">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="ed818-168">Error handling and logging</span></span>

<span data-ttu-id="ed818-169">및 `try` `catch` `async` `await` 또는의 메서드를 사용 `Promise` `catch` 하 여 클라이언트 쪽 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="ed818-170">`console.error`를 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="ed818-171">연결이 설정 될 때 기록할 이벤트의로 거와 유형을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="ed818-172">메시지는 지정 된 로그 수준 이상으로 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="ed818-173">사용 가능한 로그 수준은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="ed818-174">`signalR.LogLevel.Error`: 오류 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="ed818-175">`Error`메시지만 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="ed818-176">`signalR.LogLevel.Warning`: 잠재적 오류에 대 한 경고 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="ed818-177">로그 `Warning` 및 `Error` 메시지.</span><span class="sxs-lookup"><span data-stu-id="ed818-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ed818-178">`signalR.LogLevel.Information`: 오류가 없는 상태 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="ed818-179">`Information`, `Warning` 및 메시지를 기록 `Error` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ed818-180">`signalR.LogLevel.Trace`: 추적 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="ed818-181">허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="ed818-182">[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="ed818-183">메시지는 브라우저 콘솔에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="ed818-184">클라이언트 다시 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="ed818-185">자동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-185">Automatically reconnect</span></span>

<span data-ttu-id="ed818-186">SignalR `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)에서 메서드를 사용 하 여 자동으로 다시 연결 되도록에 대 한 JavaScript 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="ed818-187">기본적으로 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="ed818-188">매개 변수를 사용 하지 않으면는 `withAutomaticReconnect()` 각 다시 연결 시도를 시도 하기 전에 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고, 실패 한 네 번 시도 하면 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="ed818-189">다시 연결 시도를 시작 하기 전에는 상태로 `HubConnection` 전환 되 고 `HubConnectionState.Reconnecting` `onreconnecting` `Disconnected` `onclose` `HubConnection` 자동 다시 연결이 구성 되지 않은 등의 콜백 트리거를 상태로 전환 하는 대신 해당 콜백을 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="ed818-190">이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="ed818-191">클라이언트에서 처음 네 번의 시도 내에 다시 연결 하는 경우 `HubConnection` 이 상태로 다시 전환 되 `Connected` 고 `onreconnected` 콜백이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="ed818-192">이렇게 하면 연결이 다시 설정 되었음을 사용자에 게 알릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="ed818-193">연결은 서버에서 완전히 새로운 `connectionId` 것 이므로 콜백에 새가 제공 됩니다 `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="ed818-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="ed818-194">가 `onreconnected` `connectionId` `HubConnection` [협상을 건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 콜백의 매개 변수는 정의 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="ed818-195">`withAutomaticReconnect()``HubConnection`초기 시작 오류를 다시 시도 하도록를 구성 하지 않으므로 시작 실패를 수동으로 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="ed818-196">클라이언트가 처음 네 번의 시도 내에서 성공적으로 다시 연결 되지 않으면 `HubConnection` 이 상태로 전환 되 `Disconnected` 고 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="ed818-197">이를 통해 사용자에 게 연결이 영구적으로 손실 되었음을 알리고 페이지 새로 고침을 권장 하는 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="ed818-198">연결을 끊거나 다시 연결 하는 시간을 변경 하기 전에 사용자가 다시 연결 시도 횟수를 구성 하기 위해는 `withAutomaticReconnect` 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="ed818-199">앞의 예제에서는 `HubConnection` 연결이 끊긴 후 즉시 다시 연결 시도를 시작 하도록를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="ed818-200">기본 구성의 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="ed818-201">첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="ed818-202">두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="ed818-203">그런 다음 기본 구성에서와 같이 30 초 후에 다시 연결을 다시 시도 하는 대신 세 번째 다시 연결 시도 실패 후 중지 하 여 기본 동작에서 사용자 지정 동작이 다시 달라 지므로.</span><span class="sxs-lookup"><span data-stu-id="ed818-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="ed818-204">자동 다시 연결 시도의 타이밍과 수를 더 많이 제어 하려는 경우는 `withAutomaticReconnect` 라는 단일 메서드가 있는 인터페이스를 구현 하는 개체를 허용 `IRetryPolicy` `nextRetryDelayInMilliseconds` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="ed818-205">`nextRetryDelayInMilliseconds` 는 형식의 단일 인수를 사용 `RetryContext` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="ed818-206">에는 `RetryContext` `previousRetryCount` , `elapsedMilliseconds` 및 `retryReason` `number` `number` `Error` 각각, 및 인 세 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="ed818-207">첫 번째 다시 연결을 시도 하기 전에와는 모두 0이 되며 `previousRetryCount` `elapsedMilliseconds` 은 `retryReason` 연결이 끊어지는 원인이 되는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="ed818-208">실패 한 각 재시도 후에는 `previousRetryCount` 1 씩 증가 하 `elapsedMilliseconds` 고, 지금까지 밀리초 단위로 다시 연결 하는 데 걸린 시간을 반영 하도록 업데이트 되며,는 `retryReason` 마지막 다시 연결 시도가 실패 한 원인이 되는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="ed818-209">`nextRetryDelayInMilliseconds` 는 다음 다시 연결 시도 전에 대기 하는 시간 (밀리초)을 나타내는 숫자를 반환 해야 합니다. 그렇지 않으면에서 다시 연결을 중지 해야 합니다 `null` `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="ed818-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="ed818-210">또는 [수동으로 다시 연결](#manually-reconnect)에 설명 된 대로 클라이언트를 수동으로 다시 연결 하는 코드를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="ed818-211">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-211">Manually reconnect</span></span>

<span data-ttu-id="ed818-212">다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="ed818-213">함수 (이 경우 `start` 함수)가 생성 되어 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="ed818-214">`start`연결의 이벤트 처리기에서 함수를 호출 합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="ed818-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="ed818-215">실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="ed818-216">WebSocket 핸드셰이크 오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="ed818-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="ed818-217">이 섹션에서는 ASP.NET Core 허브에 대 한 연결을 설정 하려고 할 때 발생 하는 *"WebSocket 핸드셰이크 중 오류"* 예외에 대 한 도움말을 제공 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="ed818-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="ed818-218">응답 코드 400 또는 503</span><span class="sxs-lookup"><span data-stu-id="ed818-218">Response code 400 or 503</span></span>

<span data-ttu-id="ed818-219">다음 오류의 경우:</span><span class="sxs-lookup"><span data-stu-id="ed818-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="ed818-220">이 오류는 일반적으로 클라이언트 전용 Websocket 전송을 사용 하지만 서버에서 Websocket 프로토콜을 사용 하도록 설정 하지 않은 경우에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="ed818-221">응답 코드 307</span><span class="sxs-lookup"><span data-stu-id="ed818-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="ed818-222">허브 서버에서 다음을 수행 하는 경우가 많습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="ed818-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="ed818-223">HTTP와 HTTPS를 모두 수신 하 고 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="ed818-224">는에서를 호출 하 여 HTTPS를 적용 `UseHttpsRedirection` `Startup` 하거나 URL 재작성 규칙을 통해 https를 적용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="ed818-225">이 오류는를 사용 하 여 클라이언트 쪽에서 HTTP URL을 지정 하는 경우에 발생할 수 있습니다 `.withUrl("http://xxx/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="ed818-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="ed818-226">이 경우의 해결 방법은 HTTPS URL을 사용 하도록 코드를 수정 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="ed818-227">응답 코드 404</span><span class="sxs-lookup"><span data-stu-id="ed818-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="ed818-228">응용 프로그램이 localhost에서 작동 하는 경우에는 IIS 서버에 게시 한 후에이 오류를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="ed818-229">ASP.NET Core SignalR 앱이 IIS 하위 응용 프로그램으로 호스트 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="ed818-230">URL을 JavaScript 클라이언트 쪽의 하위 앱 pathbase로 설정 하지 마세요 SignalR `.withUrl("/SubAppName/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="ed818-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed818-231">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ed818-231">Additional resources</span></span>

* [<span data-ttu-id="ed818-232">JavaScript API 참조</span><span class="sxs-lookup"><span data-stu-id="ed818-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="ed818-233">JavaScript 자습서</span><span class="sxs-lookup"><span data-stu-id="ed818-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ed818-234">WebPack 및 TypeScript 자습서</span><span class="sxs-lookup"><span data-stu-id="ed818-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="ed818-235">허브</span><span class="sxs-lookup"><span data-stu-id="ed818-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ed818-236">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ed818-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ed818-237">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="ed818-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="ed818-238">원본 간 요청 (CORS)</span><span class="sxs-lookup"><span data-stu-id="ed818-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="ed818-239">Azure SignalR 서비스 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="ed818-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ed818-240">작성자: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="ed818-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="ed818-241">개발자는 ASP.NET Core SignalR JavaScript 클라이언트 라이브러리를 사용 하 여 서버 쪽 허브 코드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="ed818-242">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ed818-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="ed818-243">클라이언트 패키지를 설치 합니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="ed818-243">Install the SignalR client package</span></span>

<span data-ttu-id="ed818-244">SignalRJavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="ed818-245">다음 섹션에서는 클라이언트 라이브러리를 설치 하는 다양 한 방법에 대해 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="ed818-246">Npm를 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="ed818-246">Install with npm</span></span>

<span data-ttu-id="ed818-247">Visual Studio를 사용 하는 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="ed818-248">Visual Studio Code의 경우 **통합 터미널**에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="ed818-249">npm 패키지 콘텐츠를 \*node_modules \\ @aspnet\signalr\dist\browser \* 폴더에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="ed818-250">*Wwwroot \\ lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="ed818-251">*signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="ed818-252">SignalR요소에서 JavaScript 클라이언트를 참조 `<script>` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="ed818-253">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="ed818-254">CDN (Content Delivery Network) 사용</span><span class="sxs-lookup"><span data-stu-id="ed818-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="ed818-255">Npm 필수 구성 요소 없이 클라이언트 라이브러리를 사용 하려면 클라이언트 라이브러리의 CDN 호스트 복사본을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="ed818-256">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="ed818-257">클라이언트 라이브러리는 다음의 CDNs에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="ed818-258">cdnjs</span><span class="sxs-lookup"><span data-stu-id="ed818-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="ed818-259">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="ed818-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="ed818-260">unpkg</span><span class="sxs-lookup"><span data-stu-id="ed818-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="ed818-261">을 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="ed818-261">Install with LibMan</span></span>

<span data-ttu-id="ed818-262">라이브러리 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치 하는 데 [사용 될 수 있습니다.](xref:client-side/libman/index)</span><span class="sxs-lookup"><span data-stu-id="ed818-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="ed818-263">예를 들어 프로젝트에는 축소 된 JavaScript 파일만 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="ed818-264">이 방법에 대 한 자세한 내용은 [ SignalR 클라이언트 라이브러리 추가](xref:tutorials/signalr#add-the-signalr-client-library)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ed818-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="ed818-265">허브에 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-265">Connect to a hub</span></span>

<span data-ttu-id="ed818-266">다음 코드에서는 연결을 만들고 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="ed818-267">허브 이름은 대/소문자를 구분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="ed818-268">크로스-원본 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-268">Cross-origin connections</span></span>

<span data-ttu-id="ed818-269">일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="ed818-270">그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="ed818-271">악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="ed818-272">원본 간 요청을 허용 하려면 클래스에서 해당 요청을 사용 하도록 설정 `Startup` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="ed818-273">클라이언트에서 허브 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="ed818-273">Call hub methods from client</span></span>

<span data-ttu-id="ed818-274">JavaScript 클라이언트는 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)의 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해 허브에서 공용 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="ed818-275">`invoke`메서드는 두 개의 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="ed818-276">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-276">The name of the hub method.</span></span> <span data-ttu-id="ed818-277">다음 예제에서 허브의 메서드 이름은 `SendMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="ed818-278">허브 메서드에 정의 된 모든 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="ed818-279">다음 예에서는 인수 이름이 `message` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="ed818-280">예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원 되는 화살표 함수 구문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="ed818-281">클라이언트에서 허브 메서드를 호출 하는 것은 SignalR *기본* 모드에서 Azure 서비스를 사용 하는 경우에만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="ed818-282">자세한 내용은 질문과 [대답 (azure-Signalr GitHub 리포지토리)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ed818-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="ed818-283">`invoke`메서드는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="ed818-284">는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="ed818-285">서버에서 메서드가 오류를 throw 하는 경우 `Promise` 오류 메시지와 함께이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ed818-286">`then` `catch` `Promise` 이러한 경우 또는 구문을 처리 하려면 자체에서 및 메서드를 사용 `await` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="ed818-287">`send`메서드는 JavaScript를 반환 합니다 `Promise` .</span><span class="sxs-lookup"><span data-stu-id="ed818-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="ed818-288">`Promise`메시지가 서버에 전송 되 면이 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="ed818-289">메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="ed818-290">`then` `catch` `Promise` 이러한 경우 또는 구문을 처리 하려면 자체에서 및 메서드를 사용 `await` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="ed818-291">를 사용 하면 `send` 서버에서 메시지를 받을 때까지 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="ed818-292">따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="ed818-293">허브에서 클라이언트 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="ed818-293">Call client methods from hub</span></span>

<span data-ttu-id="ed818-294">허브에서 메시지를 수신 하려면의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 사용 하 여 메서드를 정의 합니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="ed818-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="ed818-295">JavaScript 클라이언트 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="ed818-296">다음 예제에서 메서드 이름은 `ReceiveMessage` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="ed818-297">허브가 메서드에 전달 하는 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="ed818-298">다음 예제에서 인수 값은 `message` 입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="ed818-299">에서 위의 코드는 `connection.on` 서버 쪽 코드가 메서드를 사용 하 여 호출 하는 경우 실행 됩니다 <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="ed818-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="ed818-300">SignalR 및에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 `SendAsync` 합니다 `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="ed818-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="ed818-301">모범 사례에 따라 이후에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다 `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="ed818-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="ed818-302">이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="ed818-303">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="ed818-303">Error handling and logging</span></span>

<span data-ttu-id="ed818-304">메서드를 `catch` 메서드의 끝에 연결 하 여 `start` 클라이언트 쪽 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="ed818-305">`console.error`를 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="ed818-306">연결이 설정 될 때 기록할 이벤트의로 거와 유형을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="ed818-307">메시지는 지정 된 로그 수준 이상으로 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="ed818-308">사용 가능한 로그 수준은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="ed818-309">`signalR.LogLevel.Error`: 오류 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="ed818-310">`Error`메시지만 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="ed818-311">`signalR.LogLevel.Warning`: 잠재적 오류에 대 한 경고 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="ed818-312">로그 `Warning` 및 `Error` 메시지.</span><span class="sxs-lookup"><span data-stu-id="ed818-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ed818-313">`signalR.LogLevel.Information`: 오류가 없는 상태 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="ed818-314">`Information`, `Warning` 및 메시지를 기록 `Error` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="ed818-315">`signalR.LogLevel.Trace`: 추적 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="ed818-316">허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="ed818-317">[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="ed818-318">메시지는 브라우저 콘솔에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="ed818-319">클라이언트 다시 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="ed818-320">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="ed818-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="ed818-321">3.0 이전 버전의에 대 한 JavaScript 클라이언트는 SignalR 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="ed818-322">클라이언트를 수동으로 다시 연결 하는 코드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="ed818-323">다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="ed818-324">함수 (이 경우 `start` 함수)가 생성 되어 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="ed818-325">`start`연결의 이벤트 처리기에서 함수를 호출 합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="ed818-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="ed818-326">실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.</span><span class="sxs-lookup"><span data-stu-id="ed818-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed818-327">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ed818-327">Additional resources</span></span>

* [<span data-ttu-id="ed818-328">JavaScript API 참조</span><span class="sxs-lookup"><span data-stu-id="ed818-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="ed818-329">JavaScript 자습서</span><span class="sxs-lookup"><span data-stu-id="ed818-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="ed818-330">WebPack 및 TypeScript 자습서</span><span class="sxs-lookup"><span data-stu-id="ed818-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="ed818-331">허브</span><span class="sxs-lookup"><span data-stu-id="ed818-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ed818-332">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ed818-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ed818-333">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="ed818-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="ed818-334">원본 간 요청 (CORS)</span><span class="sxs-lookup"><span data-stu-id="ed818-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="ed818-335">Azure SignalR 서비스 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="ed818-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
