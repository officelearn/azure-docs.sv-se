---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: dea6a1afaa2348fc5054bee20c534936dcafe5b5
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656638"
---
:::row:::
    :::column span="3":::
        Speech SDK stöder Windows 10 och Windows Server 2016 eller senare versioner. Tidigare versioner stöds **inte** officiellt. Det är möjligt att använda delar av Tal-SDK med tidigare versioner av Windows, även om det inte rekommenderas.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Systemkrav

Tal-SDK i Windows kräver <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ Redist som <span class="docon docon-navigate-external x-hidden-focus"></span> kan tillskrivas Visual Studio 2019</a> på systemet.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Installera för x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Installera för x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Installera för ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

För mikrofoninmatning måste Media Foundations bibliotek installeras. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det är möjligt att använda Tal SDK utan dessa bibliotek, så länge en mikrofon inte används som ljudingångsenhet.

De tal-SDK-filer som krävs kan distribueras i samma katalog som ditt program. På så sätt kan ditt program komma åt biblioteken direkt. Kontrollera att du väljer rätt version (x86/x64) som matchar ditt program.

| Namn                                            | Funktion                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, som krävs för inbyggd och hanterad distribution |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Krävs för hanterad distribution                      |

> [!NOTE]
> Från och med utgivningen 1.3.0 behövs inte filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levererad i tidigare versioner) längre. Funktionaliteten är nu integrerad i kärnan SDK.

> [!IMPORTANT]
> För Projektet Windows Forms App (.NET Framework) C# kontrollerar du att biblioteken ingår i projektets distributionsinställningar. Du kan kontrollera `Properties -> Publish Section`detta under . Klicka `Application Files` på knappen och hitta motsvarande bibliotek från rullningslistan. Kontrollera att värdet är `Included`inställt på . Visual Studio kommer att innehålla filen när projektet publiceras/distribueras.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
