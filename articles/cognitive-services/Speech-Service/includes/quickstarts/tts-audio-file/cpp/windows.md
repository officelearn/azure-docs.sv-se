---
title: 'Snabbstart: Syntetisera tal i ljudfil, C++ (Windows) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: wolfma
ms.openlocfilehash: d9ddc288c7e6aeaba2a239c4148bcd4360647075
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671199"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande utdrag:

   ```C++

    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech synthesizer using file as audio output.
    // Replace with your own audio file name.
    auto fileName = "helloworld.wav";
    auto fileOutput = AudioConfig::FromWavFileOutput(fileName);
    auto synthesizer = SpeechSynthesizer::FromConfig(config, fileOutput);

    // Converts the specified text to speech, saving the audio data in the file specified above.
    // Replace with your own text.
    auto text = "Hello world!";
    auto result = synthesizer->SpeakTextAsync(text).get();

    // Checks result for successful completion or errors.
    if (result->Reason == ResultReason::SynthesizingAudioCompleted)
    {
        cout << "Speech synthesized to [" << fileName << "] for text [" << text << "]" << std::endl;
    }
    else if (result->Reason == ResultReason::Canceled)
    {
        auto cancellation = SpeechSynthesisCancellationDetails::FromResult(result);
        cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

        if (cancellation->Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
            cout << "CANCELED: ErrorDetails=[" << cancellation->ErrorDetails << "]" << std::endl;
            cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }

   ```

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Ersätt strängen `helloworld.wav` med ditt eget filnamn.

1. Välj Spara**alla** **på** > menyraden .

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Starta felsökning** (eller tryck på **F5)** för att starta **helloworld-programmet.**

1. Texten konverteras till tal och sparas i de angivna ljuddata.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
