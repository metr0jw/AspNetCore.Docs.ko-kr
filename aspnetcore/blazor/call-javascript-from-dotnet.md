---
title: ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출
author: guardrex
description: Blazor 앱의 .NET 메서드에서 JavaScript 함수를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: f3afa9592422da911799a137f9943631ed12ab59
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819128"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>ASP.NET Core Blazor의 .NET 메서드에서 JavaScript 함수 호출

작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

Blazor 앱은 .NET 메서드에서 JavaScript 함수를 호출하고 JavaScript 함수에서 .NET 메서드를 호출할 수 있습니다. 이러한 시나리오를 *JavaScript 상호 운용성*(*JS interop*)이라고 합니다.

이 문서에서는 .NET에서 JavaScript 함수를 호출하는 방법을 설명합니다. JavaScript에서 .NET 메서드를 호출하는 방법에 대한 자세한 내용은 <xref:blazor/call-dotnet-from-javascript>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

.NET에서 JavaScript를 호출하려면 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용합니다. JS interop 호출을 실행하려면 구성 요소에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>는 원하는 수의 JSON 직렬화 가능 인수와 함께 호출하려는 JavaScript 함수에 대한 식별자를 사용합니다. 함수 식별자는 전역 범위(`window`)를 기준으로 합니다. `window.someScope.someFunction`을 호출하려는 경우 식별자는 `someScope.someFunction`입니다. 호출되기 전에 함수를 등록할 필요는 없습니다. 반환 형식 `T` 또한 JSON 직렬화 가능해야 합니다. `T`는 반환되는 JSON 형식에 가장 잘 매핑되는 .NET 형식과 일치해야 합니다.

