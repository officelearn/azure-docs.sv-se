---
title: Azure kognitiva-tjänster, kognitiva Services tal SDK API-dokumentation – självstudier, API-referens | Microsoft Docs
description: Lär dig hur du skapar och utveckla appar med SDK tal kognitiva tjänster
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: d410dda09fdd30181b633c454b1d44610b10c472
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356238"
---
# <a name="shipping-an-application"></a>Leverans av ett program

Se den [tal SDK licens](license.md), samt de [meddelanden om tredje part programvara](third-party-notices.md) när du distribuerar kognitiva Services tal SDK. Granska även den [sekretesspolicy för Microsoft](https://aka.ms/csspeech/privacy).

Beroende på plattform finns olika beroenden för att köra programmet.

## <a name="windows"></a>Windows

Kognitiva Services tal SDK har testats på Windows 10 och Windows Server 2016.

Kognitiva Services tal SDK kräver den [Microsoft Visual C++ Redistributable för Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) i systemet. Du kan hämta installationsprogram för den senaste versionen av den `Microsoft Visual C++ Redistributable for Visual Studio 2017` här:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Om ditt program använder förvaltad kod i `.Net Framework 4.6.1` eller senare krävs på måldatorn.

Media Foundation-bibliotek måste installeras för mikrofon indata. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det är möjligt att använda SDK tal utan dessa bibliotek så länge mikrofon inte används som den enhet för ljudinspelning.

Filerna som krävs tal SDK kan distribueras i samma katalog som ditt program. Det här sättet ditt program kan komma åt direkt biblioteken. Kontrollera att du väljer rätt version (Win32/x64) matchar ditt program.

| Namn | Funktion
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, som krävs för intern och hanterad distribution
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | krävs för distribution av hanterade
| `Microsoft.CognitiveServices.Speech.csharp.dll` | krävs för distribution av hanterade

## <a name="linux"></a>Linux

För ett enhetligt program som du behöver för att leverera tal SDK-biblioteket `libMicrosoft.CognitiveServices.Speech.core.so`.
Kontrollera att du väljer versionen (x86, x64) matchar ditt program. Beroende på Linux-version kan du också behöva omfattar följande beroenden:

* Delade bibliotek för GNU C-bibliotek (inklusive POSIX trådar Programming-biblioteket `libpthreads`)
* OpenSSL-bibliotek (`libssl.so.1.0.0`)
* CURL-biblioteket (`libcurl.so.4`)
* Det delade biblioteket för ALSA program (`libasound.so.2`)

På Ubuntu 16.04, till exempel bör GNU C-bibliotek vara installerad som standard. Tre senaste kan installeras med följande kommandon:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Nästa steg

* [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du identifierar tal i C#](quickstart-csharp-windows.md)
