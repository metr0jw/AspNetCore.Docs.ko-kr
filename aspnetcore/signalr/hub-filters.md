---
title: ASP.NET Core에서 허브 필터 사용 SignalR
author: brecon
description: ASP.NET Core에서 허브 필터를 사용 하는 방법에 대해 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
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
uid: signalr/hub-filters
ms.openlocfilehash: c3c44efcb3702f3edb51c821d042c2e7eb1748cd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626668"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="7101a-103">ASP.NET Core에서 허브 필터 사용 SignalR</span><span class="sxs-lookup"><span data-stu-id="7101a-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="7101a-104">허브 필터:</span><span class="sxs-lookup"><span data-stu-id="7101a-104">Hub filters:</span></span>

* <span data-ttu-id="7101a-105">ASP.NET Core 5.0 이상에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="7101a-106">클라이언트에서 허브 메서드를 호출 하기 전과 후에 논리를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="7101a-107">이 문서에서는 허브 필터를 작성 및 사용 하기 위한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="7101a-108">허브 필터 구성</span><span class="sxs-lookup"><span data-stu-id="7101a-108">Configure hub filters</span></span>

<span data-ttu-id="7101a-109">허브 필터는 전역적으로 또는 허브 유형에 따라 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="7101a-110">필터를 추가 하는 순서는 필터를 실행 하는 순서입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="7101a-111">전역 허브 필터는 로컬 허브 필터 전에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="7101a-112">허브 필터는 다음 중 한 가지 방법으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="7101a-113">구체적 형식으로 필터 추가:</span><span class="sxs-lookup"><span data-stu-id="7101a-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="7101a-114">이는 DI (종속성 주입) 또는 활성화 된 유형에 서 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="7101a-115">런타임 유형별 필터 추가:</span><span class="sxs-lookup"><span data-stu-id="7101a-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="7101a-116">이는 DI 또는 형식 활성화에서 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="7101a-117">인스턴스별 필터 추가:</span><span class="sxs-lookup"><span data-stu-id="7101a-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="7101a-118">이 인스턴스는 단일 항목 처럼 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="7101a-119">모든 허브 메서드 호출에서 동일한 인스턴스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="7101a-120">허브 필터는 허브 호출 당 생성 및 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="7101a-121">전역 상태를 필터에 저장 하거나 상태를 저장 하지 않으려면 더 나은 성능을 위해 DI에 허브 필터 형식을 singleton으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="7101a-122">또는 가능 하면 필터를 인스턴스로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="7101a-123">허브 필터 만들기</span><span class="sxs-lookup"><span data-stu-id="7101a-123">Create hub filters</span></span>

<span data-ttu-id="7101a-124">에서 상속 되는 클래스를 선언 하 `IHubFilter` 고 메서드를 추가 하 여 필터를 만듭니다 `InvokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="7101a-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="7101a-125">및 `OnConnectedAsync` `OnDisconnectedAsync` 허브 메서드를 각각 래핑하는 선택적으로를 구현할 수도 `OnConnectedAsync` 있습니다 `OnDisconnectedAsync` .</span><span class="sxs-lookup"><span data-stu-id="7101a-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="7101a-126">필터는 미들웨어와 매우 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="7101a-127">`next`메서드는 다음 필터를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="7101a-128">최종 필터는 허브 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="7101a-129">또한 필터는 `next` 에서 결과를 반환 하기 전에 허브 메서드가 호출 된 후 대기 및 실행 논리의 결과를 저장할 수 있습니다 `next` .</span><span class="sxs-lookup"><span data-stu-id="7101a-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="7101a-130">필터에서 허브 메서드 호출을 건너뛰려면를 호출 하는 대신 형식의 예외를 throw `HubException` `next` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="7101a-131">클라이언트에서 결과가 예상 되는 경우 오류를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="7101a-132">허브 필터 사용</span><span class="sxs-lookup"><span data-stu-id="7101a-132">Use hub filters</span></span>

<span data-ttu-id="7101a-133">필터 논리를 작성 하는 경우 허브 메서드 이름을 확인 하는 대신 허브 메서드의 특성을 사용 하 여 제네릭으로 설정 해 봅니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="7101a-134">금지 된 구에 대 한 허브 메서드 인수를 확인 하 고 발견 되는 모든 구를 교체 하는 필터를 고려해 보세요 `***` .</span><span class="sxs-lookup"><span data-stu-id="7101a-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="7101a-135">이 예에서는 `LanguageFilterAttribute` 클래스가 정의 되어 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="7101a-136">클래스에는 `FilterArgument` 특성을 사용 하는 경우 설정할 수 있는 라는 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="7101a-137">제거할 문자열 인수를 포함 하는 허브 메서드에 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="7101a-138">허브 필터를 정의 하 여 특성을 확인 하 고 허브 메서드 인수에서 금지 된 구를 `***` 다음과 같이 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `***`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="7101a-139">메서드에 허브 필터를 등록 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7101a-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7101a-140">모든 호출에 대해 금지 된 문구 목록을 다시 초기화 하지 않도록 하기 위해 허브 필터는 단일 항목으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="7101a-141">HubInvocationContext 개체</span><span class="sxs-lookup"><span data-stu-id="7101a-141">The HubInvocationContext object</span></span>

<span data-ttu-id="7101a-142">에는 `HubInvocationContext` 현재 허브 메서드 호출에 대 한 정보가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="7101a-143">속성</span><span class="sxs-lookup"><span data-stu-id="7101a-143">Property</span></span> | <span data-ttu-id="7101a-144">Description</span><span class="sxs-lookup"><span data-stu-id="7101a-144">Description</span></span> | <span data-ttu-id="7101a-145">Type</span><span class="sxs-lookup"><span data-stu-id="7101a-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="7101a-146">에는 `HubCallerContext` 연결에 대 한 정보가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="7101a-147">이 허브 메서드 호출에 사용 되는 허브의 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="7101a-148">호출 되는 허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="7101a-149">허브 메서드에 전달 되는 인수 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="7101a-150">이 허브 메서드 호출에 대 한 범위가 지정 된 서비스 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="7101a-151">허브 메서드 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="7101a-152">HubLifetimeContext 개체</span><span class="sxs-lookup"><span data-stu-id="7101a-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="7101a-153">에는 `HubLifetimeContext` `OnConnectedAsync` 및 허브 메서드에 대 한 정보가 포함 되어 있습니다 `OnDisconnectedAsync` .</span><span class="sxs-lookup"><span data-stu-id="7101a-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="7101a-154">속성</span><span class="sxs-lookup"><span data-stu-id="7101a-154">Property</span></span> | <span data-ttu-id="7101a-155">Description</span><span class="sxs-lookup"><span data-stu-id="7101a-155">Description</span></span> | <span data-ttu-id="7101a-156">Type</span><span class="sxs-lookup"><span data-stu-id="7101a-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="7101a-157">에는 `HubCallerContext` 연결에 대 한 정보가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="7101a-158">이 허브 메서드 호출에 사용 되는 허브의 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="7101a-159">이 허브 메서드 호출에 대 한 범위가 지정 된 서비스 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="7101a-160">권한 부여 및 필터</span><span class="sxs-lookup"><span data-stu-id="7101a-160">Authorization and filters</span></span>

<span data-ttu-id="7101a-161">허브 필터 보다 먼저 실행 되는 [허브 메서드에서 특성을 부여](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) 합니다.</span><span class="sxs-lookup"><span data-stu-id="7101a-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
