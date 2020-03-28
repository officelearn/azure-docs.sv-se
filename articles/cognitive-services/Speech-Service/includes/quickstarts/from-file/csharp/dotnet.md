---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: c92a3d1219358d7fe15cfe0ec93f1e720ee80af8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79082148"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure Speech-resurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=dotnet)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Ladda projektet och `Program.cs`öppna .
3. Ladda ner <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a> och lägg till den i ditt projekt.
    - Spara *filen Whatstheweatherlike.wav bredvid* `Program.cs` filen.
    - Välj **Lägg till > befintligt objekt**i projektets lösningsutforskare . **Solution Explorer**
    - Välj *filen Whatstheweatherlike.wav* och välj sedan knappen **Lägg till.**
    - Högerklicka på den nyligen tillagda filen, välj **Egenskaper**.
    - Ändra **kopian till utdatakatalogen** så att **den alltid kopieras**.

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt. Observera att du har skapat en `RecognizeSpeechAsync()`asynkron metod som kallas .

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>Skapa en talkonfiguration

Innan du kan `SpeechRecognizer` initiera ett objekt måste du skapa en konfiguration som använder din prenumerationsnyckel och prenumerationsregion. Infoga den här `RecognizeSpeechAsync()` koden i metoden.

> [!NOTE]
> I det `FromSubscription()` här exemplet `SpeechConfig`används metoden för att skapa . En fullständig lista över tillgängliga metoder finns i [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
> Tal-SDK kommer som standard att känna igen med hjälp av en-us för språket, se [Ange källspråk för tal till text](../../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Skapa en ljudkonfiguration

Nu måste du skapa `AudioConfig` ett objekt som pekar på ljudfilen. Det här objektet skapas inuti en medsats för att säkerställa korrekt frisläppande av ohanterat resurser. Infoga den `RecognizeSpeechAsync()` här koden i metoden, precis under talkonfigurationen.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Initiera en SpeechRecognizer

Nu ska vi skapa `SpeechRecognizer` objektet `SpeechConfig` med `AudioConfig` hjälp av och objekt som skapats tidigare. Det här objektet skapas också inuti en medsats för att säkerställa korrekt frisläppande av ohanterat resurser. Infoga den `RecognizeSpeechAsync()` här koden i metoden i den ```AudioConfig``` medsats som radbryts i objektet.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Känna igen en fras

Från `SpeechRecognizer` objektet ska du anropa `RecognizeOnceAsync()` metoden. Med den här metoden kan taltjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta känna igen tal.

Lägg till den här koden i satsen med användningssatsen:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>Visa igenkänningsresultat (eller fel)

När igenkänningsresultatet returneras av taltjänsten bör du göra något med den. Vi ska hålla det enkelt och skriva ut resultatet till konsolen.

Lägg till den `RecognizeOnceAsync()`här koden i satsen nedan:

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"We recognized: {result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="check-your-code"></a>Kontrollera din kod

Nu ska koden se ut så här:

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
                var result = await recognizer.RecognizeOnceAsync();

                switch (result.Reason)
                {
                    case ResultReason.RecognizedSpeech:
                        Console.WriteLine($"We recognized: {result.Text}");
                        break;
                    case ResultReason.NoMatch:
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        break;
                    case ResultReason.Canceled:
                        var cancellation = CancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
                
                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                        break;
                }
            }
        }
    }
}
```

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår taligenkänning med taltjänsten.

1. Kompilera koden: Välj **Bygg** > **bygglösning**på menyraden *i Visual Studio*.
2. Starta appen: Välj > **Felsökning Avsöka Startfelsökning** på menyraden eller tryck på **F5**. **Debug**
3. Startigenkänning: Ljudfilen skickas till taltjänsten, transkriberas som text och återges i konsolen.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
