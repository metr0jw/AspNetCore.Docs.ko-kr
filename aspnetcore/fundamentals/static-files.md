---
title: ASP.NET Core의 정적 파일
author: rick-anderson
description: ASP.NET Core 웹앱에서 정적 파일을 제공 및 보호하고 정적 파일 호스팅 미들웨어 동작을 구성하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 32f794d498533e846343831d2360e98939105fb1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016682"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="3f50e-103">ASP.NET Core의 정적 파일</span><span class="sxs-lookup"><span data-stu-id="3f50e-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f50e-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="3f50e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="3f50e-105">HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 기본적으로 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="3f50e-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f50e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="3f50e-107">정적 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-107">Serve static files</span></span>

<span data-ttu-id="3f50e-108">정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리 내에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="3f50e-109">기본 디렉터리는 `{content root}/wwwroot`이지만, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 메서드를 통해 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="3f50e-110">자세한 내용은 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="3f50e-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="3f50e-112">위의 코드는 웹앱 템플릿을 사용하여 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="3f50e-113">정적 파일은 [웹 루트](xref:fundamentals/index#web-root)에 상대적인 경로를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="3f50e-114">예를 들어 **웹 애플리케이션** 프로젝트 템플릿에는 `wwwroot` 폴더 내에 여러 폴더가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="3f50e-115">*wwwroot/images* 폴더를 만들고 *wwwroot/images/MyImage.jpg* 파일을 추가하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="3f50e-116">`images` 폴더의 파일에 액세스하기 위한 URI 형식은 `https://<hostname>/images/<image_file_name>`입니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="3f50e-117">예를 들면 `https://localhost:5001/images/MyImage.jpg`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="3f50e-118">웹 루트의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-118">Serve files in web root</span></span>

