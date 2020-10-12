---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81399987"
---
:::row:::
    :::column span="3":::
        C++ Speech SDK är tillgänglig i Windows, Linux och macOS. Mer information finns i <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft. CognitiveServices. Speech <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet-paket

Tal-SDK för C++ kan installeras från **Package Manager** med följande `Install-Package` kommando.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++-binärfiler och huvudfiler

Alternativt kan du installera C++ Speech SDK från binärfiler. Ladda ned SDK som ett <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">. tar- <span class="docon docon-navigate-external x-hidden-focus"></span> paket</a> och packa upp filerna i valfri katalog. Innehållet i det här paketet (som innehåller huvudfiler för både x86-och x64-mål arkitekturer) struktureras enligt följande:

  | Sökväg                   | Beskrivning                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licens                                              |
  | `ThirdPartyNotices.md` | Meddelanden från tredje part                                  |
  | `include`              | Huvudfiler för C++                                 |
  | `lib/x64`              | Inbyggt x64-bibliotek för att länka till ditt program |
  | `lib/x86`              | Inbyggt x86-bibliotek för att länka till ditt program |

  Om du vill skapa ett program kopierar eller flyttar du de binärfiler (och bibliotek) som krävs till utvecklings miljön. Ta med dem enligt vad som krävs i din build-process.

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Källkod för Windows, Linux och macOS snabb start C++ <span class="docon docon-navigate-external x-hidden-focus"></span></a>