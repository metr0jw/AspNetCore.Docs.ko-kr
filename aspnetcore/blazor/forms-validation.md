---
title: ASP.NET Core Blazor 양식 및 유효성 검사
author: guardrex
description: Blazor에서 양식 및 필드 유효성 검사 시나리오를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 3e719261315ed3a17833da7751d801d79a11ee6c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014480"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>ASP.NET Core Blazor 양식 및 유효성 검사

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

양식 및 유효성 검사는 [데이터 주석](xref:mvc/models/validation)을 사용하여 Blazor에서 지원됩니다.

다음 `ExampleModel` 형식은 데이터 주석을 사용하여 유효성 검사 논리를 정의합니다.

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

양식은 <xref:Microsoft.AspNetCore.Components.Forms.EditForm> 구성 요소를 사용하여 정의합니다. 다음 양식은 일반적인 요소, 구성 요소 및 Razor 코드를 보여 줍니다.

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

앞의 예제에서:

* 양식은 `ExampleModel` 형식에서 정의된 유효성 검사를 사용하여 `name` 필드에서 사용자 입력의 유효성을 검사합니다. 모델은 구성 요소의 `@code` 블록에 생성되고 프라이빗 필드(`exampleModel`)에 저장됩니다. 필드는 `<EditForm>` 요소의 `Model` 특성에 할당됩니다.
* <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `@bind-Value`는 다음과 같이 바인딩합니다.
  * 모델 속성(`exampleModel.Name`)을 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `Value` 속성에 바인딩합니다. 속성 바인딩에 대한 자세한 내용은 <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>를 참조하세요.
  * 변경 이벤트 대리자를 <xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소의 `ValueChanged` 속성에 바인딩합니다.
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 데이터 주석을 사용하여 유효성 검사 지원을 연결합니다.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 유효성 검사 메시지를 요약합니다.
* 양식이 성공적으로 제출되면(유효성 검사 통과) `HandleValidSubmit`가 트리거됩니다.

## <a name="built-in-forms-components"></a>기본 제공 양식 구성 요소

기본 제공 입력 구성 요소 집합을 사용하여 사용자 입력을 받고 유효성을 검사할 수 있습니다. 입력을 변경할 때와 양식을 제출할 때 입력의 유효성이 검사됩니다. 사용 가능한 입력 구성 요소는 다음 표에 나와 있습니다.

| 입력 구성 요소 | 렌더링 형식&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>을 비롯한 모든 입력 구성 요소는 임의 특성을 지원합니다. 구성 요소 매개 변수와 일치하지 않는 특성은 렌더링된 HTML 요소에 추가됩니다.

입력 구성 요소는 편집 시 유효성 검사와 필드 상태에 따른 CSS 클래스 변경의 기본 동작을 제공합니다. 일부 구성 요소는 유용한 구문 분석 논리를 포함합니다. 예를 들어 <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> 및 <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>는 구문 분석할 수 없는 값을 유효성 검사 오류로 등록하여 정상적으로 처리합니다. Null 값을 허용할 수 있는 형식은 대상 필드의 Null 허용 여부도 지원합니다(예: `int?`).

다음 `Starship` 형식은 이전 `ExampleModel`보다 큰 속성 및 데이터 주석 집합을 사용하여 유효성 검사 논리를 정의합니다.

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

위의 예제에서 `Description`은 데이터 주석이 없으므로 선택 사항입니다.

다음 양식은 `Starship` 모델에서 정의된 유효성 검사를 사용하여 사용자 입력의 유효성을 검사합니다.

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 수정된 필드와 현재 유효성 검사 메시지를 포함하여 편집 프로세스에 대한 메타데이터를 추적하는 [계단식 값](xref:blazor/components/cascading-values-and-parameters)으로 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 만듭니다. <xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 유효한 제출과 잘못된 제출에 대한 편리한 이벤트(<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>)도 제공합니다. 또는 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>를 사용하여 유효성 검사를 트리거하고 사용자 지정 유효성 검사 코드로 필드 값을 확인할 수 있습니다.

다음 예제에서는

* **`Submit`** 단추를 선택하면 `HandleSubmit` 메서드가 실행됩니다.
* 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 사용하여 양식의 유효성을 검사합니다.
* 서버에서 웹 API 엔드포인트를 호출하는 `ServerValidate` 메서드에 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 전달하여 양식의 유효성을 추가로 검사합니다(‘표시되지 않음’).
* `isValid`를 확인하여 클라이언트 쪽 및 서버 쪽 유효성 검사 결과에 따라 추가 코드를 실행합니다.

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>입력 이벤트를 기반으로 하는 InputText

<xref:Microsoft.AspNetCore.Components.Forms.InputText> 구성 요소를 사용하여 `change` 이벤트 대신 `input` 이벤트를 사용하는 사용자 지정 구성 요소를 만들 수 있습니다.

