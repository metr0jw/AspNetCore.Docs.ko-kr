---
title: ASP.NET Core Blazor WebAssembly 성능 모범 사례
author: pranavkm
description: ASP.NET Core Blazor WebAssembly 앱의 성능을 높이고 일반적인 성능 문제를 방지하기 위한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 64205dcf7aeecbe594b0164a7966b7f96bcca68b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013361"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor WebAssembly 성능 모범 사례

작성자: [Pranav Krishnamoorthy](https://github.com/pranavkm)

이 문서에서는 ASP.NET Core Blazor WebAssembly 성능 모범 사례를 위한 지침을 제공합니다.

## <a name="avoid-unnecessary-component-renders"></a>불필요한 구성 요소 렌더링 방지

Blazor의 diff 알고리즘은 알고리즘에서 구성 요소가 변경되지 않았음을 감지하는 경우 구성 요소의 렌더링을 방지합니다. 구성 요소 렌더링을 세밀하게 제어하려면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>를 재정의하세요.

초기 렌더링 이후 변경되지 않는 UI 전용 구성 요소를 작성하는 경우에는 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 `false`를 반환하도록 구성합니다.

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

대부분의 앱에는 세밀한 제어가 필요하지 않지만 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>는 UI 이벤트에 응답하는 구성 요소를 선택적으로 렌더링하는 데 사용될 수 있습니다. 수많은 구성 요소가 렌더링되는 시나리오에서는 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 사용하는 것이 중요할 수도 있습니다. 그리드의 한 셀에 있는 하나의 구성 요소에서 <xref:Microsoft.AspNetCore.Components.EventCallback>을 사용하면 그리드에서 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>가 호출되는 그리드를 살펴보겠습니다. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하면 모든 자식 구성 요소가 다시 렌더링됩니다. 적은 수의 셀만 다시 렌더링해야 하는 경우에는 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>를 사용하여 불필요한 렌더링으로 인한 성능 저하를 방지합니다.

다음 예제에서는

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 재정의되어 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 필드의 값으로 설정됩니다. 이 필드는 구성 요소가 로드될 때 초기 값으로 `false`를 갖습니다.
* 단추가 선택되면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>가 `true`로 설정되어 구성 요소가 업데이트된 `currentCount`를 사용하여 다시 렌더링되도록 강제합니다.
* 다시 렌더링이 이루어진 직후에 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>가 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>의 값을 다시 `false`로 설정하여 다음번에 단추가 선택될 때까지 추가로 다시 렌더링이 이루어지지 않도록 합니다.

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

자세한 내용은 <xref:blazor/components/lifecycle#after-component-render>를 참조하세요.

## <a name="virtualize-re-usable-fragments"></a>재사용 가능한 조각 가상화

구성 요소는 코드와 태그의 재사용 가능한 조각을 생성하는 편리한 방법을 제공합니다. 일반적으로 앱의 요구 사항에 가장 잘 맞는 개별 구성 요소를 작성하는 것이 좋습니다. 한 가지 주의할 점은 각 추가 자식 구성 요소가 부모 구성 요소를 렌더링하는 데 소요되는 총 시간에 기여한다는 것입니다. 대부분의 앱에서 추가 오버헤드는 무시해도 되는 정도입니다. 많은 수의 구성 요소를 생성하는 앱은 렌더링된 구성 요소의 수를 제한하는 등 처리 오버헤드를 줄이기 위한 전략을 고려해야 합니다.

예를 들어 구성 요소를 포함하는 수백 개의 행을 렌더링하는 그리드 또는 목록은 렌더링할 때 프로세서를 많이 사용합니다. 어떤 시점에도 구성 요소의 하위 집합만 렌더링되도록 그리드 또는 목록 레이아웃을 가상화하는 것이 좋습니다. 구성 요소 하위 집합 렌더링 예제는 [`Virtualization` 샘플 앱(aspnet/samples GitHub 리포지토리)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)에서 다음 구성 요소를 참조하세요.

* `Virtualize` 구성 요소([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): <xref:Microsoft.AspNetCore.Components.ComponentBase>를 구현하는 C#으로 작성된 구성 요소로, 사용자 스크롤을 기준으로 날씨 데이터 행 세트를 렌더링합니다.
* `FetchData` 구성 요소([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): `Virtualize` 구성 요소를 사용하여 한 번에 25개의 날씨 데이터 행을 표시합니다.

## <a name="avoid-javascript-interop-to-marshal-data"></a>JavaScript interop의 데이터 마샬링 방지

Blazor WebAssembly에서 JS(JavaScript) interop 호출은 WebAssembly-JS 경계를 트래버스해야 합니다. 이 두 가지 컨텍스트에서 콘텐츠를 직렬화 및 역직렬화하는 것은 앱에서 처리 오버헤드를 발생시킵니다. JS interop 호출이 자주 발생하면 성능이 저하되는 경우가 많습니다. 경계 주변에서 데이터 마샬링을 줄이려면 앱이 여러 개의 작은 페이로드를 하나의 큰 페이로드로 통합하여 WebAssembly와 JS 간에 이루어지는 다량의 컨텍스트 전환을 방지할 수 있는지 확인합니다.

## <a name="use-systemtextjson"></a>System.Text.Json 사용

Blazor의 JS interop 구현은 메모리 할당이 작은 고성능 JSON serialization 라이브러리인 <xref:System.Text.Json>에 의존합니다. <xref:System.Text.Json>을 사용해도 하나 이상의 대체 JSON 라이브러리를 추가하는 것에 비해 추가 앱 페이로드 크기가 발생하지 않습니다.

마이그레이션 지침은 [`Newtonsoft.Json`에서 `System.Text.Json`으로 마이그레이션하는 방법](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)을 참조하세요.

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>가능한 경우 동기식 및 역 마샬링된 JS interop API 사용

Blazor WebAssembly는 Blazor Server 앱에서 사용 가능한 단일 버전에 더해 두 개의 추가 <xref:Microsoft.JSInterop.IJSRuntime> 버전을 제공합니다.

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>을 사용하면 JS interop 호출을 동기식으로 호출할 수 있습니다. 이렇게 하면 비동기식 버전에 비해 오버헤드가 덜 발생합니다.

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>에서는 역 마샬링된 JS interop 호출이 허용됩니다.

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>을 사용하는 것이 JS interop 접근 방식 중에서 오버헤드가 가장 덜 발생하지만, 이러한 API와 상호 작용하는 데 필요한 JavaScript API는 현재 문서화되지 않은 상태이며 이후 릴리스에서 호환성이 손상되는 변경이 적용될 수 있습니다.

## <a name="reduce-app-size"></a>앱 크기 줄이기

### <a name="intermediate-language-il-linking"></a>IL(중간 언어) 연결

[Blazor WebAssembly 앱을 연결](xref:blazor/host-and-deploy/configure-linker)하면 앱의 이진 파일에서 사용되지 않는 코드를 잘라내어 앱 크기를 줄일 수 있습니다. 기본적으로 링커는 `Release` 구성에서 빌드할 때만 사용하도록 설정됩니다. 이 기능의 이점을 활용하려면 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령에서 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 옵션을 `Release`로 설정하여 배포를 위해 앱을 게시합니다.

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a>어셈블리 지연 로드

어셈블리가 경로에 필요한 경우 런타임에 어셈블리를 로드합니다. 자세한 내용은 <xref:blazor/webassembly-lazy-load-assemblies>를 참조하세요.

### <a name="compression"></a>압축

Blazor WebAssembly 앱이 게시될 때 게시하는 도중에 출력을 정적으로 압축하여 앱의 크기를 줄이고 런타임 압축의 오버헤드를 제거합니다. Blazor는 콘텐츠 협상을 수행하고 정적으로 압축된 파일을 처리하기 위해 서버에 의존합니다.

앱이 배포된 후에는 앱이 압축된 파일을 처리하는지 확인합니다. 브라우저의 개발자 도구에서 네트워크 탭을 조사하여 파일이 `Content-Encoding: br` 또는 `Content-Encoding: gz`로 처리되는지 확인합니다. 호스트가 압축된 파일을 처리하지 않는 경우 <xref:blazor/host-and-deploy/webassembly#compression>의 지침을 따르세요.

### <a name="disable-unused-features"></a>사용되지 않는 기능을 사용하지 않도록 설정

Blazor WebAssembly의 런타임에는 앱에서 더 작은 페이로드 크기를 위해 해당 기능이 필요하지 않은 경우 사용하지 않도록 설정할 수 있는 다음과 같은 .NET 기능이 포함되어 있습니다.

* 정확한 표준 시간대 정보를 위해 데이터 파일이 포함되어 있습니다. 앱에서 이 기능이 필요하지 않은 경우 앱의 프로젝트 파일에서 `BlazorEnableTimeZoneSupport` MSBuild 속성을 `false`로 설정하여 사용하지 않도록 설정하는 것이 좋습니다.

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>와 같은 API의 올바른 작동을 위해 데이터 정렬 정보가 포함되어 있습니다. 앱에서 데이터 정렬 데이터가 필요하지 않은 경우 앱의 프로젝트 파일에서 `BlazorWebAssemblyPreserveCollationData` MSBuild 속성을 `false`로 설정하여 사용하지 않도록 설정하는 것이 좋습니다.

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
