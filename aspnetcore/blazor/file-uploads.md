---
title: ASP.NET Core Blazor 파일 업로드
author: guardrex
description: InputFile 구성 요소를 사용하여 Blazor에서 파일을 업로드하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722944"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="5b4b2-103">ASP.NET Core Blazor 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="5b4b2-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="5b4b2-104">작성자: [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5b4b2-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5b4b2-105">`InputFile` 구성 요소를 사용하여 파일 업로드용을 비롯한 브라우저 파일 데이터를 .NET 코드로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="5b4b2-106">`InputFile` 구성 요소는 `file` 형식의 HTML 입력으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="5b4b2-107">기본적으로 사용자는 단일 파일을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-107">By default, the user selects single files.</span></span> <span data-ttu-id="5b4b2-108">사용자가 한 번에 여러 파일을 업로드할 수 있도록 `multiple` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="5b4b2-109">사용자가 하나 이상의 파일을 선택하면 `InputFile` 구성 요소는 `OnChange` 이벤트를 발생시키고 선택한 파일 목록 및 각 파일에 대한 세부 정보에 액세스할 수 있는 `InputFileChangeEventArgs`를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="5b4b2-110">이미지 파일을 수신하는 구성 요소는 파일에 대한 `RequestImageFileAsync` 편의 메서드를 호출하여 이미지가 앱으로 스트리밍되기 전에 브라우저의 JavaScript 런타임 내에서 이미지 데이터의 크기를 조정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="5b4b2-111">다음 예제에서는 한 구성 요소에서 여러 이미지 파일을 업로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="5b4b2-112">사용자가 선택한 파일에서 데이터를 읽으려면 파일에 대해 `OpenReadStream`을 호출하고 반환된 스트림에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="5b4b2-113">Blazor WebAssembly 앱에서 데이터는 브라우저 내의 .NET 코드로 직접 스트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="5b4b2-114">Blazor Server 앱에서는 스트림에서 파일을 읽을 때 파일 데이터가 서버의 .NET 코드로 스트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b4b2-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
