---
title: Stream codec Compressed Audio med tal SDK-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du direktuppspelar komprimerat ljud till tal tjänsten med talet SDK. Tillgängligt för C++, C#, och Java för Linux, java i Android och mål-C i iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331118"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Använda codec-komprimerad ljud inspelning med talet SDK

Talet SDK: s **komprimerade ljud inspelnings** -API ger ett sätt att strömma komprimerat ljud till tal tjänsten med hjälp av PullStream eller PushStream.

> [!IMPORTANT]
> Strömmande komprimerade indata-ljud C++stöds C#för närvarande för, och Java på Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Det finns också stöd för [java i Android](how-to-use-codec-compressed-audio-input-streams-android.md) och [mål-C på iOS-](how-to-use-codec-compressed-audio-input-streams-ios.md) plattformen.
> Tal SDK-version 1.7.0 eller senare krävs (version 1.10.0 eller senare för RHEL 8, CentOS 8).

För WAV/PCM ser du Mainline tal-dokumentationen.  Utanför WAV/PCM stöds följande codec-komprimerade indataformat:

- MP3-FILEN
- OPUS/OGG
- FLAC
- ALAW i WAV-behållare
- MULAW i WAV-behållare

## <a name="prerequisites"></a>Förutsättningar

Hantering av komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). För gstreamer-binärfiler som inte kompileras och länkas till tal-SDK. Därför måste programutvecklaren installera följande på 18,04, 16,04 och Debian 9 för att använda komprimerat indata-ljud.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

På RHEL/CentOS 8:

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempel kod med codec komprimerad ljud inspelning

Skapa `PullAudioInputStream` eller `PushAudioInputStream`för att strömma i ett komprimerat ljud format till tal tjänsten. Skapa sedan en `AudioConfig` från en instans av data ström klassen och ange komprimerings formatet för data strömmen.

Vi antar att du har en data Ströms klass som heter `myPushStream` och använder OPUS/OGG. Din kod kan se ut så här:

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
* [Se identifiera tal i Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
