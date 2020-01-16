---
title: 'Snabb start: identifiera tal från en Audio- C++ fil, (Linux)-tal service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: wolfma
ms.openlocfilehash: 7cd4faaebd3a16b927fcbb1f7752f4fb404565d8
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037760"
---
## <a name="prerequisites"></a>Krav

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=linux)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Skapa en C++-källfil med namnet `helloworld.cpp` och klistra in följande kod i den.

   ```cpp
    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech recognizer using a WAV file. The default language is "en-us".
    // Replace with your own audio file name.
    auto audioInput = AudioConfig::FromWavFileInput("whatstheweatherlike.wav");
    auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
    cout << "Recognizing first result...\n";

    // Starts speech recognition, and returns after a single utterance is recognized. The end of a
    // single utterance is determined by listening for silence at the end or until a maximum of 15
    // seconds of audio is processed.  The task returns the recognition text as result.
    // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
    // shot recognition like command or query.
    // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
    auto result = recognizer->RecognizeOnceAsync().get();

    // Checks result.
    switch (result->Reason)
    {
        case ResultReason::RecognizedSpeech:
            cout << "RECOGNIZED: Text=" << result->Text << std::endl;
            break;
        case ResultReason::NoMatch:
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
            break;
        case ResultReason::Canceled:
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

            if (cancellation->Reason == CancellationReason::Error)
            {
                cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
            break;
    }
   ```

1. Ersätt strängen `YourSubscriptionKey` i den nya filen med din prenumerationsnyckel för Speech-tjänsten.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Ersätt strängen `whatstheweatherlike.wav` med ditt eget fil namn.

> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

## <a name="build-the-app"></a>Skapa appen

> [!NOTE]
> Se till att ange kommandon nedan som en _enda kommandorad_. Det enklaste sättet att göra det är att kopiera kommandot med hjälp av **kopieringsknappen** bredvid varje kommando och klistra in den i gränssnittsprompten.

* Kör följande kommando för att skapa programmet i ett **x64**-system (64-bitars).

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Kör följande kommando för att skapa programmet i ett **x86**-system (32-bitars).

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* Kör följande kommando i ett **arm64** -system (64-bitars) för att skapa programmet.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Kör appen

1. Konfigurera inläsarens bibliotekssökväg så att den pekar på Speech SDK-biblioteket.

   * På ett **x64**-system (64-bitars) anger du följande kommando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * På ett **x86**-system (32-bitars) anger du följande kommando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * I ett **arm64** -system (64-bitars) anger du följande kommando.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Kör appen.

   ```sh
   ./helloworld
   ```

1. Ljud filen skickas till tal tjänsten och den första uttryck i filen skrivs till text, som visas i samma fönster.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
