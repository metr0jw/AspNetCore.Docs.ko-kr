---
title: ASP.NET Core에서 XSS (교차 사이트 스크립팅) 방지
author: rick-anderson
description: ASP.NET Core 앱에서이 취약점을 해결 하는 데 필요한 XSS (사이트 간 스크립팅) 및 기술에 대해 알아봅니다.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: ec8b321be08447ca634a1e28799f790f723f17d1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625624"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="ca1ed-103">ASP.NET Core에서 XSS (교차 사이트 스크립팅) 방지</span><span class="sxs-lookup"><span data-stu-id="ca1ed-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="ca1ed-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ca1ed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ca1ed-105">XSS (교차 사이트 스크립팅)는 공격자가 클라이언트 쪽 스크립트 (일반적으로 JavaScript)를 웹 페이지에 넣을 수 있도록 하는 보안 취약점입니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="ca1ed-106">다른 사용자가 영향을 받는 페이지를 로드 하면 공격자의 스크립트가 실행 되어 공격자가 cookie 및 세션 토큰을 도용 하거나 DOM 조작을 통해 웹 페이지의 내용을 변경 하거나 브라우저를 다른 페이지로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="ca1ed-107">XSS 취약점은 일반적으로 응용 프로그램에서 사용자 입력을 가져와 유효성 검사, 인코딩 또는 이스케이프 하지 않고 페이지에 출력할 때 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="ca1ed-108">XSS 로부터 응용 프로그램 보호</span><span class="sxs-lookup"><span data-stu-id="ca1ed-108">Protecting your application against XSS</span></span>

<span data-ttu-id="ca1ed-109">기본 수준의 XSS는 응용 프로그램을 속여 하 여 `<script>` 렌더링 된 페이지에 태그를 삽입 하거나 요소에 이벤트를 삽입 하는 방식으로 작동 `On*` 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="ca1ed-110">개발자는 응용 프로그램에 XSS를 도입 하지 않도록 다음 방지 단계를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="ca1ed-111">아래 나머지 단계를 수행 하지 않으면 HTML 입력에 신뢰할 수 없는 데이터를 넣지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="ca1ed-112">신뢰할 수 없는 데이터는 공격자가 제어 하는 데이터 (HTML 양식 입력, 쿼리 문자열, HTTP 헤더)가 응용 프로그램을 위반할 수 없는 경우에도 데이터베이스를 위반할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="ca1ed-113">HTML 요소 내에 신뢰할 수 없는 데이터를 넣기 전에 HTML로 인코딩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="ca1ed-114">HTML 인코딩은와 같은 문자를 사용 하 여,와 같은 &lt; 안전한 형식으로 변경 합니다 &amp; .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="ca1ed-115">신뢰할 수 없는 데이터를 HTML 특성에 배치 하기 전에 HTML로 인코딩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="ca1ed-116">HTML 특성 인코딩은 HTML 인코딩의 상위 집합이 며 "and"와 같은 추가 문자를 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="ca1ed-117">신뢰할 수 없는 데이터를 JavaScript에 배치 하기 전에 런타임에 검색 하는 콘텐츠를 포함 하는 HTML 요소에 데이터를 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="ca1ed-118">가능 하지 않은 경우 데이터가 JavaScript로 인코딩 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="ca1ed-119">JavaScript 인코딩은 JavaScript에 대 한 위험한 문자를 사용 하 여 16 진수로 바꿉니다. 예를 들어 &lt; 는로 인코딩됩니다 `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="ca1ed-120">신뢰할 수 없는 데이터를 URL 쿼리 문자열에 배치 하기 전에 URL이 인코딩 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="ca1ed-121">HTML 인코딩 사용 Razor</span><span class="sxs-lookup"><span data-stu-id="ca1ed-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="ca1ed-122">RazorMVC에서 사용 하는 엔진은이를 방지 하기 위해 정말로 작업 하지 않는 한 변수에서 모든 출력을 자동으로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="ca1ed-123">지시문을 사용할 때마다 HTML 특성 인코딩 규칙을 사용 *@* 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="ca1ed-124">Html 특성 인코딩은 HTML 인코딩의 상위 집합 이기 때문에 HTML 인코딩 또는 HTML 특성 인코딩을 사용 해야 하는지 여부를 걱정 하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="ca1ed-125">신뢰할 수 없는 입력을 JavaScript에 직접 삽입 하려고 할 때가 아니라 HTML 컨텍스트에서만 @를 사용 하도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="ca1ed-126">태그 도우미는 태그 매개 변수에서 사용 하는 입력도 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="ca1ed-127">다음 뷰를 사용 합니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="ca1ed-128">이 보기는 *Untrustedinput* 변수의 내용을 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="ca1ed-129">이 변수에는 XSS 공격에 사용 되는 일부 문자 (즉 &lt; , "및")가 포함 됩니다 &gt; .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="ca1ed-130">소스를 검사 하면 렌더링 된 출력이 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="ca1ed-131">ASP.NET Core MVC는 `HtmlString` 출력 시 자동으로 인코딩되지 않는 클래스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="ca1ed-132">이는 XSS 취약성을 노출 하므로 신뢰할 수 없는 입력과 함께 사용 하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="ca1ed-133">JavaScript Encoding 사용 Razor</span><span class="sxs-lookup"><span data-stu-id="ca1ed-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="ca1ed-134">JavaScript에 값을 삽입 하 여 보기에서 처리할 수 있는 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="ca1ed-135">두 가지 방법으로 이 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-135">There are two ways to do this.</span></span> <span data-ttu-id="ca1ed-136">값을 삽입 하는 가장 안전한 방법은 태그의 데이터 특성에 값을 추가 하 고 JavaScript에서 검색 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="ca1ed-137">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="ca1ed-137">For example:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="ca1ed-138">그러면 다음 HTML이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-138">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="ca1ed-139">실행 될 때 다음을 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-139">Which, when it runs, will render the following:</span></span>

```
<"123">
   <"123">