다음 예제에서 `CustomInputText` 구성 요소는 프레임워크의 `InputText` 구성 요소를 상속하고 이벤트 바인딩(<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>)을 `oninput` 이벤트로 설정합니다.

`Shared/CustomInputText.razor`:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

`CustomInputText` 구성 요소는 <xref:Microsoft.AspNetCore.Components.Forms.InputText>가 사용되는 모든 위치에서 사용할 수 있습니다.

`Pages/TestForm.razor`:

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a>라디오 단추

양식에서 라디오 단추를 사용하는 경우, 라디오 단추는 그룹으로 평가되기 때문에 데이터 바인딩이 다른 요소와 다르게 처리됩니다. 각 라디오 단추의 값은 고정되어 있지만 라디오 단추 그룹의 값은 선택한 라디오 단추의 값입니다. 아래 예제는 다음과 같은 작업의 방법을 보여 줍니다.

* 라디오 단추 그룹의 데이터 바인딩을 처리합니다.
* 사용자 지정 `InputRadio` 구성 요소를 사용하여 유효성 검사를 지원합니다.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

다음 <xref:Microsoft.AspNetCore.Components.Forms.EditForm>은 위의 `InputRadio` 구성 요소를 사용하여 사용자의 평가를 가져오고 유효성을 검사합니다.

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="binding-select-element-options-to-c-object-null-values"></a>C# 개체 `null` 값에 `<select>` 요소 옵션 바인딩

다음과 같은 이유로 `<select>` 요소 옵션 값을 C# 개체 `null` 값으로 나타낼 수 있는 적절한 방법은 없습니다.

* HTML 특성에는 `null` 값을 사용할 수 없습니다. HTML에서 `null`에 가장 근사한 값은 `<option>` 요소에서 HTML `value` 특성이 없는 상태입니다.
* `value` 특성이 없는 `<option>`을 선택하면 브라우저가 해당 `<option>` 요소의 텍스트 내용을 값으로 처리합니다.

Blazor 프레임워크는 기본 동작에 다음을 포함하므로 기본 동작을 억제하려고 시도하지 않습니다.

* 프레임워크에서 일련의 특수 경우 해결 방법 만들기
* 현재 프레임워크 동작에 대한 호환성이 손상되는 변경

::: moniker range=">= aspnetcore-5.0"

