---
title: AudioInputStream begrepp | Microsoft Docs
description: En översikt över funktionerna i AudioInputStream-API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 528356473c4221a815fa68cbec3426866c4cbd23
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356294"
---
# <a name="about-the-audio-input-stream-api"></a>Om ljudinsignal strömma API

Den **indata ljudström** API är ett sätt att strömma ljudströmmar till identifierare i stället för mikrofonen eller indatafilen API: er.

## <a name="api-overview"></a>API-översikt

API: N använder två komponenter, den `AudioInputStream` (ljud rådata) och `AudioInputStreamFormat`.

Den `AudioInputStreamFormat` definierar formatet för ljud data. Det kan jämföras med vanlig `WAVEFORMAT` struktur för filer i Windows.

  - `FormatTag`

    Formatet för ljud. Tal SDK stöder för närvarande endast `format 1` (PCM - little endian).

  - `Channels`

    Antal kanaler. Den aktuella tal tjänsten stöder bara en kanal (monoljud) ljud material.

  - `SamplesPerSec`

    Samplingsfrekvens. En typisk mikrofon registrering har 16000 prover per sekund.

  - `AvgBytesPerSec`

    Genomsnittligt antal byte per sekund, beräknat som `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Genomsnittligt antal byte per sekund kan vara olika för ljudströmmar som använder variabeln bithastighet.

  - `BlockAlign`

    Storleken på en enda ram beräknas som `Channels * ceil(wBitsPerSample, 8)`. På grund av utfyllnad, kan det faktiska värdet vara högre än det här värdet.

  - `BitsPerSample`

    Bitar per sampling. En typisk ljudström använder 16 bitar per sampling (CD kvalitet).

Den `AudioInputStream` basklass åsidosätts av din anpassade dataströmadapter. Det här kortet har du implementerar dessa funktioner:

   - `GetFormat()`

     Den här funktionen kallas för att få ljudströmmen format. Hämtar en pekare till AudioInputStreamFormat buffert.

   - `Read()`

     Den här funktionen anropas för att hämta data från ljudströmmen. En parameter är en pekare till att kopiera ljud data i bufferten. Den andra parametern är storleken på bufferten. Funktionen returnerar antalet byte som kopieras till bufferten. Returvärdet för `0` anger slutet på dataströmmen.

   - `Close()`

     Den här funktionen kallas för att stänga.

## <a name="usage-examples"></a>Exempel på användning

Följande steg ingår i allmänhet när du använder inkommande ljudströmmar:

  - Identifiera format för ljudströmmen. Formatet måste stödjas av SDK och tjänsten tal. För närvarande stöds följande konfiguration:

    En ljudformatet taggen (PCM), en kanal, 16000 prover per sekund, 32000 byte per sekund, två block justera (16-bitars inklusive utfyllnaden för ett exempel), 16 bitar per prov

  - Kontrollera att din kod kan ge ljud rådata om specifikationer som anges ovan. Om dina ljud källdata inte matchar format som stöds, vara ljuduppspelningen kodas till formatet som krävs.

  - Härledd din anpassade ljud Indataströmmen klass från `AudioInputStream`. Implementera den `GetFormat()`, `Read()`, och `Close()` igen. Den exakta funktionssignaturen är språkberoende men koden ser ut ungefär som detta kodexempel::

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

  - Använd din ljud Indataströmmen:

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

  - På vissa språk kan den `contosoStream` måste tas bort explicit när den är klar. Du kan inte frigöra AudioStream innan fullständig indata är skrivskyddat. I ett scenario med `StopContinuousRecognitionAsync` och `StopContinuousRecognitionAsync` kräver ett begrepp som visas i det här exemplet:

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

* [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du identifierar tal i C#](quickstart-csharp-windows.md)
