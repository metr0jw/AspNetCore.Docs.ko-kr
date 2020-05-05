---
title: ASP.NET Core Blazor 서버 앱 보호
author: guardrex
description: Blazor 서버 앱을 ASP.NET Core 애플리케이션으로 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 324b3e8ed2d931c81bb41381caa469b0ec2f2fda
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727806"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>ASP.NET Core Blazor 서버 앱 보호

[Luke Latham](https://github.com/guardrex)으로

## <a name="blazor-server-project-template"></a>Blazor 서버 프로젝트 템플릿

프로젝트를 만들 때 인증을 위해 Blazor 서버 프로젝트 템플릿을 구성할 수 있습니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.

**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.

다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.

* **인증 없음**
* **개별 사용자 계정** &ndash; 사용자 계정은 다음과 같이 저장할 수 있습니다.
  * ASP.NET Core의 [ID](xref:security/authentication/identity) 시스템을 사용하여 앱에 저장
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용
* **회사 또는 학교 계정**
* **Windows 인증**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.

| 인증 메커니즘 | 설명 |
| ------------------------ | ----------- |
| `None`(기본값)         | 인증 없음 |
| `Individual`             | ASP.NET Core ID를 사용하여 앱에 저장된 사용자 |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자 |
| `SingleOrg`              | 단일 테넌트에 대한 조직 인증 |
| `MultiOrg`               | 여러 테넌트에 대한 조직 인증 |
| `Windows`                | Windows 인증 |

`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.

* 프로젝트의 폴더를 만듭니다.
* 프로젝트의 이름을 지정합니다.

자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. <xref:blazor/get-started> 문서의 Mac용 Visual Studio 지침을 따릅니다.

1. **새 Blazor 서버 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.

1. ASP.NET Core ID를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

<xref:blazor/get-started> 문서의 .NET Core CLI 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.

| 인증 메커니즘 | 설명 |
| ------------------------ | ----------- |
| `None`(기본값)         | 인증 없음 |
| `Individual`             | ASP.NET Core ID를 사용하여 앱에 저장된 사용자 |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자 |
| `SingleOrg`              | 단일 테넌트에 대한 조직 인증 |
| `MultiOrg`               | 여러 테넌트에 대한 조직 인증 |
| `Windows`                | Windows 인증 |

`-o|--output` 옵션을 사용하면 명령에서 `{APP NAME}` 자리 표시자에 제공된 값을 사용하여 다음을 수행합니다.

* 프로젝트의 폴더를 만듭니다.
* 프로젝트의 이름을 지정합니다.

자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.

---

## <a name="secure-an-existing-app"></a>기존 앱 보호

Blazor 서버 앱은 ASP.NET Core 앱과 동일한 방식으로 보안을 위해 구성됩니다. 자세한 내용은 <xref:security/index>의 문서를 참조하세요.