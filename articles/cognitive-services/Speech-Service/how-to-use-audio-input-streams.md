---
title: Koncept för tal-SDK ljud indata strömmar
titleSuffix: Azure Cognitive Services
description: 'En översikt över funktionerna i API: t för tal SDK: s ljud inspelnings ström.'
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: e00feed416eb3e06b703a2ef4fe040f0c815716e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464297"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Om API för tal-SDK ljud inspelnings ström

API: t för tal-SDK är ett sätt att strömma ljud strömmar till igenkännings funktionen istället för att använda antingen mikrofonen eller indatafil **-API:** er.

Följande steg krävs när du använder ljud inspelnings strömmar:

- Identifiera formatet för ljud strömmen. Formatet måste stödjas av talet SDK och tal tjänsten. För närvarande stöds endast följande konfiguration:

  Ljud exempel i PCM-format, en kanal, 16000 sampel per sekund, 32000 byte per sekund, två block justera (16 bitar, inklusive utfyllnad för ett exempel), 16 bitar per sampel.

  Motsvarande kod i SDK: n för att skapa ljud formatet ser ut så här:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Kontrol lera att din kod kan tillhandahålla rå ljuddata enligt dessa specifikationer. Om dina ljud käll data inte matchar de format som stöds måste ljudet kodas till det format som krävs.

- Skapa din egen klass för ljud indata-Stream härledda från `PullAudioInputStreamCallback`. Implementera `Read()` och `Close()` medlemmar. Den exakta funktions signaturen är språk beroende, men koden kommer att se ut ungefär som i det här kod exemplet:

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Skapa en ljud konfiguration baserat på ditt ljud format och en indataströmmen. Skicka både din vanliga tal konfiguration och konfigurationen av ljud indata när du skapar din tolk. Till exempel:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se identifiera tal iC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
