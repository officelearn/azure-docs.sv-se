---
title: Stream komprimerade ljud med tal SDK - Speech Services
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att strömma komprimerat ljud till Azure Speech Services med tal SDK. Tillgänglig för C++, C#, och Java för Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012428"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Stream komprimerade ljud med Speech-SDK

Tal-SDK **filer som komprimerats ljud indata Stream** API gör det möjligt att strömma komprimerat ljud till tal-tjänsten med hjälp av PullStream eller PushStream.

> [!IMPORTANT]
> Strömning komprimerat ljud stöds bara för C++, C#, och Java i Linux (Ubuntu 16.04 eller Ubuntu 18.04).
> Stöd är begränsat till MP3- och OPUS/OGG.

## <a name="prerequisites"></a>Förutsättningar

Du måste installera beroendena för att använda komprimerade ljudindata med Speech-SDK för Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Strömmande komprimerat ljud

För att strömma i en komprimerad ljudformatet att Speech Services, skapa `PullAudioInputStream` eller `PushAudioInputStream`. Skapa sedan en `AudioConfig` från en instans av klassen stream anger komprimeringsformatet på dataströmmen.

Anta att du har en Indataströmmen klass med namnet `myPushStream` och använder OPUS/OGG. Det här är koden kan se ut: 

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

* [Få en kostnadsfri prenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
