---
title: ASP.NET Core Blazor 구성 요소 가상화
author: guardrex
description: ASP.NET Core Blazor 앱에서 구성 요소 가상화를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: eafad420d72a974cc64ebfd6abb3eff2d73a115d
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805559"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>ASP.NET Core Blazor 구성 요소 가상화

작성자: [Daniel Roth](https://github.com/danroth27)

Blazor 프레임워크의 기본 제공 가상화 지원을 사용하여 구성 요소 렌더링의 인식된 성능을 향상합니다. 가상화는 UI 렌더링을 현재 표시되는 부분으로만 제한하는 기술입니다. 예를 들어 가상화는 앱에서 긴 항목 목록을 렌더링해야 하고 지정된 시간에 항목의 하위 집합만 표시해야 하는 경우에 유용합니다. Blazor는 앱의 구성 요소에 가상화를 추가하는 데 사용할 수 있는 `Virtualize` 구성 요소를 제공합니다.

가상화를 사용하지 않는 경우 일반적인 목록은 C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 사용하여 목록의 각 항목을 렌더링할 수 있습니다.

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

목록에 수천 개의 항목이 포함된 경우 목록을 렌더링하는 데 시간이 오래 걸릴 수 있습니다. 따라서 사용자가 눈에 띄는 UI 지연을 경험할 수 있습니다.

목록의 각 항목을 한 번에 모두 렌더링하는 대신 [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 `Virtualize` 구성 요소로 바꾸고 `Items`로 고정된 항목 원본을 지정합니다. 현재 표시되는 항목만 렌더링됩니다.

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

`Context`로 구성 요소에 대한 컨텍스트를 지정하지 않은 경우 항목 콘텐츠 템플릿에서 `context` 값(`@context.{PROPERTY}`)을 사용합니다.

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

`Virtualize` 구성 요소는 컨테이너의 높이와 렌더링된 항목의 크기에 따라 렌더링할 항목의 수를 계산합니다.

`Virtualize` 구성 요소에 대한 항목 콘텐츠에는 다음이 포함될 수 있습니다.

* 이전 예제처럼 일반 HTML 및 Razor 코드
* 하나 이상의 Razor 구성 요소
* HTML/Razor 및 Razor 구성 요소의 혼합

## <a name="item-provider-delegate"></a>항목 공급자 대리자

일부 항목을 메모리로 로드하지 않으려는 경우 항목 공급자 대리자 메서드를 요청 시 요청된 항목을 비동기적으로 검색하는 구성 요소의 `ItemsProvider` 매개 변수로 지정할 수 있습니다.

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

항목 공급자는 `ItemsProviderRequest`를 수신하고 필요한 항목 수를 특정 시작 인덱스에서 시작하여 지정합니다. 그런 다음 항목 공급자는 데이터베이스나 다른 서비스에서 요청된 항목을 검색하여 총 항목 수와 함께 `ItemsProviderResult<TItem>`으로 반환합니다. 항목 공급자는 각 요청으로 항목을 검색하거나 캐시하여 쉽게 사용 가능하도록 할 수 있습니다. 항목 공급자를 사용하고 동일한 `Virtualize` 구성 요소에 대한 `Items`에 컬렉션을 할당하려고 하지 마세요.

다음 예제에서는 `EmployeeService`에서 직원을 로드합니다.

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a>자리표시자

원격 데이터 원본에서 항목을 요청하면 시간이 걸릴 수 있으므로 항목 데이터를 사용할 수 있을 때까지 자리 표시자(`<Placeholder>...</Placeholder>`)를 렌더링할 수 있습니다.

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>항목 크기

각 항목의 크기(픽셀 단위)를 `ItemSize`(기본값: 50px)로 설정할 수 있습니다.

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>오버스캔 개수

`OverscanCount`는 표시되는 지역 앞뒤에 렌더링되는 추가 항목 수를 결정합니다. 이 설정은 스크롤 중 렌더링 빈도를 줄이는 데 도움이 됩니다. 그러나 값이 높을수록 페이지에서 더 많은 요소가 렌더링됩니다(기본값: 3).

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a>상태 변경

`Virtualize` 구성 요소에 의해 렌더링된 항목을 변경하는 경우 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>를 호출하여 강제로 구성 요소를 재평가하고 다시 렌더링합니다.
