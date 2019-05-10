---
title: Stream-codec komprimerade ljud med tal SDK - Speech Services
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att strömma komprimerat ljud till Azure Speech Services med tal SDK. Tillgänglig för C++, C#, och Java för Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 41a55eca321cbe1bfa23a889b8e3ce7c701ce769
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468055"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Med hjälp av codec komprimerade ljudindata med Speech-SDK

Tal-SDK **filer som komprimerats ljud indata Stream** API gör det möjligt att strömma komprimerat ljud till tal-tjänsten med hjälp av PullStream eller PushStream.

> [!IMPORTANT]
> Strömning komprimerat ljud stöds bara för C++, C#, och Java i Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9).

Wav PCM/i dokumentationen om likriktade tal.  Utanför wav/PCM stöds följande indata codec komprimerat format:

- MP3-FILEN
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Krav för att använda codec komprimerade ljudindata

Installera dessa ytterligare beroenden för att använda komprimerade ljudindata med Speech-SDK för Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempelkod med codec komprimerade ljudindata

För att strömma i en komprimerad ljudformatet att Speech Services, skapa `PullAudioInputStream` eller `PushAudioInputStream`. Skapa sedan en `AudioConfig` från en instans av klassen stream anger komprimeringsformatet på dataströmmen.

Anta att du har en Indataströmmen klass med namnet `myPushStream` och använder OPUS/OGG. Din kod kan se ut så här:

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
