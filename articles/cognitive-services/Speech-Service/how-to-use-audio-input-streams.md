---
title: AudioInputStream begrepp | Microsoft Docs
description: 'En översikt över funktionerna i AudioInputStream API: et.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 0eafa7e88df5d00a67646ca7f82ca027602a40b3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071454"
---
# <a name="about-the-audio-input-stream-api"></a>Om ljudet indata stream API: er

Den **ljud indata Stream** API gör det möjligt att strömma ljudströmmar till identifierarna istället för att använda mikrofonen eller indatafilen API: er.

## <a name="api-overview"></a>API-översikt

API: et använder två komponenter, den `AudioInputStream` (ljud rådata) och `AudioInputStreamFormat`.

Den `AudioInputStreamFormat` definierar formatet på ljuddata. Det kan jämföras med standarden `WAVEFORMAT` struktur för wave filer på Windows.

  - `FormatTag`

    Formatet på ljudet. Tal-SDK stöder för närvarande endast `format 1` (PCM - little endian).

  - `Channels`

    Antal kanaler. Den aktuella taltjänsten stöder bara en kanal (mono) ljud material.

  - `SamplesPerSec`

    Samplingsfrekvensen. En typisk mikrofoninspelning har 16000 exempel per sekund.

  - `AvgBytesPerSec`

    Genomsnittligt antal byte per sekund, beräknad som `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Genomsnittligt antal byte per sekund kan vara olika för ljudströmmar som använder variabeln bithastighet.

  - `BlockAlign`

    Storleken på en enda bildruta beräknas som `Channels * ceil(wBitsPerSample, 8)`. På grund av utfyllnad, kan det faktiska värdet vara högre än det här värdet.

  - `BitsPerSample`

    Bitar per sampling. En typisk ljudström använder 16 bitar per sampling (CD kvalitet).

Den `AudioInputStream` basklass kommer att åsidosättas av din anpassade stream-kort. Det här kortet har att implementera dessa funktioner:

   - `GetFormat()`

     Den här funktionen anropas för att hämta formatet för ljudströmmen. Hämtar en pekare till AudioInputStreamFormat-bufferten.

   - `Read()`

     Den här funktionen anropas för att hämta data från ljudströmmen. En parameter är en pekare till bufferten att kopiera data till ljud. Den andra parametern är storleken på bufferten. Funktionen returnerar antalet byte som kopieras till bufferten. Returvärdet `0` anger slutet av dataströmmen.

   - `Close()`

     Den här funktionen anropas för att stänga.

## <a name="usage-examples"></a>Exempel på användning

I allmänhet ingår följande steg när du använder inkommande ljudströmmar:

  - Identifiera formatet för ljudströmmen. Formatet måste stödjas av SDK och speech-tjänsten. För närvarande stöds följande konfiguration:

    En ljudformatet taggen (PCM), en kanal, 16000 exempel per sekund, 32000 byte per sekund, två block justera (16-bitars inklusive utfyllnad för ett exempel), 16 bitar per exempel

  - Kontrollera att din kod kan ge ljud rådata om specifikationer som anges ovan. Om dina ljud källdata inte matchar format som stöds, måste ljudet ska kodas i formatet som krävs.

  - Härleda anpassade ljud Indataströmmen klassen från `AudioInputStream`. Implementera de `GetFormat()`, `Read()`, och `Close()` igen. Den exakta funktionssignaturen är språkberoende, men koden ser ut ungefär som detta kodexempel::

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - Använd dina ljud Indataströmmen:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - På vissa språk kan den `contosoStream` måste tas bort explicit när erkännande är klar. Du kan inte släppa AudioStream innan fullständig indata läses. I ett scenario med `StopContinuousRecognitionAsync` och `StopContinuousRecognitionAsync` krävs ett begrepp som illustreras i det här exemplet:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