```

<span data-ttu-id="ca1ed-140">JavaScript 인코더를 직접 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-140">You can also call the JavaScript encoder directly:</span></span>

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

<span data-ttu-id="ca1ed-141">이렇게 하면 브라우저에서 다음과 같이 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-141">This will render in the browser as follows:</span></span>

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> <span data-ttu-id="ca1ed-142">JavaScript에서 신뢰할 수 없는 입력을 연결 하 여 DOM 요소를 만들지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="ca1ed-143">과 `createElement()` 같이 속성 값을 적절 하 게 사용 및 할당 `node.TextContent=` 하거나, `element.SetAttribute()` / `element[attribute]=` 그렇지 않으면 DOM 기반 XSS에 직접 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="ca1ed-144">코드에서 인코더 액세스</span><span class="sxs-lookup"><span data-stu-id="ca1ed-144">Accessing encoders in code</span></span>

<span data-ttu-id="ca1ed-145">HTML, JavaScript 및 URL 인코더를 두 가지 방법으로 사용할 수 있습니다. [종속성 주입](xref:fundamentals/dependency-injection) 을 통해 코드를 삽입 하거나 네임 스페이스에 포함 된 기본 인코더를 사용할 수 있습니다 `System.Text.Encodings.Web` .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="ca1ed-146">기본 인코더를 사용 하는 경우 안전 하지 않은 것으로 간주 되는 문자 범위에 적용 된 모든 것이 적용 되지 않습니다. 기본 인코더는 가능한 가장 안전한 인코딩 규칙을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="ca1ed-147">DI를 통해 구성 가능한 인코더를 사용 하려면 생성자는 *htmlencoder*, *JavaScriptEncoder* 및 *urlencoder* 매개 변수를 적절 하 게 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="ca1ed-148">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-148">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="ca1ed-149">인코딩 URL 매개 변수</span><span class="sxs-lookup"><span data-stu-id="ca1ed-149">Encoding URL Parameters</span></span>

<span data-ttu-id="ca1ed-150">신뢰할 수 없는 입력을 값으로 사용 하 여 URL 쿼리 문자열을 작성 하려는 경우를 사용 `UrlEncoder` 하 여 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="ca1ed-151">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="ca1ed-152">인코딩 후 Url-encodedvalue 변수에는가 포함 됩니다 `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="ca1ed-153">공백, 따옴표, 문장 부호 및 기타 안전 하지 않은 문자는 16 진수 값으로 인코딩됩니다. 예를 들어 공백 문자는 %20이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="ca1ed-154">URL 경로의 일부로는 신뢰할 수 없는 입력을 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="ca1ed-155">항상 신뢰할 수 없는 입력을 쿼리 문자열 값으로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="ca1ed-156">인코더 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="ca1ed-156">Customizing the Encoders</span></span>

