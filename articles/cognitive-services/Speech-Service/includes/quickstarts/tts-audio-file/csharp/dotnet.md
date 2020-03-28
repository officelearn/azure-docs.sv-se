---
title: 'Snabbstart: Syntetisera tal i ljudfil, C# (.NET) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 4ccc68b38d98c332435e252877d258c8591aab8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925835"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Ladda projektet och `Program.cs`öppna .

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt. Observera att du har skapat en `SynthesisToAudioFileAsync()`asynkron metod som kallas .

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Innan du kan `SpeechSynthesizer` initiera ett objekt måste du skapa en konfiguration som använder din prenumerationsnyckel och prenumerationsregion. Infoga den här `SynthesisToAudioFileAsync()` koden i metoden.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Skapa en ljudkonfiguration

Nu måste du skapa ````AudioConfig```` ett objekt som pekar på ljudfilen. Det här objektet skapas inuti en medsats för att säkerställa korrekt frisläppande av ohanterat resurser. Infoga den `SynthesisToAudioFileAsync()` här koden i metoden, precis under talkonfigurationen.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Initiera en talsyntare

Nu ska vi skapa `SpeechSynthesizer` objektet `SpeechConfig` med `AudioConfig` hjälp av och objekt som skapats tidigare. Det här objektet skapas också inuti en medsats för att säkerställa korrekt frisläppande av ohanterat resurser. Infoga den `SynthesisToAudioFileAsync()` här koden i metoden i den ````AudioConfig```` medsats som radbryts i objektet.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Syntetisera text med SpeakTextAsync

Från `SpeechSynthesizer` objektet ska du anropa `SpeakTextAsync()` metoden. Den här metoden skickar texten till taltjänsten som konverterar den till ljud. Den `SpeechSynthesizer` kommer att använda `config.VoiceName` standardrösten om inte uttryckligen anges.

Lägg till den här koden i satsen med användningssatsen:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Sök efter fel

När syntesresultatet returneras av taltjänsten bör du kontrollera att texten har syntetiserats.

Lägg till den `SpeakTextAsync()`här koden i satsen nedan:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Kontrollera din kod

Nu ska koden se ut så här:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår talsyntes med hjälp av taltjänsten.

1. **Kompilera koden** - Välj **Bygg** > **bygglösning**på menyraden i Visual Studio .
2. **Starta appen** - Välj Felsökning **Debug** > **Avsöka Startfelsökning** på menyraden eller tryck på **F5**.
3. **Starta syntes** - Texten konverteras till tal och sparas i de angivna ljuddata.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
