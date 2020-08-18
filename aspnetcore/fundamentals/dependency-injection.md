---
title: ASP.NET Core에서 종속성 주입
author: rick-anderson
description: ASP.NET Core에서 종속성 주입을 구현하는 방법 및 사용 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130663"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="2355c-103">ASP.NET Core에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="2355c-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2355c-104">작성자: [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) 및 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="2355c-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="2355c-105">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="2355c-106">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="2355c-107">옵션의 종속성 주입에 대한 자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="2355c-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2355c-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="2355c-109">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="2355c-109">Overview of dependency injection</span></span>

<span data-ttu-id="2355c-110">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="2355c-111">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="2355c-112">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="2355c-113">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="2355c-114">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="2355c-115">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="2355c-116">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="2355c-117">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="2355c-118">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="2355c-119">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="2355c-120">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="2355c-121">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="2355c-122">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="2355c-123">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="2355c-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="2355c-124">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="2355c-125">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="2355c-126">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="2355c-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="2355c-127">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="2355c-128">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="2355c-129">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="2355c-130">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="2355c-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 메서드는 단일 요청의 수명인 범위가 지정된 수명으로 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="2355c-132">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="2355c-133">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="2355c-134">DI 패턴을 사용하는 경우:</span><span class="sxs-lookup"><span data-stu-id="2355c-134">With the DI pattern:</span></span>

* <span data-ttu-id="2355c-135">컨트롤러는 구체적인 형식 `MyDependency`를 사용하지 않고 인터페이스 `IMyDependency`만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="2355c-136">따라서 컨트롤러에서 사용하는 구현을 컨트롤러를 수정하지 않고 쉽게 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="2355c-137">컨트롤러는 `MyDependency`의 인스턴스를 만들지 않습니다. 해당 인스턴스는 DI 컨테이너에 의해 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="2355c-138">`IMyDependency` 인터페이스의 구현을 기본 제공 로깅 API를 사용하여 향상할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="2355c-139">업데이트된 `ConfigureServices` 메서드는 새 `IMyDependency` 구현을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="2355c-140">`MyDependency2`는 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="2355c-141">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="2355c-142">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="2355c-143">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="2355c-144">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="2355c-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="2355c-145">`ILogger<TCategoryName>`은 [프레임워크에서 제공하는 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="2355c-146">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="2355c-147">종속성 주입 용어에서 서비스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="2355c-148">일반적으로 앱의 다른 코드에 `IMyDependency` 서비스와 같은 서비스를 제공하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="2355c-149">서비스에서 웹 서비스를 사용할 수 있지만 웹 서비스와 관련 있는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="2355c-150">해당 프레임워크는 강력한 [로깅](xref:fundamentals/logging/index) 시스템을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="2355c-151">`IMyDependency` 구현은 로깅을 구현하는 것이 아니라 기본 DI를 보여 주기 위해 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="2355c-152">대부분의 앱에서는 로거를 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="2355c-153">다음 코드에서는 `ConfigureServices`에 서비스를 등록하지 않아도 되는 기본 로깅을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="2355c-154">위의 코드를 사용하여 프레임워크에서 [로깅](xref:fundamentals/logging/index)을 제공하므로 `ConfigureServices`를 업데이트할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="2355c-155">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="2355c-155">Services injected into Startup</span></span>

