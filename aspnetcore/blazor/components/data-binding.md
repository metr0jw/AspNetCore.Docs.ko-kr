---
title: ASP.NET Core Blazor 데이터 바인딩
author: guardrex
description: Blazor 앱의 구성 요소 및 DOM 요소에 대한 데이터 바인딩 기능에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 6f5ad6b8f225834c92d6e33d8bcf608b56678e67
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014674"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="f788c-103">ASP.NET Core Blazor 데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="f788c-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="f788c-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f788c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f788c-105">Razor 구성 요소는 필드, 속성 또는 Razor 식 값을 사용하여 [`@bind`](xref:mvc/views/razor#bind)라는 HTML 요소 특성을 통해 데이터 바인딩 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="f788c-106">다음 예제에서는 `CurrentValue` 속성을 텍스트 상자의 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f788c-107">텍스트 상자가 포커스를 잃으면 속성 값이 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="f788c-108">텍스트 상자는 속성 값 변경에 대한 대응이 아니라, 구성 요소가 렌더링되는 경우에만 UI에서 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="f788c-109">이벤트 처리기 코드를 실행하면 구성 요소가 자체적으로 렌더링되므로 속성 업데이트는 *일반적으로* 이벤트 처리기가 트리거되는 즉시 UI에 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="f788c-110">`CurrentValue` 속성에 [`@bind`](xref:mvc/views/razor#bind)를 사용하는 것(`<input @bind="CurrentValue" />`)은 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f788c-111">구성 요소가 렌더링되면 input 요소의 `value`를 `CurrentValue` 속성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="f788c-112">사용자가 텍스트 상자에 입력을 하고 요소 포커스를 변경하면 `onchange` 이벤트가 발생하고 `CurrentValue` 속성이 변경된 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="f788c-113">실제로는 [`@bind`](xref:mvc/views/razor#bind)에서 형식 변환이 수행되는 경우를 처리하므로 코드 생성은 더 복잡해집니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-113">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="f788c-114">원칙적으로 [`@bind`](xref:mvc/views/razor#bind)는 식의 현재 값을 `value` 특성과 연결하고 등록된 처리기를 사용하여 변경 내용을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="f788c-115">또한 `event` 매개 변수에 `@bind:event` 특성을 포함하여 다른 이벤트에 속성 또는 필드를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="f788c-116">다음 예에서는 `oninput` 이벤트에서 `CurrentValue` 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="f788c-117">요소가 포커스를 잃을 때 발생하는 `onchange`와는 달리 텍스트 상자의 값이 변경될 때 `oninput`이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="f788c-118">`value` 이외의 요소 특성을 바인딩하려면 `@bind-{ATTRIBUTE}:event` 구문에 `@bind-{ATTRIBUTE}`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="f788c-119">다음 예제에서는 `paragraphStyle` 값이 변경될 때 단락 스타일이 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="f788c-120">특성 바인딩은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-120">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="f788c-121">`@bind`는 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-121">`@bind` is valid.</span></span>
* <span data-ttu-id="f788c-122">`@Bind` 및 `@BIND`는 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-122">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="f788c-123">구문 분석할 수 없는 값</span><span class="sxs-lookup"><span data-stu-id="f788c-123">Unparsable values</span></span>

<span data-ttu-id="f788c-124">사용자가 데이터 바인딩된 요소에 구문 분석할 수 없는 값을 제공하면 바인딩 이벤트가 트리거될 때 구문 분석할 수 없는 값이 자동으로 이전 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-124">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="f788c-125">다음 시나리오를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="f788c-125">Consider the following scenario:</span></span>

* <span data-ttu-id="f788c-126">`<input>` 요소는 초기 값 `123`을 사용하여 `int` 형식에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-126">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="f788c-127">사용자는 페이지에서 요소의 값을 `123.45`로 업데이트하고 요소 포커스를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-127">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="f788c-128">위의 시나리오에서 요소의 값은 `123`으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-128">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="f788c-129">값 `123.45`가 원래 값 `123`에 따라 거부되면 사용자는 해당 값이 수용되지 않았다는 것을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-129">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="f788c-130">기본적으로 바인딩은 요소의 `onchange` 이벤트(`@bind="{PROPERTY OR FIELD}"`)에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-130">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="f788c-131">`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`를 사용하여 다른 이벤트에 대한 바인딩을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="f788c-132">`oninput` 이벤트(`@bind:event="oninput"`)의 경우 구문 분석할 수 있는 값을 도입하는 키 입력 후에 되돌려집니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-132">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="f788c-133">`oninput` 이벤트의 대상을 `int` 바인딩 형식으로 지정하는 경우 사용자는 `.` 문자를 입력할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-133">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="f788c-134">`.` 문자는 즉시 제거되므로 사용자는 정수만 허용된다는 즉각적인 피드백을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-134">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="f788c-135">사용자가 구문 분석할 수 없는 `<input>` 값을 지울 수 있어야 하는 경우와 같이 `oninput` 이벤트의 값을 되돌리는 것이 적합하지 않은 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-135">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="f788c-136">대안은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-136">Alternatives include:</span></span>

* <span data-ttu-id="f788c-137">`oninput` 이벤트를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f788c-137">Don't use the `oninput` event.</span></span> <span data-ttu-id="f788c-138">기본 `onchange` 이벤트를 사용합니다(`@bind="{PROPERTY OR FIELD}"`만 지정). 이 경우 요소가 포커스를 잃을 때까지 잘못된 값은 복귀되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-138">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="f788c-139">`int?` 또는 `string`과 같은 nullable 형식에 바인딩하고 잘못된 항목을 처리하기 위한 사용자 지정 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-139">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="f788c-140"><xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> 또는 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>와 같은 [양식 유효성 검사 구성 요소](xref:blazor/forms-validation)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-140">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="f788c-141">양식 유효성 검사 구성 요소에는 잘못된 입력을 관리하기 위한 기본 제공 지원이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-141">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="f788c-142">양식 유효성 검사 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="f788c-142">Form validation components:</span></span>
  * <span data-ttu-id="f788c-143">사용자가 연결된 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에서 잘못된 입력을 제공하고 유효성 검사 오류를 수신할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-143">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="f788c-144">사용자가 추가 Webform 데이터를 입력하는 것을 방해하지 않고 UI에서 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-144">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="f788c-145">형식 문자열</span><span class="sxs-lookup"><span data-stu-id="f788c-145">Format strings</span></span>

<span data-ttu-id="f788c-146">데이터 바인딩은 `@bind:format`을 사용하여 <xref:System.DateTime> 형식 문자열에 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-146">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="f788c-147">통화 또는 숫자 형식 등의 다른 형식 식은 현재 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-147">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="f788c-148">위의 코드에서 `<input>` 요소의 필드 형식(`type`)은 기본적으로 `text`입니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-148">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="f788c-149">`@bind:format`은 다음 .NET 형식을 바인딩하는 데 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-149">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="f788c-150"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="f788c-150"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="f788c-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="f788c-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="f788c-152">`@bind:format` 특성은 `<input>` 요소의 `value`에 적용할 날짜 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-152">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="f788c-153">이 형식은 `onchange` 이벤트가 발생할 때 값을 구문 분석하는 데도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-153">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="f788c-154">Blazor에서는 기본적으로 날짜 형식을 지정할 수 있도록 지원하므로 `date` 필드의 형식을 지정하는 것은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-154">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="f788c-155">권장 사항에도 불구하고 `date` 필드의 형식이 제공된 경우 바인딩이 올바르게 작동하려면 `yyyy-MM-dd` 날짜 형식만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-155">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="f788c-156">구성 요소 매개 변수를 사용한 부모-자식 바인딩</span><span class="sxs-lookup"><span data-stu-id="f788c-156">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="f788c-157">바인딩은 구성 요소 매개 변수를 인식합니다. 여기서 `@bind-{PROPERTY}`는 부모 구성 요소의 속성 값을 자식 구성 요소에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-157">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="f788c-158">자식에서 부모로의 바인딩은 [체인 바인딩을 사용한 자식-부모 바인딩](#child-to-parent-binding-with-chained-bind)에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-158">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="f788c-159">다음 자식 구성 요소(`ChildComponent`)에는 `Year` 구성 요소 매개 변수와 `YearChanged` 콜백이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-159">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="f788c-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601>의 이름은 구성 요소 매개 변수 이름 뒤에 `Changed` 접미사(`{PARAMETER NAME}Changed`)(위의 예에서는 `YearChanged`)를 붙여 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-160">The <xref:Microsoft.AspNetCore.Components.EventCallback%601> must be named as the component parameter name followed by the `Changed` suffix (`{PARAMETER NAME}Changed`), `YearChanged` in the preceding example.</span></span> <span data-ttu-id="f788c-161"><xref:Microsoft.AspNetCore.Components.EventCallback%601>에 대한 자세한 내용은 <xref:blazor/components/event-handling#eventcallback>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f788c-161">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="f788c-162">다음 부모 구성 요소는</span><span class="sxs-lookup"><span data-stu-id="f788c-162">The following parent component uses:</span></span>

* <span data-ttu-id="f788c-163">`ChildComponent`를 사용하고 부모의 `ParentYear` 매개 변수를 자식 구성 요소의 `Year` 매개 변수에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-163">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="f788c-164">`onclick` 이벤트는 `ChangeTheYear` 메서드를 트리거하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-164">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="f788c-165">자세한 내용은 <xref:blazor/components/event-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f788c-165">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="f788c-166">`ParentComponent`를 로드하면 다음 태그가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-166">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="f788c-167">`ParentComponent`의 단추를 선택 하 여 `ParentYear` 속성 값이 변경 되 면 `ChildComponent`의 `Year` 속성이 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-167">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="f788c-168">`Year`의 새 값은 `ParentComponent`가 다시 렌더링될 때 UI에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-168">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="f788c-169">`Year` 매개 변수는 `Year` 매개 변수 형식과 일치하는 도우미 `YearChanged` 이벤트를 포함하기 때문에 바인딩 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-169">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="f788c-170">규칙에 따르면 `<ChildComponent @bind-Year="ParentYear" />`는 기본적으로 다음을 쓰는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-170">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="f788c-171">일반적으로 속성은 `@bind-{PROPRETY}:event` 특성을 포함하여 해당 이벤트 처리기에 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-171">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="f788c-172">예를 들어, `MyProp` 속성은 다음 두 가지 특성을 사용하여 `MyEventHandler`에 바인딩될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-172">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="f788c-173">체인 바인딩을 사용한 자식-부모 바인딩</span><span class="sxs-lookup"><span data-stu-id="f788c-173">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="f788c-174">일반적인 시나리오는 데이터 바인딩 매개 변수를 구성 요소 출력의 페이지 요소에 체인하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-174">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="f788c-175">여러 수준의 바인딩이 동시에 발생하기 때문에 이 시나리오를 *체인 바인딩*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-175">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="f788c-176">페이지의 요소에 [`@bind`](xref:mvc/views/razor#bind) 구문을 사용하여 체인 바인딩을 구현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-176">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="f788c-177">이벤트 처리기 및 값은 별도로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-177">The event handler and value must be specified separately.</span></span> <span data-ttu-id="f788c-178">그러나 부모 구성 요소는 구성 요소의 매개 변수에서 [`@bind`](xref:mvc/views/razor#bind) 구문을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-178">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="f788c-179">다음 `PasswordField` 구성 요소(`PasswordField.razor`)는</span><span class="sxs-lookup"><span data-stu-id="f788c-179">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="f788c-180">`<input>` 요소의 값을 `Password` 속성으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-180">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="f788c-181">[`EventCallback`](xref:blazor/components/event-handling#eventcallback)을 사용하여 `Password` 속성의 변경 내용을 부모 구성 요소에 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-181">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="f788c-182">`ToggleShowPassword` 메서드를 트리거하는 데 `onclick` 이벤트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-182">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="f788c-183">자세한 내용은 <xref:blazor/components/event-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f788c-183">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="f788c-184">`PasswordField` 구성 요소는 다른 구성 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-184">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="f788c-185">위의 예에서 암호에 대해 검사를 수행하거나 오류를 트랩하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-185">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="f788c-186">`Password`에 대한 지원 필드를 만듭니다(다음 예제 코드의 `password`).</span><span class="sxs-lookup"><span data-stu-id="f788c-186">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="f788c-187">`Password` setter에서 검사를 수행하거나 오류를 트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-187">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="f788c-188">다음 예에서는 암호 값에 공백을 사용하는 경우 사용자에게 즉각적인 피드백을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f788c-188">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="f788c-189">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f788c-189">Additional resources</span></span>

* [<span data-ttu-id="f788c-190">양식의 라디오 단추에 바인딩</span><span class="sxs-lookup"><span data-stu-id="f788c-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="f788c-191">양식의 C# 개체 `null` 값에 `<select>` 요소 옵션 바인딩</span><span class="sxs-lookup"><span data-stu-id="f788c-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