HTML에서 가장 타당한 `null` 근사값은 빈 문자열 `value`입니다. Blazor 프레임워크는 `<select>`의 값에 대한 양방향 바인딩을 위해 `null`을 빈 문자열 변환으로 처리합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor 프레임워크는 `<select>`의 값에 대한 양방향 바인딩을 시도할 때 자동으로 `null`을 빈 문자열 변환으로 처리하지 않습니다. 자세한 내용은 [Null 값에 대한 `<select>` 바인딩 수정(dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221)을 참조하세요.

::: moniker-end

## <a name="validation-support"></a>유효성 검사 지원

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 데이터 주석을 사용하여 유효성 검사 지원을 계단식 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>에 연결합니다. 데이터 주석을 통해 유효성 검사 지원을 사용하도록 설정하려면 이 명시적 제스처가 필요합니다. 데이터 주석이 아닌 다른 유효성 검사 시스템을 사용하려면 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>를 사용자 지정 구현으로 바꿉니다. 참조 원본 [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)에서 ASP.NET Core 구현을 검사할 수 있습니다. 위의 참조 원본 링크에서는 ASP.NET Core의 향후 릴리스를 위한 제품 단위의 최신 개발을 나타내는 리포지토리 `master` 분기의 코드를 제공합니다. 다른 릴리스의 분기를 선택하려면 GitHub 분기 선택기를 사용합니다(예: `release/3.1`).

Blazor는 다음 두 가지 유형의 유효성 검사를 수행합니다.

* ‘필드 유효성 검사’는 사용자가 Tab 키를 눌러 필드를 벗어날 때 수행됩니다. 필드 유효성을 검사하는 동안 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 보고된 모든 유효성 검사 결과를 필드에 연결합니다.
* ‘모델 유효성 검사’는 사용자가 양식을 제출할 때 수행됩니다. 모델 유효성을 검사하는 동안 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소는 유효성 검사 결과에 보고된 멤버 이름을 기준으로 필드를 확인합니다. 개별 멤버와 연결되지 않은 유효성 검사 결과는 필드가 아니라 모델과 연결됩니다.

### <a name="validation-summary-and-validation-message-components"></a>유효성 검사 요약 및 유효성 검사 메시지 구성 요소

<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 [유효성 검사 요약 태그 도우미](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)와 유사하게, 모든 유효성 검사 메시지를 요약합니다.

```razor
<ValidationSummary />
```

`Model` 매개 변수를 사용하여 특정 모델의 유효성 검사 메시지를 출력합니다.
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 구성 요소는 [유효성 검사 메시지 태그 도우미](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)와 유사하게, 특정 필드의 유효성 검사 메시지를 표시합니다. 모델 속성 이름을 지정하는 람다 식과 `For` 특성을 사용하여 유효성을 검사할 필드를 지정합니다.

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> 및 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소는 임의 특성을 지원합니다. 구성 요소 매개 변수와 일치하지 않는 특성은 생성된 `<div>` 또는 `<ul>` 요소에 추가됩니다.

앱의 스타일 시트(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에서 유효성 검사 메시지 스타일을 제어합니다. 기본 `validation-message` 클래스는 유효성 검사 메시지의 텍스트 색을 빨간색으로 설정합니다.

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>사용자 지정 유효성 검사 특성

[사용자 지정 유효성 검사 특성](xref:mvc/models/validation#custom-attributes)을 사용할 때 유효성 검사 결과가 필드와 올바르게 연결되도록 하려면 <xref:System.ComponentModel.DataAnnotations.ValidationResult>를 만들 때 유효성 검사 컨텍스트의 <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>을 전달합니다.

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType>이(가) `null`인 경우 `IsValid` 메서드에서 유효성 검사를 위한 서비스 삽입은 지원되지 않습니다.

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor 데이터 주석 유효성 검사 패키지

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)는 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소를 사용하여 유효성 검사 환경 차이를 완화하는 패키지입니다. 이 패키지는 현재 ‘실험적’입니다.

### <a name="compareproperty-attribute"></a>[CompareProperty] 특성

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>는 유효성 검사 결과를 특정 멤버에 연결하지 않으므로 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> 구성 요소에서 제대로 작동하지 않습니다. 이로 인해 제출 시 전체 모델의 유효성을 검사하는 경우와 필드 수준 유효성 검사 간에 동작이 일치하지 않을 수 있습니다. [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ‘실험적’ 패키지는 해당 제한 사항을 해결하는 추가 유효성 검사 특성인 `ComparePropertyAttribute`를 도입합니다. Blazor 앱에서 `[CompareProperty]`는 [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 특성을 직접 대체합니다.

### <a name="nested-models-collection-types-and-complex-types"></a>중첩된 모델, 컬렉션 형식 및 복합 형식

Blazor는 기본 제공 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>와 함께 데이터 주석을 사용하여 양식 입력의 유효성 검사를 지원합니다. 그러나 <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>는 컬렉션 형식 또는 복합 형식 속성이 아닌, 양식에 바인딩된 모델의 최상위 속성에 대해서만 유효성을 검사합니다.

컬렉션 형식 및 복합 형식 속성을 포함한 바인딩된 모델의 전체 개체 그래프에 대해 유효성을 검사하려면 다음과 같이 ‘실험적’ [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) 패키지에서 제공하는 `ObjectGraphDataAnnotationsValidator`를 사용합니다.

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

`[ValidateComplexType]`을 사용하여 모델 속성에 주석을 답니다. 다음 모델 클래스에서 `ShipDescription` 클래스는 모델이 양식에 바인딩된 경우 유효성을 검사할 추가 데이터 주석을 포함합니다.

`Starship.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

`ShipDescription.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>양식 유효성 검사에 따라 제출 단추 사용

양식 유효성 검사에 따라 제출 단추를 사용하거나 사용하지 않도록 설정하려면 다음을 수행합니다.

* 양식의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>를 사용하여 구성 요소가 초기화될 때 모델을 할당할 수 있습니다.
* 컨텍스트의 <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> 콜백에서 양식의 유효성을 검사하여 제출 단추를 사용하거나 사용하지 않도록 설정합니다.
* `Dispose` 메서드에서 이벤트 처리기를 언후크합니다. 자세한 내용은 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>를 참조하세요.

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

위의 예제에서는 다음과 같은 경우 `formInvalid`를 `false`로 설정합니다.

* 양식에 유효한 기본값이 미리 로드된 경우
* 양식을 로드할 때 제출 단추를 사용할 수 있게 하려는 경우

이전 방법의 부작용으로, 사용자가 아무 필드나 하나를 조작하고 나면 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소에 잘못된 필드가 채워집니다. 이 시나리오는 다음 방법 중 하나로 해결할 수 있습니다.

* 양식에서 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소를 사용하지 않습니다.
* 제출 단추를 선택할 때 <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> 구성 요소가 표시되도록 설정합니다(예: `HandleValidSubmit` 메서드에서).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>문제 해결

> InvalidOperationException: EditForm에는 모델 매개 변수 또는 EditContext 매개 변수를 사용해야 합니다(둘 다 사용할 필요는 없음).

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>에 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> 또는 <xref:Microsoft.AspNetCore.Components.Forms.EditContext>가 있는지 확인합니다.

양식에 <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>을 할당하는 경우 다음 예제와 같이 모델 형식이 인스턴스화되는지 확인합니다.

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