<span data-ttu-id="2355c-156">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="2355c-157">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="2355c-158">자세한 내용은 <xref:fundamentals/startup> 및 [시작 시 구성 액세스](xref:fundamentals/configuration/index#access-configuration-in-startup)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="2355c-159">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="2355c-159">Register additional services with extension methods</span></span>

<span data-ttu-id="2355c-160">서비스 컬렉션 확장 메서드를 사용하여 서비스를 등록할 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="2355c-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="2355c-161">규칙은 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="2355c-162">종속 서비스도 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-162">The dependent services are also registered.</span></span>

<span data-ttu-id="2355c-163">다음 코드는 개별 사용자 계정을 사용하는 Razor Pages 템플릿으로 생성되며 확장 메서드 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="2355c-164">자세한 내용은 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 및 <xref:security/authentication/identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="2355c-165">확장 메서드를 작성하여 서비스를 등록하는 방법에 대한 지침은 [서비스 컬렉션 결합](#csc) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="2355c-166">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="2355c-166">Service lifetimes</span></span>

<span data-ttu-id="2355c-167">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="2355c-168">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="2355c-169">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-169">Transient</span></span>

<span data-ttu-id="2355c-170">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="2355c-171">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="2355c-172">요청을 처리하는 앱에서 Transient 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="2355c-173">Scoped</span><span class="sxs-lookup"><span data-stu-id="2355c-173">Scoped</span></span>

<span data-ttu-id="2355c-174">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="2355c-175">Entity Framework Core를 사용하는 경우 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 확장 메서드는 기본적으로 범위가 지정된 수명으로 `DbContext` 형식을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="2355c-176">요청을 처리하는 앱에서 Scoped 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="2355c-177">다음 방법 중 하나를 사용하여 미들웨어에서 범위 지정 서비스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="2355c-178">`Invoke` 또는 `InvokeAsync` 메서드에 서비스를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="2355c-179">[생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)으로 주입하면 서비스가 싱글톤처럼 작동하게 하므로 런타임에 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="2355c-180">[수명 및 등록 옵션](#lifetime-and-registration-options)의 샘플에서는 `InvokeAsync` 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="2355c-181">[팩터리 기반 미들웨어](<xref:fundamentals/middleware/extensibility>).</span><span class="sxs-lookup"><span data-stu-id="2355c-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="2355c-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 확장 메서드는 미들웨어의 등록된 형식이 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 구현하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="2355c-183">구현하는 경우 컨테이너에 등록된 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 인스턴스는 규칙 기반 미들웨어 활성화 논리를 사용하는 대신 <xref:Microsoft.AspNetCore.Http.IMiddleware> 구현을 확인하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="2355c-184">미들웨어는 앱의 서비스 컨테이너의 범위가 지정된 서비스 또는 일시적인 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="2355c-185">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="2355c-186">범위가 지정된 서비스를 싱글톤에서 해결하지 ***마세요***.</span><span class="sxs-lookup"><span data-stu-id="2355c-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="2355c-187">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="2355c-188">다음 작업은 괜찮습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-188">It's fine to:</span></span>

* <span data-ttu-id="2355c-189">범위가 지정된 서비스 또는 임시 서비스에서 싱클톤 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="2355c-190">다른 범위가 지정된 서비스 또는 임시 서비스에서 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="2355c-191">기본적으로 개발 환경에서 수명이 더 긴 다른 서비스에서 서비스를 해결하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="2355c-192">자세한 내용은 [범위 유효성 검사](#sv)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="2355c-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-193">Singleton</span></span>

<span data-ttu-id="2355c-194">싱클톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 다음과 같은 경우 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="2355c-195">처음 요청되는 경우</span><span class="sxs-lookup"><span data-stu-id="2355c-195">The first time they're requested.</span></span>
* <span data-ttu-id="2355c-196">개발자가 구현 인스턴스를 컨테이너에 직접 제공하는 경우</span><span class="sxs-lookup"><span data-stu-id="2355c-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="2355c-197">(이 방법은 거의 필요하지 않습니다.)</span><span class="sxs-lookup"><span data-stu-id="2355c-197">This approach is rarely needed.</span></span>

<span data-ttu-id="2355c-198">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="2355c-199">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="2355c-200">싱글톤 디자인 패턴을 구현하지 말고 싱글톤을 삭제하는 코드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="2355c-201">컨테이너에서 서비스를 해결한 코드에서는 서비스를 삭제하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="2355c-202">형식 또는 팩터리가 싱글톤으로 등록된 경우 컨테이너에서 싱글톤을 자동으로 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="2355c-203">싱글톤 서비스는 스레드로부터 안전해야 하며 상태 비저장 서비스에서 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="2355c-204">요청을 처리하는 앱에서 싱글톤 서비스는 애플리케이션 종료 시 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>가 삭제될 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="2355c-205">앱이 종료될 때까지 메모리가 해제되지 않으므로 싱글톤에서의 메모리 사용을 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="2355c-206">범위가 지정된 서비스를 싱글톤에서 해결하지 ***마세요***.</span><span class="sxs-lookup"><span data-stu-id="2355c-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="2355c-207">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="2355c-208">범위가 지정된 서비스 또는 임시 서비스에서 싱글톤 서비스를 해결하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="2355c-209">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="2355c-209">Service registration methods</span></span>

<span data-ttu-id="2355c-210">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="2355c-211">메서드</span><span class="sxs-lookup"><span data-stu-id="2355c-211">Method</span></span> | <span data-ttu-id="2355c-212">자동</span><span class="sxs-lookup"><span data-stu-id="2355c-212">Automatic</span></span><br><span data-ttu-id="2355c-213">개체</span><span class="sxs-lookup"><span data-stu-id="2355c-213">object</span></span><br><span data-ttu-id="2355c-214">삭제</span><span class="sxs-lookup"><span data-stu-id="2355c-214">disposal</span></span> | <span data-ttu-id="2355c-215">여러</span><span class="sxs-lookup"><span data-stu-id="2355c-215">Multiple</span></span><br><span data-ttu-id="2355c-216">구현</span><span class="sxs-lookup"><span data-stu-id="2355c-216">implementations</span></span> | <span data-ttu-id="2355c-217">인수 전달</span><span class="sxs-lookup"><span data-stu-id="2355c-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="2355c-218">예:</span><span class="sxs-lookup"><span data-stu-id="2355c-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="2355c-219">예</span><span class="sxs-lookup"><span data-stu-id="2355c-219">Yes</span></span> | <span data-ttu-id="2355c-220">예</span><span class="sxs-lookup"><span data-stu-id="2355c-220">Yes</span></span> | <span data-ttu-id="2355c-221">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="2355c-222">예제:</span><span class="sxs-lookup"><span data-stu-id="2355c-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="2355c-223">예</span><span class="sxs-lookup"><span data-stu-id="2355c-223">Yes</span></span> | <span data-ttu-id="2355c-224">예</span><span class="sxs-lookup"><span data-stu-id="2355c-224">Yes</span></span> | <span data-ttu-id="2355c-225">예</span><span class="sxs-lookup"><span data-stu-id="2355c-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="2355c-226">예:</span><span class="sxs-lookup"><span data-stu-id="2355c-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="2355c-227">예</span><span class="sxs-lookup"><span data-stu-id="2355c-227">Yes</span></span> | <span data-ttu-id="2355c-228">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-228">No</span></span> | <span data-ttu-id="2355c-229">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="2355c-230">예제:</span><span class="sxs-lookup"><span data-stu-id="2355c-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="2355c-231">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-231">No</span></span> | <span data-ttu-id="2355c-232">예</span><span class="sxs-lookup"><span data-stu-id="2355c-232">Yes</span></span> | <span data-ttu-id="2355c-233">예</span><span class="sxs-lookup"><span data-stu-id="2355c-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="2355c-234">예제:</span><span class="sxs-lookup"><span data-stu-id="2355c-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="2355c-235">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-235">No</span></span> | <span data-ttu-id="2355c-236">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-236">No</span></span> | <span data-ttu-id="2355c-237">예</span><span class="sxs-lookup"><span data-stu-id="2355c-237">Yes</span></span> |

<span data-ttu-id="2355c-238">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="2355c-239">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="2355c-240">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="2355c-241">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="2355c-242">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="2355c-243">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="2355c-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 ‘동일한 형식’의 구현이 아직 없는 경우 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="2355c-245">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="2355c-246">서비스를 등록할 때 개발자는 동일한 형식 중 하나가 아직 추가되지 않은 경우 인스턴스를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="2355c-247">일반적으로 라이브러리 작성자는 `TryAddEnumerable`을 사용하여 컨테이너에 있는 구현의 여러 복사본을 등록하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="2355c-248">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="2355c-249">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="2355c-250">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="2355c-251">서비스 등록은 동일한 형식의 여러 구현을 등록하는 경우를 제외하고 일반적으로 순서와 상관이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="2355c-252">`IServiceCollection`은 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="2355c-253">다음 코드에서는 생성자를 사용하여 서비스를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="2355c-254">`Add{LIFETIME}` 메서드는 같은 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="2355c-255">예를 들어 [AddScoped의 소스 코드](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="2355c-256">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="2355c-256">Constructor injection behavior</span></span>

<span data-ttu-id="2355c-257">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="2355c-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="2355c-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="2355c-259">종속성 주입 컨테이너에 서비스 등록 없이 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="2355c-260">[태그 도우미](xref:mvc/views/tag-helpers/intro), MVC 컨트롤러 및 [모델 바인더](xref:mvc/models/model-binding)와 같은 프레임워크 기능에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="2355c-261">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="2355c-262">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="2355c-263">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="2355c-264">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="2355c-265">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="2355c-265">Entity Framework contexts</span></span>

<span data-ttu-id="2355c-266">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="2355c-267">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="2355c-268">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="2355c-269">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="2355c-269">Lifetime and registration options</span></span>

<span data-ttu-id="2355c-270">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 식별자인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="2355c-271">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="2355c-272">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="2355c-273">`Operation` 생성자는 GUID가 지정되지 않은 경우 GUID의 마지막 4자를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="2355c-274">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="2355c-275">샘플 앱에서는 요청 내의 개체 수명과 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="2355c-276">샘플 앱의 `IndexModel` 및 미들웨어는 각 종류의 `IOperation` 형식을 요청하고 `OperationId`를 로그합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="2355c-277">미들웨어는 `IndexModel`과 비슷하며 동일한 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="2355c-278">범위가 지정된 서비스는 `InvokeAsync` 메서드에서 해결해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="2355c-279">로거 출력은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-279">The logger output shows:</span></span>

* <span data-ttu-id="2355c-280">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-280">*Transient* objects are always different.</span></span> <span data-ttu-id="2355c-281">임시 `OperationId` 값은 `IndexModel` 및 미들웨어에서 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="2355c-282">‘범위가 지정된’ 개체는 각 요청에서 동일하지만 각 요청 간에 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="2355c-283">‘싱글톤’ 개체는 모든 요청에 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="2355c-284">로깅 출력을 줄이려면 *appsettings.Development.json* 파일에서 “Logging:LogLevel:Microsoft:Error”를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="2355c-285">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="2355c-285">Call services from main</span></span>

<span data-ttu-id="2355c-286">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="2355c-287">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="2355c-288">위의 코드는 Razor Pages 자습서의 [시드 이니셜라이저 추가](xref:tutorials/razor-pages/sql?#add-the-seed-initializer)에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="2355c-289">다음 예제에서는 `Program.Main`에서 `IMyDependency`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="2355c-290">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="2355c-290">Scope validation</span></span>

<span data-ttu-id="2355c-291">앱이 [개발 환경](xref:fundamentals/environments)에서 실행 중이고 호스트를 빌드하기 위해 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)를 호출하는 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="2355c-292">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="2355c-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="2355c-293">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="2355c-294">임시 지정된 서비스는 직접 또는 간접적으로 싱글톤이나 범위가 지정된 서비스에 주입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="2355c-295">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="2355c-296">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="2355c-297">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="2355c-298">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱이 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="2355c-299">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="2355c-300">자세한 내용은 [범위 유효성 검사](xref:fundamentals/host/web-host#scope-validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="2355c-301">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="2355c-301">Request Services</span></span>

<span data-ttu-id="2355c-302">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="2355c-303">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="2355c-304">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="2355c-305">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="2355c-306">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="2355c-307">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="2355c-308">ASP.NET Core는 요청당 범위를 만들고 `RequestServices`는 범위가 지정된 서비스 공급자를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="2355c-309">범위가 지정된 모든 서비스는 요청이 활성 상태인 동안 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="2355c-310">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="2355c-311">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="2355c-311">Design services for dependency injection</span></span>

<span data-ttu-id="2355c-312">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-312">Best practices are to:</span></span>

* <span data-ttu-id="2355c-313">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="2355c-314">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="2355c-315">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="2355c-316">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="2355c-317">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="2355c-318">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="2355c-319">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="2355c-320">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="2355c-321">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="2355c-322">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="2355c-322">Disposal of services</span></span>

<span data-ttu-id="2355c-323">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="2355c-324">컨테이너에서 서비스를 해결한 코드에서는 서비스를 삭제하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="2355c-325">형식 또는 팩터리가 싱글톤으로 등록된 경우 컨테이너에서 싱글톤을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="2355c-326">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2355c-327">디버그 콘솔에는 인덱스 페이지를 새로 고칠 때마다 다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="2355c-328">서비스 컨테이너에서 만들지 않은 서비스</span><span class="sxs-lookup"><span data-stu-id="2355c-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="2355c-329">다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="2355c-330">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="2355c-330">In the preceding code:</span></span>

* <span data-ttu-id="2355c-331">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="2355c-332">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="2355c-333">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="2355c-334">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="2355c-335">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="2355c-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="2355c-336">임시적인 제한 수명</span><span class="sxs-lookup"><span data-stu-id="2355c-336">Transient, limited lifetime</span></span>

<span data-ttu-id="2355c-337">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="2355c-337">**Scenario**</span></span>

<span data-ttu-id="2355c-338">앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="2355c-339">인스턴스가 루트 범위(루트 컨테이너)에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="2355c-340">범위가 끝나기 전에 인스턴스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="2355c-341">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="2355c-341">**Solution**</span></span>

<span data-ttu-id="2355c-342">부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="2355c-343">이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="2355c-344">최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="2355c-345">해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="2355c-346">해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="2355c-347">공유 인스턴스 및 제한 수명</span><span class="sxs-lookup"><span data-stu-id="2355c-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="2355c-348">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="2355c-348">**Scenario**</span></span>

<span data-ttu-id="2355c-349">앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="2355c-350">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="2355c-350">**Solution**</span></span>

<span data-ttu-id="2355c-351">인스턴스를 범위가 지정된 수명으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="2355c-352"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="2355c-353">범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="2355c-354">수명을 종료해야 하는 경우 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="2355c-355">일반 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="2355c-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="2355c-356">임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="2355c-357">대신 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="2355c-358">루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="2355c-359">일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="2355c-360">DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="2355c-361"><xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="2355c-362">범위는 서비스의 수명을 제어하는 데 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="2355c-363">범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="2355c-364">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="2355c-364">Default service container replacement</span></span>

<span data-ttu-id="2355c-365">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="2355c-366">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="2355c-367">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="2355c-367">Property injection</span></span>
* <span data-ttu-id="2355c-368">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="2355c-368">Injection based on name</span></span>
* <span data-ttu-id="2355c-369">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="2355c-369">Child containers</span></span>
* <span data-ttu-id="2355c-370">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="2355c-370">Custom lifetime management</span></span>
* <span data-ttu-id="2355c-371">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="2355c-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="2355c-372">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="2355c-372">Convention-based registration</span></span>

<span data-ttu-id="2355c-373">ASP.NET Core 앱에서 사용할 수 있는 타사 컨테이너는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="2355c-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="2355c-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="2355c-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="2355c-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="2355c-376">유예</span><span class="sxs-lookup"><span data-stu-id="2355c-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="2355c-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="2355c-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="2355c-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="2355c-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="2355c-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="2355c-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="2355c-380">Unity</span><span class="sxs-lookup"><span data-stu-id="2355c-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="2355c-381">스레드로부터의 안전성</span><span class="sxs-lookup"><span data-stu-id="2355c-381">Thread safety</span></span>

<span data-ttu-id="2355c-382">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="2355c-383">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="2355c-384">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="2355c-385">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="2355c-386">권장 사항</span><span class="sxs-lookup"><span data-stu-id="2355c-386">Recommendations</span></span>

* <span data-ttu-id="2355c-387">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="2355c-388">C#은 비동기 생성자를 지원하지 않으므로</span><span class="sxs-lookup"><span data-stu-id="2355c-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="2355c-389">서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="2355c-390">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="2355c-391">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="2355c-392">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="2355c-393">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="2355c-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="2355c-394">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="2355c-395">서비스에 정적 액세스를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="2355c-395">Avoid static access to services.</span></span> <span data-ttu-id="2355c-396">예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="2355c-397">DI 팩터리를 빠르고 동기적으로 유지하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="2355c-398">‘서비스 로케이터 패턴’을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="2355c-399">예를 들어 DI를 대신 사용할 수 있는 경우 서비스 인스턴스를 가져오기 위해 <xref:System.IServiceProvider.GetService*>를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="2355c-400">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="2355c-400">**Incorrect:**</span></span>

    ![잘못된 코드](dependency-injection/_static/bad.png)

  <span data-ttu-id="2355c-402">**올바른 예**:</span><span class="sxs-lookup"><span data-stu-id="2355c-402">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="2355c-403">피해야 하는 또 다른 서비스 로케이터 변형은 런타임에 종속성을 해결하는 팩터리를 주입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="2355c-404">이러한 두 가지 방법 모두 [제어 반전](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="2355c-405">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="2355c-406">`ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="2355c-407">일반적으로 `BuildServiceProvider`는 개발자가 `ConfigureServices`에서 서비스를 해결하려는 경우 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="2355c-408">구성에서 `LoginPath`를 가져와야 하는 경우를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="2355c-409">다음 코드를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-409">Avoid the following code:</span></span>

  ![BuildServiceProvider를 호출하는 잘못된 코드](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="2355c-411">위 이미지에서 `services.BuildServiceProvider` 아래의 녹색 물결 모양 줄을 선택하면 다음 ASP0000 경고가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="2355c-412">ASP0000 애플리케이션 코드에서 ‘BuildServiceProvider’를 호출하면 싱글톤 서비스의 추가 복사본이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="2355c-413">‘Configure’의 매개 변수로 종속성 주입 서비스와 같은 다른 방법을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="2355c-414">`BuildServiceProvider`를 호출하면 두 번째 컨테이너가 만들어지므로, 조각난 싱글톤이 생성되고 여러 컨테이너의 개체 그래프를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="2355c-415">`LoginPath`를 가져오는 올바른 방법은 DI와 함께 옵션 패턴을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="2355c-416">삭제 가능한 임시 서비스는 삭제를 위해 컨테이너에서 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="2355c-417">따라서 최상위 컨테이너에서 해결할 경우 메모리 누수가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="2355c-418">범위 유효성 검사를 사용하도록 설정하여 앱의 범위가 지정된 서비스에서 싱글톤을 캡처하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="2355c-419">자세한 내용은 [범위 유효성 검사](#scope-validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="2355c-420">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="2355c-421">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="2355c-422">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="2355c-423">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="2355c-424">DI의 다중 테넌트에 권장되는 패턴</span><span class="sxs-lookup"><span data-stu-id="2355c-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="2355c-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore)는 다중 테넌트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="2355c-426">자세한 내용은 [Orchard Core 설명서](https://docs.orchardcore.net/en/dev/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="2355c-427">CMS 고유 기능 없이 Orchard Core Framework를 사용하여 모듈식 다중 테넌트 앱을 빌드하는 방법에 대한 예제는 https://github.com/OrchardCMS/OrchardCore.Samples 에서 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="2355c-428">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="2355c-428">Framework-provided services</span></span>

<span data-ttu-id="2355c-429">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="2355c-430">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="2355c-431">ASP.NET Core 템플릿을 기반으로 하는 앱에는 프레임워크에서 등록된 250개 이상의 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="2355c-432">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="2355c-433">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="2355c-433">Service Type</span></span> | <span data-ttu-id="2355c-434">수명</span><span class="sxs-lookup"><span data-stu-id="2355c-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="2355c-435">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="2355c-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="2355c-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="2355c-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="2355c-439">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="2355c-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="2355c-441">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="2355c-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="2355c-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="2355c-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="2355c-445">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="2355c-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="2355c-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="2355c-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="2355c-449">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2355c-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="2355c-450">IDisposables in ASP.NET Core를 삭제하는 네 가지 방법</span><span class="sxs-lookup"><span data-stu-id="2355c-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="2355c-451">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="2355c-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="2355c-452">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="2355c-452">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="2355c-453">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="2355c-453">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="2355c-454">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="2355c-454">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2355c-455">작성자: [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) 및 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="2355c-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="2355c-456">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="2355c-457">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="2355c-458">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2355c-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="2355c-459">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="2355c-459">Overview of dependency injection</span></span>

<span data-ttu-id="2355c-460">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="2355c-461">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="2355c-462">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="2355c-463">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="2355c-464">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="2355c-465">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="2355c-466">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="2355c-467">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="2355c-468">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="2355c-469">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="2355c-470">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="2355c-471">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="2355c-472">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="2355c-473">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="2355c-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="2355c-474">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="2355c-475">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="2355c-476">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="2355c-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="2355c-477">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="2355c-478">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="2355c-479">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="2355c-480">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="2355c-481">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="2355c-482">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="2355c-483">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="2355c-484">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="2355c-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="2355c-485">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="2355c-486">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2355c-487">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="2355c-488">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="2355c-489">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="2355c-490">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="2355c-491">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="2355c-492">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="2355c-493">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="2355c-494">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="2355c-495">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="2355c-496">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="2355c-497">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="2355c-498">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="2355c-499">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="2355c-500">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="2355c-500">Services injected into Startup</span></span>

<span data-ttu-id="2355c-501">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="2355c-502">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="2355c-503">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="2355c-504">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="2355c-504">Framework-provided services</span></span>

<span data-ttu-id="2355c-505">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="2355c-506">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="2355c-507">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="2355c-508">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="2355c-509">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="2355c-509">Service Type</span></span> | <span data-ttu-id="2355c-510">수명</span><span class="sxs-lookup"><span data-stu-id="2355c-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="2355c-511">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="2355c-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="2355c-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="2355c-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="2355c-515">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="2355c-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="2355c-517">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="2355c-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="2355c-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="2355c-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="2355c-521">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="2355c-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="2355c-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="2355c-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="2355c-525">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="2355c-525">Register additional services with extension methods</span></span>

<span data-ttu-id="2355c-526">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="2355c-527">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="2355c-528">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="2355c-529">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="2355c-529">Service lifetimes</span></span>

<span data-ttu-id="2355c-530">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="2355c-531">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="2355c-532">Transient</span><span class="sxs-lookup"><span data-stu-id="2355c-532">Transient</span></span>

<span data-ttu-id="2355c-533">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="2355c-534">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="2355c-535">요청을 처리하는 앱에서 Transient 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="2355c-536">Scoped</span><span class="sxs-lookup"><span data-stu-id="2355c-536">Scoped</span></span>

<span data-ttu-id="2355c-537">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="2355c-538">요청을 처리하는 앱에서 Scoped 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="2355c-539">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="2355c-540">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="2355c-541">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="2355c-542">Singleton</span><span class="sxs-lookup"><span data-stu-id="2355c-542">Singleton</span></span>

<span data-ttu-id="2355c-543">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="2355c-544">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="2355c-545">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="2355c-546">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="2355c-547">요청을 처리하는 앱에서 Singleton 서비스는 앱 종료 시 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>가 삭제될 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="2355c-548">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="2355c-549">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="2355c-550">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="2355c-550">Service registration methods</span></span>

<span data-ttu-id="2355c-551">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="2355c-552">메서드</span><span class="sxs-lookup"><span data-stu-id="2355c-552">Method</span></span> | <span data-ttu-id="2355c-553">자동</span><span class="sxs-lookup"><span data-stu-id="2355c-553">Automatic</span></span><br><span data-ttu-id="2355c-554">개체</span><span class="sxs-lookup"><span data-stu-id="2355c-554">object</span></span><br><span data-ttu-id="2355c-555">삭제</span><span class="sxs-lookup"><span data-stu-id="2355c-555">disposal</span></span> | <span data-ttu-id="2355c-556">여러</span><span class="sxs-lookup"><span data-stu-id="2355c-556">Multiple</span></span><br><span data-ttu-id="2355c-557">구현</span><span class="sxs-lookup"><span data-stu-id="2355c-557">implementations</span></span> | <span data-ttu-id="2355c-558">인수 전달</span><span class="sxs-lookup"><span data-stu-id="2355c-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="2355c-559">예:</span><span class="sxs-lookup"><span data-stu-id="2355c-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="2355c-560">예</span><span class="sxs-lookup"><span data-stu-id="2355c-560">Yes</span></span> | <span data-ttu-id="2355c-561">예</span><span class="sxs-lookup"><span data-stu-id="2355c-561">Yes</span></span> | <span data-ttu-id="2355c-562">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="2355c-563">예제:</span><span class="sxs-lookup"><span data-stu-id="2355c-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="2355c-564">예</span><span class="sxs-lookup"><span data-stu-id="2355c-564">Yes</span></span> | <span data-ttu-id="2355c-565">예</span><span class="sxs-lookup"><span data-stu-id="2355c-565">Yes</span></span> | <span data-ttu-id="2355c-566">예</span><span class="sxs-lookup"><span data-stu-id="2355c-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="2355c-567">예:</span><span class="sxs-lookup"><span data-stu-id="2355c-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="2355c-568">예</span><span class="sxs-lookup"><span data-stu-id="2355c-568">Yes</span></span> | <span data-ttu-id="2355c-569">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-569">No</span></span> | <span data-ttu-id="2355c-570">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="2355c-571">예제:</span><span class="sxs-lookup"><span data-stu-id="2355c-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="2355c-572">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-572">No</span></span> | <span data-ttu-id="2355c-573">예</span><span class="sxs-lookup"><span data-stu-id="2355c-573">Yes</span></span> | <span data-ttu-id="2355c-574">예</span><span class="sxs-lookup"><span data-stu-id="2355c-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="2355c-575">예제:</span><span class="sxs-lookup"><span data-stu-id="2355c-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="2355c-576">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-576">No</span></span> | <span data-ttu-id="2355c-577">아니요</span><span class="sxs-lookup"><span data-stu-id="2355c-577">No</span></span> | <span data-ttu-id="2355c-578">예</span><span class="sxs-lookup"><span data-stu-id="2355c-578">Yes</span></span> |

<span data-ttu-id="2355c-579">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="2355c-580">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="2355c-581">`TryAdd{LIFETIME}` 메서드는 등록된 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="2355c-582">다음 예제에서 첫 번째 줄은 `IMyDependency`에 대한 `MyDependency`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="2355c-583">두 번째 줄은 `IMyDependency`에 이미 등록된 구현이 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="2355c-584">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="2355c-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식*의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="2355c-586">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="2355c-587">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="2355c-588">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="2355c-589">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="2355c-590">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="2355c-591">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="2355c-592">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="2355c-592">Constructor injection behavior</span></span>

<span data-ttu-id="2355c-593">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="2355c-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="2355c-595">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="2355c-596">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="2355c-597">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="2355c-598">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="2355c-599">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="2355c-600">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="2355c-600">Entity Framework contexts</span></span>

<span data-ttu-id="2355c-601">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="2355c-602">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="2355c-603">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="2355c-604">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="2355c-604">Lifetime and registration options</span></span>

<span data-ttu-id="2355c-605">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="2355c-606">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="2355c-607">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="2355c-608">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="2355c-609">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="2355c-610">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="2355c-611">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="2355c-612">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="2355c-613">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="2355c-614">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="2355c-615">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="2355c-616">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="2355c-617">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="2355c-618">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="2355c-619">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="2355c-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="2355c-620">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="2355c-621">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="2355c-622">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="2355c-623">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="2355c-624">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="2355c-624">**First request:**</span></span>

<span data-ttu-id="2355c-625">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="2355c-625">Controller operations:</span></span>

<span data-ttu-id="2355c-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="2355c-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="2355c-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="2355c-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="2355c-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="2355c-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="2355c-629">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="2355c-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="2355c-630">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="2355c-630">`OperationService` operations:</span></span>

<span data-ttu-id="2355c-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="2355c-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="2355c-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="2355c-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="2355c-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="2355c-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="2355c-634">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="2355c-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="2355c-635">**두 번째 요청**:</span><span class="sxs-lookup"><span data-stu-id="2355c-635">**Second request:**</span></span>

<span data-ttu-id="2355c-636">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="2355c-636">Controller operations:</span></span>

<span data-ttu-id="2355c-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="2355c-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="2355c-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="2355c-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="2355c-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="2355c-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="2355c-640">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="2355c-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="2355c-641">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="2355c-641">`OperationService` operations:</span></span>

<span data-ttu-id="2355c-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="2355c-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="2355c-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="2355c-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="2355c-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="2355c-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="2355c-645">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="2355c-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="2355c-646">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="2355c-647">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-647">*Transient* objects are always different.</span></span> <span data-ttu-id="2355c-648">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="2355c-649">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="2355c-650">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="2355c-651">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="2355c-652">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="2355c-652">Call services from main</span></span>

<span data-ttu-id="2355c-653">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="2355c-654">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="2355c-655">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="2355c-656">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="2355c-656">Scope validation</span></span>

<span data-ttu-id="2355c-657">앱이 개발 환경에서 실행 중인 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="2355c-658">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="2355c-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="2355c-659">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="2355c-660">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="2355c-661">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="2355c-662">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="2355c-663">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="2355c-664">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="2355c-665">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="2355c-666">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="2355c-666">Request Services</span></span>

<span data-ttu-id="2355c-667">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="2355c-668">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="2355c-669">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="2355c-670">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="2355c-671">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="2355c-672">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="2355c-673">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="2355c-674">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="2355c-674">Design services for dependency injection</span></span>

<span data-ttu-id="2355c-675">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-675">Best practices are to:</span></span>

* <span data-ttu-id="2355c-676">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="2355c-677">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="2355c-678">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="2355c-679">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="2355c-680">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="2355c-681">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="2355c-682">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="2355c-683">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="2355c-684">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="2355c-685">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="2355c-686">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="2355c-686">Disposal of services</span></span>

<span data-ttu-id="2355c-687">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="2355c-688">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="2355c-689">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="2355c-690">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="2355c-690">In the following example:</span></span>

* <span data-ttu-id="2355c-691">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="2355c-692">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="2355c-693">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="2355c-694">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="2355c-695">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="2355c-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="2355c-696">임시적인 제한 수명</span><span class="sxs-lookup"><span data-stu-id="2355c-696">Transient, limited lifetime</span></span>

<span data-ttu-id="2355c-697">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="2355c-697">**Scenario**</span></span>

<span data-ttu-id="2355c-698">앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="2355c-699">인스턴스는 루트 범위에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="2355c-700">범위가 끝나기 전에 인스턴스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="2355c-701">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="2355c-701">**Solution**</span></span>

<span data-ttu-id="2355c-702">부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="2355c-703">이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="2355c-704">최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="2355c-705">해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="2355c-706">해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="2355c-707">공유 인스턴스 및 제한 수명</span><span class="sxs-lookup"><span data-stu-id="2355c-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="2355c-708">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="2355c-708">**Scenario**</span></span>

<span data-ttu-id="2355c-709">앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="2355c-710">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="2355c-710">**Solution**</span></span>

<span data-ttu-id="2355c-711">인스턴스를 범위가 지정된 수명으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="2355c-712"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="2355c-713">범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="2355c-714">수명을 종료해야 하는 경우 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="2355c-715">일반 지침</span><span class="sxs-lookup"><span data-stu-id="2355c-715">General Guidelines</span></span>

* <span data-ttu-id="2355c-716">임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="2355c-717">대신 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="2355c-718">루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="2355c-719">일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="2355c-720">DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="2355c-721"><xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="2355c-722">범위는 서비스의 수명을 제어하는 데 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="2355c-723">범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="2355c-724">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="2355c-724">Default service container replacement</span></span>

<span data-ttu-id="2355c-725">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="2355c-726">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="2355c-727">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="2355c-727">Property injection</span></span>
* <span data-ttu-id="2355c-728">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="2355c-728">Injection based on name</span></span>
* <span data-ttu-id="2355c-729">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="2355c-729">Child containers</span></span>
* <span data-ttu-id="2355c-730">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="2355c-730">Custom lifetime management</span></span>
* <span data-ttu-id="2355c-731">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="2355c-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="2355c-732">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="2355c-732">Convention-based registration</span></span>

<span data-ttu-id="2355c-733">ASP.NET Core 앱에서 사용할 수 있는 타사 컨테이너는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="2355c-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="2355c-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="2355c-735">DryIoc</span><span class="sxs-lookup"><span data-stu-id="2355c-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="2355c-736">유예</span><span class="sxs-lookup"><span data-stu-id="2355c-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="2355c-737">LightInject</span><span class="sxs-lookup"><span data-stu-id="2355c-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="2355c-738">Lamar</span><span class="sxs-lookup"><span data-stu-id="2355c-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="2355c-739">Stashbox</span><span class="sxs-lookup"><span data-stu-id="2355c-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="2355c-740">Unity</span><span class="sxs-lookup"><span data-stu-id="2355c-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="2355c-741">스레드로부터의 안전성</span><span class="sxs-lookup"><span data-stu-id="2355c-741">Thread safety</span></span>

<span data-ttu-id="2355c-742">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="2355c-743">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="2355c-744">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="2355c-745">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="2355c-746">권장 사항</span><span class="sxs-lookup"><span data-stu-id="2355c-746">Recommendations</span></span>

* <span data-ttu-id="2355c-747">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="2355c-748">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="2355c-749">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="2355c-750">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="2355c-751">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="2355c-752">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="2355c-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="2355c-753">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="2355c-754">서비스에 정적 액세스를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="2355c-754">Avoid static access to services.</span></span> <span data-ttu-id="2355c-755">예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="2355c-756">‘서비스 로케이터 패턴’은 [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합하므로 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="2355c-757">DI를 대신 사용할 수 있는 경우에는 <xref:System.IServiceProvider.GetService*>를 호출하여 서비스 인스턴스를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="2355c-758">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="2355c-758">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="2355c-759">**올바른 예**:</span><span class="sxs-lookup"><span data-stu-id="2355c-759">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="2355c-760">런타임에 <xref:System.IServiceProvider.GetService*>를 종속성을 확인하는 팩토리를 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="2355c-761">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2355c-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="2355c-762">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="2355c-763">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="2355c-764">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="2355c-765">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2355c-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2355c-766">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2355c-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="2355c-767">IDisposables in ASP.NET Core를 삭제하는 네 가지 방법</span><span class="sxs-lookup"><span data-stu-id="2355c-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="2355c-768">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="2355c-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="2355c-769">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="2355c-769">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="2355c-770">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="2355c-770">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="2355c-771">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="2355c-771">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
