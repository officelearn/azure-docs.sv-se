---
title: Azure Cognitive Services och Cognitive Services SDK API för taligenkänning dokumentation – självstudier, API-referens
description: Lär dig hur du skapar och utvecklar appar med Cognitive Services tal SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 65ff0e47cf7a53d519bfd0c50ea4c3ebd09a5766
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "41987662"
---
# <a name="shipping-an-application"></a>Skicka ett program

Notera den [tal SDK licens](license.md), samt de [meddelanden om tredje part programvara](third-party-notices.md) när du distribuerar i Cognitive Services tal SDK. Läs också den [Microsoft Privacy Statement](https://aka.ms/csspeech/privacy).

Beroende på vilken plattform finns olika beroenden för att köra ditt program.

## <a name="windows"></a>Windows

Cognitive Services tal SDK har testats på Windows 10 och Windows Server 2016.

Cognitive Services tal SDK kräver den [Microsoft Visual C++ Redistributable för Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) i systemet. Du kan hämta installationsprogram för den senaste versionen av den `Microsoft Visual C++ Redistributable for Visual Studio 2017` här:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Om ditt program använder förvaltad kod i `.NET Framework 4.6.1` eller senare krävs på måldatorn.

Media Foundation-bibliotek måste installeras för mikrofon indata. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det är möjligt att använda SDK: N för tal utan dessa bibliotek så länge mikrofon inte används som enheten för ljudinspelning.

De tal SDK-filerna som krävs kan distribueras i samma katalog som ditt program. Det här sättet som ditt program kan komma åt direkt biblioteken. Kontrollera att du väljer rätt version (Win32/x64) matchar ditt program.

| Namn | Funktion
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, som krävs för intern och hanterad distribution
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | krävs för hanterad distribution
| `Microsoft.CognitiveServices.Speech.csharp.dll` | krävs för hanterad distribution

## <a name="linux"></a>Linux

För ett internt program som du behöver för att skicka tal SDK-biblioteket `libMicrosoft.CognitiveServices.Speech.core.so`.
Kontrollera att du väljer versionen (x86, x64) matchar ditt program. Beroende på Linux-version kan du också behöva omfattar följande beroenden:

* Delade bibliotek av GNU C-bibliotek (inklusive POSIX trådar Programming-biblioteket `libpthreads`)
* OpenSSL-bibliotek (`libssl.so.1.0.0`)
* CURL-biblioteket (`libcurl.so.4`)
* Det delade biblioteket för ALSA program (`libasound.so.2`)

På Ubuntu 16.04, till exempel bör GNU C-bibliotek vara installerad som standard. Tre senaste kan installeras med följande kommandon:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
