---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 296dea2e92d494cb9feaeb9f0c942b6a7da57abb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656545"
---
:::row:::
    :::column span="3":::
        C++ Speech SDK är tillgängligt på Windows, Linux och macOS. Mer information finns i <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet-paket

C++ Tal-SDK kan installeras från **Pakethanteraren** med följande `Install-Package` kommando.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++-binärfiler och rubrikfiler

Alternativt kan C++ Tal SDK installeras från binärfiler. Ladda ner SDK som en <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">.tar paket <span class="docon docon-navigate-external x-hidden-focus"></span> </a> och packa upp filerna i en katalog som du väljer. Innehållet i det här paketet (som innehåller rubrikfiler för både x86- och x64-målarkitekturer) är strukturerat enligt följande:

  | Sökväg                   | Beskrivning                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licens                                              |
  | `ThirdPartyNotices.md` | Meddelanden från tredje part                                  |
  | `include`              | Header-filer för C++                                 |
  | `lib/x64`              | Inbyggt x64-bibliotek för länkning med ditt program |
  | `lib/x86`              | Inbyggt x86-bibliotek för länkning med ditt program |

  Om du vill skapa ett program kopierar eller flyttar du de binärfiler som krävs (och biblioteken) till utvecklingsmiljön. Inkludera dem som krävs i din byggprocess.

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Snabbstarts-C++-källkod för Windows, Linux och macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>