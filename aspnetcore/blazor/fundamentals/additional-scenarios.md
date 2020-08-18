---
title: ASP.NET Core Blazor 호스팅 모델 구성
author: guardrex
description: ASP.NET Core Blazor 호스팅 모델 구성의 추가 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: dbad91e46a95d9ab5ec62d66e0d9a18938ff4520
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014466"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>ASP.NET Core Blazor 호스팅 모델 구성

[Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) 및 [Luke Latham](https://github.com/guardrex)

이 문서에서는 호스팅 모델 구성에 대해 설명합니다.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalR 인증에 대한 원본 간 협상

‘이 섹션은 Blazor WebAssembly에 적용됩니다.’

cookie 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 SignalR의 기본 클라이언트를 구성하려면 다음을 수행합니다.

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>를 사용하여 원본 간 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 요청에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>를 설정합니다.

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 옵션에 <xref:System.Net.Http.HttpMessageHandler>를 할당합니다.

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

자세한 내용은 <xref:signalr/configuration#configure-additional-options>를 참조하세요.

## <a name="reflect-the-connection-state-in-the-ui"></a>UI에 연결 상태 반영

‘이 섹션은 Blazor Server에 적용됩니다.’

클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다. 다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.

UI를 사용자 지정하려면 `_Host.cshtml` Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

앱의 스타일 시트(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에 다음을 추가합니다.

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.

| CSS 클래스                       | 표시&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | 연결이 끊어졌습니다. 클라이언트가 다시 연결하는 중입니다. 모달을 표시합니다. |
| `components-reconnect-hide`     | 서버에 대해 활성 연결이 다시 설정되었습니다. 모달을 숨깁니다. |
| `components-reconnect-failed`   | 네트워크 오류로 인해 다시 연결하지 못했습니다. 다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다. |
| `components-reconnect-rejected` | 다시 연결이 거부되었습니다. 서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다. 앱을 다시 로드하려면 `location.reload()`를 호출합니다. 이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.<ul><li>서버 쪽 회로에서 크래시가 발생한 경우</li><li>서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우. 사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</li><li>서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</li></ul> |

## <a name="render-mode"></a>렌더링 모드

‘이 섹션은 Blazor Server에 적용됩니다.’

Blazor Server 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다. 이는 `_Host.cshtml` Razor 페이지에서 설정합니다.

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>는 구성 요소에 대해 다음을 구성합니다.

* 페이지에 미리 렌더링할지 여부
* 페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부

| 렌더링 모드 | 설명 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server 앱의 마커를 렌더링합니다. 구성 요소의 출력은 포함되지 않습니다. 사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 구성 요소를 정적 HTML에 렌더링합니다. |

정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a>Blazor Server 앱에 적합하게 SignalR 클라이언트 구성

‘이 섹션은 Blazor Server에 적용됩니다.’

`Pages/_Host.cshtml` 파일에서 Blazor Server 앱이 사용하는 SignalR 클라이언트를 구성합니다. `Blazor.start`를 호출하는 스크립트를 `_framework/blazor.server.js` 스크립트 뒤, `</body>` 태그 안에 넣습니다.

### <a name="logging"></a>로깅

SignalR 클라이언트 로깅을 구성하려면

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.
* 클라이언트 작성기에서 로그 수준을 사용하여 `configureLogging`을 호출하는 구성 개체(`configureSignalR`)를 전달합니다.

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

앞의 예에서 `information`은 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>의 로그 수준과 동일합니다.

### <a name="modify-the-reconnection-handler"></a>다시 연결 처리기 수정

다음과 같은 사용자 지정 동작에 대한 다시 연결 처리기의 회로 연결 이벤트를 수정할 수 있습니다.

* 연결이 삭제되는 경우 사용자에게 알립니다.
* 회로가 연결된 경우 클라이언트에서 로깅을 수행합니다.

연결 이벤트를 수정하려면

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.
* 삭제된 연결(`onConnectionDown`) 및 설정/다시 설정된 연결(`onConnectionUp`)의 연결 변경에 대한 콜백을 등록합니다. `onConnectionDown`과 `onConnectionUp`을 **모두** 지정해야 합니다.

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>다시 연결 다시 시도 횟수 및 간격 조정

다시 연결 다시 시도 횟수 및 간격을 조정하려면

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.
* 다시 시도 횟수(`maxRetries`)와 각 다시 시도에 허용되는 기간(밀리초)(`retryIntervalMilliseconds`)을 설정합니다.

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

### <a name="hide-or-replace-the-reconnection-display"></a>다시 연결 표시 숨기기 또는 바꾸기

다시 연결 표시를 숨기려면

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.
* 다시 연결 처리기의 `_reconnectionDisplay`를 빈 개체(`{}` 또는 `new Object()`)로 설정합니다.

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

다시 연결 표시를 바꾸려면 앞의 예에서 `_reconnectionDisplay`를 표시할 요소로 설정합니다.

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

자리 표시자 `{ELEMENT ID}`는 표시할 HTML 요소의 ID입니다.

## <a name="influence-html-head-tag-elements"></a>HTML `<head>` 태그 요소에 영향

‘이 섹션은 Blazor WebAssembly 및 Blazor Server의 예정된 ASP.NET Core 5.0 릴리스에 적용됩니다.’

렌더링된 `Title`, `Link` 및 `Meta` 구성 요소는 HTML `<head>` 태그 요소에서 데이터를 추가하거나 업데이트합니다.

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

앞의 예제에서 `{TITLE}`, `{URL}` 및 `{DESCRIPTION}`의 자리 표시자는 문자열 값, Razor 변수 또는 Razor 식입니다.

다음 특성이 적용됩니다.

* 서버 쪽 사전 렌더링이 지원됩니다.
* `Value` 매개 변수는 `Title` 구성 요소의 유일하게 유효한 매개 변수입니다.
* `Meta` 및 `Link` 구성 요소에 제공되는 HTML 특성은 [추가 특성](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters)에서 캡처되고 렌더링된 HTML 태그로 전달됩니다.
* 여러 `Title` 구성 요소의 경우 페이지 제목은 렌더링된 마지막 `Title` 구성 요소의 `Value`를 반영합니다.
* 특성이 동일한 여러 `Meta` 또는 `Link` 구성 요소가 포함된 경우 `Meta` 또는 `Link` 구성 요소마다 정확히 하나의 HTML 태그가 렌더링됩니다. 두 개의 `Meta` 또는 `Link` 구성 요소는 렌더링된 동일한 HTML 태그를 참조할 수 없습니다.
* 기존 `Meta` 또는 `Link` 구성 요소의 매개 변수에 대한 변경 내용은 렌더링된 HTML 태그에 반영됩니다.
* `Link` 또는 `Meta` 구성 요소가 더 이상 렌더링되지 않아 프레임워크에서 삭제되는 경우 렌더링된 HTML 태그가 제거됩니다.

프레임워크 구성 요소 중 하나가 자식 구성 요소에서 사용되는 경우 렌더링된 HTML 태그는 프레임워크 구성 요소를 포함하는 자식 구성 요소가 렌더링되는 한 부모 구성 요소의 다른 모든 자식 구성 요소에 영향을 미칩니다. 자식 구성 요소에서 이러한 프레임워크 구성 요소 중 하나를 사용하는 것과 `wwwroot/index.html` 또는 `Pages/_Host.cshtml`에 HTML 태그를 배치하는 것의 차이는 프레임워크 구성 요소의 렌더링된 HTML 태그입니다.

* 애플리케이션 상태에 의해 수정될 수 있습니다. 하드 코드된 HTML 태그는 애플리케이션 상태에 의해 수정될 수 없습니다.
* 부모 구성 요소가 더 이상 렌더링되지 않으면 HTML `<head>`에서 제거됩니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/logging/index>
