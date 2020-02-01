---
title: Utveckla appar med tjänsten Speech SDK-tal
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar ett program som använder tal-SDK på plattformar som stöds.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 4f75adba27c8173f918fa1afbd44f307d50eb995
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902019"
---
# <a name="ship-an-application"></a>Skicka ett program

Notera den [tal SDK licens](https://aka.ms/csspeech/license201809), samt de [meddelanden om programvara från tredje part](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) när du distribuerar Azure Cognitive Services tal SDK. Läs också den [Microsoft Privacy Statement](https://aka.ms/csspeech/privacy).

Beroende på vilken plattform finns olika beroenden för att köra ditt program.

## <a name="windows"></a>Windows

Cognitive Services tal SDK har testats på Windows 10 och Windows Server 2016.

Cognitive Services Speech SDK kräver [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) i systemet. Du kan hämta installationsprogram för den senaste versionen av den `Microsoft Visual C++ Redistributable for Visual Studio 2019` här:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Om programmet använder förvaltad kod i `.NET Framework 4.6.1` eller senare krävs på måldatorn.

Media Foundation-bibliotek för mikrofon indata måste vara installerad. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det går att använda SDK: N för tal utan dessa bibliotek, så länge en mikrofon inte används som enheten för ljudinspelning.

De tal SDK-filerna som krävs kan distribueras i samma katalog som ditt program. Det här sättet som ditt program kan komma åt direkt biblioteken. Kontrollera att du väljer rätt version (Win32/x64) som matchar ditt program.

| Namn | Funktion |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, som krävs för intern och hanterad distribution |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | krävs för hanterad distribution                      |

> [!NOTE]
> Från och med versionen av 1.3.0-filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levererades i tidigare versioner) behövs inte längre. Funktionerna är nu integrerade i Core SDK.

> [!NOTE]
> För .NET Framework-projektet (Windows Forms app C# ) ser du till att biblioteken ingår i projektets distributions inställningar. Du kan kontrol lera detta under `Properties -> Publish Section`. Klicka på knappen `Application Files` och hitta motsvarande bibliotek i listan rulla nedåt. Kontrol lera att värdet är inställt på `Included`. Visual Studio kommer att inkludera filen när projektet publiceras/distribueras.

## <a name="linux"></a>Linux

Talet SDK stöder för närvarande Ubuntu 16,04-, Ubuntu 18,04-och Debian 9-distributioner.
För ett internt program, måste du skicka tal SDK-biblioteket `libMicrosoft.CognitiveServices.Speech.core.so`.
Kontrollera att du väljer den versionen (x86, x64) som matchar ditt program. Beroende på Linux-version kan behöva du också omfattar följande beroenden:

- Delade bibliotek av GNU C-bibliotek (inklusive POSIX trådar Programming-biblioteket `libpthreads`)
- OpenSSL-biblioteket (`libssl.so.1.0.0` eller `libssl.so.1.0.2`)
- Det delade biblioteket för ALSA program (`libasound.so.2`)

På Ubuntu bör GNU C-biblioteken redan installeras som standard. Tre senaste kan installeras med hjälp av följande kommandon:

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

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Se hur du kan känna igen tal i C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
