---
title: Utveckla appar med tjänsten Speech SDK-tal
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar appar med hjälp av tal-SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 166ae00085f07ef24d746b60947a31e7680a0f00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490998"
---
# <a name="ship-an-application"></a>Skicka ett program

Titta på [tal-SDK-licensen](https://aka.ms/csspeech/license201809)och [program vara från tredje part](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) när du distribuerar Azure Cognitive Services Speech SDK. Läs även [Microsofts sekretess policy](https://aka.ms/csspeech/privacy).

Beroende på plattform finns det olika beroenden för att köra programmet.

## <a name="windows"></a>Windows

Cognitive Services Speech SDK testas i Windows 10 och på Windows Server 2016.

Cognitive Services Speech SDK kräver [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) i systemet. Du kan ladda ned installations program för den senaste versionen av `Microsoft Visual C++ Redistributable for Visual Studio 2019` här:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Om programmet använder hanterad kod krävs `.NET Framework 4.6.1` eller senare på mål datorn.

För inmatade mikrofoner måste Media Foundation-bibliotek installeras. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det är möjligt att använda tal-SDK utan dessa bibliotek, så länge en mikrofon inte används som enhet för ljud inspelning.

De nödvändiga SDK-filerna för tal kan distribueras i samma katalog som ditt program. På så sätt kan ditt program komma åt biblioteken direkt. Kontrol lera att du väljer rätt version (Win32/x64) som matchar ditt program.

| Namn | Funktion
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, krävs för intern och hanterad distribution
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Krävs för hanterad distribution

>[!NOTE]
> Från och med versionen av 1.3.0-filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levererades i tidigare versioner) behövs inte längre. Funktionerna är nu integrerade i Core SDK.

>[!NOTE]
> För .NET Framework-projektet (Windows Forms app C# ) ser du till att biblioteken ingår i projektets distributions inställningar. Du kan kontrol lera detta under `Properties -> Publish Section`. Klicka på knappen `Application Files` och hitta motsvarande bibliotek i listan rulla nedåt. Kontrol lera att värdet är inställt på `Included`. Visual Studio kommer att inkludera filen när projektet publiceras/distribueras.

## <a name="linux"></a>Linux

Talet SDK stöder för närvarande Ubuntu 16,04-, Ubuntu 18,04-och Debian 9-distributioner.
För ett internt program måste du leverera tal SDK-biblioteket `libMicrosoft.CognitiveServices.Speech.core.so`.
Se till att välja den version (x86, x64) som matchar ditt program. Beroende på Linux-versionen kan du också behöva inkludera följande beroenden:

* De delade biblioteken för GNU C-biblioteket (inklusive programmerings biblioteket för POSIX-trådar `libpthreads`)
* OpenSSL-biblioteket (`libssl.so.1.0.0` eller `libssl.so.1.0.2`)
* Det delade biblioteket för ALSA-program (`libasound.so.2`)

På Ubuntu bör GNU C-biblioteken redan installeras som standard. De sista tre kan installeras med hjälp av följande kommandon:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Installera följande paket på Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se identifiera tal iC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
