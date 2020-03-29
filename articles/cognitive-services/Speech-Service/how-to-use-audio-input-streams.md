---
title: Tal SDK-ljudinmatningsströmsbegrepp
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna i Speech SDK:s API för ljudinmatningsström.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109946"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Om API:et för ljudinmatningsström för Tal SDK

Speech SDK:s **API för ljudinmatningsström** är ett sätt att strömma ljud till kännenheterna i stället för att använda antingen mikrofonen eller API:erna för indatafilen.

Följande steg krävs när du använder ljudingångsströmmar:

- Identifiera formatet på ljudströmmen. Formatet måste stödjas av tal-SDK och taltjänsten. För närvarande stöds endast följande konfiguration:

  Ljudexempel i PCM-format, en kanal, 16000 samplingar per sekund, 32000 byte per sekund, två blockjustering (16 bitar inklusive utfyllnad för ett prov), 16 bitar per prov.

  Motsvarande kod i SDK för att skapa ljudformatet ser ut så här:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Se till att koden kan tillhandahålla RAW-ljuddata enligt dessa specifikationer. Om ljudkällans data inte matchar formaten som stöds måste ljudet kodas om till önskat format.

- Skapa din egen ljudinmatningsströmsklass som härleds från `PullAudioInputStreamCallback`. Genomföra `Read()` och `Close()` medlemmar. Den exakta funktionssignaturen är språkberoende, men koden liknar det här kodexemplet:

  ```csharp
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

- Skapa en ljudkonfiguration baserat på ljudformat och inmatningsström. Passera både din vanliga talkonfiguration och ljudinmatningskonfigurationen när du skapar din identifierare. Ett exempel:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Se hur du känner igen tal i C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
