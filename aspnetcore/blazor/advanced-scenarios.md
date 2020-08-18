---
title: ASP.NET Core Blazor 고급 시나리오
author: guardrex
description: 수동 RenderTreeBuilder 논리를 앱에 통합하는 방법을 포함하여 Blazor의 고급 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
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
uid: blazor/advanced-scenarios
ms.openlocfilehash: 4bd73acd821a8791d7f6cc93545edc2e39a6f2c7
ms.sourcegitcommit: 68d03d1aee8906b53bda66f8f1e0747efc3007e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88051786"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a>ASP.NET Core Blazor 고급 시나리오

작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

## <a name="no-locblazor-server-circuit-handler"></a>Blazor Server 회로 처리기

Blazor Server에서는 코드를 통해 사용자 회로 상태 변경 시 코드를 실행할 수 있게 해주는 ‘회로 처리기’를 정의할 수 있습니다. 회로 처리기는 `CircuitHandler`에서 파생되고 앱의 서비스 컨테이너에 클래스를 등록하여 구현됩니다. 다음 회로 처리기 예제에서는 열린 SignalR 연결을 추적합니다.

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

회로 처리기는 DI를 사용하여 등록됩니다. 회로 인스턴스별로 범위가 지정된 인스턴스가 생성됩니다. 위의 예제에서 `TrackingCircuitHandler`를 사용할 경우, 모든 회로의 상태를 추적해야 하기 때문에 singleton 서비스가 생성됩니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

사용자 지정 회로 처리기의 메서드에서 처리되지 않은 예외가 throw될 경우 이 예외는 Blazor Server 회로에 치명적입니다. 처리기의 코드 또는 호출된 메서드에서 예외를 허용하려면 오류 처리 및 로깅 기능을 사용하여 하나 이상의 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 문에 코드를 래핑합니다.

사용자가 연결을 끊었으며 프레임워크에서 회로 상태를 정리하고 있어서 회로가 종료될 경우 프레임워크는 회로의 DI 범위를 삭제합니다. 범위를 삭제하면 <xref:System.IDisposable?displayProperty=fullName>을 구현하는, 회로 범위가 지정된 DI 서비스가 모두 삭제됩니다. 삭제하는 동안 처리되지 않은 예외를 throw하는 DI 서비스가 있을 경우 프레임워크에서 예외를 기록합니다.

## <a name="manual-rendertreebuilder-logic"></a>수동 RenderTreeBuilder 논리

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>는 C# 코드를 사용한 구성 요소 수동 빌드를 포함하여 구성 요소와 요소를 조작하기 위한 메서드를 제공합니다.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>를 사용하여 구성 요소를 만드는 것은 고급 시나리오입니다. 구성 요소의 형식이 잘못된 경우(예: 닫히지 않은 태그) 정의되지 않은 동작이 발생할 수 있습니다.

다른 구성 요소에 수동으로 빌드할 수 있는 다음 `PetDetails` 구성 요소를 확인합니다.

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

다음 예제에서 `CreateComponent` 메서드의 루프는 세 개의 `PetDetails` 구성 요소를 생성합니다. 시퀀스 번호를 사용하는 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 메서드에서 시퀀스 번호는 소스 코드 줄 번호입니다. Blazor diff 알고리즘은 개별 호출이 아니라 개별 코드 줄에 해당하는 시퀀스 번호를 사용합니다. <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 메서드를 사용하여 구성 요소를 만드는 경우 시퀀스 번호의 인수를 하드 코딩합니다. **계산 또는 카운터를 사용하여 시퀀스 번호를 생성하면 성능이 저하될 수 있습니다.** 자세한 내용은 [시퀀스 번호는 실행 순서가 아니라 코드 줄 번호와 관련이 있음](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) 섹션을 참조하세요.

`BuiltContent` 구성 요소:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <xref:Microsoft.AspNetCore.Components.RenderTree>의 형식을 사용하여 렌더링 작업 ‘결과’를 처리할 수 있습니다. 해당 형식은 Blazor 프레임워크 구현의 내부 세부 정보이며, ‘불안정’하고 이후 릴리스에서 변경될 수 있는 것으로 간주되어야 합니다.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>시퀀스 번호는 실행 순서가 아니라 코드 줄 번호와 관련이 있음