<span data-ttu-id="ca1ed-157">기본적으로 인코더는 기본 라틴어 유니코드 범위로 제한 된 안전 목록을 사용 하 고 해당 범위 밖의 모든 문자를 해당 문자 코드로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="ca1ed-158">이 동작은 Razor 인코더를 사용 하 여 문자열을 출력 하는 TagHelper 및 HtmlHelper 렌더링에도 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="ca1ed-159">이를 바탕으로 하는 이유는 알려지지 않은 또는 향후 브라우저 버그를 방지 하는 것입니다 (이전 브라우저 버그는 영어가 아닌 문자 처리를 기반으로 구문 분석을 수행 함).</span><span class="sxs-lookup"><span data-stu-id="ca1ed-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="ca1ed-160">웹 사이트에서 중국어, 키릴 자모 또는 다른 사용자와 같은 라틴 문자가 아닌 문자를 많이 사용 하는 경우이는 원하는 동작이 아닐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="ca1ed-161">에서 시작 하는 동안 응용 프로그램에 적합 한 유니코드 범위를 포함 하도록 인코더 안전 목록을 사용자 지정할 수 있습니다 `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="ca1ed-162">예를 들어 기본 구성을 사용 하는 경우 Razor 와 같은 htmlhelper를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="ca1ed-163">웹 페이지의 소스를 볼 때 중국어 텍스트가 인코딩된 상태로 다음과 같이 렌더링 된 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="ca1ed-164">인코더가 안전 하 게 처리 하는 문자를 확대 하려면의 메서드에 다음 줄을 삽입 합니다 `ConfigureServices()` `startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="ca1ed-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="ca1ed-165">이 예에서는 CjkUnifiedIdeographs 유니코드 범위를 포함 하도록 안전 목록을 확대 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="ca1ed-166">이제 렌더링 된 출력이 다음과 같은 상태가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="ca1ed-167">안전 목록 범위는 언어가 아닌 유니코드 코드 차트로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="ca1ed-168">[유니코드 표준](https://unicode.org/) 에는 문자를 포함 하는 차트를 찾는 데 사용할 수 있는 [코드 차트](https://www.unicode.org/charts/index.html) 목록이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="ca1ed-169">각 인코더, Html, JavaScript 및 Url은 별도로 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="ca1ed-170">Safe 목록의 사용자 지정은 DI를 통해 원본 인코더에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="ca1ed-171">기본값을 통해 인코더에 직접 액세스 하는 경우 기본 `System.Text.Encodings.Web.*Encoder.Default` 라틴어 전용 safelist 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="ca1ed-172">인코딩 수행 위치</span><span class="sxs-lookup"><span data-stu-id="ca1ed-172">Where should encoding take place?</span></span>

<span data-ttu-id="ca1ed-173">일반적으로 허용 되는 방법은 인코딩이 출력 지점에서 발생 하 고 인코딩된 값이 데이터베이스에 저장 되지 않아야 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="ca1ed-174">출력 지점에서 인코딩을 사용 하면 HTML에서 쿼리 문자열 값과 같은 데이터 사용을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="ca1ed-175">또한 검색 전에 값을 인코딩하지 않고 인코더에 대 한 변경 내용 또는 버그 수정을 활용할 수 있도록 하 여 데이터를 쉽게 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="ca1ed-176">XSS 방지 기술로 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="ca1ed-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="ca1ed-177">유효성 검사는 XSS 공격을 제한 하는 데 유용한 도구 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="ca1ed-178">예를 들어 0-9 문자만 포함 하는 숫자 문자열은 XSS 공격을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="ca1ed-179">사용자 입력에서 HTML을 허용할 때 유효성 검사가 더 복잡해 집니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="ca1ed-180">HTML 입력을 구문 분석 하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="ca1ed-181">포함 된 HTML을 제거 하는 Markdown와 결합 된는 다양 한 입력을 허용 하는 안전한 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="ca1ed-182">유효성 검사만 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-182">Never rely on validation alone.</span></span> <span data-ttu-id="ca1ed-183">유효성 검사 또는 삭제가 수행 되었는지 여부에 관계 없이 항상 신뢰할 수 없는 입력을 출력 전에 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ca1ed-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