<span data-ttu-id="3f50e-119">기본 웹앱 템플릿은 `Startup.Configure`에서 <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> 메서드를 호출하여 정적 파일을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="3f50e-120">매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 [웹 루트](xref:fundamentals/index#web-root)에 있는 파일을 제공 가능으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="3f50e-121">다음 태그는 *wwwroot/images/MyImage.jpg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="3f50e-122">위의 코드에서 물결표 문자 `~/`는 [웹 루트](xref:fundamentals/index#web-root)를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="3f50e-123">웹 루트 외부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-123">Serve files outside of web root</span></span>

<span data-ttu-id="3f50e-124">제공할 정적 파일이 [웹 루트](xref:fundamentals/index#web-root) 외부에 있는 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="3f50e-125">요청은 정적 파일 미들웨어를 다음과 같이 구성하여 `red-rose.jpg` 파일에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="3f50e-126">위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조는 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="3f50e-127">`https://<hostname>/StaticFiles/images/red-rose.jpg`에 대한 요청은 *red-rose.jpg* 파일에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="3f50e-128">다음 태그는 *MyStaticFiles/images/red-rose.jpg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="3f50e-129">HTTP 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="3f50e-129">Set HTTP response headers</span></span>

<span data-ttu-id="3f50e-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 개체를 사용하여 HTTP 응답 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="3f50e-131">다음 코드는 [웹 루트](xref:fundamentals/index#web-root)에서 제공되는 정적 파일을 구성하는 것 외에도 `Cache-Control` 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="3f50e-132">정적 파일은 600초 동안 공개적으로 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-132">Static files are publicly cacheable for 600 seconds:</span></span>

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="3f50e-134">정적 파일 권한 부여</span><span class="sxs-lookup"><span data-stu-id="3f50e-134">Static file authorization</span></span>

<span data-ttu-id="3f50e-135">정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="3f50e-136">`wwwroot` 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="3f50e-137">권한 부여를 기반으로 파일을 제공하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="3f50e-138">파일을 `wwwroot` 외부의 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="3f50e-139">권한 부여가 적용되는 작업 메서드를 통해 파일을 제공하고 <xref:Microsoft.AspNetCore.Mvc.FileResult> 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-139">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="3f50e-140">디렉터리 검색</span><span class="sxs-lookup"><span data-stu-id="3f50e-140">Directory browsing</span></span>

<span data-ttu-id="3f50e-141">디렉터리 검색을 통해 지정된 디렉터리 내에 디렉터리를 나열할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="3f50e-142">기본적으로 디렉터리 검색은 보안상의 이유로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="3f50e-143">자세한 내용은 [고려 사항](#sc)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="3f50e-144">다음을 통해 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="3f50e-145">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 사용</span><span class="sxs-lookup"><span data-stu-id="3f50e-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="3f50e-146">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 사용</span><span class="sxs-lookup"><span data-stu-id="3f50e-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="3f50e-147">위의 코드를 통해 URL `https://<hostname>/MyImages`를 사용하여 각 파일 및 폴더에 대한 링크가 제공되는 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![디렉터리 검색](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="3f50e-149">기본 문서 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-149">Serve default documents</span></span>

<span data-ttu-id="3f50e-150">기본 페이지를 설정하면 방문자에게 사이트의 시작 지점이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="3f50e-151">정규화된 URI 없이 `wwwroot`의 기본 페이지를 제공하려면 <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="3f50e-152">기본 파일을 제공하려면 `UseStaticFiles` 전에 `UseDefaultFiles`를 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="3f50e-153">`UseDefaultFiles`는 파일을 제공하지 않는 URL 재작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="3f50e-154">`UseDefaultFiles`를 사용하면 `wwwroot`의 폴더에 대한 요청이 다음을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="3f50e-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="3f50e-155">*default.htm*</span></span>
* <span data-ttu-id="3f50e-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="3f50e-156">*default.html*</span></span>
* <span data-ttu-id="3f50e-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="3f50e-157">*index.htm*</span></span>
* <span data-ttu-id="3f50e-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="3f50e-158">*index.html*</span></span>

<span data-ttu-id="3f50e-159">목록에서 찾은 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="3f50e-160">브라우저 URL은 요청된 URI를 계속 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="3f50e-161">다음 코드는 기본 파일 이름을 *mydefault.html*로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="3f50e-162">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="3f50e-163">기본 문서의 UseFileServer</span><span class="sxs-lookup"><span data-stu-id="3f50e-163">UseFileServer for default documents</span></span>

<span data-ttu-id="3f50e-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>는 `UseStaticFiles`, `UseDefaultFiles` 및 선택적으로 `UseDirectoryBrowser`의 기능을 병행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="3f50e-165">`app.UseFileServer`를 호출하여 정적 파일 및 기본 파일을 제공할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="3f50e-166">디렉터리 검색은 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="3f50e-167">다음 코드는 `UseFileServer`와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="3f50e-168">다음 코드를 사용하면 정적 파일, 기본 파일 및 디렉터리 검색을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="3f50e-169">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="3f50e-170">다음 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="3f50e-171">다음 코드를 사용하면 `MyStaticFiles`의 정적 파일, 기본 파일 및 디렉터리 검색을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="3f50e-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>는 `EnableDirectoryBrowsing` 속성 값이 `true`인 경우 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="3f50e-173">URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="3f50e-174">URI</span><span class="sxs-lookup"><span data-stu-id="3f50e-174">URI</span></span>            |      <span data-ttu-id="3f50e-175">응답</span><span class="sxs-lookup"><span data-stu-id="3f50e-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="3f50e-176">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="3f50e-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="3f50e-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="3f50e-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="3f50e-178">*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 `https://<hostname>/StaticFiles`는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![정적 파일 목록](static-files/_static/db2.png)

<span data-ttu-id="3f50e-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 후행 `/`가 없는 대상 URI에서 후행 `/`가 있는 대상 URI로 클라이언트 쪽 리디렉션을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="3f50e-181">예를 들어 `https://<hostname>/StaticFiles`에서 `https://<hostname>/StaticFiles/`로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="3f50e-182">*StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시(`/`)가 있어야 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="3f50e-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="3f50e-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="3f50e-184"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 클래스에는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 하는 `Mappings` 속성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-184">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="3f50e-185">다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="3f50e-186">*.rtf* 확장명은 대체되며 *.mp4*는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="3f50e-187">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="3f50e-188">[MIME 콘텐츠 형식](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="3f50e-189">비표준 콘텐츠 형식</span><span class="sxs-lookup"><span data-stu-id="3f50e-189">Non-standard content types</span></span>

<span data-ttu-id="3f50e-190">정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="3f50e-191">사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="3f50e-192">요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="3f50e-193">디렉터리 검색이 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="3f50e-194">다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="3f50e-195">다음 코드는 위의 코드와 함께 `Startup.Configure`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="3f50e-196">위의 코드를 사용하면 알 수 없는 콘텐츠 형식의 파일에 대한 요청은 이미지로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="3f50e-197"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes>를 사용하도록 설정하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-197">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="3f50e-198">기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="3f50e-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="3f50e-200">여러 위치에서 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-200">Serve files from multiple locations</span></span>

<span data-ttu-id="3f50e-201">`UseStaticFiles` 및 `UseFileServer`는 기본적으로 `wwwroot`를 가리키는 파일 공급자로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="3f50e-202">다른 위치에서 파일을 제공하기 위해 다른 파일 공급자와 `UseStaticFiles` 및 `UseFileServer`의 추가 인스턴스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="3f50e-203">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/15578)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="3f50e-204">정적 파일의 보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="3f50e-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="3f50e-205">`UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="3f50e-206">프로덕션 환경에서는 디렉터리 검색을 비활성화하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="3f50e-207">`UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="3f50e-208">전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="3f50e-209">`<content_root>/wwwroot`와 같은 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="3f50e-210">MVC 뷰, Razor Pages, 구성 파일 등과 해당 파일을 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="3f50e-211">`UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 기본 파일 시스템의 대/소문자 구분 및 문자 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="3f50e-212">예를 들어 Windows는 대/소문자를 구분하지 않지만 macOS 및 Linux는 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="3f50e-213">IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="3f50e-214">IIS 정적 파일 처리기는 사용되지 않으며 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="3f50e-215">서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="3f50e-216">**모듈** 기능으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="3f50e-217">목록에서 **StaticFileModule**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="3f50e-218">**동작** 사이드바에서 **제거**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="3f50e-219">IIS 정적 파일 처리기를 사용하도록 설정되었으며 **그리고** ASP.NET Core 모듈이 올바르게 구성되지 않은 경우, 정적 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="3f50e-220">예를 들어 *web.config* 파일이 배포되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="3f50e-221">*.cs* 및 *.cshtml*을 포함한 코드 파일을 앱 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 외부에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="3f50e-222">따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="3f50e-223">그러면 서버 쪽 코드가 유출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f50e-224">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3f50e-224">Additional resources</span></span>

* [<span data-ttu-id="3f50e-225">미들웨어</span><span class="sxs-lookup"><span data-stu-id="3f50e-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="3f50e-226">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="3f50e-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f50e-227">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3f50e-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="3f50e-228">HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="3f50e-229">일부 구성은 이러한 파일을 제공하는 데 필수적입니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="3f50e-230">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f50e-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="3f50e-231">정적 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-231">Serve static files</span></span>

<span data-ttu-id="3f50e-232">정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리 내에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="3f50e-233">기본 디렉터리는 *{content root}/wwwroot*이지만, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 메서드를 통해 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-233">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="3f50e-234">자세한 정보는 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="3f50e-235">앱의 웹 호스트에서 콘텐츠 루트 디렉터리를 인식하도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="3f50e-236">`WebHost.CreateDefaultBuilder` 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="3f50e-237">정적 파일은 [웹 루트](xref:fundamentals/index#web-root)에 상대적인 경로를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="3f50e-238">예를 들어 **웹 애플리케이션** 프로젝트 템플릿에는 `wwwroot`폴더 내에 여러 폴더가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="3f50e-239">*images* 하위 폴더에 있는 파일에 액세스하기 위한 URI 형식은 *http://\<server_address>/images/\<image_file_name>* 입니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="3f50e-240">예를 들어 *http://localhost:9189/images/banner3.svg* 와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="3f50e-241">.NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="3f50e-242">.NET Core를 대상으로 하는 경우 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 이 패키지가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="3f50e-243">정적 파일을 제공할 수 있도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="3f50e-244">웹 루트 내부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-244">Serve files inside of web root</span></span>

<span data-ttu-id="3f50e-245">`Startup.Configure` 내에서 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-245">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="3f50e-246">매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 [웹 루트](xref:fundamentals/index#web-root)에 있는 파일을 제공 가능으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="3f50e-247">다음 표시는 *wwwroot/images/banner1.svg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="3f50e-248">위의 코드에서 물결표 문자 `~/`는 [웹 루트](xref:fundamentals/index#web-root)를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="3f50e-249">웹 루트 외부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-249">Serve files outside of web root</span></span>

<span data-ttu-id="3f50e-250">제공할 정적 파일이 [웹 루트](xref:fundamentals/index#web-root) 외부에 있는 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="3f50e-251">요청은 정적 파일 미들웨어를 다음과 같이 구성하여 *banner1.svg* 파일에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="3f50e-252">위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조는 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="3f50e-253">*http://\<server_address>/StaticFiles/images/banner1.svg*에 대한 요청은 *banner1.svg* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="3f50e-254">다음 태그는 *MyStaticFiles/images/banner1.svg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="3f50e-255">HTTP 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="3f50e-255">Set HTTP response headers</span></span>

<span data-ttu-id="3f50e-256"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 개체를 사용하여 HTTP 응답 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-256">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="3f50e-257">다음 코드는 [웹 루트](xref:fundamentals/index#web-root)에서 제공되는 정적 파일을 구성하는 것 외에도 `Cache-Control` 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="3f50e-258"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> 메서드는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-258">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="3f50e-259">개발 환경에서 파일은 10분(600초) 동안 공개적으로 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="3f50e-261">정적 파일 권한 부여</span><span class="sxs-lookup"><span data-stu-id="3f50e-261">Static file authorization</span></span>

<span data-ttu-id="3f50e-262">정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="3f50e-263">*wwwroot* 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="3f50e-264">권한 부여를 기반으로 파일을 제공하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="3f50e-265">파일을 *wwwroot* 외부의 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="3f50e-266">권한 부여가 적용되는 작업 메서드를 통해서 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="3f50e-267"><xref:Microsoft.AspNetCore.Mvc.FileResult> 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-267">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="3f50e-268">디렉터리 검색 사용</span><span class="sxs-lookup"><span data-stu-id="3f50e-268">Enable directory browsing</span></span>

<span data-ttu-id="3f50e-269">디렉터리 검색을 사용하면 웹앱 사용자가 지정된 디렉터리 내의 디렉터리 목록 및 파일을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="3f50e-270">기본적으로 디렉터리 검색은 보안상의 이유로 비활성화되어 있습니다([고려할 사항](#sc) 참조).</span><span class="sxs-lookup"><span data-stu-id="3f50e-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="3f50e-271">`Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 메서드를 호출하여 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-271">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="3f50e-272">`Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 메서드를 호출하여 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="3f50e-273">위의 코드를 통해 URL *http://\<server_address>/MyImages*를 사용하여 각 파일 및 폴더에 대한 링크가 제공되는 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![디렉터리 검색](static-files/_static/dir-browse.png)

<span data-ttu-id="3f50e-275">검색을 활성화하는 경우의 보안 위험에 대한 [고려할 사항](#considerations)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="3f50e-276">다음 예제에서 두 `UseStaticFiles` 호출을 참고하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="3f50e-277">첫 번째 호출은 *wwwroot* 폴더에서 정적 파일 제공을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="3f50e-278">두 번째 호출은 URL *http://\<server_address>/MyImages*를 사용하여 *wwwroot/images* 폴더의 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="3f50e-279">기본 문서 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-279">Serve a default document</span></span>

<span data-ttu-id="3f50e-280">기본 홈페이지를 설정하면 방문자가 사이트를 방문할 때 논리적 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="3f50e-281">사용자가 URI를 정규화하지 않더라도 기본 페이지를 제공하려면 `Startup.Configure`에서 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-281">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="3f50e-282">기본 파일을 제공하려면 `UseStaticFiles` 전에 `UseDefaultFiles`를 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="3f50e-283">`UseDefaultFiles`는 실제로 파일을 제공하지 않는 URL 재작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="3f50e-284">파일을 제공하도록 `UseStaticFiles`를 통해 정적 파일 미들웨어를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="3f50e-285">`UseDefaultFiles`를 사용하면 폴더에 대한 요청이 다음 파일들을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="3f50e-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="3f50e-286">*default.htm*</span></span>
* <span data-ttu-id="3f50e-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="3f50e-287">*default.html*</span></span>
* <span data-ttu-id="3f50e-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="3f50e-288">*index.htm*</span></span>
* <span data-ttu-id="3f50e-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="3f50e-289">*index.html*</span></span>

<span data-ttu-id="3f50e-290">목록에서 찾은 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="3f50e-291">브라우저 URL은 요청된 URI를 계속 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="3f50e-292">다음 코드는 기본 파일 이름을 *mydefault.html*로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="3f50e-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="3f50e-293">UseFileServer</span></span>

<span data-ttu-id="3f50e-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>는 `UseStaticFiles`, `UseDefaultFiles` 및 선택적으로 `UseDirectoryBrowser`의 기능을 병행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="3f50e-295">다음 코드는 정적 파일 및 기본 파일 제공을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="3f50e-296">디렉터리 검색은 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="3f50e-297">다음 코드는 매개 변수가 없는 오버로드에 추가적으로 디렉터리 검색을 활성화하여 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="3f50e-298">다음 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="3f50e-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="3f50e-299">**wwwroot**</span></span>
  * <span data-ttu-id="3f50e-300">**css**</span><span class="sxs-lookup"><span data-stu-id="3f50e-300">**css**</span></span>
  * <span data-ttu-id="3f50e-301">**images**</span><span class="sxs-lookup"><span data-stu-id="3f50e-301">**images**</span></span>
  * <span data-ttu-id="3f50e-302">**js**</span><span class="sxs-lookup"><span data-stu-id="3f50e-302">**js**</span></span>
* <span data-ttu-id="3f50e-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="3f50e-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="3f50e-304">**images**</span><span class="sxs-lookup"><span data-stu-id="3f50e-304">**images**</span></span>
    * <span data-ttu-id="3f50e-305">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="3f50e-305">*banner1.svg*</span></span>
  * <span data-ttu-id="3f50e-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="3f50e-306">*default.html*</span></span>

<span data-ttu-id="3f50e-307">다음 코드는 `MyStaticFiles`의 정적 파일, 기본 파일 및 디렉터리 검색을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="3f50e-308">`EnableDirectoryBrowsing` 속성 값이 `true`인 경우 `AddDirectoryBrowser`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="3f50e-309">URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="3f50e-310">URI</span><span class="sxs-lookup"><span data-stu-id="3f50e-310">URI</span></span>            |                             <span data-ttu-id="3f50e-311">응답</span><span class="sxs-lookup"><span data-stu-id="3f50e-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="3f50e-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="3f50e-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="3f50e-313">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="3f50e-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="3f50e-314">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="3f50e-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="3f50e-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="3f50e-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="3f50e-316">*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 *http://\<server_address>/StaticFiles*는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![정적 파일 목록](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="3f50e-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 `http://{SERVER ADDRESS}/StaticFiles`(후행 슬래시 없음)에서 `http://{SERVER ADDRESS}/StaticFiles/`(후행 슬래시 있음)로 클라이언트 쪽 리디렉션을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="3f50e-319">*StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시가 있어야 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="3f50e-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="3f50e-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="3f50e-321"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 클래스에는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 하는 `Mappings` 속성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-321">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="3f50e-322">다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="3f50e-323">*.rtf* 확장은 대체되며 *.mp4*는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="3f50e-324">[MIME 콘텐츠 형식](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="3f50e-325">비표준 콘텐츠 형식</span><span class="sxs-lookup"><span data-stu-id="3f50e-325">Non-standard content types</span></span>

<span data-ttu-id="3f50e-326">정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="3f50e-327">사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="3f50e-328">요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="3f50e-329">디렉터리 검색이 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="3f50e-330">다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="3f50e-331">위의 코드를 사용하면 알 수 없는 콘텐츠 형식의 파일에 대한 요청은 이미지로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="3f50e-332"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes>를 사용하도록 설정하는 것은 보안상 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-332">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="3f50e-333">기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="3f50e-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="3f50e-335">여러 위치에서 파일 제공</span><span class="sxs-lookup"><span data-stu-id="3f50e-335">Serve files from multiple locations</span></span>

<span data-ttu-id="3f50e-336">`UseStaticFiles` 및 `UseFileServer`은(는) *wwwroot*를 가리키는 파일 공급자를 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="3f50e-337">다른 위치에서 파일을 제공하기 위해 다른 파일 공급자와 `UseStaticFiles` 및 `UseFileServer`의 추가 인스턴스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="3f50e-338">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/15578)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="3f50e-339">고려 사항</span><span class="sxs-lookup"><span data-stu-id="3f50e-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="3f50e-340">`UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="3f50e-341">프로덕션 환경에서는 디렉터리 검색을 비활성화하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="3f50e-342">`UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="3f50e-343">전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="3f50e-344">*\<content_root>/wwwroot*와 같은 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="3f50e-345">MVC 뷰, Razor Pages(2.x에만 해당), 구성 파일 등과 해당 파일을 분리하세요.</span><span class="sxs-lookup"><span data-stu-id="3f50e-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="3f50e-346">`UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 기본 파일 시스템의 대/소문자 구분 및 문자 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="3f50e-347">예를 들어 Windows는 대/소문자를 구분하지 않는 반면 macOS 및 Linux는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="3f50e-348">IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="3f50e-349">IIS 정적 파일 처리기는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="3f50e-350">모듈에서 처리하기 전에는 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="3f50e-351">서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="3f50e-352">**모듈** 기능으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="3f50e-353">목록에서 **StaticFileModule**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="3f50e-354">**동작** 사이드바에서 **제거**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="3f50e-355">IIS 정적 파일 처리기를 사용하도록 설정되었으며 **그리고** ASP.NET Core 모듈이 올바르게 구성되지 않은 경우, 정적 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="3f50e-356">예를 들어 *web.config* 파일이 배포되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="3f50e-357">코드 파일( *.cs* 및 *.cshtml* 포함)을 앱 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 외부에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="3f50e-358">따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="3f50e-359">그러면 서버 쪽 코드가 유출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3f50e-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f50e-360">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3f50e-360">Additional resources</span></span>

* [<span data-ttu-id="3f50e-361">미들웨어</span><span class="sxs-lookup"><span data-stu-id="3f50e-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="3f50e-362">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="3f50e-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