Razor 구성 요소 파일(`.razor`)은 항상 컴파일됩니다. 컴파일 단계를 사용하여 런타임에 앱 성능을 개선하는 정보를 삽입할 수 있으므로 컴파일은 코드 해석보다 잠재적 이점이 있습니다.

해당 개선 사항의 주요 예로 ‘시퀀스 번호’가 있습니다. 시퀀스 번호는 정렬된 개별 코드 줄에서 생성된 해당 출력을 런타임에 표시합니다. 런타임은 이 정보를 사용하여 선형 시간으로 효율적인 트리 diff를 생성하며, 일반적인 트리 diff 알고리즘에서 가능한 속도보다 훨씬 더 빠릅니다.

다음 Razor 구성 요소(`.razor`) 파일을 고려해 보세요.

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

위의 코드는 다음과 같이 컴파일됩니다.

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

코드를 처음 실행할 때 `someFlag`가 `true`인 경우 작성기는 다음을 받게 됩니다.

| 순서 | 형식      | 데이터   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 첫째  |
| 1        | 텍스트 노드 | Second |

`someFlag`가 `false`로 전환되었으며, 태그를 다시 렌더링했다고 가정합니다. 이번에는 작성기가 다음을 받게 됩니다.

| 순서 | 형식       | 데이터   |
| :------: | ---------- | :----: |
| 1        | 텍스트 노드  | Second |

런타임은 diff를 통해 시퀀스 `0`의 항목이 제거된 것을 확인하고 다음과 같은 간단한 *편집 스크립트*를 생성합니다.

* 첫 번째 텍스트 노드를 제거합니다.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>프로그래밍 방식으로 시퀀스 번호를 생성할 경우 발생하는 문제

대신, 다음과 같은 렌더링 트리 작성기 논리를 작성했다고 가정합니다.

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

이제 첫 번째 출력은 다음과 같습니다.

| 순서 | 형식      | 데이터   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 첫째  |
| 1        | 텍스트 노드 | Second |

이 결과는 이전 사례와 동일하므로 부정적인 이슈가 없습니다. 두 번째 렌더링에서 `someFlag`는 `false`이고, 출력은 다음과 같습니다.

| 순서 | 형식      | 데이터   |
| :------: | --------- | ------ |
| 0        | 텍스트 노드 | Second |

이번에는 diff 알고리즘에서 ‘두 가지’ 변경 사항이 발생했음을 확인하고, 다음과 같은 편집 스크립트를 생성합니다.

* 첫 번째 텍스트 노드의 값을 `Second`로 변경합니다.
* 두 번째 텍스트 노드를 제거합니다.

시퀀스 번호를 생성하면 원본 코드에서 `if/else` 분기 및 루프가 있던 위치에 대한 유용한 정보가 모두 손실됩니다. 이로 인해 diff의 길이가 이전보다 **두 배**가 됩니다.

여기서는 간단한 예제를 사용했지만, 복잡하고 깊이 중첩된 구조와 특히 루프를 사용하는 보다 현실적인 사례에서는 일반적으로 성능 비용이 더 높습니다. 삽입 또는 제거된 루프 블록이나 분기를 즉시 확인하는 대신, diff 알고리즘은 렌더링 트리를 재귀적으로 깊이 반복해야 합니다. 이로 인해 이전 구조와 새 구조 간의 관계에 대한 잘못된 정보가 diff 알고리즘에 제공되기 때문에 일반적으로 긴 편집 스크립트를 작성해야 합니다.

### <a name="guidance-and-conclusions"></a>지침 및 결론

* 시퀀스 번호를 동적으로 생성하면 앱 성능이 저하됩니다.
* 컴파일 시간에 캡처하지 않는 한, 필요한 정보가 없기 때문에 프레임워크에서 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.
* 수동으로 구현된 긴 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리 블록을 작성하면 안 됩니다. `.razor` 파일을 사용하고 컴파일러를 통해 시퀀스 번호를 처리하는 것이 좋습니다. 수동 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 논리를 사용해야 하는 경우, 긴 코드 블록을 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> 호출에 래핑된 작은 조각으로 분할합니다. 영역마다 고유한 시퀀스 번호 공간이 있으므로, 각 영역 내에서 0(또는 다른 임의 숫자)부터 다시 시작할 수 있습니다.
* 시퀀스 번호를 하드 코딩한 경우, 시퀀스 번호의 값만 증가하면 diff 알고리즘을 사용할 수 있습니다. 초기 값과 간격은 관련이 없습니다. 한 가지 타당한 옵션은 코드 줄 번호를 시퀀스 번호로 사용하거나 0부터 시작하고 1씩, 100씩 또는 선호하는 간격만큼 늘리는 것입니다. 
* Blazor는 시퀀스 번호를 사용하는 반면, 다른 트리 diff UI 프레임워크는 시퀀스 번호를 사용하지 않습니다. diff는 시퀀스 번호를 사용할 때 훨씬 더 빠르며, Blazor는 `.razor` 파일을 작성하는 개발자를 위해 시퀀스 번호를 자동으로 처리하는 컴파일 단계의 이점이 있습니다.

