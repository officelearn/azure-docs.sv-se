---
title: Stream codec Compressed Audio med tal SDK-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du direktuppspelar komprimerat ljud till Azure Speech Services med talet SDK. Tillgängligt för C++, C#, och Java för Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559552"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Använda codec-komprimerad ljud inspelning med talet SDK

Talet SDK: s **komprimerade ljud inspelnings** -API ger ett sätt att strömma komprimerat ljud till tal tjänsten med hjälp av PullStream eller PushStream.

> [!IMPORTANT]
> Strömmande komprimerat ljud stöds bara C++för C#, och Java på Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9).
> Tal SDK-version 1.4.0 eller högre krävs.

För WAV/PCM ser du Mainline tal-dokumentationen.  Utanför WAV/PCM stöds följande codec-komprimerade indataformat:

- MP3-FILEN
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Krav för att använda codec-komprimerad ljud inspelning

Installera dessa ytterligare beroenden för att använda komprimerade ljud indata med talet SDK för Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempel kod med codec komprimerad ljud inspelning

Skapa `PullAudioInputStream` eller`PushAudioInputStream`för att strömma i ett komprimerat ljud format till tal tjänsterna. Skapa sedan en `AudioConfig` från en instans av data ström klassen och ange komprimerings formatet för data strömmen.

Vi antar att du har en data Ströms klass som `myPushStream` heter och använder Opus/OGG. Din kod kan se ut så här:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
