---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 138a9ef9d483ca0d460bab7185d646669650f83e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399871"
---
:::row:::
    :::column span="3":::
        Tal-SDK: n stöder Windows 10 och Windows Server 2016 eller senare versioner. Tidigare versioner stöds **inte** officiellt. Det är möjligt att använda delar av talet SDK med tidigare versioner av Windows, även om det inte rekommenderas.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Systemkrav

Talet SDK i Windows kräver <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ Redistributable för Visual Studio 2019 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i systemet.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Installera för x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Installera för x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Installera för ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

För inmatade mikrofoner måste Media Foundation-bibliotek installeras. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det är möjligt att använda tal-SDK utan dessa bibliotek, så länge en mikrofon inte används som enhet för ljud inspelning.

De nödvändiga SDK-filerna för tal kan distribueras i samma katalog som ditt program. På så sätt kan ditt program komma åt biblioteken direkt. Kontrol lera att du väljer rätt version (x86/x64) som matchar ditt program.

| Name                                            | Funktion                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, krävs för intern och hanterad distribution |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Krävs för hanterad distribution                      |

> [!NOTE]
> Från och med versionen av 1.3.0 behövs `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` inte längre filen (levererades i tidigare versioner). Funktionerna är nu integrerade i Core SDK.

> [!IMPORTANT]
> Se till att biblioteken ingår i projektets distributions inställningar för Windows Forms app (.NET Framework) C#-projekt. Du kan kontrol lera detta `Properties -> Publish Section`under. Klicka på `Application Files` knappen och hitta motsvarande bibliotek i listan rulla nedåt. Kontrol lera att värdet är inställt på `Included`. Visual Studio kommer att inkludera filen när projektet publiceras/distribueras.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