## <a name="perform-large-data-transfers-in-no-locblazor-server-apps"></a>Blazor Server 앱에서 대량 데이터 전송 수행

일부 시나리오에서는 JavaScript와 Blazor 간에 대량 데이터를 전송해야 합니다. 일반적으로 다음과 같은 경우 대량 데이터 전송이 발생합니다.

* 브라우저 파일 시스템 API를 사용하여 파일을 업로드하거나 다운로드하는 경우
* 타사 라이브러리와의 interop이 필요한 경우

Blazor Server에는 성능 문제를 일으킬 수 있는 큰 단일 메시지의 전달을 방지하기 위한 제한 사항이 있습니다.

JavaScript와 Blazor 간에 데이터를 전송하는 코드를 개발하는 경우 다음 지침을 확인합니다.

* 데이터를 작은 조각으로 분할하고, 서버가 모든 데이터를 받을 때까지 데이터 세그먼트를 순차적으로 보냅니다.
* JavaScript 및 C# 코드에서 큰 개체를 할당하면 안 됩니다.
* 데이터를 보내거나 받을 때 주 UI 스레드를 장기간 차단하면 안 됩니다.
* 프로세스가 완료되거나 취소되면 사용된 메모리를 해제합니다.
* 보안을 위해 다음과 같은 추가 요구 사항을 적용합니다.
  * 전달할 수 있는 최대 파일 또는 데이터 크기를 선언합니다.
  * 클라이언트에서 서버로의 최소 업로드 속도를 선언합니다.
* 서버가 데이터를 받은 후에 데이터를 다음과 같이 처리할 수 있습니다.
  * 모든 세그먼트가 수집될 때까지 메모리 버퍼에 임시로 저장합니다.
  * 즉시 사용합니다. 예를 들어 각 세그먼트가 수신됨에 따라 데이터를 즉시 데이터베이스에 저장하거나 디스크에 쓸 수 있습니다.

다음 파일 업로더 클래스는 클라이언트와의 JS interop을 처리합니다. 업로더 클래스는 JS interop을 사용하여 다음을 수행합니다.

* 클라이언트를 폴링하여 데이터 세그먼트를 보냅니다.
* 폴링 시간이 초과되 면 트랜잭션을 중단합니다.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

앞의 예제에서:

* `maxBase64SegmentSize`를 `maxBase64SegmentSize = segmentSize * 4 / 3`에서 계산된 `8192`로 설정합니다.
* 하위 수준 .NET Core 메모리 관리 API를 사용하여 서버의 메모리 세그먼트를 `uploadedSegments`에 저장합니다.
* `ReceiveFile` 메서드를 사용하여 JS interop을 통해 업로드를 처리합니다.
  * `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`와의 JS interop을 통해 파일 크기를 바이트 단위로 결정합니다.
  * 수신할 세그먼트 수를 계산하여 `numberOfSegments`에 저장합니다.
  * `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`와의 JS interop을 통해 `for` 루프에서 세그먼트를 요청합니다. 디코딩 전에 마지막 세그먼트를 제외한 모든 세그먼트는 8,192바이트여야 합니다. 클라이언트가 효율적인 방식으로 데이터를 보내도록 강제 적용합니다.
  * 수신된 각 세그먼트에 대해 <xref:System.Convert.TryFromBase64String%2A>을 사용하여 디코딩 전에 검사를 수행합니다.
  * 업로드가 완료되면 데이터를 포함하는 스트림이 새 <xref:System.IO.Stream>(`SegmentedStream`)으로 반환됩니다.

분할된 스트림 클래스는 세그먼트 목록을 검색할 수가 없는 읽기 전용 <xref:System.IO.Stream>으로 공개합니다.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

다음 코드는 데이터를 수신할 JavaScript 함수를 구현합니다.

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