사전 렌더링을 사용하도록 설정한 Blazor Server 앱의 경우 초기 사전 렌더링 중에 JavaScript를 호출할 수 없습니다. JavaScript interop 호출은 브라우저와의 연결이 설정될 때까지 지연됩니다. 자세한 내용은 [Blazor Server 앱이 사전 렌더링 중인 경우 검색](#detect-when-a-blazor-server-app-is-prerendering) 섹션을 참조하세요.

다음 예제는 JavaScript 기반 디코더인 [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder)를 기준으로 합니다. 이 예제에서는 C# 메서드에서 개발자 코드의 요구 사항을 기존 JavaScript API로 오프로드하는 JavaScript 함수를 호출하는 방법을 보여 줍니다. JavaScript 함수는 C# 메서드에서 바이트 배열을 수신하고, 배열을 디코딩하고, 표시를 위해 구성 요소에 텍스트를 반환합니다.

`wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `TextDecoder`를 사용하여 전달된 배열을 디코딩하고 디코딩된 값을 반환하는 JavaScript 함수를 제공합니다.

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

앞의 예제에 표시된 코드와 같은 JavaScript 코드는 스크립트 파일에 대한 참조를 사용하여 JavaScript 파일(`.js`)에서 로드할 수도 있습니다.

```html
<script src="exampleJsInterop.js"></script>
```

다음 구성 요소는

* 구성 요소 단추( **`Convert Array`** )가 선택된 경우 `JSRuntime`을 사용하여 `convertArray` JavaScript 함수를 호출합니다.
* JavaScript 함수를 호출한 후에는 전달된 배열이 문자열로 변환됩니다. 이 문자열은 표시를 위해 구성 요소로 반환됩니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

<xref:Microsoft.JSInterop.IJSRuntime> 추상화를 사용하려면 다음 방법 중 하나를 채택합니다.

* Razor 구성 요소(`.razor`)에 <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 주입합니다.

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다. 이 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출되며 값을 반환하지 않습니다.

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <xref:Microsoft.JSInterop.IJSRuntime> 추상화를 클래스(`.cs`)에 주입합니다.

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  `wwwroot/index.html`(Blazor WebAssembly) 또는 `Pages/_Host.cshtml`(Blazor Server)의 `<head>` 요소 내에 `handleTickerChanged` JavaScript 함수를 제공합니다. 이 함수는 `JSRuntime.InvokeAsync`를 사용하여 호출되며 값을 반환합니다.

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)를 사용하여 동적 콘텐츠를 생성하려면 `[Inject]` 특성을 사용합니다.

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

이 항목과 함께 제공되는 클라이언트 쪽 샘플 앱에서는 사용자 입력을 수신하고 환영 메시지를 표시하기 위해 DOM과 상호 작용하는 두 가지 JavaScript 함수를 앱에서 사용할 수 있습니다.

* `showPrompt`: 사용자 입력(사용자 이름)을 수락하라는 메시지를 표시하고 호출자에게 이름을 반환합니다.
* `displayWelcome`: 호출자의 환영 메시지를 `id`가 `welcome`인 DOM 개체에 할당합니다.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

JavaScript 파일을 참조하는 `<script>` 태그를 `wwwroot/index.html` 파일(Blazor WebAssembly) 또는 `Pages/_Host.cshtml` 파일(Blazor Server)에 배치합니다.

`wwwroot/index.html`(Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml`(Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

`<script>` 태그를 동적으로 업데이트할 수 없으므로 구성 요소 파일에 `<script>` 태그를 넣지 마세요.

.NET 메서드는 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>을 호출하여 `exampleJsInterop.js` 파일의 JavaScript 함수와 상호 운용합니다.

<xref:Microsoft.JSInterop.IJSRuntime> 추상화는 Blazor Server 시나리오를 허용하기 위해 비동기적으로 진행됩니다. 앱이 Blazor WebAssembly 앱이고 JavaScript 함수를 동기적으로 호출하려는 경우에는 <xref:Microsoft.JSInterop.IJSInProcessRuntime>으로 다운캐스트하고 대신 <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A>를 호출합니다. 대부분의 JS interop 라이브러리는 비동기 API를 사용하여 모든 시나리오에서 라이브러리를 사용할 수 있도록 하는 것이 좋습니다.

샘플 앱에는 JS interop를 시연하기 위한 구성 요소가 포함되어 있습니다. 이 구성 요소는 다음을 수행합니다.

* JavaScript 프롬프트를 통해 사용자 입력을 받습니다.
* 처리를 위해 구성 요소에 텍스트를 반환합니다.
* DOM과 상호 작용하여 환영 메시지를 표시하는 두 번째 JavaScript 함수를 호출합니다.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

1. 구성 요소의 **`Trigger JavaScript Prompt`** 단추를 선택하여 `TriggerJsPrompt`가 실행되면 `wwwroot/exampleJsInterop.js` 파일에 제공된 JavaScript `showPrompt` 함수가 호출됩니다.
1. `showPrompt` 함수는 HTML로 인코딩되고 구성 요소로 반환되는 사용자 입력(사용자 이름)을 수락합니다. 이 구성 요소는 지역 변수, `name`에 사용자 이름을 저장합니다.
1. `name`에 저장된 문자열은 환영 메시지에 통합됩니다.그런 후 이 메시지는 JavaScript 함수, `displayWelcome`에 전달되고, 이 함수는 환영 메시지를 제목 태그로 렌더링합니다.

## <a name="call-a-void-javascript-function"></a>Void JavaScript 함수 호출

[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) 또는 [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined)를 반환하는 JavaScript 함수는 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용하여 호출됩니다.

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Blazor Server 앱을 미리 렌더링 중인 경우 검색
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>요소에 대한 참조 캡처

일부 JS interop 시나리오에는 HTML 요소에 대한 참조가 필요합니다. 예를 들어, UI 라이브러리에는 초기화를 위해 요소 참조가 필요하거나, `focus` 또는 `play`와 같은 요소에서 명령 같은 API를 호출해야 할 수 있습니다.

다음 방법을 사용하여 구성 요소의 HTML 요소에 대한 참조를 캡처합니다.

* HTML 요소에 `@ref` 특성을 추가합니다.
* 해당 이름이 `@ref` 특성 값과 일치하는 <xref:Microsoft.AspNetCore.Components.ElementReference> 형식의 필드를 정의합니다.

다음 예에서는 `username` `<input>` 요소에 대한 참조를 캡처하는 방법을 보여 줍니다.

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> 요소 참조만 사용하여 Blazor와 상호 작용하지 않는 빈 요소의 내용을 변경합니다. 이 시나리오는 타사 API가 요소에 콘텐츠를 제공하는 경우에 유용합니다. Blazor는 요소와 상호 작용하지 않으므로 요소의 Blazor 표시와 DOM 간에 충돌이 발생할 가능성이 없습니다.
>
> 다음 예제에서는 Blazor가 DOM과 상호 작용하여 이 요소의 목록 항목(`<li>`)을 채우기 때문에 순서가 지정되지 않은 목록(`ul`)의 콘텐츠를 변경하는 것은 *위험*합니다.
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> JS interop이 요소 `MyList`의 내용을 변경하고 Blazor가 요소에 diff를 적용하려고 하면 diff는 DOM과 일치하지 않습니다.

.NET 코드와 관련하여 <xref:Microsoft.AspNetCore.Components.ElementReference>는 불투명 핸들입니다. <xref:Microsoft.AspNetCore.Components.ElementReference>으로 수행할 수 있는 *유일한* 작업이 JS interop을 통해 JavaScript 코드에 전달하는 것입니다. 이렇게 하면 JavaScript 쪽 코드는 일반적인 DOM API에서 사용할 수 있는 `HTMLElement` 인스턴스를 수신합니다.

예를 들어, 다음 코드는 요소에 포커스를 설정할 수 있도록 하는 .NET 확장 메서드를 정의합니다.

`exampleJsInterop.js`:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

값을 반환하지 않는 JavaScript 함수를 호출하려면 <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>를 사용합니다. 다음 코드는 캡처된 <xref:Microsoft.AspNetCore.Components.ElementReference>로 이전 JavaScript 함수를 호출하여 사용자 이름 입력에 포커스를 설정합니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

확장 메서드를 사용하려면 <xref:Microsoft.JSInterop.IJSRuntime> 인스턴스를 수신하는 정적 확장 메서드를 만듭니다.

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` 메서드는 개체에서 직접 호출됩니다. 다음 예제에서는 `Focus` 메서드를 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username` 변수는 구성 요소가 렌더링된 후에만 채워집니다. JavaScript 코드에 채워지지 않은 <xref:Microsoft.AspNetCore.Components.ElementReference>가 전달되면 JavaScript 코드는 `null` 값을 받습니다. 구성 요소에서 렌더링을 완료한 후 요소에 초기 포커스를 설정하기 위해 요소 참조를 조작하려면 [`OnAfterRenderAsync` 또는 `OnAfterRender` 구성 요소 수명 주기 메서드](xref:blazor/components/lifecycle#after-component-render)를 사용합니다.

제네릭 형식으로 작업하고 값을 반환하는 경우 <xref:System.Threading.Tasks.ValueTask%601>를 사용합니다.

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`는 형식이 있는 개체에 대해 직접 호출됩니다. 다음 예제에서는 `GenericMethod`가 `JsInteropClasses` 네임스페이스에서 사용할 수 있다고 가정합니다.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>구성 요소 간 참조 요소

<xref:Microsoft.AspNetCore.Components.ElementReference>는 구성 요소의 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 메서드에서만 유효한 것으로 보장되며 요소 참조는 `struct`이므로 구성 요소 간에 요소 참조를 전달할 수 없습니다.

부모 구성 요소는 다른 구성 요소에서 요소 참조를 사용할 수 있도록 하기 위해 다음을 수행할 수 있습니다.

* 자식 구성 요소가 콜백을 등록할 수 있도록 허용합니다.
* 전달된 요소 참조를 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 이벤트 중에 등록된 콜백을 호출합니다. 간접적으로 이 방법을 사용하면 자식 구성 요소가 부모의 요소 참조와 상호 작용할 수 있습니다.

다음 Blazor WebAssembly 예제에서 이 방법을 보여 줍니다.

`wwwroot/index.html`의 `<head>`에서 다음을 수행합니다.

```html
<style>
    .red { color: red }
</style>
```

`wwwroot/index.html`의 `<body>`에서 다음을 수행합니다.

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor`(부모 구성 요소):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

`Shared/SurveyPrompt.razor`(자식 구성 요소):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 앱 어셈블리 이름입니다(예: `BlazorSample`).

## <a name="harden-js-interop-calls"></a>JS interop 호출 강화

JS interop는 네트워킹 오류로 인해 실패할 수 있으며 신뢰할 수 없는 것으로 처리되어야 합니다. 기본적으로 Blazor Server 앱은 서버에서 1분 후에 JS interop 호출 시간을 제한합니다. 앱에서 좀 더 적극적인 시간 제한을 허용할 수 있는 경우 다음 방법 중 하나를 사용하여 시간 제한을 설정합니다.

* `Startup.ConfigureServices`에서 전역적으로 시간 제한을 지정합니다.

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* 구성 요소 코드의 호출마다 단일 호출은 다음과 같이 제한 시간을 지정할 수 있습니다.

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

리소스 소모에 대한 자세한 내용은 <xref:blazor/security/server/threat-mitigation>를 참조하세요.

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>순환 개체 참조 방지

순환 참조를 포함하는 개체는 다음 중 하나에 대해 클라이언트에서 직렬화될 수 없습니다.

* .NET 메서드 호출
* 반환 형식에 순환 참조가 있는 경우 C#에서 JavaScript 메서드 호출

자세한 내용은 다음 문제를 참조하세요.

* [순환 참조가 지원되지 않음, 두 가지 사용(dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [제안: 직렬화할 때 순환 참조를 처리하는 메커니즘 추가(dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/call-dotnet-from-javascript>
* [InteropComponent.razor 예제(dotnet/AspNetCore GitHub 리포지토리, 3.1 릴리스 분기)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Blazor Server 앱에서 대량 데이터 전송 수행](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
